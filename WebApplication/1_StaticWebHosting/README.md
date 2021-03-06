# Module 1: Static Web Hosting with Amazon S3

In this module you'll configure Amazon Simple Storage Service (S3) to host the static resources for your web application. In subsequent modules you'll add dynamic functionality to these pages using JavaScript to call remote RESTful APIs built with AWS Lambda and Amazon API Gateway.

## Architecture Overview

The architecture for this module is very straightforward. All of your static web content including HTML, CSS, JavaScript, images and other files will be stored in Amazon S3. Your end users will then access your site using the public website URL exposed by Amazon S3. You don't need to run any web servers or use other services in order to make your site available.

![Static website architecture](../images/static-website-architecture.png)

## Implementation Instructions

Each of the following sections provide an implementation overview and detailed, step-by-step instructions. The overview should provide enough context for you to complete the implementation if you're already familiar with the AWS Management Console or you want to explore the services yourself without following a walkthrough.

If you're using the latest version of the Chrome, Firefox, or Safari web browsers the step-by-step instructions won't be visible until you expand the section.

### 1. Create an S3 Bucket

Amazon S3 can be used to host static websites without having to configure or manage any web servers. In this step you'll create a new S3 bucket that will be used to host all of the static assets (e.g. HTML, CSS, JavaScript, and image files) for your web application.

#### High-Level Instructions

Use the console or AWS CLI to create an Amazon S3 bucket. Keep in mind that your bucket's name must be globally unique across all regions and customers. We recommend using a name like `wildrydes-firstname-lastname`. If you get an error that your bucket name already exists, try adding additional numbers or characters until you find an unused name.

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. In the AWS Management Console choose **Services** then select **S3** under Storage.

1. Choose **+Create Bucket**

1. Provide a globally unique name for your bucket such as `wildrydes-firstname-lastname`.

1. Select the Region you've chosen to use for this workshop from the dropdown.

1. Choose **Create** in the lower left of the dialog without selecting a bucket to copy settings from.

    ![Create bucket screenshot](../images/create-bucket.png)

</p></details>

### 2. Upload Content

Upload the website assets for this module to your S3 bucket. You can use the AWS Management Console or the AWS CLI.

<details>
<summary><strong>Console step-by-step instructions (expand for details)</strong></summary><p>

1. Download an archive of this repository using [this link](https://github.com/awslabs/aws-serverless-workshops/archive/master.zip).

1. Unzip the archive you downloaded on your local machine.

1. Open the AWS Management Console in Chrome. Choose **Services** then select **S3** under Storage.

1. Select the bucket you created in the previous step and ensure you are viewing the **Objects** tab.

1. Open either Windows File Explorer or macOS Finder and browse to the expanded contents of the zip file you downloaded in the first step.

1. Browse to the WebApplication/1_StaticWebHosting/website directory on your local machine.

1. Select all of the files and subdirectories under the website directory. Ensure that the website directory itself is not selected.

1. Drag and drop the selected files from your local filesystem to the content under the **Objects** tab in the S3 console.

1. Choose **Upload** in the lower left of the dialog box that appears.

1. Wait for the upload to complete, and ensure you see the contents of the website directory listed in the S3 console. If you only see a single `website` directory, you should delete it from your bucket and follow these instructions again ensuring that you select only the contents of the directory before dragging and dropping into the S3 console.

</p></details>

### 3. Add a Bucket Policy to Allow Public Reads

You can define who can access the content in your S3 buckets using a bucket policy. Bucket policies are JSON documents that specify what principals are allowed to execute various actions against the objects in your bucket.

#### High-Level Instructions

You will need to add a bucket policy to your new Amazon S3 bucket to let anonymous users view your site. By default your bucket will only be accessible by authenticated users with access to your AWS account.

This policy allows anyone on the Internet to view your content. The easiest way to update a bucket policy is to use the console. Select the bucket, choose the permission tab and then select Bucket Policy.

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. In the S3 console, select the name of the bucket you created in section 1.

1. Choose the **Permissions** tab, then choose **Bucket Policy**.

1. Enter the following policy document into the bucket policy editor replacing `[YOUR_BUCKET_NAME]` with the name of the bucket you created in section 1:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Principal": "*",
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::[YOUR_BUCKET_NAME]/*"
            }
        ]
    }
    ```

    ![Update bucket policy screenshot](../images/update-bucket-policy.png)

1. Choose **Save** to apply the new policy.

</p></details>

### 4. Enable Website Hosting

By default objects in an S3 bucket are available via URLs with the structure http://<Regional-S3-prefix>.amazonaws.com/<bucket-name>/<object-key>. In order to serve assets from the root URL (e.g. /index.html), you'll need to enable website hosting on the bucket. This will make your objects available at the AWS Region-specific website endpoint of the bucket: <bucket-name>.s3-website-<AWS-region>.amazonaws.com

You can also use a custom domain for your website. For example http://www.wildrydes.com is hosted on S3. Setting up a custom domain is not covered in this workshop, but you can find detailed instructions in our [documentation](http://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-custom-domain-walkthrough.html).

#### High-Level Instructions

Using the console, enable static website hosting. You can do this on the Properties tab after you've selected the bucket. Set `index.html` as the index document, and leave the error document blank. See the documentation on [configuring a bucket for static website hosting](https://docs.aws.amazon.com/AmazonS3/latest/dev/HowDoIWebsiteConfiguration.html) for more details.

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. From the bucket detail page in the S3 console, choose the **Properties** tab.

1. Choose the **Static website hosting** card.

1. Select **Use this bucket to host a website** and enter `index.html` for the Index document. Leave the other fields blank.

1. Note the **Endpoint** URL at the top of the dialog before choosing **Save**. You will use this URL throughout the rest of the workshop to view your web application. From here on this URL will be referred to as your website's base URL.

1. Click **Save** to save your changes.

    ![Enable website hosting screenshot](../images/enable-website-hosting.png)

</p></details>


## Implementation Validation

After completing these implementation steps you should be able to access your static website by visiting the the website endpoint URL for your S3 bucket.

Visit your website's base URL (this is the URL you noted in section 4) in the browser of your choice. You should see the Wild Rydes home page displayed. If you need to lookup the base URL, visit the S3 console, select your bucket and then click the **Static Web Hosting** card on the **Properties** tab.

If the page renders correctly (see below for an example screenshot), you can move on to the next module, [User Management](../2_UserManagement).

![Wild Rydes homepage screenshot](../images/wildrydes-homepage.png)
