# FAQ

<details>

<summary>Can I safely update a chart that someone shared with me?</summary>

Yes. Every change on an existing chart creates a new version of it (see the URL being updated)

Tips: you can always use browser history to revert a change

</details>

<details>

<summary>How can I find my user id?</summary>

You can user SN10 if it's in production, but otherwise you can use the following step by step:

1. visit app.gitbook.com (or app.staging-gitbook.com)
2. In the Chrome open View => Developer => Javascript console
3. Open the network tab in the console
4. Add a filter \_amp
5. Refresh the page&#x20;
6. Click on any \_amp event
7. In the small window, on the payload tab
8. In the displayed code, find the user:XXX&#x20;
9. XXX is your user ID

![](<../.gitbook/assets/image (4).png>)

</details>

<details>

<summary>How can I debug my user?</summary>

Having a way to see which events are being sent while performing some action in the UI, is a great way to discover event name before performing an analysis.

First of all, you need to find your user id like exaplained [here](https://app.gitbook.com/o/d8f63b60-89ae-11e7-8574-5927d48c4877/s/PD6vo63VAIxoJMIoo1eK/\~/changes/3/about-amplitude/faq#how-can-i-find-my-user-id)

:rotating\_light:we should always leverage staging or dev environment to test some flows to avoid creating noise in production :rotating\_light:

**Option 1**

User the [User Look-up page](https://app.amplitude.com/analytics/gitbook-com/activity) in Amplitude:

1. open the page
2. select the correct Amplitude env
3. search for your `User Id`
4. end then you will see below the live event feed

**Option 2**

Use the [event explorer feature](https://help.amplitude.com/hc/en-us/articles/360050836071-Event-Explorer-View-event-streams-in-real-time)

1. Open a new segmentation chart
2. Click Event Explorer
3. Click Add New User
4. Fill it with the `User Id`
5. You can now navigate and you should see some event coming

</details>
