# Ingesting Data from Blob Storage into HDInsight Kafka

[Azure Blob Storage](https://azure.microsoft.com/en-us/services/storage/blobs/ "Azure Blob Storage") can handle all your unstructured data, scaling up or down as required. You no longer have to manage it, you only pay for what you use, and you save money over on-premises storage options.

[Kafka](https://docs.microsoft.com/en-us/azure/hdinsight/kafka/apache-kafka-introduction) is an open-source, distributed streaming platform. It's often used as a message broker, as it provides functionality similar to a publish-subscribe message queue. [HDInsight Kafka](https://azure.microsoft.com/en-us/services/hdinsight/apache-kafka/) is a managed service that provides a simplified configuration for Apache Kafka.

##Goal

In this tutorial, you will learn how to leverage Azure services with StreamSets to read data from Blob Storage and send data into HDInsight Kafka cluster.

## Prerequisites

You can download sample data for this tutorial from the following location: https://www.streamsets.com/documentation/datacollector/sample_data/tutorial/nyc_taxi_data.csv and copy into your Blob Storage.

## Creating the Kafka Topic

When working with HDInsight Kafka, we need to pre-create the Kafka topic. Ssh into the terminal for StreamSets Data Collector on HDInsight and run the following command to create a Kafka topic. Remember to replace the zookeeper_url with your own uri for zookeeper.

	sshuser@ed10-adlsst:~$ /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic taxi_sdc --zookeeper <zookeeper_uri>

## Creating a Pipeline

Now let's get some data flowing! In your browser, login to SDC and create a new pipeline.

Select Origin from the Drop down list: Hadoop FS Standalone
![image alt text](/img/BlobToKafka/SelectSource_Hadoop.png)
**Hadoop FS tab**

* **Hadoop FS URI**: this has the form `wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>`

* **Hadoop FS Configuration**
	* **fs.azure.account.key.pranavkyo.blob.core.windows.net** = '<your storage account key>'
	* **fs.azure.account.keyprovider.pranavkyo.blob.core.windows.net** = 'org.apache.hadoop.fs.azure.SimpleKeyProvider'

![image alt text]((/img/BlobToKafka/HadoopFS_HadoopFS.png)

**Files**

* **Files Directory**: <Blob Storage path for the sample file>

* **File Name Pattern**: *

* **Read Order**: Last Modified Timestamp

![image alt text]((/img/BlobToKafka/HadoopFS_Files.png)

**Data Format tab**

* **Data Format**: Delimited

* **Header Line**: With Header Line

![image alt text]((/img/BlobToKafka/HadoopFS_DataFormat.png)

Configure the pipeline's **Error Records** property according to your preference. Since this is a tutorial, you could discard error records, but in a production system you would write them to a file or queue for later analysis.

Now hit the preview button to check that you can read records from the file. Click the Hadoop FS stage and you should see ten records listed in the preview panel. You can click into them to see the individual fields and their values:

![image alt text]((/img/BlobToKafka/Preview.png)