# Azure DataFactory Essentials

Azure Data Factory (ADF) is a cloud-based data integration service provided by Microsoft Azure. It enables organizations to orchestrate and automate the movement and transformation of data across various data sources and destinations.

ADF acts as a centralized control hub that allows you to create, schedule, and manage data pipelines. A data pipeline is a series of steps that extract data from different sources, apply transformations, and load it into a target location. These pipelines can be used for various purposes, such as data migration, data warehousing, data lakes, and real-time analytics.

ADF offers several components and products that work together to accomplish data integration tasks:

**Data Flows:** This component allows you to visually design and execute data transformations without writing code. You can easily perform activities like filtering, aggregating, joining, and sorting data to prepare it for analysis or loading into a destination.

**Linked Services:** A linked service is a connection to a specific data source or destination. ADF supports a wide range of connectors for various data platforms, including on-premises databases, cloud-based storage, software-as-a-service (SaaS) applications, and big data platforms. Linked services define the connection details and credentials required to access the data.

**Data Sets:** A data set represents a data structure within a data store. It defines the schema, location, and properties of the data to be processed. ADF supports different types of data sets, such as files, tables, folders, and queues.

**Triggers:** Triggers are used to schedule and execute data pipelines based on specific time intervals, events, or external dependencies. You can set up triggers to run pipelines periodically or in response to changes in data sources or other external systems.

**Integration Runtimes:** Integration runtimes provide the execution environment for data movement and transformation activities. ADF offers different runtimes to suit various scenarios, including Azure, self-hosted, and Azure-SSIS (SQL Server Integration Services) integration runtimes.

**Monitoring and Management:** ADF provides comprehensive monitoring and management capabilities to track the execution of pipelines, monitor data flows, and troubleshoot issues. It offers built-in logging, alerts, and diagnostic tools to ensure the reliability and performance of your data integration processes.

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



## Example of a basic data factory pipeline repository, using Python and Apache Airflow as the workflow management tool


```
data-factory-pipeline/
├── dags/
│   ├── __init__.py
│   └── data_pipeline.py
├── scripts/
│   └── data_processing.py
├── utils/
│   └── helpers.py
├── config.py
└── README.md

```

**dags/:** This directory contains the Directed Acyclic Graphs (DAGs) for the data pipeline. A DAG is a collection of tasks with dependencies, defining the workflow. In this example, we have a single DAG called data_pipeline.py, responsible for orchestrating the data processing tasks.

**scripts/:** This directory holds the actual scripts responsible for data processing. For instance, data_processing.py could contain the logic for reading data from a source, performing transformations, and storing the processed data in a destination.

**utils/:** This directory contains utility functions or helper modules that can be used across different parts of the pipeline. For instance, helpers.py could include reusable functions for handling common data processing tasks.

**config.py:** This file contains configuration parameters for the data pipeline. It may include details such as data source paths, destination paths, API keys, database credentials, etc.

**README.md:** This file provides documentation about the data factory pipeline repository. It can include an overview of the pipeline, instructions on how to set up and run the pipeline, and any other relevant information.

**data_pipeline.py:**

```python
from datetime import datetime
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from scripts.data_processing import process_data

default_args = {
    'start_date': datetime(2023, 1, 1),
    'retries': 1,
}

# Define the DAG (Directed Acyclic Graph)
dag = DAG(
    'data_pipeline',
    default_args=default_args,
    schedule_interval='0 0 * * *',  # Run once daily at midnight
)

# Define a PythonOperator to execute the data processing task
process_task = PythonOperator(
    task_id='process_data',
    python_callable=process_data,
    dag=dag,
)

# Set up task dependencies (if any)
# Uncomment the following line if there are dependencies:
# process_task.set_upstream(other_task)

```


**data_processing.py:**

```python
from utils.helpers import read_data, transform_data, write_data

def process_data():
    # Read data from a source file
    data = read_data('/path/to/source.csv')

    # Perform data transformations
    transformed_data = transform_data(data)

    # Write processed data to a destination file
    write_data(transformed_data, '/path/to/destination.csv')

```


**helpers.py**

```python
def read_data(file_path):
    # Placeholder implementation for reading data from a source
    # Replace with actual code to read data from the file
    print(f"Reading data from {file_path}")
    data = []
    # Read data logic goes here
    return data

def transform_data(data):
    # Placeholder implementation for performing data transformations
    # Replace with actual code to transform the data
    transformed_data = []
    for record in data:
        # Transformation logic goes here
        transformed_record = record.upper()  # Example transformation
        transformed_data.append(transformed_record)
    return transformed_data

def write_data(data, file_path):
    # Placeholder implementation for writing data to a destination
    # Replace with actual code to write data to the file
    print(f"Writing data to {file_path}")
    # Write data logic goes here

```


In Apache Airflow, the **config.py** file is not directly related to the pipeline definition or execution. However, it can be used to store configuration parameters or settings that are used within your Airflow environment:

```python
# Airflow Configuration File

# Database Configuration
SQLALCHEMY_DATABASE_URI = 'sqlite:////path/to/airflow.db'  # SQLite database path

# Executor Configuration
EXECUTOR = 'LocalExecutor'  # Use LocalExecutor for local execution

# Airflow Web Server Configuration
WEB_SERVER_PORT = 8080  # Port number for the Airflow web server

# Airflow Scheduler Configuration
SCHEDULER_INTERVAL = '@daily'  # Scheduler interval for running DAGs

# Email Configuration
SMTP_HOST = 'smtp.example.com'  # SMTP server hostname
SMTP_PORT = 587  # SMTP server port
SMTP_USERNAME = 'your_username'  # SMTP username
SMTP_PASSWORD = 'your_password'  # SMTP password
SMTP_STARTTLS = True  # Enable STARTTLS for secure connection

# Other Configuration Parameters
# Add any other relevant configuration parameters here

```
