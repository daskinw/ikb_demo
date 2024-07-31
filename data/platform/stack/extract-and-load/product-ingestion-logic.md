# Product Ingestion Logic

{% hint style="danger" %}
TODO:&#x20;

* Amplitude duplicates some event in their raw files which could lead to duplicate event ingest in the destination. We should implement a process of cleaning it.
* We don't clean our bucket (raw data)
* The ingestion logic is based on a bad assumption: Amplitude always export hourly data in an hourly order. In fact, it happens sometimes that 9pm export got extracted before 8pm data, which lead to 8pm to be missing in the db
{% endhint %}

## Introduction

We are gonna describe the process of loading Amplitude export data into BigQuery.&#x20;

## Export

As mentioned, we are using Amplitude data export features which perform hourly raw json export in GCP Cloud Storage.

{% embed url="https://analytics.amplitude.com/gitbook-com/connections/project/242119/destinations/detail/MTE4?source=data+destinations+table" %}
Amplitude Production setting
{% endembed %}

## Tables & Views

### External

We have an external table on `.json` files

<details>

<summary>Query</summary>

```sql
CREATE OR REPLACE EXTERNAL TABLE amplitude.cs_raw_events
(
  data STRING
)
OPTIONS (
  format = 'CSV',
  field_delimiter = '\t',
  uris = ['gs://gitbook-amplitude-prod-242119/242119/242119_*.json.gz']
);

```

</details>

We have an external table on `_complete` files

<details>

<summary>Query</summary>

```sql
CREATE OR REPLACE EXTERNAL TABLE amplitude.cs_raw_complet
(  
    data STRING
)
OPTIONS (  
    format = 'CSV',  
    field_delimiter = '\t',  
    uris = ['gs://gitbook-amplitude-prod-242119/242119/242119_*_complete']
);
```

</details>

### Internal

We have a view on top of each other to carry basic transformation and make it easy to query.

On `complete`

<details>

<summary>Query</summary>

```sql
CREATE OR REPLACE VIEW amplitude.cs_complet
AS 
    select 
        _FILE_NAME as filename,
        PARSE_DATE('_%Y-%m-%d_', REGEXP_EXTRACT(_FILE_NAME, '_[0-9\\-]*_')) file_date,
        CAST(REGEXP_EXTRACT(_FILE_NAME, '_([0-9]+)_complet') as INT64) as file_hour,
        PARSE_DATETIME('%Y-%m-%d_%H', REGEXP_EXTRACT(_FILE_NAME, '_([0-9\\-\\_]*)_complet')) as file_datetime
    from
        amplitude.cs_raw_complet c
```

</details>

On `.json`

<details>

<summary>Query</summary>

```sql
CREATE OR REPLACE VIEW amplitude.cs_events
AS 
    select
        PARSE_DATE('_%Y-%m-%d_', REGEXP_EXTRACT(_FILE_NAME, '_[0-9\\-]*_')) file_date,
        CAST(REGEXP_EXTRACT(_FILE_NAME, '_([0-9]+)#') as INT64) as file_hour,
        _FILE_NAME as filename,JSON_EXTRACT_SCALAR(d.data, '$.event_time') as event_time,
        JSON_EXTRACT_SCALAR(d.data, '$.server_received_time') as server_received_time,
        JSON_EXTRACT_SCALAR(d.data, '$.client_event_time') as client_event_time,
        JSON_EXTRACT_SCALAR(d.data, '$.user_id') as user_id,
        JSON_EXTRACT_SCALAR(d.data, '$.event_type') as event_name,
        JSON_EXTRACT(d.data, '$.event_properties') as event_properties,
        JSON_EXTRACT(d.data, '$.group_properties') as group_properties,
        JSON_EXTRACT(d.data, '$.user_properties') as user_properties,
        JSON_EXTRACT(d.data, '$.groups') as groups_id,
        JSON_EXTRACT_SCALAR(d.data, '$.app') as app,
        JSON_EXTRACT_SCALAR(d.data, '$.device_carrier') as device_carrier,
        JSON_EXTRACT_SCALAR(d.data, '$.city') as city,
        JSON_EXTRACT_SCALAR(d.data, '$.uuid') as uuid,
        JSON_EXTRACT_SCALAR(d.data, '$.platform') as platform,
        JSON_EXTRACT_SCALAR(d.data, '$.os_version') as os_version,
        JSON_EXTRACT_SCALAR(d.data, '$.amplitude_id') as amplitude_id,
        JSON_EXTRACT_SCALAR(d.data, '$.user_creation_time') as user_creation_time,
        JSON_EXTRACT_SCALAR(d.data, '$.version_name') as version_name,
        JSON_EXTRACT_SCALAR(d.data, '$.ip_address') as ip_address,
        JSON_EXTRACT_SCALAR(d.data, '$.paying') as paying,
        JSON_EXTRACT_SCALAR(d.data, '$.dma') as dma,
        JSON_EXTRACT_SCALAR(d.data, '$.client_upload_time') as client_upload_time,
        JSON_EXTRACT_SCALAR(d.data, '$.library') as library,
        JSON_EXTRACT_SCALAR(d.data, '$.amplitude_attribution_ids') as amplitude_attribution_ids,
        JSON_EXTRACT_SCALAR(d.data, '$.device_type') as device_type,
        JSON_EXTRACT_SCALAR(d.data, '$.device_manufacturer') as device_manufacturer,
        JSON_EXTRACT_SCALAR(d.data, '$.start_version') as start_version,
        JSON_EXTRACT_SCALAR(d.data, '$.location_lng') as location_lng,
        JSON_EXTRACT_SCALAR(d.data, '$.server_upload_time') as server_upload_time,
        JSON_EXTRACT_SCALAR(d.data, '$.event_id') as event_id,
        JSON_EXTRACT_SCALAR(d.data, '$.location_lat') as location_lat,
        JSON_EXTRACT_SCALAR(d.data, '$.os_name') as os_name,
        JSON_EXTRACT_SCALAR(d.data, '$.amplitude_event_type') as amplitude_event_type,
        JSON_EXTRACT_SCALAR(d.data, '$.device_brand') as device_brand,
        JSON_EXTRACT_SCALAR(d.data, '$.groups') as groups_list,
        JSON_EXTRACT_SCALAR(d.data, '$.data') as data,
        JSON_EXTRACT_SCALAR(d.data, '$.device_id') as device_id,
        JSON_EXTRACT_SCALAR(d.data, '$.language') as languages,
        JSON_EXTRACT_SCALAR(d.data, '$.device_model') as device_model,
        JSON_EXTRACT_SCALAR(d.data, '$.country') as country,
        JSON_EXTRACT_SCALAR(d.data, '$.region') as region,
        JSON_EXTRACT_SCALAR(d.data, '$.is_attribution_event') as is_attribution_event,
        JSON_EXTRACT_SCALAR(d.data, '$.adid') as adid,
        JSON_EXTRACT_SCALAR(d.data, '$.session_id') as session_id,
        JSON_EXTRACT_SCALAR(d.data, '$.device_family') as device_family,
        JSON_EXTRACT_SCALAR(d.data, '$.sample_rate') as sample_rate,
        JSON_EXTRACT_SCALAR(d.data, '$.idfa') as idfa
          from
              amplitude.cs_raw_events d
```

</details>

## Destination tables

{% hint style="info" %}
Dbt repository should the source of truth for the following query transformation
{% endhint %}

On an hourly basis in Dbt, we ingest new data coming in with the following query

<details>

<summary>Query</summary>

```sql
{{
    config(
        materialized='incremental',
        partition_by={
            "field": 'event_date',
            "data_type": 'date'
        },
        partition_by_required='true',
        cluster_by = ['event_site','event_name']
    )
}}

select
    IF(event_name like '%Marketing Site%','marketing_site',IF(event_name like '%Public Space%','public_space','app')) as event_site,
    date(event_time) as event_date,
    cast(event_time as datetime) as event_time,
    event_name,
    user_id,
    device_id,
    IF(event_name like '%View %','view','track') as event_type,
    IF(ARRAY_LENGTH(JSON_EXTRACT_ARRAY(e.groups_id, '$.spaceId'))>0,JSON_EXTRACT_SCALAR(e.groups_id, '$.spaceId[0]'),null) as space_id,
    IF(ARRAY_LENGTH(JSON_EXTRACT_ARRAY(e.groups_id, '$.orgId'))>0,JSON_EXTRACT_SCALAR(e.groups_id, '$.orgId[0]'),null) as org_id,
    cast(server_received_time as datetime) as server_received_time,
    cast(user_creation_time as datetime) as user_creation_time,
    cast(client_event_time as datetime) as client_event_time,
    cast(session_id as INT64) as session_id,
    e.* EXCEPT(event_time,server_received_time,client_event_time,user_creation_time,session_id,event_name,user_id,device_id),

from {{source('amplitude','cs_events')}} e
{% raw %}
{% if is_incremental() %}

  where 
    -- safeguard condition to make sure we don't query too many data (cost savings)
    file_date >= DATE_ADD(CURRENT_DATE,INTERVAL -4 DAY) and
    -- we use filename filter to make sure we only insert data coming from not already loaded files
    -- as Amplitude exports hour in filename like 9 instead of 09, we can not sort filename but we have to created a datetime of the file name
    PARSE_DATETIME('%Y-%m-%d_%H', REGEXP_EXTRACT(filename, '_([0-9\\-\\_]*)#')) > (select max(PARSE_DATETIME('%Y-%m-%d_%H', REGEXP_EXTRACT(filename, '_([0-9\\-\\_]*)#'))) from {{ this }} where event_date > DATE_ADD(CURRENT_DATE,INTERVAL -4 day))
    -- Amplitude pushes a yyyy-mm-dd_H_complet file to tell the hour is fulle exported on the GCP
    and PARSE_DATETIME('%Y-%m-%d_%H', REGEXP_EXTRACT(filename, '_([0-9\\-\\_]*)#')) <= (select max(file_datetime) from amplitude.cs_complet where file_date >= DATE_ADD(CURRENT_DATE,INTERVAL -4 day))

{% endif %}
{% endraw %}
```

</details>

### Partition & clusters

We decided to partition our underlaying data using event\_date and to make it mandatory via the following command

```sql
ALTER TABLE IF EXISTS production_stg_external.amplitude_events
SET OPTIONS(
    require_partition_filter = true
)
```

In order to improve performance and reduce scanning cost, we cluster the data per `event_site` and `event_name`(subset of the model):

```sql
{{
    config(
        materialized='incremental',
        partition_by={
            "field": 'event_date',
            "data_type": 'date'
        },
        partition_by_required='true',
        cluster_by = ['event_site','event_name']
    )
}}
```

### Transformation

{% hint style="warning" %}
There are lot to improve on this side but it requires to reload all historical data
{% endhint %}

<details>

<summary>Query</summary>

```sql
select
    IF(event_name like '%Marketing Site%','marketing_site',IF(event_name like '%Public Space%','public_space','app')) as event_site,
    date(event_time) as event_date,
    cast(event_time as datetime) as event_time,
    event_name,
    user_id,
    device_id,
    IF(event_name like '%View %','view','track') as event_type,
    IF(ARRAY_LENGTH(JSON_EXTRACT_ARRAY(e.groups_id, '$.spaceId'))>0,JSON_EXTRACT_SCALAR(e.groups_id, '$.spaceId[0]'),null) as space_id,
    IF(ARRAY_LENGTH(JSON_EXTRACT_ARRAY(e.groups_id, '$.orgId'))>0,JSON_EXTRACT_SCALAR(e.groups_id, '$.orgId[0]'),null) as org_id,
    cast(server_received_time as datetime) as server_received_time,
    cast(user_creation_time as datetime) as user_creation_time,
    cast(client_event_time as datetime) as client_event_time,
    cast(session_id as INT64) as session_id,
    e.* EXCEPT(event_time,server_received_time,client_event_time,user_creation_time,session_id,event_name,user_id,device_id),

from {{source('amplitude','cs_events')}} e
{% raw %}
{% if is_incremental() %}

  where 
    -- safeguard condition to make sure we don't query too many data (cost savings)
    file_date >= DATE_ADD(CURRENT_DATE,INTERVAL -4 DAY) and
    -- we use filename filter to make sure we only insert data coming from not already loaded files
    -- as Amplitude exports hour in filename like 9 instead of 09, we can not sort filename but we have to created a datetime of the file name
    PARSE_DATETIME('%Y-%m-%d_%H', REGEXP_EXTRACT(filename, '_([0-9\\-\\_]*)#')) > (select max(PARSE_DATETIME('%Y-%m-%d_%H', REGEXP_EXTRACT(filename, '_([0-9\\-\\_]*)#'))) from {{ this }} where event_date > DATE_ADD(CURRENT_DATE,INTERVAL -4 day))
    -- Amplitude pushes a yyyy-mm-dd_H_complet file to tell the hour is fulle exported on the GCP
    and PARSE_DATETIME('%Y-%m-%d_%H', REGEXP_EXTRACT(filename, '_([0-9\\-\\_]*)#')) <= (select max(file_datetime) from amplitude.cs_complet where file_date >= DATE_ADD(CURRENT_DATE,INTERVAL -4 day))

{% endif %}
{% endraw %}
```

</details>

## Repository

{% embed url="https://github.com/GitbookIO/data-dbt/tree/master/models/stg_external/events_amplitude" %}
Dbt transformation
{% endembed %}
