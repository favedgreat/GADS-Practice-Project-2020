##Google Cloud Fundamentals: Getting Started with Compute Engine
#Objectives:
- Create a Compute Engine virtual machine using the Google Cloud Platform (GCP) Console.

- Create a Compute Engine virtual machine using the gcloud command-line interface.

- Connect between the two instances.

#Task 1: Sign in to the Google Cloud Platform (GCP) Console
1. Make sure you signed into Qwiklabs using an `incognito window`

2. Note the lab's `access time` and make sure you can finish in that time block.
> There is no pause feature. You can restart if needed, but you have to start at the beginning.

3. When ready, `Click` *start_lab*

4. Note your lab credentials. You will use them to sign in to Cloud Platform Console. img/open_google_console.png

5. **Click** `Open Google Console.`

6. **Click** `Use another account` and copy/paste credentials for `this` lab into the prompts.
> If you use other credentials, you'll get errors or incur charges.

7. Accept the terms and skip the recovery resource page


#Task 2: Create a virtual machine using the GCP Console(Now translated to Cloud shell)
1. In GCP console, on the top right toolbar, `click` the Open Cloud Shell button

2. Click `Continue`

3. To create a VM instance called `my-vm-1` in the zone Quiklabs assigned, execute this command:
    `gcloud compute instances create "my-vm-1" --machine-type "n1-standard-1" --zone` <zone-name> `--image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default" --tags allow-http`

4. To create firewall rule `allow http traffic` to allow http traffic, execute this command:
    `gcloud compute firewall-rules create allow-HTTP-traffic --action allow --direction INGRESS --rules tcp:80 --target-tags allow-http`
> You could Type `Exit` in Cloud shell if need for exiting arises.


#Task 3: Create a virtual machine using the gcloud command line
> Skip to step **3** if you did not exit in the previous Task.

1. In GCP console, on the top right toolbar, `click` the Open Cloud Shell button

2. Click `Continue`

3. Enter the following command in the shell to list the available zones in the region:
    `gcloud compute zones list | grep` <region-name>
> choose a zone from the output of this zone and use the command to filter the results to list the zones associated to `us-central-1`

4. Choose a zone from that list other than the zone to which Qwiklabs assigned you. For example, if Qwiklabs assigned you to region `us-central1` and zone `us-central1-a` you might choose zone `us-central1-b`

5. To set your default zone to the one you just chose, enter this partial command `gcloud config set compute/zone` followed by the zone you chose. Enter the following command:
    `gcloud config set compute/zone` <zone-name>

6. To create a VM instance called `my-vm-2` in that zone, execute this command:
    `gcloud compute instances create "my-vm-2" --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default"`
> Note: The VM can take about two minutes to launch and be fully available for use


#Task 4: Connect between VM instances
1. In the cloud shell, execute the following command to list the running Vitual Machines in the project:
    `gcloud compute instances list`

2. Execute the following command to *ssh* into the virtual machine of your choice:
    `gcloud compute ssh my-vm-2`

3. Use the `ping` utility command to confirm that `my-vm-2` can reach `my-vm-1` over the network:
    `ping my-vm-1`

4. Press `Ctrl+C` to abort the ping command.

5. Use the `ssh` command to open a command prompt on my-vm-1
    `ssh my-vm-1`
>If you are prompted about whether you want to continue connecting to a host with unknown authenticity, enter yes to confirm that you do.

6. At the command prompt on my-vm-1, install the Nginx web server:
    `sudo apt-get install nginx-light -y`

7. Use the nano text editor to add a custom message to the home page of the web server:
    `sudo nano /var/www/html/index.nginx-debian.html`

8. Use the *arrow keys* to move the cursor to the line just below the h1 header. Add text like this, and replace YOUR_NAME with your name:
    `Hi from YOUR_NAME`


9. Press **Ctrl+O** and then press **Enter** to save your edited file, and then press **Ctrl+X** to exit the nano text editor.

10. Confirm that the web server is serving your new page. At the command prompt on `my-vm-1`, execute this command:
    `curl http://localhost/`
> The response will be the HTML source of the web server's home page, including your line of custom text.

11. To exit the command prompt on `my-vm-1`, execute this command:
    `exit`
>You will return to the command prompt on my-vm-2

12. To confirm that my-vm-2 can reach the web server on my-vm-1, at the command prompt on my-vm-2, execute this command:
    `curl http://my-vm-1/`
> The response will again be the HTML source of the web server's home page, including your line of custom text.

13. In the Navigation menu, click `Compute Engine` > `VM instances`.

14. Copy the External IP address for `my-vm-1` and paste it into the address bar of a new browser tab.
    `gcloud compute instances list`
> You will see your web server's home page, including your custom text.

<!-- 
Congratulations!
Google Cloud Fundamental - Getting Started with Compute Engine ends here 
-->