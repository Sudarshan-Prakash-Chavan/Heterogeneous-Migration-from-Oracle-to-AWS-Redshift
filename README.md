# Heterogeneous-Migration-from-Oracle-to-AWS-Redshift

This project outlines the steps to migrate data from an Oracle database hosted on Amazon RDS to an Amazon Redshift cluster using the AWS Schema Conversion Tool (SCT) and AWS Database Migration Service (DMS).

## Overview

AWS Schema Conversion Tool (SCT) simplifies the process of migrating to AWS by converting your existing database schema to a format compatible with Amazon Redshift. The AWS Database Migration Service (DMS) then migrates the data with minimal downtime.

## Architecture

![Architecture Diagram](architecture/architecture_diagram.png)

### Components Explained:
- **Amazon RDS (Oracle)**: Managed Oracle database instance that serves as the source.
- **AWS Schema Conversion Tool (SCT)**: Converts the Oracle schema to Redshift-compatible format.
- **AWS DMS**: Facilitates the actual data migration from Oracle to Redshift.
- **Amazon Redshift**: Managed data warehouse that receives the migrated data.
- **SQL Workbench**: Tool used to connect and execute SQL commands against the databases.

## Pre-requisites

1. **Download and Install the Required Tools**:
   - **AWS Schema Conversion Tool**: Download from [AWS SCT Download](https://s3.amazonaws.com/publicsctdownload/Windows/aws-schema-conversion-tool-1.0.latest.zip).
   - **MySQL Workbench**: Download and install from [MySQL Workbench Download](https://dev.mysql.com/downloads/installer/).
     - Local MySQL Server Password: `Admin123$$`
   - **JDBC Drivers**:
     - Oracle JDBC: `ojdbc8.jar`
     - Redshift JDBC: `redshift-jdbc42-2.1.0.9.jar`
     - Download links available at [AWS SCT Installation](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html#CHAP_Installing.JDBCDrivers) and [SQL Workbench](https://www.sql-workbench.eu/downloads.html).

2. **AWS Setup**:
   - Create an RDS Oracle instance with password `Admin1234`.
   - Launch an Amazon Redshift cluster with password `admin123`.

## Step-by-Step Migration Process

### Setting Up the Environment

1. **Open the AWS SCT**:
   - Navigate to `Settings > Global Settings`.
   - Set your AWS Service Profile with the required credentials.
   - Configure the paths for the Oracle and Redshift JDBC drivers you downloaded.

2. **Create the Oracle RDS Instance**:
   - Specify the password as `Admin1234` and click the "Create Database" button.

3. **Launch the Redshift Cluster**:
   - Specify the password as `admin123`.

4. **Set Up SQL Workbench**:
   - Open SQL Workbench.
   - Create a connection to the Oracle RDS instance using the JDBC URL format:
     ```
     jdbc:oracle:thin:@<your_oracle_endpoint>:1521:ORCL
     ```
   - Make the Redshift cluster publicly accessible, then connect using the JDBC URL from the Redshift console.

5. **Prepare Source Data**:
   - Download the sample schema file from [DMS Samples](http://docs.aws.amazon.com/dms/latest/sbs/samples/dms-sbs-RDSOracle2Redshift.zip).
   - Copy the contents into SQL Workbench connected to your Oracle instance.
   - Select all commands in the editor and click "Run" to set up the source data.

### Schema Conversion

1. **Create a New Project in SCT**:
   - Open SCT and create a new project, specifying a suitable name.

2. **Add the Source**:
   - Go to `File > Add Source`, entering the RDS Oracle endpoint as the server name.

3. **Add the Target**:
   - Go to `File > Add Target`, entering the Redshift endpoint without the port number and database name.

4. **Create the Mapping**:
   - Select the schemas from the Oracle source.
   - Choose the `SH` schema for conversion and initiate the schema conversion.

5. **Apply the Schema**:
   - Click "Yes" to apply the converted schema to your Redshift database.
   - Refresh the schema view to confirm that the `SH` schema is available in Redshift.

### Data Migration

1. **Create a Replication Instance in DMS**:
   - In the DMS Console, create a new replication instance using the default VPC.

2. **Create Source Endpoint**:
   - Create a source endpoint for the Oracle RDS instance using the password `Admin1234`.
   - Note that the connection cannot be tested until the replication instance is available.

3. **Create Target Endpoint**:
   - Create a target endpoint for Redshift, using the password `admin123`.
   - Leave other settings at default and click "Create".

4. **Test Endpoint Connections**:
   - Test both the source and target endpoint connections in the DMS console.

5. **Create Migration Task**:
   - In DMS, create a migration task.
   - Set a selection rule for the `SH` schema and click "Create".

## Verification

- After the migration task is complete, switch to the Redshift Query Editor.
- Verify that the table data has been migrated correctly by running SELECT queries against the migrated tables.

## Conclusion

This project provides a comprehensive guide to migrating data from Oracle to AWS Redshift using the AWS Schema Conversion Tool and AWS Database Migration Service. By following the outlined steps, you can effectively set up, configure, and execute migrations, ensuring that your data is seamlessly transitioned to the cloud.
