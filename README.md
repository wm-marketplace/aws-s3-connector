## Connector  Introduction
Connector is a Java based backend extension for WaveMaker applications. Connectors are built as Java modules & exposes java based SDK to interact with the connector implementation.
Each connector is built for a specific purpose and can be integrated with one of the external services. Connectors are imported & used in the WaveMaker application. Each connector runs on its own container thereby providing the ability to have it’s own version of the third party dependencies.
## Features of Connectors
1. Connector is a java based extension which can be integrated with external services and reused in many Wavemaker applications.
1. Each connector can work as an SDK for an external system.
1. Connectors can be imported once in a WaveMaker application and used many times in the applications by creating multiple instances.
1. Connectors are executed in its own container in the WaveMaker application, as a result there are no dependency version conflict issues between connectors.
## About AWS S3 Connector
## Introduction
Amazon S3 (Simple Storage Service) is a scalable, high-speed, low-cost web-based service designed for online backup and archiving of data and application programs. It allows to upload, store, and download any type of files up to 5 TB in size. This service allows the subscribers to access the same systems that Amazon uses to run its own web sites. The subscriber has control over the accessibility of data, i.e. privately/publicly accessible.
This connector exposes api to interact with AWS S3 from WaveMaker application.
## Prerequisite
1. AWS Bucket access with a Key and Secret
1. Java 1.8 or above
1. Maven 3.1.0 or above
1. Any java editor such as Eclipse, Intelij..etc
1. Internet connection
## Build
You can build this connector using following command
```
mvn clean install
```

## Deploy
You can import connector dist/aws-s3-connector.zip artifact in WaveMaker studio application using file upload option.On after uploading into wavemaker, you can update your profile properties such as AWS key and Secret.


## Integrate Amazon S3 (Simple Storage Service) into WaveMaker App

Learn how to implement file upload, download, and delete operations on an S3 bucket using the AWS S3 Connector.

## AWS S3 Connector
Amazon S3 (Simple Storage Service) is a scalable, high-speed, low-cost web-based service designed for online backup and archiving of data and application programs. It allows uploading, storing, and downloading any type of files up to 5 TB in size. This service enables subscribers to access the same systems that Amazon uses to run its own websites. The subscriber has control over the accessibility of data, i.e., privately/publicly accessible.

This connector exposes APIs to interact with AWS S3 from a WaveMaker application.

## Step 1: Importing the aws-s3-connector to the project
1. Download the latest `aws-s3-connector` zip [here](https://github.com/wm-marketplace/aws-s3-connector/releases).
2. Import the downloaded `aws-s3-connector` zip into your app using the **Import Resource** option in the **Connector** folder.
![image](https://github.com/user-attachments/assets/165a7bfb-e0d7-4e52-a222-b08f48ea7d4f)
![image](https://github.com/user-attachments/assets/91e62974-3938-4d9e-a9c8-f216007f73d7)

## Step 2: Configure aws-s3-connector properties in profiles
By default, externalized connector properties are added in the project profiles.

Connector externalized properties are prefixed with `connector.${connectorName}`:

```
connector.aws-s3-connector.default.aws.accessKey=
connector.aws-s3-connector.default.aws.accessSecret=
connector.aws-s3-connector.default.aws.clientRegion=
connector.aws-s3-connector.default.aws.bucketName=
```

## Step 3: Perform File Operations in Storage Account
Autowire the Connector Service into the added JavaService.

### Import Statement:
```java
import com.wavemaker.connector.awss3connector.S3Connector;

@Autowired
private S3Connector s3Connector;
```

### 1. Upload file to Amazon S3 bucket
Uploads a new file object to the specified Amazon S3 bucket.

#### Import statements required:
```java
import org.springframework.web.multipart.MultipartFile;
import java.io.IOException;
```

#### Source Code:
```java
public void uploadFile(MultipartFile file){
    try{
        s3Connector.uploadFileToS3(file.getInputStream(), file.getOriginalFilename(), null);
    } catch (IOException e) {
        throw new RuntimeException("Exception occurred while uploading file: " + e);
    }
}
```

### 2. List files from Amazon S3
Returns a list of summary information about the objects in the specified bucket.

#### Import statements required:
```java
import com.wavemaker.connector.awss3connector.AWSS3ObjectSummary;
import java.util.List;
```

#### Source Code:
```java
public List<AWSS3ObjectSummary> listObjects(){
    return s3Connector.listS3Objects();
}
```

### 3. Download file from S3
Gets the object from Amazon S3 under the specified bucket.

#### Import statements required:
```java
import com.wavemaker.runtime.commons.file.model.DownloadResponse;
import java.io.OutputStream;
import java.io.ByteArrayOutputStream;
import java.io.ByteArrayInputStream;
```

#### Source Code:
```java
public DownloadResponse downloadFile(String s3KeyName){
    try{
        OutputStream data = s3Connector.downloadFile(s3KeyName);
        DownloadResponse downloadResponse = new DownloadResponse();
        ByteArrayOutputStream outStream = (ByteArrayOutputStream) data;
        downloadResponse.setContents(new ByteArrayInputStream(outStream.toByteArray()));
        downloadResponse.setInline(false);
        downloadResponse.setFileName(s3KeyName);
        return downloadResponse;
    } catch (IOException e) {
        throw new RuntimeException("Exception occurred while downloading file: " + e);
    }
}
```

### 4. Delete file in S3
Deletes the specified object in the specified bucket.

#### Source Code:
```java
public void deleteFile(String s3KeyName){
    s3Connector.deleteFile(s3KeyName);
}
```

> **Note**: `s3KeyName` is the file key name. Example: `IMG_20200202.jpg`.

