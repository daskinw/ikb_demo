# 🎆 Transformation

## Introduction

We heavily use Dbt Cloud as our transformation layer. It allows us to build complex business logic in pure SQL, which could be understood and leveraged but lot of employees.

## Why Dbt

Dbt allows us only deal with SQL and .yml file. On top of it, it's heavily inspired by Software Engineering best practices:

* Versioning: integrated with GitHub
* DRY (Don't Repeat Yourself): we can build 1 model or macro to embed a business logic and reference it later
* All is SQL
* (Almost) Not Db provider dependant
* Data Lineage
* Freshness
* Documentation
* Data Quality
* Alerting
* ...

## Dbt organization

### Code

The code is hosted and versioned in a GitHub repo:

{% embed url="https://github.com/GitbookIO/data-dbt" %}
GitHub
{% endembed %}

### Organization

We try to use folder to organise our code.&#x20;

```
// top model folder
model
|── customer_io // one folder per tool that connects directly to BigQuery
|── ... // other tool such as Census, Calixa....
|── mart // main folder to build business logic
|── stg_external // all models built on top of raw data... should be renamed to stg
|── reporting // where we build model to power any consolidate reporting
|── temporary // a folder excluded to any Dbt run where we can version some usefull work
```

### Materialized approach

We try to have a global approach regarding materialization based on the use case

<table><thead><tr><th width="290">Model</th><th>Default materialisation<select><option value="c8ebc91916e6452aaa9a7c59093aa9d0" label="view" color="blue"></option><option value="0f1711d43c0b4aa3ba510580ceed069d" label="table" color="blue"></option></select></th></tr></thead><tbody><tr><td>stg_external</td><td><span data-option="c8ebc91916e6452aaa9a7c59093aa9d0">view</span></td></tr><tr><td>reporting</td><td><span data-option="0f1711d43c0b4aa3ba510580ceed069d">table</span></td></tr><tr><td>third party model</td><td><span data-option="c8ebc91916e6452aaa9a7c59093aa9d0">view</span></td></tr><tr><td>mart</td><td><span data-option="0f1711d43c0b4aa3ba510580ceed069d">table</span></td></tr></tbody></table>

### Incremental

We try to use incrementality logic when it makes sense to avoid running complexe SQL logic behind the scene.

{% hint style="info" %}
ie: every calculation based on tracking event **must** be defined as incremental model to avoid scanning the event table on the fly!
{% endhint %}

### Documentation

We do have some key table documented but we should have a more consistent approach. If you feel some documentation is missing, take the ownership and do it!

{% embed url="https://cloud.getdbt.com/accounts/5266/jobs/14812/docs/#!/overview" %}

### Freshness

Freshness helps us to keep a close eyes to our key data source. It's mandatory to add any freshness check to any new source added!

{% embed url="https://cloud.getdbt.com/next/deploy/5266/projects/7541/sources" %}
Data source pages
{% endembed %}

### Work environment

Dbt helps us to separate working environment. Currently, staging is not used:

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>the 3 current environments</p></figcaption></figure>

### Environment variable

We leverage Dbt environment variable to make sure we always use production data source which allow us to properly test our models:

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Dbt environment variable</p></figcaption></figure>

We can then call this variable in Dbt src yml file:

<pre><code>version: 2

sources:
  - name: clearbit
    description: Data source coming from our Firestore backend system to support the app
    schema: clearbit
    database: <a data-footnote-ref href="#user-content-fn-1">"{{env_var('DBT_BQ_SOURCES')}}"</a>
    loader: 'manual'
    tables:
      - name: raw_clearbit
        description: A table to store all raw exports running manual from python script
</code></pre>

### Testing

There are 2 type of test in Dbt: the one defined in `yml` file and the one defined in SQL under `/test` folder.

We currently run test in 2 different stages in `All models` job

* `dbt test -m source:*`
* _...then run all models on top of raw sources..._
* `dbt test --exclude source:*`

### Schedule

Please refer to this page to learn about Dbt runs

{% content-ref url="../orchestration/dbt-cloud.md" %}
[dbt-cloud.md](../orchestration/dbt-cloud.md)
{% endcontent-ref %}

## Collaboration

We use GitHub and branches to ship new SQL logic.

* Always start from master

Please check that you have `branch:master` at the top left. Otherwise, check out branch

![](<../../.gitbook/assets/image (4).png>)

* Then create a new branch

![](<../../.gitbook/assets/image (2).png>)

* Do your changes and commit them :tada:
* Create a PR (redirected to GitHub web app)
* Wait for review before merge
* Merge!

[^1]: we always refer to production database
