# User & group properties

## User properties

Using Identify call, we set user properties that could be used to filter events.

As today - Feb 2023 - here is the list of current user properties tracked:

```json
"user_properties": {
    "email": "remi@gitbook.io",
    
    // all feature flags have a dedicated entrance
    "featureFlag_DISCOVERY_PAGE": false,
    ...
    "featureFlag_USE_AUTO_DETECT_LONG_POLLING": false,
    
    // attribution from Amplitude SDK to save the first channel
    "initial_referrer": "https://blog.gitbook.com/product-updates/gitbook-3.0-document-everything-from-start-to-ship",
    "initial_referring_domain": "blog.gitbook.com",
    "initial_utm_campaign": "chndth",
    "initial_utm_content": "startup",
    "initial_utm_medium": "trademark",
    "initial_utm_source": "content",
    "initial_utm_term": "eazel7",
    
    // most recent referrer from Amplitude SDK
    "referrer": "https://0xax.gitbooks.io/",
    "referring_domain": "0xax.gitbooks.io",
    
    // GitBook user attributes
    "userCollections": 1,
    "userEmail": "remi@gitbook.io",
    "userEmailDomain": "gitbook.io",
    "userIntegrationsInstalled": [
      "slack"
    ],
    "userName": "Rémi Gonnu",
    "userPrivateCollections": 1,
    "userPrivateSpaces": 17,
    "userPublicCollections": 0,
    "userPublicSpaces": 1,
    "userRiskScore": 80,
    "userSignUpDate": "2019-06-10",
    "userSignUpMonth": "2019-06-01",
    "userSignUpProvider": [
      "google.com"
    ],
    "userSignUpWeek": "2019-06-10",
    "userSpaces": 18,
    "userTestingVariant": 0,
    "user_id": "k78HeV171BTeNuwBVhZhs24cN1g1",
    
    // Most recent attribution from Amplitude SDK
    "utm_campaign": "trademark",
    "utm_content": "powered_by",
    "utm_medium": "referral",
    "utm_source": "public_site_legacy",
    "utm_term": "0xax"
  }
```

## Group properties

To allow advanced analysis, we implemented group - also called account - tracking.

Analysing our data at the user level is not enough and having the ability to peform analysis at other aggregation level open new capabilities.

On top of that, **space**, **collection** and **organization** are objects with properties attached so we can look at their evolution over time. ie: organizationMembers allow us to analyse the evolution of member within an organization over time.

{% tabs %}
{% tab title="spaceId properties" %}
```json
{
    "spaceCreateDate": "2023-02-20",
    "spaceCreateMonth": "2023-02-01",
    "spaceCreateWeek": "2023-02-20",
    "spaceDefaultPermission": "inherit",
    "spaceEditMode": "live",
    "spaceFilesTotal": 26,
    "spaceFooterGroups": 0,
    "spaceFooterHasCopyright": false,
    "spaceFooterHasLogo": false,
    "spaceFooterLinks": 0,
    "spaceHasFooter": false,
    "spaceHasFullLogo": false,
    "spaceHasGitSync": false,
    "spaceHasGoogleAnalytics": false,
    "spaceHasIndexEnabled": false,
    "spaceHasIntercom": false,
    "spaceHasPageRating": false,
    "spaceHasPublicExportPDF": false,
    "spaceHasShareableLink": false,
    "spaceHeaderLinks": 0,
    "spaceHeaderSubLinks": 0,
    "spaceIntegrationsInstalled": [
    ],
    "spaceName": "Amplitude",
    "spacePagesGroup": 3,
    "spacePagesLink": 0,
    "spacePagesSheet": 13,
    "spacePagesTotal": 16,
    "spaceTestingVariant": 0,
    "spaceThemeCorners": "default",
    "spaceThemeMode": "default",
    "spaceVisibility": "private"
}
```
{% endtab %}

{% tab title="orgId properties" %}
```json
{
    "organizationBaseDomain": "tesla",
    "organizationBillingCustomerId": "cus_KMexG0WHvUkATx",
    "organizationBillingCycle": "monthly",
    "organizationBillingInterval": "monthly",
    "organizationBillingPlanId": "plan_Dz59xKFZIcEwaq",
    "organizationBillingPriceId": "price_1LefSp4z3ukbMzIvR1pkyrBw",
    "organizationBillingProduct": "plus",
    "organizationBillingStatus": "trialing",
    "organizationBillingTrialEnd": "2022-09-21T14:55:18.000Z",
    "organizationCollections": 0,
    "organizationCreateDate": "2021-10-07",
    "organizationCreateMonth": "2021-10-01",
    "organizationCreateWeek": "2021-10-04",
    "organizationEmailDomains": [
    ],
    "organizationFeaturesSet": "plus",
    "organizationGuests": 0,
    "organizationIntegrationsInstalled": [
    ],
    "organizationKey": "wJFJRiM0x1o8KzmiBqmt",
    "organizationMembers": 2,
    "organizationName": "Tesla",
    "organizationPrivateCollections": 0,
    "organizationPrivateSpaces": 5,
    "organizationPublicCollections": 0,
    "organizationPublicSpaces": 1,
    "organizationSAMLProviders": [
    ],
    "organizationSpaces": 6,
    "organizationSubDomain": "tesla",
    "organizationTeams": 0,
    "organizationTestingVariant": 0,
    "organizationType": "business",
    "organizationUseCase": "teamKnowledgeBase"
}
```
{% endtab %}

{% tab title="collection" %}
{% hint style="danger" %}
Currently broken
{% endhint %}
{% endtab %}
{% endtabs %}

### How it works

<img src="../.gitbook/assets/file.excalidraw (4).svg" alt="explanation" class="gitbook-drawing">

Here is how it translates into tracking

```json
groups:{
         "orgId":"d8f63b60-89ae-11e7-8574-5927d48c4877",
         "spaceId":"ffzy7N5Kq7eKqqt4dokD",
         "changeRequest":"nMlRxD6V2mX2BcF08a1Z",
         "page":"MhVfMqtqUZZ3ZUfovVBD"
}
```

### Leverage group ids

We don't have full control on the rendering and naming of group ids in Amplitude UI, which could be misleading. On top of that, some events are not well formatted which could create some confusion:

<figure><img src="../.gitbook/assets/image (2).png" alt="" width="370"><figcaption><p>spaceId</p></figcaption></figure>

Here, we can see that we have multiple spaceId defined but the correct one is the last one called `Group name (spaceId)`

## Calculated fields

Amplitude now allows us to create calulcated field on top of user or group properties. it could be very useful.

Here is the list of the most useful one:

* **account\_day\_since\_signup:** gives the number of days between the event day and the sign-up day of the users
* **organization\_day\_since\_creation**: gives the number of days between the event day and the creation day of the organization
* **space\_day\_since\_creation**: gives the number of days between the event day and the creation day of the space
* **isPaid:** true/false boolean to tell if the event is attached under a paid organization
* **channel:** aims to describe the user attribution channel => can <mark style="background-color:red;">**ONLY**</mark> be used on `Marketing Site` event
