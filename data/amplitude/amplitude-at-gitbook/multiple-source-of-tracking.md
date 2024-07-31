# Multiple source of tracking

The application (app.gitbook.com) is the most complex part being tracked in Amplitude but we also track event coming from:

* any of our public documentation: blog.gitbook.com, docs.gitbook.com
* any of public documentation published using GitBook: [https://docs.rocket.chat/](https://docs.rocket.chat/)
* our marketing website: gitbook.com

We decided to track all of this traffic under the same Amplitude account so we could create analysis from visitor on a public content => trademark click => marketing site visitor => product sign-up....and conversion!

The downside is that it adds complexity on how to perform an analysis using Amplitude.

{% hint style="info" %}
Marketing Site and Public View are tracked as **Inactive** event.
{% endhint %}

<details>

<summary>Marketing Site tracking</summary>

All events tracked on the marketing site use the same prefix `marketing_site`

![](<../.gitbook/assets/image (1).png>)

![](<../.gitbook/assets/image (26).png>)

</details>

<details>

<summary>Public Doc tracking</summary>

All events performed on a public documentation use the same prefix `public_content:`

![](<../.gitbook/assets/image (7).png>)

Some events could be fired by public content and in-app content. In that case, please use the event property called `screenSite` to distinguish it:

[https://analytics.amplitude.com/gitbook-com/chart/new/df8k9ca](https://analytics.amplitude.com/gitbook-com/chart/new/df8k9ca)

</details>

&#x20;
