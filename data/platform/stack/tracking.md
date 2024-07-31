# ⚡ Tracking

## Implementation

Currently, the engineering team is the one implementing the tracking.

We have 2 SDK running together side-by-side:

* Amplitude: we send all data (in-app, marketing and public) to Amplitude without any limit. As Amplitude tracking data gets ingested in BigQuery, we can then access tracking data in SQL
* Segment: we only send in-app data to avoid crazy cost

## Tracking philosophy

* we prefer to track more events than missing information
* there is low chance that a product update doesn't drive tracking change
* every screen, modal, side panel...should generate a view event
* every **space**, **collection** or **organization** new properties should be recorded as a group property
* the data team is always available to provide context and guidance. Feel free to ping in Slack (#team-data) or GitHub PR

## Event still guide

### Event name

Pattern (Bold = optional): `[`**`group1_`**`][group2]_[group3][`**`_group4`**`]`

<details>

<summary>group1</summary>

**Optional**

We distinguish 2 type of events: `view` and `track`. All views will be prefixed with `view_` when `track` will not be prefixed.

</details>

<details>

<summary>group2</summary>

It represents the object generating the event: account, space, organization, billing, edit...

</details>

<details>

<summary>group3</summary>

It's a verb that describe the action being performed: edit, create, remove...

</details>

<details>

<summary>group4</summary>

**Optional**

If group 3 is not enough to get a unique description of the action, then we can add more context by defining group 4: `organization_remove_member`

</details>

### Event property

* We use camelCase naming
* `view` event all share common event properties:
  * `screen`: same as the name of the event. It's useful when we group all view events into a generic one and use groupBy function
  * `screenSection`: the object creating the event
  * `screenSite`: \[app, public\_content, marketing] to group the source of the even
  * `screenURL`: the full URL of the screen
  * `screenPageTitle`: the page title
  * `screenPath`: everything extract after the `/`
  * `screenDomain`: domain generating the view
* We try to avoid giving a too generic name such as `source`, which could mean lot of different things based on the context. When using Any Active Event in Amplitude, we are gonna mix different things into a single field.

### What's a view?

We decided to track any change in the screen as a new view: moving from one page to another, opening a modal, opening a side panel...

{% hint style="info" %}
Basically, every time the focus of the user changes, then it's a new view.
{% endhint %}

We track generic event names as much as we can: navigating from one page to another one under a space will generate only `view_space` events. But we are gonna use [event properties](tracking.md#event-property) to define which page is it.&#x20;

### Group tracking

#### Definition

Definition: group is a way of adding other unique identifier on top of `user_id` to perform analysis grouped by another dimension. It's a dedicated paid feature in Amplitude called [account](https://help.amplitude.com/hc/en-us/articles/115001765532-Account-level-reporting-in-Amplitude).

As a product lead company, we need to optimise our product as much as we can. Then, tracking events only at the user level is not enough. Organization level is indeed needed as a B2B solution - to look at company overal activity - but not only: we need to track group at any GitBook product container level that have a lifecycle and need deep understanding.

A good example is `Space` container: a space has a lifecycle - from create to delete - and it's a critical container in GitBook app. Tracking `space_id` is key to understand how we get from `space_create` to a space receiving million of pageviews.

<details>

<summary>Space</summary>

Exhaustive list [here](https://github.com/GitbookIO/gitbook-x/blob/1ac01df3f2eb535f2cb1b358bb7f73a5ddcaee56/packages/app-analytics/common/types.ts#L271)

```
{
    spaceName: string;
    spaceCreateDate: string;
    spaceCreateWeek: string;
    spaceCreateMonth: string;
    spaceSlug: string | undefined;
    ...
}
```

</details>

<details>

<summary>Organization</summary>

Exhaustive list [here](https://github.com/GitbookIO/gitbook-x/blob/1ac01df3f2eb535f2cb1b358bb7f73a5ddcaee56/packages/app-analytics/common/types.ts#L184)

```
{    
    organizationKey: string;
    organizationName: string;
    organizationType: OrganizationType;
    organizationUseCase?: OrganizationUseCase;
    organizationCommunityType?: OrganizationCommunityType;
    ...
}
```

</details>

<details>

<summary>Collection</summary>

Exhaustive list [there](https://github.com/GitbookIO/gitbook-x/blob/1ac01df3f2eb535f2cb1b358bb7f73a5ddcaee56/packages/app-analytics/common/types.ts#L239)

```
{
    collectionName: string;
    collectionCreateDate: string;
    collectionCreateWeek: string;
    collectionCreateMonth: string;
    /** An id used for staged rollouts and experiments. */
    collectionTestingVariant: number;
    ...
}
```

</details>

<details>

<summary>Change Request</summary>

No group property track but only change request id

</details>

<details>

<summary>Page</summary>

No group property track but only page id

</details>

<details>

<summary>Others</summary>

We have been tracking other object such as screen, team...but it's not useful for now

</details>

#### Example

When we track the following view, we track a bunch of context: the page, attached to a change request, attached to a space, attached to an organization.

Then we track all of these unique id which help us to better understand the context and to perform advanced analysis at any of these objects level.

On top of that, space, collection and organization are objects with properties attached so we can look at their evolution over time. ie: organizationMembers allow us to analyse the evolution of member within an organization over time.

<img src="../.gitbook/assets/file.drawing.svg" alt="View definition" class="gitbook-drawing">

Here is how it translates into tracking

```json
groups:{
         "orgId":"d8f63b60-89ae-11e7-8574-5927d48c4877",
         "spaceId":"ffzy7N5Kq7eKqqt4dokD",
         "changeRequest":"nMlRxD6V2mX2BcF08a1Z",
         "page":"MhVfMqtqUZZ3ZUfovVBD"
}
```

## Legacy tracking

When we launched GitBook X code base in October 2021, we completely re-vamp our tracking. To allow pre and post release analysis, we enabled Amplitude Govern which allow event transformation.

Also, the marketing site tracking was not updated at the same time and still follow pre-GBX launch naming convention.

## Tracking definition

{% embed url="https://github.com/GitbookIO/gitbook-x/blob/1ac01df3f2eb535f2cb1b358bb7f73a5ddcaee56/packages/app-analytics/common/types.ts" %}
In-app tracking reference code
{% endembed %}

