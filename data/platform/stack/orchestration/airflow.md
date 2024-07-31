# Airflow

<table><thead><tr><th width="147">Link</th><th>Description</th></tr></thead><tbody><tr><td><a href="https://console.cloud.google.com/storage/browser/us-west4-dev-c720e2c2-bucket/dags;tab=objects?project=gitbook-analytics&#x26;prefix=&#x26;forceOnObjectsSortingFiltering=false">Storage</a></td><td>Our bucket to store files supporting Airflow</td></tr><tr><td><a href="https://console.cloud.google.com/composer/environments?project=gitbook-analytics">Instance</a></td><td>Cloud Composer service</td></tr><tr><td><a href="https://github.com/GitbookIO/data-airflow">GitHub</a></td><td>Our repo to code versioning</td></tr><tr><td><a href="https://y0e931e29e9eb9701p-tp.appspot.com/admin/">UI</a></td><td>Our Web UI to interact with DAGs</td></tr></tbody></table>

## Description

Airflow is our fallback solution when we have to deal with bespoke implementation. We try to avoid it as much as possible but sometimes it's needed and convenient.

## DAGs

<table><thead><tr><th>Name</th><th width="122.33333333333331">Schedule</th><th>Description</th></tr></thead><tbody><tr><td><a href="https://y0e931e29e9eb9701p-tp.appspot.com/admin/airflow/tree?dag_id=FIRESTORE_TO_BQ_V1">FIRESTORE_TO_BQ_V1</a></td><td>30 3 * * *</td><td>It aims to load all Firestore export to BigQuery</td></tr><tr><td><a href="https://y0e931e29e9eb9701p-tp.appspot.com/admin/airflow/tree?dag_id=GITHUB_ENRICHMENT_V1">GITHUB_ENRICHMENT_V1</a></td><td>45 6 * * *</td><td>To develop support Dev Rel function, we developed a script enriching GitSync repo to get GitHub stars</td></tr><tr><td><a href="https://y0e931e29e9eb9701p-tp.appspot.com/admin/airflow/tree?dag_id=HUBSPOT_CRM_V1">HUBSPOT_CRM_V1</a></td><td>30 4 * * *</td><td>Our critical DAGs that performed multiple steps to maintain Hubspot CRM in a good shape</td></tr></tbody></table>

## TODO

* Backup and Versioning: we don't a good backup of this infra
* Upgrade: we should upgrade it to Airflow 2.X
* Main scheduler: we currently have many different schedulers and an option could be to have Airflow scheduling everything
* Observability: we do have some check in Dbt about the state of each runs, but we could and should do a better job here
* Update Hubspot DAG:&#x20;
  * we should implement the new Hubspot API capabilities allowing to update Custom Object in batch operation
  * overall: challenge the global implementation and find potential optimisation

