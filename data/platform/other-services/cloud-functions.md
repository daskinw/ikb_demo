# Cloud Functions

<table><thead><tr><th width="108">Link</th><th>Description</th></tr></thead><tbody><tr><td><a href="https://console.cloud.google.com/functions/list?project=gitbook-analytics">Link</a></td><td>List of all Cloud Functions</td></tr></tbody></table>

## Description

We leverage Cloud Function capabilities to perform different actions:

* react on a tracking event (Segment destination)
* provide an HTTP interface to perform a ML prediction (lead scoring)
* extend SQL capabilities with Remote Functions

## TODO

* Add versioning (code is not in GH currently)
* Add observability
* Decide on the MVP regarding [NPS Slack message](https://console.cloud.google.com/functions/details/us-central1/staging-segment-refiner?env=gen1\&project=gitbook-analytics) moving to production or delete it
