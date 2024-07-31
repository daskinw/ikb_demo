# 🔁 Extract & Load

## Introduction

Sources are increasing and ingesting new sources could be tricky. We tend to use external/paid services as much as possible to avoid maintaining scripts.&#x20;

We currently use [Stitch](https://app.stitchdata.com/client/176956/pipeline/v2/sources) as much as possible. This is a `set and forget` solution as we don't have to maintain scripts overtime. Stitch mainly covers 3rd party product data but we have a various set of other sources.

We also use Fivetran for Hubspot customer object to replicate GitBook custom object.

## Sources

### SaaS Services

As mentioned earlier, we use Stitch for 3rd party product data. We currently have:

* **Stripe**: our payment platform
* **Hubspot**: our CRM and Sales platform
* **HelpScout**: our support platform

### Product Analytics

#### Introduction

As a product lead company, our product generates massive inputs and we should always try to leverage this treasure to take better decision.

We currently use [Amplitude](https://analytics.amplitude.com/gitbook-com) to perform product analytics. We use [an export feature](https://help.amplitude.com/hc/en-us/articles/360061685151-Export-Amplitude-data-to-Google-Cloud-Storage) of it to get all tracked data to a GCP bucket.

Exports happened every hour with 1 or 2 hours ingestion latency.

#### Content exported

Amplitude exports every single event ingested. On top of the event collected, Amplitude enriches the event with `user properties` and `group properties`. Amplitude exports all of these extra information which is very handy.

<details>

<summary>Schema</summary>

```javascript
{
  "server_received_time": UTC ISO-8601 timestamp,
  "app": int,
  "device_carrier": string,
  "$schema":int,
  "city": string,
  "user_id": string,
  "uuid": UUID,
  "event_time": UTC ISO-8601 timestamp,
  "platform": string,
  "os_version": string,
  "amplitude_id": long,
  "processed_time": UTC ISO-8601 timestamp,
  "user_creation_time": UTC ISO-8601 timestamp,
  "version_name": string,
  "ip_address": string,
  "paying": boolean,
  "dma": string,
  "group_properties": dict,
  "user_properties": dict,
  "client_upload_time": UTC ISO-8601 timestamp,
  "$insert_id": string,
  "event_type": string,
  "library":string,
  "amplitude_attribution_ids": string,
  "device_type": string,
  "device_manufacturer": string,
  "start_version": string,
  "location_lng": float,
  "server_upload_time": UTC ISO-8601 timestamp,
  "event_id": int,
  "location_lat": float,
  "os_name": string,
  "amplitude_event_type": string,
  "device_brand": string,
  "groups": dict,
  "event_properties": dict,
  "data": dict,
  "device_id": string,
  "language": string,
  "device_model": string,
  "country": string,
  "region": string,
  "is_attribution_event": bool,
  "adid": string,
  "session_id": long,
  "device_family": string,
  "sample_rate": null,
  "idfa": string,
  "client_event_time": UTC ISO-8601 timestamp,
 }json
```

</details>

#### GCP Bucket

|                                                                                                         PROD                                                                                                        |                                                                                                         STAGING                                                                                                        |                                                                                                                DEV                                                                                                               |
| :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                                                                                  gitbook-analytics                                                                                                  |                                                                        <mark style="color:orange;background-color:red;">gitbook-analytics</mark>                                                                       |                                                                                                       gitbook-analytics-dev                                                                                                      |
| [BUCKET](https://console.cloud.google.com/storage/browser/gitbook-amplitude-prod-242119;tab=objects?forceOnBucketsSortingFiltering=false\&project=gitbook-analytics\&prefix=\&forceOnObjectsSortingFiltering=false) | [BUCKET](https://console.cloud.google.com/storage/browser/gitbook-amplitude-staging-237714;tab=objects?forceOnBucketsSortingFiltering=false\&project=gitbook-analytics\&prefix=\&forceOnObjectsSortingFiltering=false) | [BUCKET](https://console.cloud.google.com/storage/browser/gitbook-analytics-amplitude-dev-366084;tab=objects?forceOnBucketsSortingFiltering=false\&project=gitbook-analytics-dev\&prefix=\&forceOnObjectsSortingFiltering=false) |

#### Content files structure

Amplitude follows a logic to export all the data:

```
242119 //folder with the Amplitude project id
├── 237714_2021-01-04_9#922.json.gz // several files per hour exported
├── 237714_2021-01-04_9#123.json.gz
└── 237714_2021-01-04_9_complete // to nofify the end of the export, one file with complete

```

#### Ingestion Logic

Please refer to the [Product Ingestion Logic](product-ingestion-logic.md) to learn more on the different stages of loading raw data from Amplitude.

{% content-ref url="product-ingestion-logic.md" %}
[product-ingestion-logic.md](product-ingestion-logic.md)
{% endcontent-ref %}

### Infrastructure

Using GCP platform and Log Sink feature, we can export at scale Google Log into BigQuery effortless.

Currently we only use is to have a better view on cost:

{% embed url="https://console.cloud.google.com/logs/router?project=gitbook-analytics" %}
Sink
{% endembed %}

### Handcrafted

It's sometimes quite handy to have the ability to easily plug spreadsheet or csv into BigQuery, so business could update it.

We currently have 2 different way to ingest CSV or spreadsheet:&#x20;

* using a BigQuery view on top of a Google Spreadsheet
* using [`seeds`](https://docs.getdbt.com/docs/building-a-dbt-project/seeds) feature from Dbt

### Internal - App data

Having access to our App data allows us to rely on consolidate data such as Organization, Spaces, Collections... and Users.

We rely on daily Firestore export and please find different links below:

|                                                                                                     Bucket                                                                                                     |                                                                Script                                                                |                                               Loading system                                              |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------: |
| [LINK](https://console.cloud.google.com/storage/browser/gitbook-x-prod-firestore-data;tab=objects?forceOnBucketsSortingFiltering=false\&project=gitbook-x-prod\&prefix=\&forceOnObjectsSortingFiltering=false) | [LINK](https://github.com/GitbookIO/gitbook-x/blob/7116bf5252c14e755ef378951d0fbed3e6a4016f/.github/workflows/prod-daily-backup.yml) | [Airflow DAG](https://y0e931e29e9eb9701p-tp.appspot.com/admin/airflow/code?dag\_id=FIRESTORE\_TO\_BQ\_V1) |





