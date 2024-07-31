---
description: List of live exercices during traning
---

# Exercises

<details>

<summary>1 - Space reader</summary>

* **Goal 1:** List of emails who visited this space during the past 28d, offset by 1d
* **Goal 2:** Sort emails by total of unique pageview

Solution [here](https://app.amplitude.com/analytics/gitbook-com/chart/iwioyt6p)

</details>

<details>

<summary>2 - MAU</summary>

* **Goal 1:** get a chart with Monthly Active User (MAU) over the past 12 months
* **Goal 2:** get a split between users who signed-up during the month vs not
* **Goal 3:** change MAU to Monthly Active Editor (MAE) (doing `Any Edit Event`)
* **Goal 4:** % of MAE who are new users (sign-up during the month)
* **Extra:** % of MAU being MAE (regardless of being new/old)

Solution [here](https://app.amplitude.com/analytics/gitbook-com/chart/5t70uy7t)

</details>

<details>

<summary>3 - Edit in CR</summary>

* **Goal 1:** total block insert (`edit_page_document_insert_block`) in paid (`isPaid`) organizations done under a change request or not
* **Goal 2:** create a rate of monthly rate of block being inserted under CR on total
* **Goal 3:** exclude space with GitSync is installed (spaceHasGitSync) and verify that having GitSync install drive to 100%
* **Goal 4:** filter on space visibility to `private`&#x20;
* **Goal 5:** duplicate the cohort and create 2 others with organizations with more than 50 members and another one with less than 5 members

Solution [here](https://app.amplitude.com/analytics/gitbook-com/chart/j76lr6ji)

</details>

<details>

<summary>4 - Time to invite</summary>

**Goal 1:** get a funnel to find the % of organization being created (`organization_create`) and have at least 2 members after 28 days

&#x20;**Goal 2:** analyze these rates by organization use case => we could leverage organizationUseCase but we prefer the event property useCase that is always set and ready)

**Goal 3:** check overtime (monthly) to see if rate are changing or not&#x20;

**Goal 4**: look at the time to convert to see the spread of inviting during the 1st 28 days

Solution here

</details>

{% embed url="https://app.amplitude.com/analytics/gitbook-com/notebook/ae4piza6" %}
Notebook ref
{% endembed %}
