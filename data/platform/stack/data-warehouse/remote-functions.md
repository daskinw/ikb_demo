# Remote Functions

## Introduction

Remote function in BigQuery allows to extend SQL to Python. We can now call functions written in Cloud Function with parameters and get result in SQL

## How to

#### Add a function to the router function

[Like this one](https://console.cloud.google.com/functions/details/us-central1/prod-bigquery-functions?env=gen1\&project=gitbook-analytics)

**Create the connection**

```
bq mk --connection --display_name='BigQuery Connection to Cloud Function' --connection_type=CLOUD_RESOURCE --project_id=gitbook-analytics --location=US bq_connection
```

**Check the connection**

```
bq show --location=US --connection  bq_connection
```

#### Create some remote function

{% hint style="info" %}
we use `user_defined_context` to route the remote function to the correct python function
{% endhint %}

```
CREATE or REPLACE FUNCTION production.utility_stemmer(x STRING) RETURNS STRING
REMOTE WITH CONNECTION us.bq_connection
OPTIONS (
  endpoint = 'https://us-central1-gitbook-analytics.cloudfunctions.net/prod-bigquery-functions',
  user_defined_context = [("function", "utility_stemmer")]
);

CREATE or REPLACE FUNCTION production.customer_io_get_segment_count(x STRING) RETURNS STRING
REMOTE WITH CONNECTION us.bq_connection
OPTIONS (
  endpoint = 'https://us-central1-gitbook-analytics.cloudfunctions.net/prod-bigquery-functions',
  user_defined_context = [("function", "customer_io_get_segment_count")]
)
```

## Current one

#### Customer.io

This one was created as a test implementation. We daily check the number of profile we currently have to compare it against our subscription limits. If we are about to reach the limit, we then fire a Dbt alert using Dbt test.

{% embed url="https://console.cloud.google.com/functions/details/us-central1/prod-bigquery-functions?env=gen1&project=gitbook-analytics" %}
Cloud Functions
{% endembed %}

```sql
select production.customer_io_get_segment_count('36') as segment_count
```

#### Stemmer

```sql
select production.utility_stemmer('care cared and caring') as care
```
