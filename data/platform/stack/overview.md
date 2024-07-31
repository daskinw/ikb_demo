# 🗺️ Overview

## Introduction

The data team is currently in charge of aggregating all types of company data in a Data Warehouse, but also in charge of making product analytics available to everyone.

As a product lead company, we can not afford building in-house a product analytics solution and we decided to leverage bespoke solution: Amplitude.

In parallel, we need to build our data warehouse to answer other types of problematic such as Go-To-Market, growth and company wide reports.

## Philosophy

{% hint style="info" %}
Buy and not build & maintain
{% endhint %}

We believe that our main impacts is on business side so we always look to reduce our time spending on building and maintaining stuff. Which explains why we tend to use and leverage SaaS solution at scale.

## Data Flow

![Data Warehouse link](<../.gitbook/assets/Platform (2) (1).jpg>)

## Sources

### High level

All inputs that could end into our Data Warehouse

* **Services:** 3rd party solution that generates some kind of input that could be useful
* **Product:** via event tracking, our product generates a ton of input that could help answering many types of questions. We use Amplitude
* **Infrastructure:** using GCP platform, we could create log sink in GCP to forward log event into BigQuery
* **Handcrafted:** it's sometimes quite handy to have the ability to easily plug spreadsheet or csv to BigQuery
* **Internal:** internally, we generate some data that could useful. Today, we mainly ingest Firestore which is the product database to record our object (users, spaces, collections...)

### Table summary

<table><thead><tr><th>Data Source</th><th>Pipeline<select><option value="127920e5e6d64d63ad910092bb4e1b4a" label="Airflow" color="blue"></option><option value="d8d82db9ba1a44609d51af64fdd0fee7" label="Stitch" color="blue"></option><option value="bdd25b652b2d40e3a5559dfdca32c64b" label="Fivetran" color="blue"></option><option value="e8c5fec4fe694d2e86c26daf636a5156" label="Dbt" color="blue"></option><option value="af2c4da19db649e5a7ef3d1f5fcb425b" label="GCP" color="blue"></option></select></th><th>Raw schema</th><th>Audience<select><option value="7304ecf8b4fb45ec81c50d70aa5c65c2" label="Core" color="blue"></option><option value="e82f1b565c8046b28b501e44e900b8ce" label="Product" color="blue"></option><option value="cf9703bad4c04f1993711c376fef3192" label="GTM" color="blue"></option><option value="65ab7d3e689749a49bb81fc6c04f1838" label="Finance" color="blue"></option><option value="aacd9d20101641e396bbcab85c002344" label="Marketing" color="blue"></option><option value="3d51edea91cd45c8998704bce75a3230" label="Support" color="blue"></option></select></th><th>Notes</th></tr></thead><tbody><tr><td>Amplitude</td><td><span data-option="e8c5fec4fe694d2e86c26daf636a5156">Dbt</span></td><td>amplitude</td><td><span data-option="e82f1b565c8046b28b501e44e900b8ce">Product</span></td><td>We currently only replicate production data</td></tr><tr><td>Firestore</td><td><span data-option="127920e5e6d64d63ad910092bb4e1b4a">Airflow</span></td><td>firestore</td><td><span data-option="7304ecf8b4fb45ec81c50d70aa5c65c2">Core</span></td><td></td></tr><tr><td>HelpScout</td><td><span data-option="d8d82db9ba1a44609d51af64fdd0fee7">Stitch</span></td><td>stitch_help_scout</td><td><span data-option="3d51edea91cd45c8998704bce75a3230">Support</span></td><td></td></tr><tr><td>Hubspot</td><td><span data-option="bdd25b652b2d40e3a5559dfdca32c64b">Fivetran</span></td><td>fivetran_hubspot</td><td><span data-option="cf9703bad4c04f1993711c376fef3192">GTM</span></td><td>We use Fivetran to only replicate GitBook org custom object</td></tr><tr><td>Hubspot</td><td><span data-option="d8d82db9ba1a44609d51af64fdd0fee7">Stitch</span></td><td>stitch_hubspot</td><td><span data-option="cf9703bad4c04f1993711c376fef3192">GTM</span></td><td>We replicate all basics objects using Stitch</td></tr><tr><td>Stripe</td><td><span data-option="d8d82db9ba1a44609d51af64fdd0fee7">Stitch</span></td><td>stitch_stripe_tables</td><td><span data-option="7304ecf8b4fb45ec81c50d70aa5c65c2">Core</span></td><td>Not heavily used, we should see if still needed</td></tr><tr><td>Stripe</td><td><span data-option="d8d82db9ba1a44609d51af64fdd0fee7">Stitch</span></td><td>stitch_stripe</td><td><span data-option="7304ecf8b4fb45ec81c50d70aa5c65c2">Core</span></td><td>Main Stripe replication. We also have an historical event table under the schema stitch_stripe_historical</td></tr><tr><td>Google sheets</td><td><span data-option="af2c4da19db649e5a7ef3d1f5fcb425b">GCP</span></td><td>google_sheets</td><td><span data-option="7304ecf8b4fb45ec81c50d70aa5c65c2">Core</span></td><td>Connected Google Sheets to BigQuery</td></tr></tbody></table>

## Ingestion

All of above sources require some work or tool to be ingested into our Data Warehouse

* **SaaS Connectors:** we currently only Stitch.com to ingest third party data. Also, some 3rd party tools (such as Amplitude, Dbt...) have integrated connectors to replicate their data into our Warehouse or Data Lake&#x20;
* **Connectors:** we can also develop bespoke connector using Cloud Composer (Airflow) in Python

## Orchestration

We currently have (too) many different places to schedule processes:

* **Airflow**: we can use CRON definition to schedule DAGs
* **SaaS scheduler**: we have many SaaS tools that have their own scheduling. It adds lot of complexity and point of failure

## Data Warehouse

This is funniest part of the work: build a **scalable** and **powerful** data structure that will allow us to support business decisions.&#x20;

We build models over models to model our business. We currently use Dbt as our transformation layer, because we love models.

Did we mention model?

## Analytics

While building our data warehouse is key, we should always think on how we can leverage this treasure.

* **Descriptive:** we have a set of way to leverage and query our data warehouse to build insights
* **Predictive:** based on our data history and decent volume, we could build various models which gives us another set of capabilities

## Endpoint

Being able to leverage our data warehouse in other services open new capabilities.

* **SaaS Connector**: some tools could directly leverage our warehouse (Customer.io) but others require external service - also called Reverse ETL - such as Census
* **Connector**: sometimes, we need some homemade scripts to move our data to other services and we can use Cloud Composer (Airflow) to support it

## Stage x Tools

<table><thead><tr><th width="190">Stage</th><th>Tools</th></tr></thead><tbody><tr><td>Extraction</td><td>Stitch, Fivetran, Segment, Airflow, Amplitude</td></tr><tr><td>Loading</td><td>Stitch, Fivetran, Segment, Ariflow, Dbt</td></tr><tr><td>Orchestration</td><td>Stitch, Fivetran, Segment, Airflow, Dbt</td></tr><tr><td>Data Warehouse</td><td>BigQuery (GCP)</td></tr><tr><td>Transformation</td><td>Dbt</td></tr><tr><td>Data Viz</td><td>Tableau, Amplitude</td></tr><tr><td>IDE</td><td>Husprey, Dbt Cloud, GCP UI</td></tr><tr><td>Advanced Analytics</td><td>Local Jupyter NoteBook</td></tr><tr><td>Reverse ETL</td><td>Census, Airflow</td></tr></tbody></table>
