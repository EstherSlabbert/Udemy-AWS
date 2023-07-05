# S3

- [S3](#s3)
  - [AWS CLI commands](#aws-cli-commands)
    - [High-level](#high-level)
    - [Low-level](#low-level)
  - [S3 costs](#s3-costs)
  - [S3 storage classes](#s3-storage-classes)
  - [Policies](#policies)
  - [Data protection](#data-protection)
  - [Logging and Analytics](#logging-and-analytics)
  - [Basic host a static website](#basic-host-a-static-website)

S3 = Simple Secure Storage. Amazon's object storage service.

[Scale the Cloud's YouTube Channel](https://www.youtube.com/channel/UCf5K-sBAII0pM3Ff2l9wbXg)

## AWS CLI commands

`pip install --user --upgrade awscli`: update to latest version.

`aws configure`: set up access and secret access keys and region etc.

Replace <> with actual desired values in the following commands.

### High-level

`aws s3 ls`: list all buckets from all regions.

`aws s3 ls s3://<bucket-name>`: list files/objects in a bucket.

`aws s3 ls s3://<bucket-name> --recursive`: list all file/end-point objects in a bucket.

`aws s3 mb s3://<bucket-name> --region <eu-west-1>`: create bucket in a specific region.

`aws s3 cp <file-name.type> s3://<bucket-name>`: upload a file.

`aws s3 <file-name.type> s3://<bucket-name>/<new-file-name.type>`: upload a file with a new name.

`aws s3 cp <path/to/folder> s3://<bucket-name> --recursive`: upload all files and subfolders in a folder.

`aws s3 cp <path/to/folder> s3://<bucket-name> --recursive --exclude "*.docx"`: uploads files in a folder excluding the specified exceptions inbetween "", in this case all files ending in type .docx.

`aws s3 cp <path/to/folder> s3://<bucket-name> --recursive --exclude "*" --include "*.txt"`: uploads files in a folder excluding the specified exceptions inbetween "", in this case all files, and copies over the specified included files ending in type .txt.

> **_Note:_** Filters/tags that appear last in the command can override those that appear first. Can also specify a part of a file name instead of file type `"*part-of-file-name*"` or part of file name and include a type with `"*part-of-file-name*.jpg"`.

`aws s3 cp <path/to/folder> s3://<bucket-name>/<subfolder-name> --recursive`: upload all files  in a folder to a logical subfolder in a bucket.

`aws s3 cp s3://<bucket-name>/<file-name.type> <path\to\copy\the\file-name.type>`: download a file from a bucket. (download path includes file name or new file name you'd like to download it as).

`aws s3 cp s3://<bucket-name> <path\to\copy\to> --recursive`: bulk download.

`aws s3 cp s3://<original-bucket-name> s3://<bucket-name-to-copy-to> --recursive`: copy all objects between buckets.

`aws s3 mv s3://<original-bucket-name> s3://<bucket-name-to-move-to> --recursive`: move all objects to a different bucket.

`aws s3 rb s3://<bucket-name>`: delete/remove bucket. (can only delete an empty bucket with this)

`aws s3 rb s3://<bucket-name> --force`: delete/remove bucket and all of its contents.

`aws s3 rm s3://<bucket-name>/<file-name.type>`: delete/remove a file in a bucket.

`aws s3 rm s3://<bucket-name>/<folder-name> --recursive`: delete/remove all files and subfolders within a folder in a bucket.

`aws s3 sync <path/to/folder> s3://<bucket-name>`: syncs folder to bucket as it only copies over new/modified files and does not remove deleted items.

`aws s3 sync <path/to/folder> s3://<bucket-name> --delete`: syncs folder to bucket and removes deleted items from bucket.

`aws s3 sync s3://<original-bucket-name> s3://<other-bucket-name>`: sync files between s3 buckets.

`aws s3 cp <path/to/folder> s3://<bucket-name> --recursive --metadata "<key>=<key-value>, <value>=<value-value>"`: add metadata to files as uploaded. (Must copy over files each time you want to change the metadata.)

`aws s3 cp <path/to/folder> s3://<bucket-name> --recursive --storage-class <STORAGE-CLASS>`: uploads files to a bucket with a specific storage class (storage class options: `STANDARD_IA`, `ONZONE_IA`, `GLACIER`)

`aws s3 cp s3://<bucket-name>/<object-name.type> s3://<bucket-name>/<object-name.type> --storage-class <STORAGE-CLASS>`: change storage class of an object in a bucket.

`aws s3 cp s3://<bucket-name>/<folder-name> s3://<bucket-name>/<folder-name> --recursive --storage-class <STORAGE-CLASS>`: change storage class of all objects in a folder of a bucket.

### Low-level

`aws s3api put-bucket-tagging --bucket <bucket-name> --tagging TagSet=[{Key=<key-name>, Value=<value-name>}, {Key=<key-name>, Value=<value-name>}]`: adds tags to a bucket.

`aws s3api get-bucket-tagging --bucket <bucket-name>`: allows you to get/see tags on a bucket.

`aws s3api delete-bucket-tagging --bucket <bucket-name>`: deletes/removes bucket's tags.

`aws s3api put-bucket-tagging --bucket <bucket-name> --tagging TagSet=[{Key=<key-name>, Value=<new-value-name>}]`: updates and completly removes prior tag for a set key.

`aws s3api list-objects --bucket <bucket-name>`: lists objects and info about them in a bucket.

`aws s3api put-object-tagging --bucket <bucket-name> --key <path-inside-bucket/object-name.type> --tagging TagSet=[{Key=<key-name>, Value=<value-name>}, {Key=<key-name>, Value=<value-name>}]`: add tag to object. (will completely replace current tags, so add all the tags you want it to have at once)

`aws s3api get-object-tagging --bucket <bucket-name> --key <path-inside-bucket/object-name.type>`: see/get object tags. (TagSet in json format.)

`aws s3api delete-object-tagging --bucket <bucket-name> --key <path-inside-bucket/object-name.type>`: deletes/removes object tags.

`aws s3api head-object --bucket <bucket-name> --key <path-inside-bucket/object-name.type>`: get metadata from an object including other info that is attached to the object.

`aws s3api list-objects --bucket <bucket-name> --prefix <folder-name>`: lists all info for metadata of multiple objects in the same folder.

`aws s3api put-bucket-versioning --bucket <bucket-name> --versioning-configuration Status=Enabled`: enables versioning.

`aws s3api put-bucket-versioning --bucket <bucket-name> --versioning-configuration Status=Suspended`: suspends versioning.

`aws s3api get-bucket-versioning --bucket <bucket-name>`: gets versioning status.

`aws s3api list-object-versions --bucket <bucket-name>`: lists object versions in a bucket.

`aws s3api put-bucket-encryption --bucket <bucket-name> --serer-side-encryption-configuration "{""Rules"": [{""ApplyServerSideEncryptionByDefault"": {""SSEAlgorithm"": ""AES256""}}]}"`: enables encryption on Windows machine.

`aws s3api get-bucket-encryption --bucket <bucket-name>`: gets encryption rules or return an error if no encryption is present.

`aws s3api delete-bucket-encryption --bucket <bucket-name>`: delete encryption on a bucket.

`aws s3api put-bucket-versioning --bucket <bucket-name> --versioning-configuration MFADelete=Enabled, Status=Enabled --mfa arn:aws:iam::<mfa-device-arn>:mfa/root-account-mfa-device <6-digit-authentication-code>`: enables MFA for deletion of buckets - versioning must be enabled to enable MFA for deletion of buckets. (Find MFA details in Security Credentials > MFA).

`aws s3api delete-object --bucket <bucket-name> --key <file-name.type>`: deletes object or version of object (replaces object with a delete marker if versioning enabled).

`aws s3api delete-object --bucket <bucket-name> --key <file-name.type> --version-id <version-id> --mfa arn:aws:iam::<mfa-device-arn>:mfa/root-account-mfa-device <6-digit-authentication-code>`: deletes a specific version of a file, will be denied if mfa enabled and no mfa authentication given.

`aws s3api put-bucket-versioning --bucket <bucket-name> --versioning-configuration MFADelete=Disabled, Status=Suspended --mfa arn:aws:iam::<mfa-device-arn>:mfa/root-account-mfa-device <6-digit-authentication-code>`: disables MFA delete and disabled versioning (Status=Suspended).

## S3 costs

Pay:

- Per GB stored per month ($0.023 -$0.004)
- Per 1,000 requests (get, put, post, list) ($0.005-$0.01 per 1,000 requests)
- Data Transfer out ($0.00 - $0.09 per GB)

Data Transfer in is free.

5GB free standard storage, 10,000 get requests, 2,000 put requests, and 15GB Data Transfer out free for 1st year.

S3: Standard, Standard IA, One Zone IA, Glacier - most expensive per GB stored to least expensive per GB stored; least expensive per request to most expensive per request.

[S3 pricing](https://aws.amazon.com/s3/pricing/)

You can go to Billing Cost Explorer and filter by service and/ tag for spending data.

## S3 storage classes

You can assign a storage class for items in a bucket or for an entire bucket.

[AWS S3 storage classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html)

S3 Storage Classes: Standard, Standard IA, One Zone IA, Glacier.

You can set up lifecycle management to change the storage class automatically through giving it rules in the management tab of the bucket and lifecycle. Rules can sort by tags or prefixes (folder) - scope - and can specify versions and you can specify how many days the object(s) will move to a different storage task or delete the object(s) (expire).

## Policies

Security is designed to be least privilege based. (i.e. Deny > Allow. If one policy allows and another denies the deny overrules the allow.)

Access Control Lists (ACLs): Grant high level permissions (list, read, write, permission to change permissions), typically granting access to other AWS accounts. Can be used to quickly make a bucket/file public. (can be set when creating a bucket or after a bucket has been created in the Permissions tab)

IAM policies: JSON document that allows/denies group(s)/user(s)/service(s) (principal(s)) to perform specific actions on specific resources (e.g. buckets, objects, tags etc.). (created in IAM dashboard)

Bucket policies: JSON document that is attached to a bucket to allow or deny a principal to perform actions on specific resources (i.e. buckets, objects, tags). (can be set when creating a bucket or after a bucket has been created in the Permissions tab)

[S3 Policies and permissions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-policy-language-overview.html)

## Data protection

Enable encryption when creating a bucket or after creating a bucket in the properties tab of the bucket. Default keys (SSE-S3) managed by AWS S3 Managed Keys or AWS KMS (SSE-KMS), which allows you to manage custom keys (more stringent control and privacy) and has its own costs associated with the service.

Enable versioning in the Properties tab of an already created bucket.
To remove a version and revert back to a previous version you can just delete the current version. You can restore deleted versions by deleting the delete marker.

[Replication](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication.html) can be used to create data redundancy (which ensures the data will be available in the event of a disaster), minimise latency and increase operational efficiency (by having the data closer to the destination). (Performed from a created bucket in the Management tab, replication rules). Rules are in effect for only the new versions uploaded after the rules have been put in place. Higher priority rules > lower priority rules. Deletions do not replicate.

Multi-factor Authentication (MFA) Delete: enabled from the command line only using root acccount access, with versioning enabled.

## Logging and Analytics

Set up event notification to email from bucket's Properties tab > Event notifications. (Must have an SNS Topic for email subscription to add to this)

Server Access Logging: can record all requests on a bucket. (Enabled when creating bucket or in Properties tab > Server access logging, stores log in another bucket in the same region that has permissions to list and write the logs to it in the Permissions tab > ACL > S3 log delivery group).

[Log Format for Server Access Logging](https://docs.aws.amazon.com/AmazonS3/latest/userguide/LogFormat.html)

Object Level Logging with CloudTrail: centralized and granular logging. (Set up a logging trail with CloudTrail with specific data events for object(s) in a bucket and store it in an S3 bucket per day logs in each region).

## Basic host a static website

1. Use Route 53 DNS service to register a domain with a specific name (costs ~$12 per year).
2. Create the bucket to host the static webpage. The bucket name should be the same as the domain name. Ensure you do enable public access to the bucket.
3. Upload the website files to the bucket.
4. Create a policy to allow getObject permissions to public.
5. Go to properties tab > Static website hosting and choose the .html file to display by default and the one you wish to display as a error message.
---
Note:

Can use redirect rules to replace a prefix with a different one for the path to certain files eg.

```xml
 <RoutingRules>
    <RoutingRule>
    <Condition>
      <KeyPrefixEquals>images/</KeyPrefixEquals>
    </Condition>
    <Redirect>
      <ReplaceKeyPrefixWith>img/</ReplaceKeyPrefixWith>
    </Redirect>
    </RoutingRule>
  </RoutingRules>
```

---
6. Route 53 > Hosted zones > select registered DNS and create a new Record Set. Leave the Name blank. For Alias Target select the appropriate S3 endpoint. Can leave routing policy as simple and Evaluate Target Health as No.
7. Go to url that you registered with DNS. Can view different .html files by adding path `/<html-file-name.html>`
