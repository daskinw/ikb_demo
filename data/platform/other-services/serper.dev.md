---
description: Search Engine Results Page API
---

# Serper.dev

### Links

| Description             | Link                                                                                              |
| ----------------------- | ------------------------------------------------------------------------------------------------- |
| Serper.dev              | [Here](https://serper.dev)                                                                        |
| Airflow Job             | [Here](https://y0e931e29e9eb9701p-tp.appspot.com/admin/airflow/tree?dag\_id=HOMEMADE\_ENRICHMENT) |
| Airflow Job Source code | [Here](https://github.com/GitbookIO/data-airflow/blob/main/dags/HOMEMADE\_ENRICHMENT.py)          |
| dbt Models              | [Here](https://github.com/GitbookIO/data-dbt/tree/master/models/airflow/homemade\_enrichment)     |

### Description

A Search Engine Result Page API (SERP) is a service that provides the results of a search in a structured way, avoiding the necessity of parsing manually the page ourselves.&#x20;

We currently use this service to get information about organizations that are unknown to us/Clearbit. Those usually are smaller or new companies with almost no online presence.

### Usage

We gather the custom domains of all these orgs and make a request to Serper.dev API, then we use the snippet in the results as input to Google's Classification API. With that, we are able to guess what kind of company it is, which industry they are into.

