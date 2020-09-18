# Automation using Python

### Problem:

You work for an online fruits store, and you need to develop a system that will update the catalog information with data provided by your suppliers. The suppliers send the data as large images with an associated description of the products in two files (.TIF for the image and .txt for the description). The images need to be converted to smaller jpeg images and the text needs to be turned into an HTML file that shows the image and the product description. The contents of the HTML file need to be uploaded to a web service that is already running using Django. You also need to gather the name and weight of all fruits from the .txt files and use a Python request to upload it to your Django server.

You will create a Python script that will process the images and descriptions and then update your company's online website to add the new products.

Once the task is complete, the supplier should be notified with an email that indicates the total weight of fruit (in lbs) that were uploaded. The email should have a PDF attached with the name of the fruit and its total weight (in lbs).

Finally, in parallel to the automation running, we want to check the health of the system and send an email if something goes wrong.

### What to do:

Write a script that summarizes and processes sales data into different categories

Generate a PDF using Python

Automatically send a PDF by email

Write a script to check the health status of the system

### Fetching supplier data

You'll first need to get the information from the supplier that is currently stored in a Google Drive file. The supplier has sent data as large images with an associated description of the products in two files (.TIF for the image and .txt for the description).

Here, you'll find two script files `download_drive_file.sh` and the `example_upload.py` files. You can view it by using the following command.

```
ls ~/
```

To download the file from the supplier onto our linux-instance virtual machine we will first grant executable permission to the `download_drive_file.sh` script.

```
sudo chmod +x ~/download_drive_file.sh
```

Run the download_drive_file.sh shell script with the following arguments:

```
./download_drive_file.sh 1LePo57dJcgzoK4uiI_48S01Etck7w_5f supplier-data.tar.gz
```

You have now downloaded a file named `supplier-data.tar.gz` containing the supplier's data. Let's extract the contents from this file using the following command:

```
tar xf ~/supplier-data.tar.gz
```

This creates a directory named `supplier-data`, that contains subdirectories named `images` and `descriptions`.

List contents of the `supplier-data` directory using the following command:

```
ls ~/supplier-data
```

The subdirectory images contain `images` of various fruits, while the `descriptions` subdirectory has text files containing the description of each fruit. You can have a look at any of these text files using `cat` command.

```
cat ~/supplier-data/descriptions/007.txt
```

The first line contains the name of the fruit followed by the weight of the fruit and finally the description of the fruit.

### Working with supplier images

In this section, you will write a Python script named `changeImage.py` to process the supplier images. You will be using the PIL library to update all images within `~/supplier-data/images` directory to the following specifications:

Size: Change image resolution from 3000x2000 to 600x400 pixel

Format: Change image format from .TIFF to .JPEG

Create and open the file using `nano` editor.

```
nano ~/changeImage.py
```

Add a shebang line in the first line.

```
#!/usr/bin/env python3
```

This is the challenge section, where you will be writing a script that satisfies the above objectives.

Note: The raw images from `images` subdirectory contains alpha transparency layers. So, it is better to first convert RGBA 4-channel format to RGB 3-channel format before processing the images. Use convert("RGB") method for converting RGBA to RGB image.

After processing the images, save them in the same path `~/supplier-data/images`, with a JPEG extension.

Once you have completed editing the `changeImage.py` script, save the file by clicking Ctrl-o, Enter key, and Ctrl-x.

Grant executable permissions to the `changeImage.py` script.

```
sudo chmod +x ~/changeImage.py
```

Now run the changeImage.py script:

```
./changeImage.py
```

Now, let's check the specifications of the images you just updated. Open any image using the following command:

```
file ~/supplier-data/images/003.jpeg
```

### Uploading images to web server

You have modified the fruit images through `changeImage.py` script. Now, you will have to upload these modified images to the web server that is handling the fruit catalog. To do that, you'll have to use the Python `requests` module to send the file contents to the `[linux-instance-IP-Address]/upload` URL.

Copy the `external IP address` of your instance from the Connection Details Panel on the left side and enter the IP address in a new web browser tab. This opens a web page displaying the text "Fruit Catalog".

In the home directory, you'll have a script named `example_upload.py` to upload images to the running fruit catalog web server. To view the `example_upload.py` script use the `cat` command.

```
cat ~/example_upload.py
```

In this script, we are going to upload a sample image named `icon.sheet.png`.

Grant executable permission to the `example_upload.py` script.

```
sudo chmod +x ~/example_upload.py
```

Execute the example_upload.py script, which will upload the images.

```
./example_upload.py
```

Now check out that the file icon.sheet.png was uploaded to the web server by visiting the URL `[linux-instance-IP-Address]/media/images/`, followed by clicking on the file name.

In a similar way, you are going to write a script named `supplier_image_upload.py` that takes the jpeg images from the `supplier-data/images` directory that you've processed previously and uploads them to the web server fruit catalog.

Use the nano editor to create a file named `supplier_image_upload.py`:

```
nano ~/supplier_image_upload.py
```

Complete the script with the same technique as used in the file `example_upload.py`.

Once you have completed editing the `supplier_image_upload.py` script, save the file by typing Ctrl-o, Enter key, and Ctrl-x.

Grant executable permission to the `changeImage.py` script.

```
sudo chmod +x ~/supplier_image_upload.py
```

Run the changeImage.py script.

```
./supplier_image_upload.py
```

Refresh the URL opened earlier, and now you should find all the images uploaded successfully.

### Uploading the descriptions

The Django server is already set up to show the fruit catalog for your company. You can visit the main website by entering `linux-instance-IP-Address` in the URL bar or by removing `/media/images` from the existing URL opened earlier.

Check out the Django REST framework, by navigating to `linux-instance-IP-Address/fruits` in your browser.

Currently, there are no products in the fruit catalog web-server. You can create a test fruit entry by entering the following into the content field:

`
{"name": "Test Fruit", "weight": 100, "description": "This is the description of my test fruit", "image_name": "icon.sheet.png"}
`

After entering the above data into the content field click on the POST button. Now visit the main page of your website (by going to `http://[linux-instance-external-IP]`), and the new test fruit you uploaded appears.

To add fruit images and their descriptions from the supplier on the fruit catalog web-server, create a new Python script that will automatically POST the fruit images and their respective description in JSON format.

Write a Python script named `run.py` to process the text files (001.txt, 003.txt ...) from the `supplier-data/descriptions` directory. The script should turn the data into a JSON dictionary by adding all the required fields, including the image associated with the fruit (image_name), and uploading it to `http://[linux-instance-external-IP]/fruits` using the Python requests library.

Create `run.py` using the nano editor:

```
nano ~/run.py
```

Add the shebang line and import necessary libraries.

```
#! /usr/bin/env python3
```

```
import os
import requests
```

Now, you'll have to process the .txt files (named 001.txt, 002.txt, ...) in the `supplier-data/descriptions/` directory and save them in a data structure so that you can then upload them via JSON. Note that all files are written in the following format, with each piece of information on its own line:

name

weight (in lbs)

description

The data model in the Django application `fruit` has the following fields: `name`, `weight`, `description` and `image_name`. The `weight` field is defined as an integer field. So when you process the weight information of the fruit from the .txt file, you need to convert it into an integer. For example if the weight is "500 lbs", you need to drop "lbs" and convert "500" to an integer.

The `image_name` field will allow the system to find the image associated with the fruit. Don't forget to add all fields, including the `image_name`! The final JSON object should be similar to:

`
{"name": "Watermelon", "weight": 500, "description": "Watermelon is good for relieving heat, eliminating annoyance and quenching thirst. It contains a lot of water, which is good for relieving the symptoms of acute fever immediately. The sugar and salt contained in watermelon can diuretic and eliminate kidney inflammation. Watermelon also contains substances that can lower blood pressure.", "image_name": "010.jpeg"}
`

Iterate over all the fruits and use post method from Python requests library to upload all the data to the URL `http://[linux-instance-external-IP]/fruits`

Once you complete editing `run.py` script, save the file by clicking Ctrl-o, Enter key, and Ctrl-x.

Grant executable permission to the `run.py` script.

```
sudo chmod +x ~/run.py
```

Run the `run.py` script:

```
./run.py
```

Now go to the main page of your website (by going to `http://[linux-instance-IP-Address]/`) and check out how the new fruits appear.

### Generate a PDF report

Once the `images` and `descriptions` have been uploaded to the fruit store web-server, you will have to generate a PDF file to send to the supplier, indicating that the data was correctly processed. To generate PDF reports, you can use the `ReportLab` library. The content of the report should look like this:

```
Processed Update on <Today's date>

[blank line]

name: Apple

weight: 500 lbs

[blank line]

name: Avocado

weight: 200 lbs

[blank line]

...
```

Create a script reports.py to generate PDF report to supplier using the nano editor:

```
nano ~/reports.py
```

Add a shebang line in the first line.

```
#!/usr/bin/env python3
```

Using the `reportlab` Python library, define the method `generate_report` to build the PDF reports. We have already covered how to generate PDF reports in an earlier lesson; you will want to use similar concepts to create a PDF report named processed.pdf.

Once you have finished editing the script `reports.py`, save the file by typing Ctrl-o, Enter key, and Ctrl-x.

Create another script named `report_email.py` to process supplier fruit description data from `supplier-data/descriptions` directory. Use the following command to create `report_email.py`.

```
nano ~/report_email.py
```

Add a shebang line.

```
#!/usr/bin/env python3
```

Import all the necessary libraries(`os`, `datetime` and `reports`) that will be used to process the text data from the `supplier-data/descriptions` directory into the format below:

```
name: Apple

weight: 500 lbs

[blank line]

name: Avocado

weight: 200 lbs

[blank line]

...
```

Once you have completed this, call the main method which will process the data and call the `generate_report` method from the `reports` module:

```
if __name__ == "__main__":
```

You will need to pass the following arguments to the `reports.generate_report` method: the text description processed from the text files as the `paragraph` argument, the report title as the `title` argument, and the file path of the PDF to be generated as the `attachment` argument (use '`/tmp/processed.pdf`')

```
reports.generate_report(attachment, title, paragraph)
```

Once you have completed the `report_email.py` script. Save the file by typing Ctrl-o, Enter key, and Ctrl-x.

### Send report through email

Once the PDF is generated, you need to send the email using the `emails.generate_email()` and `emails.send_email()` methods.

Create `emails.py` using the nano editor using the following command:

```
nano ~/emails.py
```

Define `generate_email` and `send_email` methods by importing necessary libraries.

Once you have finished editing the `emails.py` script, save the file by typing Ctrl-o, Enter key, and Ctrl-x.

Now, open the `report_email.py` script using the nano editor:

```
nano ~/report_email.py
```

Once you define the `generate_email` and `send_email` methods, call the methods under the main method after creating the PDF report:

```
if __name__ == "__main__":
```

Use the following details to pass the parameters to `emails.generate_email()`:



From: `automation@example.com`

To: `username@example.com`

Replace `username` with the `username` given in the Connection Details Panel on the right hand side.

Subject line: Upload Completed - Online Fruit Store

E-mail Body: All fruits are uploaded to our website successfully. A detailed list is attached to this email.

Attachment: Attach the path to the file `processed.pdf`



Once you have finished editing the `report_email.py` script, save the file by typing Ctrl-o, Enter key, and Ctrl-x.

Grant executable permissions to the script `report_email.py`.

```
sudo chmod +x ~/report_email.py
```

Run the `report_email.py` script.

```
./report_email.py
```

Now, check the webmail by visiting `[linux-instance-external-IP]/webmail`. Here, you'll need a login to roundcube using the username and password mentioned in the Connection Details Panel on the left hand side, followed by clicking Login.

Now you should be able to see your inbox, with one unread email. Open the mail by double clicking on it. There should be a report in PDF format attached to the mail. View the report by opening it.

### Health check

This is the last part of the lab, where you will have to write a Python script named `health_check.py` that will run in the background monitoring some of your system statistics: CPU usage, disk space, available memory and name resolution. Moreover, this Python script should send an email if there are problems, such as:



Report an error if CPU usage is over 80%

Report an error if available disk space is lower than 20%

Report an error if available memory is less than 500MB

Report an error if the hostname "localhost" cannot be resolved to "127.0.0.1"



Create a python script named `health_check.py` using the nano editor:

```
nano ~/health_check.py
```

Add a shebang line.

```
#!/usr/bin/env python3
```

Import the necessary Python libraries (eg. `shutil`, `psutil`) to write this script.

Complete the script to check the system statistics every 60 seconds, and in event of any issues detected among the ones mentioned above, an email should be sent with the following content:



From: `automation@example.com`

To: `username@example.com`

Replace username with the username given in the Connection Details Panel on the right hand side.

Subject line:

CPU usage is over 80% -> Error - CPU usage is over 80%

Available disk space is lower than 20% -> Error - Available disk space is less than 20%

Available memory is less than 500MB -> Error - Available memory is less than 500MB

Hostname "localhost" cannot be resolved to "127.0.0.1" -> Error - localhost cannot be resolved to 127.0.0.1

E-mail Body: Please check your system and resolve the issue as soon as possible.



Note: There is no attachment file here, so you must be careful while defining the `generate_email()` method in the emails.py script or you can create a separate `generate_error_report()` method for handling non-attachment email.

Once you have completed the `health_check.py` script. Save the file by typing Ctrl-o, Enter key, and Ctrl-x.

Grant executable permissions to the script `health_check.py`.

```
sudo chmod +x ~/health_check.py
```

Run the file.

```
./health_check.py
```

Next, go to the webmail inbox and refresh it. There should only be an email something goes wrong, so hopefully you don't see a new email.

To test out your script, you can install the `stress` tool.

```
sudo apt install stress
```

Next, call the tool using a good number of CPUs to fully load our CPU resources:

```
stress --cpu 8
```

Allow the stress test to run, as it will maximize our CPU utilization. Now run `health_check.py` by opening another SSH connection to the `linux-instance`. Navigate to `Accessing the virtual machine` on the navigation pane on the right-hand side to open another connection to the instance.

Now run the script:

```
./health_check.py
```

Check your inbox for any new email.

Close the `stress --cpu` command by clicking Ctrl-c.





