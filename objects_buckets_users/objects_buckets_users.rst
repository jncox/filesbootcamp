.. _objects_buckets_users:

--------------------------------
Objects: Creating Bukets & Users
--------------------------------

*The estimated time to complete this lab is 60 minutes.*

Overview
++++++++


Create Bucket In Prism
+++++++++++++++++++++++

A bucket is a sub-repository within an object store which can have policies applied to it, such as versioning, WORM, etc. By default a newly created bucket is a private resource to the creator. The creator of the bucket by default has read/write permissions, and can grant permissions to other users.

#. Click the **Name** of the existing Object Store (**NAME?**) to manage it.

#. Click **Create Bucket**, and fill out the following fields:

   - **Name**  - *your-name*-my-bucket
   - **Enable Versioning** - Checked

   .. figure:: images/buckets_05.png

#. Click **Create**.

   .. note:: Buckets created via Prism Central are owned by the Prism Central admin.

   If versioning is enabled, new versions can be uploaded of the same object for required changes, without losing the original data.

   Lifecycle policies define how long to keep data in the system.

   Once the bucket is created, it can be configured with WORM.

   WORM (Write Once, Read Many) storage prevents the editing, overwriting, renaming, or deleting data and is crucial in heavily regulated industries (finance, healthcare, public agencies, etc.) where sensitive data is collected and stored. Examples include e-mails, account information, voice mails, and more.

   .. note::

     Note that if WORM is enabled on the bucket, this will supersede any lifecycle policy.

#. Check the box next to your *your-name*-**my-bucket** bucket, and click **Configure WORM**. Note you have the ability to define a WORM data retention period on a per bucket basis.

#. Check the box next to your *your-name*-**my-bucket** bucket, and click **Share**.

   This is where you will be able to share your bucket with other users. You can configure read access (download), write access (upload), or both, on a per user basis. Since we haven't created any users yet, just click **Cancel**.

   .. figure:: images/buckets_share.png

User Management
+++++++++++++++

In this exercise you will create generate access and secret keys to access the object store, that will be used throughout the lab.

#. From the Buckets UI, click on **Access Keys** and click **Add People**.

   .. figure:: images/buckets_add_people.png

#. Select **Add people not in Active Directory** and enter your e-mail address.

   .. figure:: images/buckets_add_people2.png

#. Click **Next**.

#. Click **Download Keys** to download a .csv file containing the **Access Key** and **Secret Key**.

   .. figure:: images/buckets_add_people3.png

#. Click **Close**.

#. Open the file with a text editor.

   .. figure:: images/buckets_csv_file.png

#. Repeat the process above for a second user, so that you have two pairs of keys.

   .. note::

     Keep the text files open so that you have the access and secret keys readily available for future labs.

Accessing & Creating Buckets With Cyberduck
+++++++++++++++++++++++++++++++++++++++++++

In this exercise you will use `Cyberduck <https://cyberduck.io/>`_ to create and use buckets in the object store using your generated access key. Cyberduck is a multi-platform GUI application that supports multiple protocols including FTP, SFTP, WebDAV, and S3.

.. note::

  Cyberduck ver 7.1.0 is already installed on the Windows Tools VM you deployed earlier. If it is not already pinned to the taskbar, you can find it in the Windows menu.


Download the Sample Images
..........................

#. Login to *Initials*\ **-Windows-ToolsVM** via RDP using the following credentials:

   - **Username** - NTNXLAB\\Administrator
   - **password** - nutanix/4u

#. `Click here <https://s3.amazonaws.com/get-ahv-images/sample-pictures.zip>`_ to download the sample images to your Windows-ToolsVM. Once the download is complete, extract the contents of the .zip file.

Use Cyberduck to Create A Bucket
................................

#. Launch **Cyberduck** (Click the Window icon > Down Arrow > Cyberduck).

   If you are prompted to update Cyberduck, click **Skip This Version**.

#. Click on **Open Connection**.

   .. figure:: images/buckets_06.png

#. Select **Amazon S3** from the dropdown list.

#. Enter the following fields for the user created earlier, and click **Connect**:

   - **Server**  - *Objects Client Used IP*
   - **Port**  - 443
   - **Access Key ID**  - *Generated When User Created*
   - **Password (Secret Key)** - *Generated When User Created*

   .. figure:: images/buckets_08.png

#. Check the box **Always Trust** and then click **Continue** on the **The certificate is not valid** dialog box.

   .. figure:: images/invalid_certificate.png

#. Click **Yes** to continue installing the self-signed certificate.

#. Once connected, right-click anywhere inside the pane and select **New Folder**.

#. Enter the following name for your bucket, and click **Create**:

   - **Bucket Name** - *your-name*-bucket

   .. note::

     Bucket names must be lower case and only contain letters, numbers, periods and hyphens.

     Additionally, all bucket names must be unique within a given Object Store. Note that if you try to create a folder with an existing bucket name (e.g. *your-name*-my-bucket), creation of the folder will not succeed.

   Creating a bucket in this fashion allows for self-service for entitled users, and is no different than a bucket created via the Prism Buckets UI.

#. Double-click into the bucket, and right click and select **Upload**.

#. Navigate to your downloads directory and find the Sample Pictures folder. Upload one or more pictures to your bucket.


Takeaways
+++++++++

What are the key things you should know about **Nutanix Objects**?

- Nutanix Objects provides a simple and scalable S3-compatible object storage solution, optimized for DevOps, Long Term Retention and Backup Target use cases.

- A 2TiB Objects license is included with every AOS cluster. After that, it is licensed by used capacity (as opposed to number of nodes).

- Nutanix Objects can be deployed on an AHV cluster, with ESXi support on the roadmap.

- Nutanix Objects will be enabled and deployed from Prism Central.
