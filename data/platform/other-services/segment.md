# Segment

<table><thead><tr><th width="108">Link</th><th>Description</th></tr></thead><tbody><tr><td><a href="https://app.segment.com/gitbook-com/home?period=last-24-hours&#x26;v2=enabled">Link</a></td><td>Our CDP to connect third party data to each other and avoid having Eng involved in data topics</td></tr></tbody></table>

## Description

Segment acts as a CDP here and helps us connecting multiple tools together. It serves GoToMarket and RevOps needs.

## Connected App

<table><thead><tr><th width="249.33333333333331">Name</th><th>Description</th><th>Flow<select multiple><option value="6abbd151095a44358385c9b8f8135ebc" label="Source" color="blue"></option><option value="cf9a294e97414388a1602183c4348177" label="Destination" color="blue"></option></select></th></tr></thead><tbody><tr><td>Application</td><td>Our web application tracking (front)</td><td><span data-option="6abbd151095a44358385c9b8f8135ebc">Source</span></td></tr><tr><td>Backend</td><td>Our backend tracking (server)</td><td><span data-option="6abbd151095a44358385c9b8f8135ebc">Source</span></td></tr><tr><td>Cloud Function</td><td>Dedicated Cloud Function channel</td><td><span data-option="6abbd151095a44358385c9b8f8135ebc">Source, </span><span data-option="cf9a294e97414388a1602183c4348177">Destination</span></td></tr><tr><td>Manual</td><td>A channel dedicated to manual script</td><td><span data-option="6abbd151095a44358385c9b8f8135ebc">Source</span></td></tr><tr><td>Amplitude</td><td>Product Analytics tool</td><td><span data-option="cf9a294e97414388a1602183c4348177">Destination</span></td></tr><tr><td>Amplitude Cohort</td><td>A way to sync Amplitude cohort</td><td><span data-option="6abbd151095a44358385c9b8f8135ebc">Source</span></td></tr><tr><td>Census</td><td>Reverse ETL solution</td><td><span data-option="6abbd151095a44358385c9b8f8135ebc">Source</span></td></tr><tr><td>Customer.io</td><td>Email marketing solution</td><td><span data-option="6abbd151095a44358385c9b8f8135ebc">Source, </span><span data-option="cf9a294e97414388a1602183c4348177">Destination</span></td></tr><tr><td>Refiner</td><td>Survey solution</td><td><span data-option="6abbd151095a44358385c9b8f8135ebc">Source</span></td></tr><tr><td>BigQuery</td><td>Our DWH solution</td><td><span data-option="cf9a294e97414388a1602183c4348177">Destination</span></td></tr><tr><td>Hubspot</td><td>CRM solution</td><td><span data-option="cf9a294e97414388a1602183c4348177">Destination</span></td></tr><tr><td>HelpScout</td><td>Support solution</td><td><span data-option="cf9a294e97414388a1602183c4348177">Destination</span></td></tr></tbody></table>

## What is tracked there

We decided to go for a contract based on API calls as opposed to MAU. (40 millions as it is today). To avoid crazy numbers, we decided to only track `in-app` events and identify and not `marketing site` or `public pageviews`.

## Connection naming convention

We try to leverage tag features and use the following naming convention: \[Environment] - \[Application Name].

Few example:

* Prod - Application
* Staging - Application
* Dev - Application
* Prod - Amplitude
* ...

## Segment function

Another aspect of Segment being used is Segment Function: we can write JS function to:

* Source Function: react on an incoming Webhook and generates a Segment actions - identify, track...
* Destination Function: react on an incoming Segment actions

### Source function

We currently use this feature to parse Livestorm webhook event and generate Track event so we can get Livestorm key events in Amplitude and BigQuery. ([function)](https://app.segment.com/gitbook-com/functions/catalog/sfnc\_PsrIGpNhDv)

### Destination function

We currently don't leverage this feature.

