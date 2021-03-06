---
title: Release notes
titleSuffix: SQL Server 2019 big data clusters
description: This article describes the latest updates and known issues for SQL Server 2019 big data clusters (preview). 
author: rothja 
ms.author: jroth 
manager: craigg
ms.date: 12/07/2018
ms.topic: conceptual
ms.prod: sql
ms.custom: seodec18
---

# Release notes for SQL Server 2019 big data clusters

This article provides the latest updates and known issues for the latest release of SQL Server big data clusters. The following table links you to the section for the releases covered in this article.

| Release | Date |
|---|---|
| [CTP 2.2](#ctp22) | December 2018 |
| [CTP 2.1](#ctp21) | November 2018 |
| [CTP 2.0](#ctp20) | October 2018 |

[!INCLUDE [Limited public preview note](../includes/big-data-cluster-preview-note.md)]

## <a id="ctp22"></a> CTP 2.2 (December 2018)

The following sections describe the new features and known issues for big data clusters in SQL Server 2019 CTP 2.2.

### What's in the CTP 2.2 release?

- Use SparkR from Azure Data Studio on a big data cluster.
- Cluster Admin Portal accessed with `/portal` (**https://\<ip-address\>:30777/portal**).
- Master pool service name changed from `service-master-pool-lb` and `service-master-pool-nodeport` to `endpoint-master-pool`.
- New version of **mssqlctl** and updated images.
- Miscellaneous bug fixes and improvements.

### Known issues

The following sections provide known issues for SQL Server big data clusters in CTP 2.2.

#### Deployment

- Upgrading a big data data cluster from a previous release is not supported. You must backup and delete any existing big data cluster before deploying the latest release. For more information, see [Upgrade to a new release](deployment-guidance.md#upgrade).

- After deploying on AKS, you might see the following two warning events from the deployment. Both of these events are known issues, but they do not prevent you from successfully deploying the big data cluster on AKS.

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- If a big data cluster deployment fails, the associated namespace is not removed. This could result in an orphaned namespace on the cluster. A workaround is to delete the namespace manually before deploying a cluster with the same name.

#### Cluster administration portal

The cluster administration portal does not display the endpoint for the SQL Server master instance. To find the IP address and port for the master instance, use the following **kubectl** command:

```
kubectl get svc endpoint-master-pool -n <your-cluster-name>
```

#### External tables

- It is possible to create a data pool external table for a table that has unsupported column types. If you query the external table, you get a message similar to the following:

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- If you query a storage pool external table, you might get an error if the underlying file is being copied into HDFS at the same time.

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

#### Spark and notebooks

- POD IP addresses may change in the Kubernetes environment as PODs restarts. In the scenario where the master-pod restarts, the Spark session may fail with `NoRoteToHostException`. This is caused by JVM caches that don't get refreshed with new IP addresses.

- If you have Jupyter already installed and a separate Python on Windows, Spark notebooks might fail. To work around this issue, upgrade Jupyter to the latest version.

- In a notebook, if you click the **Add Text** command, the text cell is added in preview mode rather than edit mode. You can click on the preview icon to toggle to edit mode and edit the cell.

#### HDFS

- If you right-click on a file in HDFS to preview it, you might see the following error:

   `Error previewing file: File exceeds max size of 30MB`

   Currently there is no way to preview files larger than 30 MB in Azure Data Studio.

- Configuration changes to HDFS that involve changes to hdfs-site.xml are not supported.

#### Security

- The SA_PASSWORD is part of the environment and discoverable (for example in a cord dump file). You must reset the SA_PASSWORD on the master instance after deployment. This is not a bug but a security step. For more information on how to change the SA_PASSWORD in a Linux container, see [Change the SA password](../linux/quickstart-install-connect-docker.md#sapassword).

- AKS logs may contain SA password for big data cluster deployments.

## <a id="ctp21"></a> CTP 2.1 (November 2018)

The following sections describe the new features and known issues for big data clusters in SQL Server 2019 CTP 2.1.

### What's in the CTP 2.1 release?

- [Deploy Python and R apps](big-data-cluster-create-apps.md) in a big data cluster.
- New version of **mssqlctl** and updated images. 
- Miscellaneous bug fixes and improvements.

### Known issues

The following sections provide known issues for SQL Server big data clusters in CTP 2.1.

#### Deployment

- Upgrading a big data data cluster from a previous release is not supported. You must backup and delete any existing big data cluster before deploying the latest release. For more information, see [Upgrade to a new release](deployment-guidance.md#upgrade).

- After deploying on AKS, you might see the following two warning events from the deployment. Both of these events are known issues, but they do not prevent you from successfully deploying the big data cluster on AKS.

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- If a big data cluster deployment fails, the associated namespace is not removed. This could result in an orphaned namespace on the cluster. A workaround is to delete the namespace manually before deploying a cluster with the same name.

#### Admin portal

- When you [create an app using msqlctl-ctp command](big-data-cluster-create-apps.md) and deploy it on a SQL Server big data cluster, the Cluster Admin Portal shows the pods where the application was deployed as "Unknown" in the Controller section of the Admin Portion.

#### External tables

- It is possible to create a data pool external table for a table that has unsupported column types. If you query the external table, you get a message similar to the following:

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- If you query a storage pool external table, you might get an error if the underlying file is being copied into HDFS at the same time.

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

#### Spark and notebooks

- POD IP addresses may change in the Kubernetes environment as PODs restarts. In the scenario where the master-pod restarts, the Spark session may fail with `NoRoteToHostException`. This is caused by JVM caches that don't get refreshed with new IP addresses.

- If you have Jupyter already installed and a separate Python on Windows, Spark notebooks might fail. To work around this issue, upgrade Jupyter to the latest version.

- In a notebook, if you click the **Add Text** command, the text cell is added in preview mode rather than edit mode. You can click on the preview icon to toggle to edit mode and edit the cell.

#### HDFS

- If you right-click on a file in HDFS to preview it, you might see the following error:

   `Error previewing file: File exceeds max size of 30MB`

   Currently there is no way to preview files larger than 30 MB in Azure Data Studio.

- Configuration changes to HDFS that involve changes to hdfs-site.xml are not supported.

#### Security

- The SA_PASSWORD is part of the environment and discoverable (for example in a cord dump file). You must reset the SA_PASSWORD on the master instance after deployment. This is not a bug but a security step. For more information on how to change the SA_PASSWORD in a Linux container, see [Change the SA password](../linux/quickstart-install-connect-docker.md#sapassword).

- AKS logs may contain SA password for big data cluster deployments.

## <a id="ctp20"></a> CTP 2.0 (October 2018)

The following sections describe the new features and known issues for big data clusters in SQL Server 2019 CTP 2.0.

### What's in the CTP 2.0 release?

- Simple deployment experience using mssqlctl management tool
- Native notebook experience in Azure Data Studio
- Query HDFS files via Storage Instance of SQL Server
- Data virtualization via master to SQL Server, Oracle, MongoDB, and HDFS
- Data virtualization wizard for SQL Server and Oracle in Azure Data Studio
- ML Services on master
- Cluster administration portal that you can use for monitoring and troubleshooting
- Spark job submit in Azure Data Studio 
- Spark UI in the cluster administration portal
- Volume mounting to storage classes
- Queries over data pools from master
- Show plan for distributed queries in SSMS
- Pip package for mssqlctl management tool
- Built-in deployment engine through controller service

### Known issues

The following sections provide known issues for SQL Server big data clusters in CTP 2.0.

#### Deployment

- If you are using Azure Kubernetes Service (AKS), the recommended version of Kubernetes is 1.10.*, which does not support disk resizing. You should make sure you are sizing the storage accordingly at deployment time. For more information on how to adjust storage sizes, see the [Data persistence](concept-data-persistence.md) article. For Kubernetes deployed on VMs, the recommended version is 1.11.

- After deploying on AKS, you might see the following two warning events from the deployment. Both of these events are known issues, but they do not prevent you from successfully deploying the big data cluster on AKS.

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- If a big data cluster deployment fails, the associated namespace is not removed. This could result in an orphaned namespace on the cluster. A workaround is to delete the namespace manually before deploying a cluster with the same name.

#### External tables

- It is possible to create a data pool external table for a table that has unsupported column types. If you query the external table, you get a message similar to the following:

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- If you query a storage pool external table, you might get an error if the underlying file is being copied into HDFS at the same time.

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

#### Spark and notebooks

- POD IP addresses may change in the Kubernetes environment as PODs restarts. In the scenario where the master-pod restarts, the Spark session may fail with `NoRoteToHostException`. This is caused by JVM caches that don't get refreshed with new IP addresses.

- If you have Jupyter already installed and a separate Python on Windows, Spark notebooks might fail. To work around this issue, upgrade Jupyter to the latest version.

- In a notebook, if you click the **Add Text** command, the text cell is added in preview mode rather than edit mode. You can click on the preview icon to toggle to edit mode and edit the cell.

#### HDFS

- If you right-click on a file in HDFS to preview it, you might see the following error:

   `Error previewing file: File exceeds max size of 30MB`

   Currently there is no way to preview files larger than 30 MB in Azure Data Studio.

- Configuration changes to HDFS that involve changes to hdfs-site.xml are not supported.

#### Security

- The SA_PASSWORD is part of the environment and discoverable (for example in a cord dump file). You must reset the SA_PASSWORD on the master instance after deployment. This is not a bug but a security step. For more information on how to change the SA_PASSWORD in a Linux container, see [Change the SA password](../linux/quickstart-install-connect-docker.md#sapassword).

- AKS logs may contain SA password for big data cluster deployments.

## Next steps

For more information about SQL Server big data clusters, see [What are SQL Server 2019 big data clusters?](big-data-cluster-overview.md).
