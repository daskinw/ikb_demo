# Customer.io

|                                                      |                              |
| ---------------------------------------------------- | ---------------------------- |
| [Link](https://fly.customer.io/env/115960/dashboard) | Our email marketing platform |

### Description <a href="#description" id="description"></a>

We currently use this service to power email marketing for bespoke campaigns and also for event based campaigns.

## BigQuery connection <a href="#connected-app" id="connected-app"></a>

Customer.io provides [an easy way](https://fly.customer.io/env/115960/integrations/sql-sync/bigquery) to leverage DWH effort and sync users properties at scale.

We have a dedicated schema `production_customer_io` in Dbt to sync the 2.

## Overages

As we create profile in Customer.io on the fly using Segment integration, it could create lot of user profile in it. Our pricing is based on 1M profiles. Few things to note:

* It's not based on active users. ie: the pricing doesn't look at emails sent. We do have a limit on this side but it's more a sanity check
* They have a watermark pricing logic: if you delete some user profiles during a billing cycle, the deleted account still count until the end of the billing cycle. In other words, the baseline is only refreshed at the start of a new billing period

To help that front, we have implemented a sanity check that run on a daily basis:

1. there is a user segment in Customer.io with a total count of profile [here](https://fly.customer.io/env/115960/segments/36/overview)
2. using a remote function, we run an API call to get the count
3. there is a test in Dbt calling the count => if the value is higher than 700k, then the test fails

By doing that we can have a high level check of the growth of number of account in case there is an issue happening.
