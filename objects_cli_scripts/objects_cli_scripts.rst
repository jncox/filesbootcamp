.. _objects_cli_scripts:

---------------------------------
Objects: Using From CLI & Scripts
---------------------------------

*The estimated time to complete this lab is 60 minutes.*

Overview
++++++++

Accessing Objects from the CLI
++++++++++++++++++++++++++++++

While tools like Cyberduck help to visualize how data is access within an object store, Buckets is primarily an object store service that is designed to be accessed and consumed over S3 APIs.

Amazon's S3 (Simple Storage Service) is the largest public cloud storage service, and has subsequently become the de-facto standard object storage API due to developer and ISV adoption. Buckets provides an S3 compliant interface to allow for maximum portability, as well as support for existing "cloud native" applications.

In this exercise you will leverage ``s3cmd`` to access your buckets using the CLI.

You will need the **Access Key** and **Secret Key** for the user created earlier in this lab.

Setting up s3cmd (CLI)
......................

#. From the *initials*-**Linux-ToolsVM**, run ``s3cmd --configure`` and enter the following to configure access to the Object Store:

   .. note::

   For anything not specified below, just hit enter to leave the defaults. Do **NOT** set an encryption password and do **NOT** use HTTPS protocol.

   .. code-block:: bash

     s3cmd --configure

     - **Access Key**  - *Access Key*
     - **Secret Key**  - *Secret Key*
     - **Default Region [US]**  - us-east-1
     - **S3 Endpoint [s3.amazonaws.com]**  - *OBJECT-STORE-IP*\ :80
     - **DNS-style bucket+hostname:port template for accessing a bucket [%(bucket)s.s3.amazonaws.com]**  - *OBJECT-STORE-IP*
     - **Encryption password** - Leave Blank
     - **Path to GPG program [/usr/bin/gpg]**  - Leave Blank
     - **Use HTTPS protocol [Yes]**  - No
     - **HTTP Proxy server name**  - Leave Blank
     - **Test access with supplied credentials?**  - Y (Yes)

#. The output should look similar to this and match your environment:

   .. code-block:: bash

     New settings:
       Access Key: Ke2hEtehmOZoXYCrQnzUn_2EDD9Eqf0L
       Secret Key: p6sxh_FhxEyIteslQJKfDlezKrtJro9C
       Default Region: us-east-1
       S3 Endpoint: 10.20.95.51:80
       DNS-style bucket+hostname:port template for accessing a bucket: 10.20.95.51
       Encryption password:
       Path to GPG program: /usr/bin/gpg
       Use HTTPS protocol: False
       HTTP Proxy server name:
       HTTP Proxy server port: 0

     Test access with supplied credentials? [Y/n] y
     Please wait, attempting to list all buckets...
     Success. Your access key and secret key worked fine :-)

     Now verifying that encryption works...
     Not configured. Never mind.

     Save settings? [y/N] y
     Configuration saved to '/root/.s3cfg'

#. Type **Y** and press **Return** to save the configuration.

Create A Bucket And Add Objects To It Using s3cmd (CLI)
.......................................................

#. Now let's use s3cmd to create a new bucket called *your-name*\ **-clibucket**.

#. From the same Linux command line, run the following command:

   .. code-block:: bash

     s3cmd mb s3://yourname-clibucket

#. You should see the following output:

   .. code-block:: bash

     Bucket 's3://yourname-clibucket/' created

#. List your bucket with the **ls** command:

   .. code-block:: bash

     s3cmd ls

#. You will see a list of all the buckets in the object-store.

#. To see just your buckets run the following command:

   .. code-block:: bash

     s3cmd ls | grep yourname

#. Now that we have a new bucket, let's upload some data to it.

#. If you do not already have the Sample-Pictures.zip, download it to your Linux-ToolsVM.

   .. code-block:: bash

     curl https://s3.amazonaws.com/get-ahv-images/sample-pictures.zip -o sample-pictures

#. Run the following command to upload one of the images to your bucket:

   .. code-block:: bash

     s3cmd put --acl-public --guess-mime-type image01.jpg s3://<your-bucket-name>/image01.jpg

#. You should see the following output:

   .. code-block:: bash

     s3://yourname-clibucket/image01.jpg
     WARNING: Module python-magic is not available. Guessing MIME types based on file extensions.
     upload: 'image01.jpg' -> 's3://xyz-cli-bob-bucket/image01.jpg'  [1 of 1]
     1048576 of 1048576   100% in    7s   142.74 kB/s  done
     Public URL of the object is: http://10.20.95.51:7200/xyz-cli-bob-bucket/image01.jpg

#. If desired, repeat with more images.

#. Run the **la** command to list all objects in all buckets:

   .. code-block:: bash

     s3cmd la

#. To see just objects in your buckets, run the following command:

   .. code-block:: bash

     s3cmd la | grep *initials*

Creating and Using Buckets From Scripts
+++++++++++++++++++++++++++++++++++++++

While tools like Cyberduck help to visualize how data is access within an object store, Nutanix Objects is primarily an object store service that is designed to be accessed and consumed over S3 APIs.

Amazon Web Services's S3 (Simple Storage Service) is the largest public cloud storage service, and has subsequently become the de-facto standard object storage API due to developer and ISV adoption. Objects provides an S3 compliant interface to allow for maximum portability, as well as support for existing "cloud native" applications.

In this exercise you will use **Boto 3**, the AWS SDK for Python, to manipulate your buckets using Python scripts.

You will need the **Access Key** and **Secret Key** for the user account created earlier in this lab.

Listing and Creating Buckets with Python
........................................

In this exercise, you will modify a sample script to match your environment, which will list all the buckets available to that user. You will then modify the script to create a new bucket using the existing S3 connection.

#. From the *Initials*\ **-Linux-ToolsVM**, run ``vi list-buckets.py`` and paste in the script below. You will need to modify the **endpoint_ip**, **access_key_id**, and **secret_access_key** values before saving the script.


 .. note::

   If you are not comfortable with ``vi`` or alternative command line text editors, you can modify the script in a GUI text editor then paste the completed script into ``vi``.

   In ``vi``, type ``i`` and then right-click to paste into the text file.

   Press **Ctrl + C** then type ``:wq`` and press **Return** to save the file.

 .. code-block:: python

   #!/usr/bin/python

   import boto3
   import warnings
   warnings.filterwarnings("ignore")

   endpoint_ip= "OBJECT-STORE-IP" #Replace this value
   access_key_id="ACCESS-KEY" #Replace this value
   secret_access_key="SECRET-KEY" #Replace this value
   endpoint_url= "https://"+endpoint_ip+":443"

   session = boto3.session.Session()
   s3client = session.client(service_name="s3", aws_access_key_id=access_key_id, aws_secret_access_key=secret_access_key, endpoint_url=endpoint_url, verify=False)

   # list the buckets
   response = s3client.list_buckets()

   for b in response['Buckets']:
     print (b['Name'])

#. Execute ``python list-buckets.py`` to run the script. Verify that the output lists any buckets you have created for using your first user account.

#. Using the previous script as a base, and the `Boto 3 documentation <https://boto3.amazonaws.com/v1/documentation/api/latest/guide/s3-examples.html>`_, can you modify the script to create a **new** bucket and then list all buckets?

 .. note::

   Totally stuck? Take a look at the completed script :download:`here <create-bucket.py>`

   If attempting to download the script results in an Access Denied error, log out of any AWS accounts from your browser or open the download link in **Incognito (Private Browsing)** mode.

Uploading Multiple Files to Buckets with Python
...............................................

#. From the *Initials*\ **-Linux-ToolsVM**, run the following to create 100 1KB files to be used as sample data for uploading:

 .. code-block:: bash

   mkdir sample-files
   for i in {1..100}; do dd if=/dev/urandom of=sample-files/file$i bs=1024 count=1; done

 While the sample files contain random data, these could just as easily be log files that need to be rolled over and automatically archived, surveillance video, employee records, and so on.

#. Modify your existing script or create a new script based on the example below:

 .. code-block:: python

   #!/usr/bin/python

   import boto3
   import glob
   import re
   import warnings
   warnings.filterwarnings("ignore")

   # user defined variables
   endpoint_ip= "OBJECT-STORE-IP" #Replace this value
   access_key_id="ACCESS-KEY" #Replace this value
   secret_access_key="SECRET-KEY" #Replace this value
   bucket="BUCKET-NAME-TO-UPLOAD-TO" #Replace this value
   name_of_dir="sample-files"

   # system variables
   endpoint_url= "https://"+endpoint_ip+":443"
   filepath = glob.glob("%s/*" % name_of_dir)

   # connect to object store
   session = boto3.session.Session()
   s3client = session.client(service_name="s3", aws_access_key_id=access_key_id, aws_secret_access_key=secret_access_key, endpoint_url=endpoint_url, verify=False)

   # go through all the files in the directory and upload
   for current in filepath:
       full_file_path=current
       m=re.search('sample-files/(.*)', current)
       if m:
         object_name=m.group(1)
       print("Path to File:",full_file_path)
       print("Object name:",object_name)
       response = s3client.put_object(Bucket=bucket, Body=full_file_path, Key=object_name)

 The `put_object <https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html?highlight=put_object#S3.Bucket.put_object>`_ method is used for the file upload. Optionally this method can be used to define the metadata, content type, permissions, expiration, and other key information associated with the object.

 Core S3 APIs resemble RESTful APIs for other web services, with PUT calls allowing for adding objects and associated settings/metadata, GET calls for reading objects or information about objects, and DELETE calls for removing objects.

#. Execute the script and use Cyberduck to verify the sample files are available.

 .. figure:: images/buckets_18.png

 Similar S3 SDKs are available for languages including Java, JavaScript, Ruby, Go, C++, and others, making it very simple to leverage Nutanix Buckets using your language of choice.

Takeaways
+++++++++

What are the key things you should know about **Nutanix Objects**?

- Nutanix Objects provides a simple and scalable S3-compatible object storage solution, optimized for DevOps, Long Term Retention and Backup Target use cases.

- A 2TiB Objects license is included with every AOS cluster. After that, it is licensed by used capacity (as opposed to number of nodes).

- Nutanix Objects can be deployed on an AHV cluster, with ESXi support on the roadmap.

- Nutanix Objects will be enabled and deployed from Prism Central.
