# Challenge options

As for challenging, this is a great question which we will create a guide for eventually:

1. For bot like behavior, implementing a Captcha is a good start that will get rid of most of the junk. We have a guide for triggering this based on the response from Castle [https://docs.castle.io/docs/using-captcha-with-castle](https://docs.castle.io/docs/using-captcha-with-castle).
2. For the more sophisticated bots as well as abusive users, the next step would be to ask the user to verify their e-mail or phone in order to continue using your app. This would get rid off pretty much all bots, since this flow is really hard to automate at scale. Especially for phone numbers, which are typically hard to acquire.
3. You may want to permanently disallow disposable/junk e-mails, as these typically sign up with no serious intent of using your app [https://docs.castle.io/docs/block-signups-with-spam-emails](https://docs.castle.io/docs/block-signups-with-spam-emails)
4. If you do phone verification, in order to make it harder to abusive users, you may way to allow a phone number to only be used once. You can accomplish this either by implementing it yourself or use the Aggregations feature to check "accounts per phone number"
5. An alternative approach for abusive accounts is to use "hellbanning", ie. let the user believe that the spammy action succeeded (eg. when creating content or sending invites). This can sometimes be more effective to slow down or prevent the abusive behavior. You can use Castle [Lists](https://docs.castle.io/docs/lists) to set up a flow to add users automatically to a "banned users" list, so that you can keep track of this manually. The condition for putting a user in this state should typically be something aggressive, such as the Aggregation checks you've set up, where you are certain that this is behavior that you don't want.

Let me know if this is helpful
