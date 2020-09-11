## Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL
# Objectives:
- Create a Cloud Storage bucket and place an image into it.

- Create a Cloud SQL instance and configure it.

- Connect to the Cloud SQL instance from a web server.

- Use the image in the Cloud Storage bucket on a web page.

# Task 1: Sign in to the Google Cloud Platform (GCP) Console
1. Make sure you signed into Qwiklabs using an `incognito window`

2. Note the lab's `access time` and make sure you can finish in that time block.
> There is no pause feature. You can restart if needed, but you have to start at the beginning.

3. When ready, `Click` *start_lab*

4. Note your lab credentials. You will use them to sign in to Cloud Platform Console. img/open_google_console.png

5. **Click** `Open Google Console.`

6. **Click** `Use another account` and copy/paste credentials for `this` lab into the prompts.
>NOTE: If you use other credentials, you'll get errors or incur charges.

7. Accept the terms and skip the recovery resource page


# Task 2: Deploy a web server VM instance(Now translated to Cloud shell)
1. In GCP console, on the top right toolbar, `click` the Open Cloud Shell button

2. Click `Continue`

3. To create a web server VM instance called `bloghost` in the **Region** and **zone** Quiklabs assigned, execute this command:
    `gcloud compute instances create "bloghost" --machine-type "n1-standard-1" --zone` <zone-name> `--image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default" --tags allow-http --metadata startup-script="apt-get update; apt-get install apache2 php php-mysql -y; service apache2 restart"`

4. To create firewall rule `allow http traffic` to allow http traffic, execute this command:
    `gcloud compute firewall-rules create allow-HTTP-traffic --action allow --direction INGRESS --rules tcp:80 --target-tags allow-http`
>NOTE: You could Type `Exit` in Cloud shell if need for exiting arises.

5. Execute the command to list the `vm instances` within the project:
    `gcloud compute instances list`
> Copy the `bloghost` internal and external IP address to a text editor for use later in the lab


# Task 3: Create a Cloud Storage bucket using the gsutil command line
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


# Task 4: Create the Cloud SQL instance
> Skip to step **3** if you did not exit the cloud shell in the previous Task.

1. In GCP console, on the top right toolbar, `click` the Open Cloud Shell button

2. Click `Continue`

3. Execute the command to create `SQL instance`
    `gcloud sql instances create blog-db --zone` <zone-name> `--root-password` <password>
> Use the same `region` and `zone` into which you launched the `bloghost instance`. The best performance is achieved by placing the `client` and the `database` close to each other.
> Choose a `password` that you remember. There's no need to obscure the password because you'll use mechanisms to connect that aren't open access to everyone

4. Execute the following command to list the SQL instances:
    `gcloud sql instances list`
> Copy the `blog-db` *Public IP* address to a text editor for use later in this lab

5. Execute the following command to create a `User`
    `gcloud sql users create blogdbuser --instance blog-db --password` <user-password>

6. Execute the following command to list vm instances in the project
    `gcloud compute instances list`
> Copy the External IP address of the `bloghost` VM to a text editor 

   - Execute the following command to add `Network`
    `gcloud compute networks create web-front-end --range` <bloghost-external-ip>/32
> Be sure to use the external IP address of your VM instance followed by /32. Do not use the VM instance's internal IP address

# Task 5: Configure an application in a Compute Engine instance to use Cloud SQL
> Skip to step **3** if you did not exit in the previous Task.

1. In GCP console, on the top right toolbar, `click` the Open Cloud Shell button

2. Click `Continue`

3. Enter the following command to `ssh` into the VM instance
    `gcloud compute ssh bloghost`
4. Execute the command below to change the working directory
    `cd /var/www/html`

5. Use the nano text editor to edit a file called index.php:
    `sudo nano index.php`

6. Paste the content below into the file:

    <html>
    <head><title>Welcome to my excellent blog</title></head>
    <body>
    <h1>Welcome to my excellent blog</h1>
    <?php
    $dbserver = "CLOUDSQLIP";
    $dbuser = "blogdbuser";
    $dbpassword = "DBPASSWORD";
    // In a production blog, we would not store the MySQL
    // password in the document root. Instead, we would store it in a
    // configuration file elsewhere on the web server VM instance.

    $conn = new mysqli($dbserver, $dbuser, $dbpassword);

    if (mysqli_connect_error()) {
            echo ("Database connection failed: " . mysqli_connect_error());
    } else {
            echo ("Database connection succeeded.");
    }
    ?>
    </body></html>
> In a later step, you will insert your Cloud SQL instance's IP address and your database password into this file. For now, leave the file unmodified.

7. Press Ctrl+O, and then press Enter to save your edited file.

8. Press Ctrl+X to exit the nano text editor.

9. Restart the web server with the command:
    `sudo service apache2 restart`

10. Open a new web browser tab and paste into the address bar your `bloghost VM instance's external IP address` followed by `/index.php`. The URL will look like this:
    <bloghost-external-ip>`/index.php`
> Be sure to use the external IP address of your VM instance followed by `/index.php`. 
> Do not use the VM instance's internal IP address.

**Output Expected:**
    `Database connection failed: ...`
> This message occurs because you have not yet configured PHP's connection to your Cloud SQL instance.

11. Return to your ssh session on bloghost. Use the nano text editor to edit index.php again.
    `sudo nano index.php`

15. In the nano text editor, replace `CLOUDSQLIP` with the `Cloud SQL instance Public IP address` that you noted above. Leave the quotation marks around the value in place.

16. In the nano text editor, replace `DBPASSWORD` with the `Cloud SQL database password` that you defined above. Leave the quotation marks around the value in place.

17. Press `Ctrl+O`, and then press `Enter` to save your edited file.

18. Press `Ctrl+X` to exit the nano text editor.

19. Restart the web server:
    `sudo service apache2 restart`
    
20. Return to the web browser tab in which you opened your bloghost VM instance's external IP address. When you load the page, the following message appears:
    `Database connection succeeded.`
> In an actual blog, the database connection status would not be visible to blog visitors. Instead, the database connection would be managed solely by the administrator


# Task 6: Configure an application in a Compute Engine instance to use a Cloud Storage object
> Skip to step **3** if you did not exit in the previous Task.

1. In GCP console, on the top right toolbar, `click` the Open Cloud Shell button

2. Click `Continue`

3. Execute:

4. Return to your ssh session on your `bloghost` VM instance.

5. Enter this command to set your working directory to the document root of the web server:
    `cd /var/www/html`

6. Use the nano text editor to edit index.php:
    `sudo nano index.php`

7. Use the arrow keys to move the cursor to the line that contains the h1 element. Press Enter to open up a new, blank screen line, and then paste the URL you copied earlier into the line.

8. Paste this HTML markup immediately before the URL:
    `<img src='`

9. Place a closing single quotation mark and a closing angle bracket at the end of the URL:
    `'>`

**Output Expected:**
<img src='https://storage.googleapis.com/qwiklabs-gcp-0005e186fa559a09/my-excellent-blog.png'>

**NOTE:*** Do not copy the link
> The effect of these steps is to place the line containing <img src='...'> immediately before the line containing <h1>...</h1>

10. Press `Ctrl+O`, and then press `Enter` to save your edited file.

11. Press `Ctrl+X` to exit the nano text editor.

12. Restart the web server:
    `sudo service apache2 restart`

13. Return to the web browser tab in which you opened your bloghost VM instance's external IP address. When you load the page, its content now includes a banner image.


<!-- Documentation ends here! -->