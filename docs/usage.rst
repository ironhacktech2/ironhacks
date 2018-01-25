===============
Forum structure 
===============

You may specify the number of forums that you would like to have under the admin interface. 

=====================
Qualtrix Integration
=====================

All the quizzes created on the Qualtrics platform have a snippet of JavaScript which stores the user data in the response (as known as *Embedded data*).

We store the `user_email` and the `user_id` which are sent in the url.

The code in the Qualtrics quizzes is set in the last block, last question (which may happen to be an empty question, used for tracking):

This approach is being used for all the quizzes: the sign up survey and the other technical quizzes.

===============================
Login and Registration Process
===============================

When the user is not authenticated, the main page displays two buttons:
a login button and a <kbd>Sign up</kbd> button.

When the login button is clicked, the `/login` url is opened.
The login url redirects to the GitHub authentication workflow (OAuth2) where the user should accept access of our GitHub app in their GitHub account.

The difference between the <kbd>Sign in</kbd> and <kbd>Sign up</kbd> buttons is that for the sign in,
we assume that the user already has an account, therefore we make the redirection
to GitHub APIs and authenticate them with their GitHub account. In case they do
*not* have an account on the site, they will have to choose the hack type *after*
the GitHub authentication.

In <kbd>Sign up</kbd> case we will assume that the user doesn't have an account yet, therefore
we ask them to choose the hack type and only then the GitHub authentication is
made. Still, if an existing user clicks the <kbd>Sign up</kbd> button, they will log in
into their account anyways.

This is the authentication workflow:

![](http://i.imgur.com/b0RRhc6.png)

In the `controllers/register.js` file, we create a new user or authenticate an existing user.
For the first-time users, we display the selection of the hack type: Purdue, Bogota and Platzi, and then redirect to the survey.
At this moment, we start a session on the server side, but we don't write any data in the users' database yet.

After the user completes the survey, they are redirected back, and the user account is created.

To redirect the users to the survey, we send the `Location` header from the server to the client. Additional data is added in the url parameters to track the user:

 - `email`: The email of the user
 - `user_id`: The user id
 - `redirect_to`: The redirection url where they will arrive after answering the survey.

Using a JavaScript snippet in the survey page we store the email and the user id in the survey answer and we detect when the survey is done and redirect the users back to the app.

When they finish the survey, they are redirected to the main app and the account is created.

When creating a user account, we do *not* assign a `hack_id` to the user but we wait until the contest is started.
The `hack_id` is a number based on which we create multiple forums inside of the same hack type.
The range can be configured in the admin dashboard.

If the user is already registered, they are authenticated based on the existing data.

The `hack_id` values are assigned either when the user signs up (if the contest is already started) or when the contest starts.
The default value for `hack_id` is `null`. This is changed automatically when the contest starts.

The algorithm which assigns the hack ids is designed to create groups of an equal number of users.
Specifically, the user will join in the hack id with the fewest users at that moment.

The database query is: find how many users we have in each hack id, for a given hack type.
Then, join the current user in the hack id with the fewest users.

```js
function generateGetHackId(hType, name) {
    return cb => {
        User.model.aggregate([{
            $match: {
                "profile.hack_id": { $ne: null },
                "profile.hack_type": name
            }
        }, {
            $group: {
                _id: "$profile.hack_id",
                total: { $sum: 1 }
            }
        }], (err, docs) => {
            if (err) { return cb(0); }
            const ids = Array(hType.subforums_count + 1).fill(0);
            docs.forEach(c => {
                ids[c._id] = c.total;
            });
            let minId = 0;
            let min = ids[minId];
            ids.forEach((count, index) => {
                if (count < min) {
                    minId = index;
                    min = ids[minId];
                }
            });
            cb(minId);
        });
    };
}

forEach(HACK_TYPES, (c, name) => {
    c.getHackId = generateGetHackId(c, name);
});
```

The function which assigns the hack id values to the users is in the `HackTypes` controller (`app/controllers/HackTypes.js`).

This function receives as input a hack type object and groups the users inside of the hack type.

```js
const assignHackIdsToUsers = hType => {
    const usersCursor = User.model.find({
        "profile.hack_id": null,
        "profile.hack_type": hType.name
    }).cursor();

    usersCursor.on("data", cDoc => {
        usersCursor.pause();
        hType.getHackId(uHackId => {
            User.update({
                _id: cDoc._id
            }, {
                profile: {
                    hack_id: uHackId
                }
            }, (err, data) => {
                if (err) { Bloggify.log(err); }
                usersCursor.resume();
            });
        });
    });

    usersCursor.on("error", err => {
        Bloggify.log(err);
    });

    usersCursor.on("end", cDoc => {
        Bloggify.log(`Grouped the studends from ${hType.name}.`);
    });
};
```

The function above is called when the countdown finishes, being triggered by a
scheduler:

```js
const setScheduleForHackType = name => {
    if (name.name) {
        name = name.name;
    }

    let hackTypeObj = HACK_TYPES[name];
    if (hackTypeObj.startSchedule) {
        hackTypeObj.startSchedule.cancel();
    }

    hackTypeObj.startSchedule = schedule.scheduleJob(hackTypeObj.start_date, () => {
        assignHackIdsToUsers(hackTypeObj);
    });
};
```

Or, it may be triggered when we make changes in the admin dashboard, changing the
start of the contest.

```js
if (new Date() > thisHackType.start_date) {
    if (thisHackType.startSchedule) {
        thisHackType.startSchedule.cancel();
    }
    assignHackIdsToUsers(thisHackType);
} else {
    setScheduleForHackType(thisHackType);
}
```

To catch the `save` event, we add a hook using the `addHook` method defined
by the [`bloggify-mongoose`](https://github.com/Bloggify/bloggify-mongoose) plugin.

```js
Settings.model.addHook("post", "save", update);
```
