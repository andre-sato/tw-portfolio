# Data sharing process with third-parties
Data Sharing allows secure and simplified file exchanges, including standard and sensible data (PII) between the company and third-parties through SFTP. The solution uses *AWS Transfer Family* and *Amazon S3 services* to provide the following components:

- SFTP server: enables third-party entities to access and manage files through SFTP, using softwares like FileZilla or a SFTP-based automation.
- S3 bucket: stores the data to be sent/received and is used to access the files internally.

While third-parties manage files through the SFTP connection, services and employees do it directly through Amazon S3. Only third-parties should access the SFTP server using FileZilla.
⚠️The solution described in this guide provides a simple way to exchange data using a widely-used tool.

## B2B Data Sharing Integration Solution
SFTP Data Sharing is limited to data in file format only. If a more complex integration is needed that does not have this limitation, use a solution other than SFTP, such as HTTP APIs or message brokers.

### How to set up integration with third-parties
To set up integration with a third-party, please follow the steps below:

1. Send instructions to the third-party
Send the playbook link containing the instructions the third-party must follow. As mentioned earlier, third-parties may access S3 buckets through FileZilla. 
While using FileZilla is strongly recommended, other SFTP clients can be used if necessary, for example if the third-party wants to automate SFTP access. 

2. Receive the third-party's public key
After the third-party followed the instructions in the playbook to generate an SSH key pair, they must share the public key. 
A valid public key should start with “ssh-rsa” followed by a sequence of characters then the private key file name (private-key-xyzw.pem)

3. Define the SFTP server files' location (S3 bucket & path)
For optimal Data Sharing experience and security, it is recommended using the default `data-sharing-<country>-prod bucket`. 
However, if your team already uses a different bucket, specify it using the following format: `<bucket>/<path>/<third-party name>`. The <path> is optional, for example `bucket/files/<third-party-name>` or `bucket/<third-party-name>`. 

### Shared data lifecycle

When receiving files from the third-party, there's no need to create the folder in the bucket. It is automatically created when the third-party connects for the first time.

When sending files, it is necessary to create the specific third-party folder manually on AWS Portal S3 Bucket page if it doesn’t already exists on the bucket.
We recommend the S3 bucket to be located in the same AWS Region as the SFTP server.

To ensure data security and compliance, notice that a lifecycle policy must be in place for the bucket and path. 
It will automatically delete all files after 30 days. 

> Notice that the folder creation in the bucket is delayed until the third-party access the SFTP for the first time. To send files, for the first time, create the folder with the defined name.
