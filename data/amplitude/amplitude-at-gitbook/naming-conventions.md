---
description: >-
  To allow the number of events to scale, we apply a naming convention for event
  and properties.
---

# Naming conventions

### Event name

Pattern (Bold = optional): `[`**`group1_`**`][`**`group2_`**`][group3]_[group4][`**`_group5`**`]`

ie: `view_organization_settings_billing`

<details>

<summary>group1  (none)</summary>

**Optional**

We track 3 products under the same Amplitude account and need a way to distinguish then easiliy:&#x20;

* `public_content` prefix aims to describe all events performed a published version
* `marketing_site` prefix aims to describe all events performed under gitbook.com marketing site
* for in-product event performed under app.gitbook.com, there is no prefix

</details>

<details>

<summary>group2  (view)</summary>

**Optional**

We distinguish 2 type of events: `view` and `track`. All views will be prefixed with `view_` when `track` will not be prefixed.

</details>

<details>

<summary>group3 (organization)</summary>

It represents the object generating the event: account, space, organization, billing, edit...

</details>

<details>

<summary>group4 (settings)</summary>

It's a verb that describe the action being performed: edit, create, remove...

</details>

<details>

<summary>group5 (billings)</summary>

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
* We try to avoid giving a too generic name such as `source`, which could means lot of different thing based on the context. When using Any Active Event in Amplitude, we are gonna mix different things into a single field.

### What's a view?

We decided to track any change in the screen as a new view: moving from one page to another, opening a modal, opening a side panel...

Basically, every time the focus of the user changes, then it's a new view.

We track generic event name as much as we can: navigating from one page to another one under a space will generate only `view_space` events. But we are gonna use [event properties](naming-conventions.md#event-property) to define which page is it.&#x20;

