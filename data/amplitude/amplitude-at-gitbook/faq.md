# FAQ

<details>

<summary>How to exclude authenticated users on Marketing Site?</summary>

A large portion of marketing site visitor are existing users. To isolate them, please use the following hack

[https://analytics.amplitude.com/gitbook-com/chart/new/f54gbyg](https://analytics.amplitude.com/gitbook-com/chart/new/f54gbyg)

</details>

<details>

<summary>How to see all my events being sent?</summary>

There are multiple options

**Option 1**

New segmentation chart => ![](<../.gitbook/assets/image (18).png>)=> set your userId

**Option 2**

Use the user look-up functionality on your userId and enable `Live event updates`

**Option 3 (my fav one)**

Use the browser console => network => filter on `_amp` look at all the events being sent using the payload information.

**Note**: you will only see client-side events here but you will have access to group id being sent.

To better present the data, you can always paste the payload in [JSONFormatter](https://jsonformatter.curiousconcept.com):

![](<../.gitbook/assets/image (15).png>)

</details>

<details>

<summary>How to exclude in-app spam users?</summary>

We do have some spam users signing up quite a lot, which makes some numbers going wild.

To exclude them from in-app, please always exclude the cohort called `[Acquisition] - Users - Exclude Spam` ([link](https://analytics.amplitude.com/gitbook-com/cohort/db7esd0) and [example](https://analytics.amplitude.com/gitbook-com/chart/new/qg2oo1r?source=redirect%3A%20new%20chart%20created))

</details>
