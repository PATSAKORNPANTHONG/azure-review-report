# Testing Guide

[← Back to README](../README.md)

This guide explains how to use the generated `reviews.csv` file to test your Azure Function.

## Prerequisites

-   Ensure you have generated the `reviews.csv` file in the root of your project.
-   Ensure your Azure Function is running (either locally or deployed).

## Option 1: Testing Locally

1.  **Start the Function App**
    Run the following command in your terminal:
    ```bash
    func start
    ```

2.  **Simulate Blob Upload**
    You can simulate a blob upload if you are using Azurite or just waiting for the trigger if you connected to a real storage account even when running locally.
    
    *Note: The easiest way to test locally with a real storage account is to upload the file to your Azure Storage Account.*

## Option 2: Testing with Azure Storage (Cloud)

This method tests the end-to-end flow using the real Azure Storage trigger.

### 1. Locate the Test File
You should have a file named `reviews.csv` in your project root with sample data:
```csv
product_id,user_id,rating,comment
101,501,5,Great product! Highly recommended.
...
```

### 2. Upload to Azure Blob Storage

You can use the Azure CLI to upload this specific file to the `reviews` container.

**Command:**
```bash
az storage blob upload --account-name <YOUR_STORAGE_ACCOUNT_NAME> --container-name reviews --name reviews.csv --file reviews.csv --auth-mode key
```
*Replace `<YOUR_STORAGE_ACCOUNT_NAME>` with your actual storage account name.*

**Alternatively, use Azure Portal:**
1.  Navigate to your Storage Account.
2.  Go to **Containers** -> **reviews**.
3.  Click **Upload**.
4.  Select `reviews.csv` from your local project folder.
5.  Click **Upload**.

### 3. Verify Execution

1.  **Check Logs**:
    -   **Local**: Check your terminal running `func start`.
    -   **Azure**: Go to your Function App -> **Log Stream**.
    
    You should see logs indicating:
    `Python blob trigger function processed blob ... Name: reviews.csv ...`

2.  **Check Database**:
    -   Connect to your MongoDB instance (using MongoDB Compass or `mongosh`).
    -   Query the `reviews` collection.
    -   Verify the data from `reviews.csv` has been inserted.

## Troubleshooting

-   **Trigger not firing?**
    -   Ensure the container name is exactly `reviews`.
    -   Ensure the file has a `.csv` extension.
    -   Check `AzureWebJobsStorage` connection string.
-   **Data not in MongoDB?**
    -   Check the function logs for any errors (e.g., connection timeout, authentication failure).
    -   Verify your `MONGO_URI` and `MONGO_DB_NAME` settings.
