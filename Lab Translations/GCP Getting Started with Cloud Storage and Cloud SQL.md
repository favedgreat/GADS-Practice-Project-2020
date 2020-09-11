##Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL
#Objectives:
- Create a Cloud Storage bucket and place an image into it.

- Create a Cloud SQL instance and configure it.

- Connect to the Cloud SQL instance from a web server.

- Use the image in the Cloud Storage bucket on a web page.

#Task 1: Sign in to the Google Cloud Platform (GCP) Console
1. Make sure you signed into Qwiklabs using an `incognito window`

2. Note the lab's `access time` and make sure you can finish in that time block.
> There is no pause feature. You can restart if needed, but you have to start at the beginning.

3. When ready, `Click` *start_lab*

4. Note your lab credentials. You will use them to sign in to Cloud Platform Console. img/open_google_console.png

5. **Click** `Open Google Console.`

6. **Click** `Use another account` and copy/paste credentials for `this` lab into the prompts.
>NOTE: If you use other credentials, you'll get errors or incur charges.

7. Accept the terms and skip the recovery resource page


#Task 2: Deploy a web server VM instance(Now translated to Cloud shell)
1. In GCP console, on the top right toolbar, `click` the Open Cloud Shell button

2. Click `Continue`

3. To create a web server VM instance called `bloghost` in the **Region** and **zone** Quiklabs assigned, execute this command:
    `gcloud compute instances create "bloghost" --machine-type "n1-standard-1" --zone` <zone-name> `--image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default" --tags allow-http`

4. To create firewall rule `allow http traffic` to allow http traffic, execute this command:
    `gcloud compute firewall-rules create allow-HTTP-traffic --action allow --direction INGRESS --rules tcp:80 --target-tags allow-http`
>NOTE: You could Type `Exit` in Cloud shell if need for exiting arises.

5. 

6. Execute the command to list the `vm instances` within the project:
    `gcloud compute instances list`
> Copy the `bloghost` internal and external IP address to a text editor for use later in the lab


#Task 3: Create a Cloud Storage bucket using the gsutil command line
> Skip to step **3** if you did not exit the cloud shell in the previous Task.

1. In GCP console, on the top right toolbar, `click` the Open Cloud Shell button

2. Click `Continue`

3. For convenience, enter your chosen location into an environment variable called `LOCATION`. Enter one of these commands:
    - `export LOCATION=US`
                or
    - `export LOCATION=EU`
                or
    - `export LOCATION=ASIA`

4. In Cloud Shell, the `DEVSHELL_PROJECT_ID` environment variable contains your `project ID`(which are often global). Enter this command to make a bucket named after your project ID(or a unique id of your choice)
    `gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID`

5. Retrieve a banner image from a publicly accessible Cloud Storage location:
    `gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png`

6. Copy the banner image to your newly created Cloud Storage bucket:
    `gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png`

7. Modify the Access Control List of the object you just created so that it is readable by everyone:
    `gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png`


#Task 4: Create the Cloud SQL instance
> Skip to step **3** if you did not exit the cloud shell in the previous Task.

1. In GCP console, on the top right toolbar, `click` the Open Cloud Shell button

2. Click `Continue`

3. Execute the command to create `SQL instance`
    `gcloud sql instances create blog-db --zone` <zone-name> `--root-password` <password>
> Use the same `region` and `zone` into which you launched the `bloghost instance`. The best performance is achieved by placing the `client` and the `database` close to each other.
> Choose a `password` that you remember. There's no need to obscure the password because you'll use mechanisms to connect that aren't open access to everyone

4. 