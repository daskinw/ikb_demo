# Dbt Cloud

<table><thead><tr><th width="105">Link</th><th>Description</th></tr></thead><tbody><tr><td><a href="https://cloud.getdbt.com/next/deploy/5266/projects/7541/jobs">Here</a></td><td>Dbt web job pages</td></tr></tbody></table>

## Introduction

We currently run Dbt refresh using Dbt Cloud scheduler and it's not wired to any other system. In other words, the execution of Dbt don't depend on other jobs to run or succeed.

## Jobs

<table><thead><tr><th width="165.33333333333331">Name</th><th>Description</th><th>Schedule</th></tr></thead><tbody><tr><td>All models</td><td>The main model that update all models except reporting and product event</td><td>At 4am and noon</td></tr><tr><td>Hourly - Events</td><td>The model that load and consolidate Amplitude export into tables</td><td>Every hour</td></tr><tr><td>Hourly - Reporting</td><td>The model that updates all model powering reporting, mainly Tableau</td><td>Every hour</td></tr><tr><td>Modified Models</td><td>A job to manually update models that have changed since <code>All models</code> last run</td><td>Manual</td></tr><tr><td>Seed</td><td>A job to manually call the <code>seed</code> command in production</td><td>Manual</td></tr></tbody></table>

## Models architecture

<table><thead><tr><th width="155.33333333333331">Folder name</th><th width="478">Description</th><th data-type="checkbox">Include in daily process</th></tr></thead><tbody><tr><td>airflow</td><td>All the models needed to be prepare to support Airflow runs. We should never have business logic in Airflow, but in Dbt models here.</td><td>true</td></tr><tr><td>calixa</td><td>All models that could serve Calixa sync. All business logic here</td><td>true</td></tr><tr><td>census</td><td>All models that serve Reverse ETL logic in Census. All business logic here.</td><td>true</td></tr><tr><td>customer_io</td><td>All models that server users sync to power Customer.io campaigns. All business logic here.</td><td>true</td></tr><tr><td>mart</td><td>Main folder where we build all business logics and aggregates tables</td><td>true</td></tr><tr><td>ml</td><td>All models needed to support Machine Learning job.</td><td>true</td></tr><tr><td>reporting</td><td>All consolidate reporting should use data from here and never query other models.</td><td>true</td></tr><tr><td>share</td><td>All CTE that we want to store that could be shared lately between data users.</td><td>false</td></tr><tr><td>stg_external</td><td>All models built on top of raw sources. Mart should only query data from stg_external directly</td><td>true</td></tr><tr><td>temporary</td><td>An historical folder to store all past models and keep a backup.</td><td>false</td></tr></tbody></table>
