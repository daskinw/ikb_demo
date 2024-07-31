# 🏬 Data Warehouse

## Documentation

{% embed url="https://cloud.getdbt.com/accounts/5266/jobs/14812/docs/#!/overview" %}
Dbt docs
{% endembed %}

## Schema

| Dataset                   | Definition                                            |
| ------------------------- | ----------------------------------------------------- |
| production\_airflow       | All tables needed to run Airflow sync                 |
| production\_census        | All tables needed to run Census sync                  |
| production\_customer\_io  | All tables needed to run Customer.io sync             |
| production\_mart          | All datamart that could be used for advanced analysis |
| production\_ml            | All datamart related to Machine Learning initiatives  |
| production\_reporting     | Tables that power Tableau reporting                   |
| production\_stg\_external | All staging tables that could be used                 |

## Staging schema

### Organization

<table><thead><tr><th>Source</th><th width="345">Description</th><th>Basename</th></tr></thead><tbody><tr><td>Apollo</td><td>We use Apollo (since 2023-08) to enrich sign-up. Here we store all processes</td><td><code>stg_apollo_XXX</code></td></tr><tr><td>Amplitude</td><td>We load Amplitude data on an early basis to run advanced product analaytics query and cross it with other data sources. 🚨Massive data set, please use app table when possible and filter 🚨</td><td><code>amplitude_XXX</code> (mainly <code>amplitude_app_events</code>)</td></tr><tr><td>Firestore</td><td>We load some of Firestore data into BQ</td><td><code>stg_backend_XXX</code></td></tr><tr><td>Hubspot</td><td>We replicate Hubspot data into BQ using Stitch and Fivetran</td><td><code>stg_hubspot_XXX</code></td></tr><tr><td>Refiner</td><td>We leverage Amplitude tracking to extract Refiner survey answer</td><td><code>stg_refiner_answers</code></td></tr><tr><td>Stripe</td><td>We load most Stripe data to perform MRR calculation and many more</td><td><code>stg_stripe_XXX</code></td></tr></tbody></table>

### Amplitude

#### Descritpion

Having the tracking into BigQuery opens new opportunity where you can cross in-app activities and MRR expansion, guess use-case and feature discoverability...and many more.

But navigating the tracking is not always easy so here are some helps:

* `amplitude_events`: all events in Amplitude are replicated here on an hourly basis with some properties being parsed to make it easy to leverage. That being said, the table is massive and the cost of quering could easily go crazy.&#x20;
* `amplitude_app_events`: it's a subset of `amplitude_events` table focusing on event performed in the app (`event_site = app`). There are also some columns automatically extracted which helps querying it

#### Example

Let's count the number of daily editor inserting a code block in a public space, similar to [this Amplitude chart](https://app.amplitude.com/analytics/gitbook-com/chart/new/hplqzmme)

```sql
select
    event_date,
    count(distinct user_id) as count_users
from
    `gitbook-analytics.production_stg_external.amplitude_app_events` e
where
    -- filter on past 7 days
    e.event_date >= DATE_ADD(CURRENT_DATE(),interval -7 day)
    -- filter on adding a block event
    and e.event_name = 'edit_page_document_insert_block'
    -- filter on an event property to make sure it's a code block type
    and JSON_EXTRACT_SCALAR(e.event_properties,'$.blockType') = 'code'
    -- filter on a space properties
    and JSON_EXTRACT_SCALAR(e.space_properties,'$.spaceVisibility') = 'public'
group by
    1
order by
    1
```

## Mart schema

Under `production_mart` folder, we have a bunch of sub folders to categorise the mart use case.

```
// mart folder
production_mart
|── core // core folder
    |── production_mart_core_XXXX.sql // core mart model
```

<table><thead><tr><th width="149.5">Folder</th><th>Explanations</th></tr></thead><tbody><tr><td>Core</td><td>Generic tables that serve multiple purposes</td></tr><tr><td>Marketing</td><td>Everything related to marketing topics</td></tr><tr><td>Product</td><td>Everything based on product analytics data</td></tr><tr><td>Revenue</td><td>Everything related to revenue calculation</td></tr><tr><td>Spam</td><td>Everything related to spam calculations</td></tr><tr><td>Support</td><td>Everything relation to support team topics</td></tr><tr><td>UA</td><td>User acquisition questions</td></tr></tbody></table>

## Security

We don't have a strong security layer in place. We basically have 3 ways of accessing our DWH:

* User level: the user account is added directly as an owner
* Role:
  * Data Analyst: as defined in `permissions_data_analyst` Dbt model, the user can list all staging and mart schema + edit in any mart schema
  * Data Reader: as defined in permissions\_data\_reader Dbt model, the user can view staging mart and reporting schema

## TODO

* we don't have any backup system in place
