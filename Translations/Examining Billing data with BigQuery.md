
# Examining Billing data with BigQuery

## Objectives
In this lab, you learn how to perform the following tasks:

    Sign in to BigQuery from the Cloud Console

    Create a dataset

    Create a table

    Import data from a billing CSV file stored in a bucket

    Run complex queries on a larger dataset

## Steps

Task 1: Use BigQuery to import data

    Create a dataset:

        bq mk imported_billing_data

    Create a table and import:

        bq load imported_billing_data.sampleinfotable gs://cloud-training/archinfra/export-billing-example.csv Auto detect

Task 2: Examine the table
    Click sampleinfotable.

    Click Details. As you can see in Number of Rows, this is a relatively small table with 44 rows.

    Click Preview.

    Locate the row that has the Description: Network Internet Ingress from EMEA to Americas.

    Scroll to the Cost column.

    Locate the row that has the Description: Network Internet Egress from Americas to China.

Task 3: Compose a simple query

    Construct a simple query based on the Cost field
        
        bq query SELECT * FROM `imported_billing_data.sampleinfotable`
WHERE Cost > 0


Task 4: Analyze a large billing dataset with SQL

    For New Query, paste the following:

        bq query 
        SELECT 
            product, 
            resource_type, 
            start_time, 
            end_time, 
            cost, 
            project_id, 
            project_name,   
            project_labels_key, 
            currency, 
            currency_conversion_rate, 
            usage_amount, 
            usage_unit
        FROM
            `cloud-training-prod-bucket.arch_infra.billing_data`

    To find the latest 100 records where there were charges (cost > 0), for New Query, paste the following:

        bq query
        SELECT
            product,
            resource_type,
            start_time,
            end_time,
            cost,
            project_id,
            project_name,
            project_labels_key,
            currency,
            currency_conversion_rate,
            usage_amount,
            usage_unit
        FROM
            `cloud-training-prod-bucket.arch_infra.billing_data`
        WHERE
            Cost > 0
        ORDER BY end_time DESC
        LIMIT
            100

To find all charges that were more than 3 dollars, for Compose New Query, paste the following:

        bq query
        SELECT
            product,
            resource_type,
            start_time,
            end_time,
            cost,
            project_id,
            project_name,
            project_labels_key,
            currency,
            currency_conversion_rate,
            usage_amount,
            usage_unit
        FROM
            `cloud-training-prod-bucket.arch_infra.billing_data`
        WHERE
            cost > 3
To find the product with the most records in the billing data, for New Query, paste the following:

        bq query
        SELECT
            product,
            COUNT(*) AS billing_records
        FROM
            `cloud-training-prod-bucket.arch_infra.billing_data`
        GROUP BY
            product
        ORDER BY billing_records DESC

To find the most frequently used product costing more than 1 dollar, for New Query, paste the following:

        bq query
        SELECT
            product,
            COUNT(*) AS billing_records
        FROM
            `cloud-training-prod-bucket.arch_infra.billing_data`
        WHERE
            cost > 1
        GROUP BY
            product
        ORDER BY
            billing_records DESC

To find the most commonly charged unit of measure, for Compose New Query, paste the following:

        bq query
        SELECT
            usage_unit,
            COUNT(*) AS billing_records
        FROM
            `cloud-training-prod-bucket.arch_infra.billing_data`
        WHERE cost > 0
        GROUP BY
            usage_unit
        ORDER BY
            billing_records DESC

To find the product with the highest aggregate cost, for New Query, paste the following:

        bq query
        SELECT
            product,
            ROUND(SUM(cost),2) AS total_cost
        FROM
            `cloud-training-prod-bucket.arch_infra.billing_data`
        GROUP BY
            product
        ORDER BY
            total_cost DESC

            