# Azure Data Factory Essentials and Notes

## 1. Linked Services
Linked Services define the connection to the data sources or destinations used in the pipeline. Here's an example of creating a Linked Service for an Azure Blob Storage account:

```json
# Linked Service: Azure Blob Storage
{
    "name": "AzureBlobStorageLinkedService",
    "type": "AzureBlobStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;EndpointSuffix=core.windows.net"
    }
}

```

## 2. Datasets
Datasets represent the input or output data for activities in the pipeline. They define the structure and location of the data. Here's an example of creating a Dataset for a CSV file in Azure Blob Storage:

```json
# Dataset: CSV file
{
    "name": "BlobStorageCSV",
    "type": "AzureBlob",
    "linkedServiceName": {
        "referenceName": "AzureBlobStorageLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n",
            "quoteChar": "\"",
            "nullValue": ""
        },
        "fileName": "data.csv",
        "folderPath": "container/path"
    }
}

```


## 3. Pipelines
Pipelines orchestrate the flow of data activities. They define the sequence and dependencies between activities. Here's an example of a pipeline with a Copy activity:

```json
# Pipeline: Data Copy
{
    "name": "DataCopyPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyActivity",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobStorageCSV",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SQLServerTable",
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "SqlSink"
                    }
                }
            }
        ],
        "parameters": {}
    }
}

```

## Example

To provide a more comprehensive example, let's assume you want to create and run the pipeline using Azure CLI. Here's the step-by-step code for creating and triggering the pipeline:

**1: Create a new pipeline using Azure CLI**

```bash
# Define your Azure Data Factory and resource group names
resourceGroupName="myResourceGroup"
dataFactoryName="myDataFactory"

# Create the pipeline using the JSON code
az datafactory pipeline create --resource-group $resourceGroupName --factory-name $dataFactoryName --name "DataCopyPipeline" --definition "@pipeline.json"

```

**2: Define the Linked Services and Datasets**

You can use the JSON code provided earlier to create the Linked Services and Datasets using the Azure CLI. Here's an example for the Linked Service:

```bash
# Create the Linked Service for Azure Blob Storage
az datafactory linked-service create --resource-group $resourceGroupName --factory-name $dataFactoryName --name "AzureBlobStorageLinkedService" --properties "@azure_blob_linked_service.json"

```

**3: Set up activity dependencies and parameters within the pipeline**

If your pipeline requires activity dependencies or parameters, you can configure them using the Azure CLI. Here's an example of setting up a dependency between two activities in the pipeline:

```bash
# Set up dependency between activities in the pipeline
az datafactory pipeline create-run --resource-group $resourceGroupName --factory-name $dataFactoryName --name "DataCopyPipeline" --is-recovery false --reference-pipeline-run-id "pipelineRunId1" --reference-outputs "{ \"activityName1\": { \"value\": \"@pipeline().output.activityName1\" } }"

```

**4: Trigger the pipeline to run using Azure CLI**

To trigger the pipeline run, use the following command:

```bash
# Trigger the pipeline run
az datafactory pipeline create-run --resource-group $resourceGroupName --factory-name $dataFactoryName --name "DataCopyPipeline"

```

**5: Monitor the pipeline run status**

You can monitor the status of the pipeline run to check if it has completed successfully or encountered any errors. Use the following command:

```bash
# Get the status of the pipeline run
az datafactory pipeline-run query-by-factory --resource-group $resourceGroupName --factory-name $dataFactoryName --last-updated-after 2023-05-30T00:00Z --last-updated-before 2023-05-31T00:00Z

```
