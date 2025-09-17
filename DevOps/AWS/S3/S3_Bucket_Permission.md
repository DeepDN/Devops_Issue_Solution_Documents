# AWS S3 Bucket permission

Specific bucket permission sharing with specific IAM user

link:- https://www.atensoftware.com/p90.php?q=309

# **How to grant access to a single Amazon AWS S3 bucket**

Use these instructions to share programmatic full read/write/list access to a single Amazon S3 bucket in your Amazon Web Services (AWS) account. You can optionally configure the bucket for public read access, suitable for access via HTTPS (static web site hosting).

### **Choose a bucket name**

If your website is mystore.com, then we suggest naming the bucket like `atensoftware.mystore.com`, so you know the purpose of the bucket.

**Enter your Bucket Name in the following input box to automatically customize the instructions.** (This information is not transmitted or saved anywhere.)

### **Create the bucket**

1. Sign into the [Amazon AWS S3 Management Console](https://s3.console.aws.amazon.com/s3/)
2. Click the *Create Bucket* button.
3. Enter a bucket name, like `atensoftware.mystore.com`
4. Select the *US East (Ohio) us-east-2* region.
5. Set *Object Ownership* to **ACLs disabled (recommended)**.
6. Leave checkboxes for *Block public access* checked.
7. Set *Bucket Versioning* to **Disable**
8. Do not add any *Tags*
9. Leave *Default encryption* at defaults: *Encryption key type* = **Amazon S3 managed keys (SSE-S3)** and *Bucket Key* = **Enable**.
10. Do not change any *Advanced settings*
11. Click the *Create bucket* button.

### **(OPTIONAL) Enable public HTTPS web access to the bucket**

Complete this section to enable public web access to files in the bucket.

1. From S3 Console, click the *Buckets* tab in side-bar.
2. Click on the bucket that you created, e.g. `atensoftware.mystore.com`
3. Click the *Permissions* tab.
4. Click the *Edit* button in the *Block public access (bucket settings)* section.
5. Uncheck the *Block all public access* checkbox and all four checkboxes underneath it.
6. Click the *Save changes* button.
7. Type `confirm` in the box that pops up, andregulqrisation click the *Confirm* button.
8. Scroll down to the *Bucket policy* section and click the *Edit* button
9. Copy and paste the text below, replacing *atensoftware.mystore.com* with the name of the bucket you created:
    
    ```
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "PublicReadGetObject",
                "Effect": "Allow",
                "Principal": "*",
                "Action": [
                    "s3:GetObject"
                ],
                "Resource": [
                    "arn:aws:s3:::atensoftware.mystore.com/*"
                ]
            }
        ]
    }
    ```
    
10. Click the *Save changes* button.

If you upload a file named `image1.jpeg` to the bucket, then the publicly-accessible URL to an object in the bucket will look like this: `https://s3.us-east-2.amazonaws.com/atensoftware.mystore.com/image1.jpeg`

### **Create a permission policy for the bucket**

This permission policy will be assigned to the user created in a later step.

1. Go to the [Amazon AWS IAM Management Console](https://console.aws.amazon.com/iam/)
2. Click *Policies* on the side-bar.
3. Click the *Create Policy* button
4. Click the *JSON* tab
5. Copy and paste the following into the text box, taking care to replace *atensoftware.mystore.com* with the bucket name you created:
    
    ```
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "s3:ListBucket"
                ],
                "Resource": [
                    "arn:aws:s3:::atensoftware.mystore.com"
                ]
            },
            {
                "Effect": "Allow",
                "Action": ["s3:*"],
                "Resource": "arn:aws:s3:::atensoftware.mystore.com/*"
            }
        ]
    }
    ```
    
6. Click the *Next: Tags* button.
7. Click the *Next: Review* button.
8. Enter *SingleBucketFullAccess* as the *Name*
9. Click the *Create policy* button.

### **Create a user with access to the bucket**

1. Go to the [Amazon AWS IAM Management Console](https://console.aws.amazon.com/iam/)
2. Click *Users* on the side-bar.
3. Click the *Add users* button.
4. Enter `atensoftware` as the *User name*.
5. Check the *Programmatic access* checkbox for *Access type*. Leave *AWS Management Console access* unchecked.
6. Click the *Next: Permissions* button.
7. Select *Attach existing policies directly*.
8. Type `single` in the *Filter policies* search box.
9. Check the box for the *SingleBucketFullAccess* policy that was created earlier.
10. Click the *Next: Tags* button
11. Click the *Next: Review* button
12. Click the *Create user* button
13. Continue to the next section before closing the browser.

### **Send us the user credentials**

1. Click the *Show Secret access key* link and leave the browser window/tab open.
2. Go to our [Secure Login and Password Form](https://www.atensoftware.com/p342.php) in a new browser window/tab.
3. Select *NOT APPLICABLE* as the *Shopping Engine*.
4. Copy the *Access key ID* and *Secret access key* from the Amazon IAM *User Security Credentials* to our *Secure Login and Password* form, placing them in the *Login* and *Password* boxes, respectively.
5. Enter the bucket name, e.g. `atensoftware.mystore.com`, in the *Additional Notes* box.
6. Click the *Submit* button.

If later you decide to revoke the permissions, simply delete the 'atensoftware' user. You can also delete the policy and bucket if you no longer need it.