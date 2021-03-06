# Exporting Data to Your Amazon S3 Bucket<a name="export-data-repository"></a>

In Amazon FSx for Lustre, you can export files and their associated metadata that you have written or modified in your file system to your durable data repository on Amazon S3 at any time\. When you export a file or directory, your file system exports only data files and metadata that were created or modified since the last export or since file system creation\. Such an export includes POSIX metadata\. 

## Setting the Export Prefix<a name="export-prefix"></a>

You set the export path, or prefix, when you create your file system using the Amazon FSx console, CLI, or API\. The export prefix specifies the path to export changed data and metadata to on your data repository on S3\. You can't change the export prefix after the file system is created\.

### To Set the Export Prefix \(Console\)<a name="export-path-lustre-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **Create file system**\.

1. Choose **Amazon FSx for Lustre** for the file system type, and then choose **Next**\.

1. Provide the information required for the **File system details** and **Network and security** sections\. For more information, see [Step 1: Create Your Amazon FSx for Lustre File System](getting-started-step1.md)\.

1. Expand the **Data repository integration** section and choose **Amazon S3** for **Data repository type**\. Specify the Amazon S3 **Import bucket** and an optional **Import prefix** as the data repository source\.

1. Choose one of the three **Export prefix** options:
   + **A unique prefix that Amazon FSx creates in your bucket** – Choose this option to export new and changed objects using a prefix generated by Amazon FSx for Lustre\. The prefix looks like the following: `/FSxLustrefile-system-creation-timestamp`\. The timestamp is in UTC format, for example `FSxLustre20181105T222312Z`\. This option is the default\. 
   + **The same prefix that you imported from \(replace existing objects with updated ones\)** – Choose this option to replace existing objects with updated ones\.
   + **A prefix you specify** – Choose this option to preserve your imported data and to export new and changed objects using a prefix that you specify\.   
![\[The Data repository integration page\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/Data-repository-int.png)

1. \(Optional\) Set **Maintenance preferences** or use the system defaults\.

1. Choose **Next** and review the file system settings\.

1. Choose **Create file system**\.

### Setting the Export Path \(AWS CLI\)<a name="export-path-lustre-cli"></a>

You can specify an export path, or prefix, when you create your file system using the AWS CLI or the Amazon FSx API\.
+ To create an Amazon FSx for Lustre file system, use the Amazon FSx CLI command [https://docs.aws.amazon.com/cli/latest/reference/fsx/create-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/create-file-system.html) as shown following\. The corresponding API operation is [CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html)\.

  ```
  $ aws fsx create-file-system \
        --client-request-token CRT1234 \
        --file-system-type LUSTRE \
        --lustre-configuration DeploymentType=PERSISTENT_1,ImportPath=s3://lustre-export-test-bucket/,ExportPath=s3://lustre-export-test-bucket/export \
        --storage-capacity 3600 \
        --subnet-ids subnet-123456 \
        --tags Key=Name,Value=Lustre-TEST-1 \
        --region us-east-2
  ```

After successfully creating the file system, Amazon FSx returns the file system description as JSON, as shown in the following example\.

```
{

    "FileSystems": [
        {
            "OwnerId": "owner-id-string",
            "CreationTime": 1549310341.483,
            "FileSystemId": "fs-0123456789abcdef0",
            "FileSystemType": "LUSTRE",
            "Lifecycle": "CREATING",
            "StorageCapacity": 3600,
            "VpcId": "vpc-123456",
            "SubnetIds": [
                "subnet-123456"
            ],
            "NetworkInterfaceIds": [
                "eni-039fcf55123456789"
            ],
            "DNSName": "fs-0123456789abcdef0.fsx.us-east-2.amazonaws.com",
            "ResourceARN": "arn:aws:fsx:us-east-2:123456:file-system/fs-0123456789abcdef0",
            "Tags": [
                {
                    "Key": "Name",
                    "Value": "Lustre-TEST-1"
                }
            ],
            "LustreConfiguration": {
                "WeeklyMaintenanceStartTime": "2:04:30",
                "DeploymentType": "PERSISTENT_1",
                "DataRepositoryConfiguration": {
                    "ImportPath": "s3://lustre-export-test-bucket/",
                    "ExportPath": "s3://lustre-export-test-bucket/export",
                    "ImportedFileChunkSize": 1024
                }
            }
        }
    ]
}
```

## Viewing a File System's Export Path<a name="view-export-path"></a>

You can view a file system's export path using the Amazon FSx for Lustre console, the AWS CLI, and the API\.

### To View a File System's Export Path \(Console\)<a name="view-export-path-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Choose **File system name** or **File system ID** for the Amazon FSx for Lustre file system that you want to view the export path for\. The file system details page appears for that file system\. 

1. Choose the **Data repository** tab\. The **Data repository integration** panel appears, showing the import and export paths\.  
![\[Amazon FSx Lustre file system data repository integration panel.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/show_exprt_path.png)

### To View a File System's Export Path \(CLI\)<a name="view-export-path-cli"></a>
+ To determine the export path for your file system, use the [https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) command\.

  ```
  aws fsx describe-file-systems
  ```

  Look for the `ExportPath` property under `LustreConfiguration` in the response\.

  ```
  {
      "OwnerId": "0123456789ab",
      "CreationTime": 1563382847.014,
      "FileSystemId": "",
      "FileSystemType": "LUSTRE",
      "Lifecycle": "AVAILABLE",
      "StorageCapacity": 3600,
      "VpcId": "vpc-6296a00a",
      "SubnetIds": [
          "subnet-1111111"
      ],
      "NetworkInterfaceIds": [
          "eni-0c288d5b8cc06c82d",
          "eni-0f38b702442c6918c"
      ],
      "DNSName": "fs-0123456789abcdef0.fsx.us-east-2.amazonaws.com",
      "ResourceARN": "arn:aws:fsx:us-east-2:267731178466:file-system/fs-0123456789abcdef0",
      "Tags": [
          {
              "Key": "Name",
              "Value": "Lustre System"
          }
      ],
      "LustreConfiguration": {
          "WeeklyMaintenanceStartTime": "6:09:30",
          "DataRepositoryConfiguration": {
              "ImportPath": "s3://lustre-export-test-bucket/",
      =======>"ExportPath": "s3://lustre-export-test-bucket/FSxLustre20190717T164753Z",<========
              "ImportedFileChunkSize": 1024
          }
      }
  },
  ```

## Exporting Changed Data and Metadata<a name="export-data-repo-task"></a>

You can export data and metadata changes, including POSIX metadata, from your Amazon FSx file system to its linked data repository\. Associated POSIX metadata includes ownership, permissions, and timestamps\. To perform this export, you use a data repository task\.

Data repository tasks optimize data transfer by tracking changes between your Amazon FSx file system and its linked data repository\. Only files or directories with new or modified data or metadata are exported\. By exporting this data, you can implement and maintain access controls between your Amazon FSx for Lustre file system and its linked durable data repository on Amazon S3\. For more information, see [Transferring Data and Metadata with Data Repository Tasks](data-repository-tasks.md)\. 

Use the following procedures to export data and metadata changes by using the Amazon FSx console and CLI\.

### To Export Data and Metadata Changes \(Console\)<a name="create-data-repo-task-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. On the navigation pane, choose **File systems**, then choose the Lustre file system that you want to create the task for\.

1. For **Actions**, choose **Export to data repository**\. This choice is not available if the file system isn't linked to a data repository on S3\. The **Create data repository task** page appears\.  
![\[Create data repository task page\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/create-exprt-task-dialog1.png)

   **Data repository task type** is set to **Export to repository**, which is the only task type currently supported\. The **Export destination** value is the export prefix that you defined when you created the file system\.

1. \(Optional\) Specify up to 32 directories or files to export from your Amazon FSx file system by providing the paths to those directories or files in **File system export paths**\. The paths you provide need to be relative to the mount point of the file system\. If the mount point is `/mnt/fsx` and `/mnt/fsx/path1` is a directory or file on the file system you want to export, then the path to provide is `path1`\.
**Note**  
If a path that you provide isn't valid, the task fails\.

1. \(Optional\) Choose **Enable** under **Completion report** to generate a task completion report after the task completes\. A *task completion report* provides details about the files processed by the task that meet the scope provided in **Report scope**\. To specify the location for Amazon FSx to deliver the report, enter a relative path on the file system's linked S3 data repository for **Report path**\.

1. Choose **Create data repository task**\. 

   A notification at the top of the **File systems** page shows the task that you just created in progress\. 

To view the task status and details, choose **Data repository tasks \(Lustre\)** on the navigation pane\. The default sort order shows the most recent task at the top of the list\. 

To view a task summary from this page, choose **Task ID** for the task you just created\. The **Summary** page for the task appears\. 

### To Export Data and Metadata Changes \(CLI\)<a name="create-data-repo-task-cli"></a>
+ Use the [https://docs.aws.amazon.com/cli/latest/reference/fsx/create-data-repository-task.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/create-data-repository-task.html) CLI command to export data and metadata changes on your Amazon FSx for Lustre file system\. The corresponding API operation is [ `CreateDataRepositoryTask`](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateDataRepositoryTask.html)\.

  ```
  $ aws fsx create-data-repository-task \
      --file-system-id fs-0123456789abcdef0 \
      --type EXPORT_TO_REPOSITORY \
      --paths path1,path2/file1 \
      --report Enabled=true,Scope=FAILED_FILES_ONLY,Format=REPORT_CSV_20191124,Path=s3://dataset-01/reports
  ```

  After successfully creating the data repository task, Amazon FSx returns the task description as JSON, as shown in the following example\.

  ```
  {
      "Task": {
          "TaskId": "task-123f8cd8e330c1321",
          "Type": "EXPORT_TO_REPOSITORY",
          "Lifecycle": "PENDING",
          "FileSystemId": "fs-0123456789abcdef0",
          "Paths": ["path1", "path2/file1"],
          "Report": {
              "Path":"s3://dataset-01/reports",
              "Format":"REPORT_CSV_20191124",
              "Enabled":true,
              "Scope":"FAILED_FILES_ONLY"
          },
          "CreationTime": "1545070680.120",
          "ClientRequestToken": "10192019-drt-12",
          "ResourceARN": "arn:aws:fsx:us-east-1:123456789012:task:task-123f8cd8e330c1321"
      }
  }
  ```

After creating the task to export data to the linked data repository on S3, you can check the status of the export\. For more information about viewing data repository tasks, see [Accessing Data Repository Tasks](managing-data-repo-task.md#view-data-repo-tasks)\.