---
layout:       post
title:        "Getting Started with BigQuery"
author:       "Eric"
header-style: text
catalog:      true
tags:
    - BigQuery
    - Google
---

> I started to learn BigQuery

# Getting Started with BigQuery for Beginners

BigQuery is a powerful tool for analyzing large datasets using SQL. This guide will help you get started with BigQuery, covering the basics and providing resources to deepen your understanding.

## 1. What is BigQuery?

BigQuery is a fully managed data warehouse that allows you to run fast SQL queries over large datasets. It’s serverless, meaning you don’t need to manage infrastructure, and it scales automatically based on your workload.

## 2. Setting Up BigQuery

### Step 1: Create a Google Cloud Project1. Go to [Google Cloud Console](https://console.cloud.google.com/).
2. Sign in with your Google account.
3. Click on the project dropdown and select **New Project**.
4. Name your project and click **Create**.

### Step 2: Enable the BigQuery API1. In the Google Cloud Console, navigate to the **API & Services** page.
2. Search for **BigQuery API** and enable it for your project.

### Step 3: Access BigQuery1. In the Google Cloud Console, go to the **BigQuery** page under **Big Data**.
2. This will open the BigQuery console, where you can start querying data.

## 3. Loading Data into BigQuery

### Option 1: Uploading CSV Files1. In the BigQuery console, click on your project.
2. Click **Create Dataset** and name your dataset.
3. Within the dataset, click **Create Table**.
4. Select **Upload** and choose your CSV file.
5. Configure the table schema (you can let BigQuery auto-detect it) and click **Create Table**.

### Option 2: Querying Public Datasets1. In the BigQuery console, expand the **Public Datasets** section.
2. Browse available datasets and click on a table to preview its data.
3. Click **Query Table** to start writing SQL queries against the dataset.

## 4. Writing Your First SQL Query### Basic Query Example```sql
SELECT
  name,
  COUNT(*) AS name_count
FROM
  `bigquery-public-data.usa_names.usa_1910_current`
WHERE
  gender = 'F'
GROUP BY
  name
ORDER BY
  name_count DESC
LIMIT 10;

