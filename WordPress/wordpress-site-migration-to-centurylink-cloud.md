{{{
  "title": "WordPress Site Migration to CenturyLink Cloud",
  "date": "07-17-2015",
  "author": "Bill Burge",
  "attachments": [],
  "contentIsHTML": false
}}}
### IMPORTANT NOTE

There is currently no automated method for migrating an existing WordPress site to CenturyLink Cloud's WordPress. There are multiple methods that could be used to export and import an existing WordPress site into a CenturyLink Cloud WordPress site. This is one tested method. 

### This migration path assumes the following:

* Working knowledge of basic WordPress functionality
* Working knowledge of MySQL
* Working knowledge of PHPMyAdmin
* Working knowledge of Git.
* Working knowledge of how to [Install WordPress Plugins to CenturyLink Cloud WordPress](wordpress-plugin-installation.md "WordPress Plugin Installation on CenturyLink Cloud")
* Working knowledge of how to [Create a CenturyLink Object Storage Bucket](../Object Storage/using-object-storage-from-the-control-portal.md)

### Prerequisites:

1.	An existing WordPress site
5. A CenturyLink Object Storage bucket including the access key id and secret access key

## On the source WordPress site:

2. Open All-in-One WP Migration

  ![](../images/wp_site_migration/wp_site_migration_00.png)

3. Select Export

  ![](../images/wp_site_migration/wp_site_migration_01.png)

  * Find: source site database prefix
  * Replace: CenturyLink database prefix

  ![](../images/wp_site_migration/wp_site_migration_02.png)
	
5. Expand Advanced options, select the following options, and click EXPORT TO


  ![](../images/wp_site_migration/wp_site_migration_03.png)

6.	Select where you would like to save the file
	
  ![](../images/wp_site_migration/wp_site_migration_04.png)

7.	Click DOWNLOAD LOCALHOST

  ![](../images/wp_site_migration/wp_site_migration_05.png)


## CenturyLink Git Changes:

1. Clone your CenturyLink Git repository to your desktop



## CenturyLink WordPress Site


3. Select Import

  ![](../images/wp_site_migration/wp_site_migration_06.png)

4. Drag your exported file into the Drag & Drop to upload area

  ![](../images/wp_site_migration/wp_site_migration_07.png)

5. Click Continue

  ![](../images/wp_site_migration/wp_site_migration_08.png)

6. This will begin the import of your database

  *sometimes this hangs and you will need to try your site in another browser tab*

  ![](../images/wp_site_migration/wp_site_migration_09.png)

7. Login to WordPress using your source blogs username/password.

  ![](../images/wp_site_migration/wp_site_migration_10.png)

## Fixing Media Content

  Media content is persistently stored in CenturyLink Object Storage

  Before continuing:

1. [Configure a CenturyLink Object Storage Bucket](../Object Storage/using-object-storage-from-the-control-portal.md)

2. Install an application for mounting Amazon Web Services (AWS) S3 Buckets

  _[CyberDuck for Mac](https://cyberduck.io) is used in this example._

 * **Username:** CenturyLink Object Storage Access Key ID
 * **Password:** CenturyLink Object Storage Secret Access Key ID

  ![](../images/wp_site_migration/wp_site_migration_11.png) 

4. Right Click your bucket and select New Folder and create a new folder named wp-content


7. Expand the settings icon and select Everyone

  ![](../images/wp_site_migration/wp_site_migration_15.png) 


  ![](../images/wp_site_migration/wp_site_migration_16.png) 

9. Login to phpMyAdmin


  ![](../images/wp_site_migration/wp_site_migration_17.png) 
	

  ![](../images/wp_site_migration/wp_site_migration_18.png) 

12. Edit the following SQL statement, paste it into SQL, and click Go

  Replace:

  * **ctl\_postmeta\_table_name1**: the postmeta table name in your db
  * **ctl\_bucket\_character\_length** the character count of your CenturyLink Object * Storage Bucket Name
  * **ctl\_bucket\_name**: your CenturyLink Object Storage Bucket Name
  * **ctl\_postmeta\_table\_name2**: the postmeta table name in your db

  Code to edit:

  `INSERT INTO ctl_postmeta_table_name1 (post_id, meta_key, meta_value)`
  `SELECT post_id, 'amazonS3_info' as meta_key, CONCAT('a:2:{s:6:"bucket";s:ctl_bucket_character_length:"ctl_bucket_name";s:3:"key";s:', CHAR_LENGTH(meta_value) + 19,':"wp-content/uploads/',meta_value,'";}') FROM ctl_postmeta_table_name2 WHERE meta_key = '_wp_attached_file'`

  In this example:

  * **ctl\_postmeta\_table_name1**: postmeta
  * **ctl\_bucket\_character\_length**: 14
  * **ctl\_bucket\_name**: wordpresstest1
  * **ctl\_postmeta\_table\_name2**: postmeta

  `INSERT INTO postmeta (post_id, meta_key, meta_value)`
  `SELECT post_id, 'amazonS3_info' as meta_key, CONCAT('a:2:{s:6:"bucket";s:14:"wordpresstest1";s:3:"key";s:', CHAR_LENGTH(meta_value) + 19,':"wp-content/uploads/',meta_value,'";}') FROM postmeta WHERE meta_key = '_wp_attached_file'`
	
  ![](../images/wp_site_migration/wp_site_migration_19.png) 


  ![](../images/wp_site_migration/wp_site_migration_20.png)

13. In the postmeta table you will now see amazonS3_info rows

  ![](../images/wp_site_migration/wp_site_migration_21.png)

14. [Configure WordPress Persistent Storage](wordpress-persistent-storage-configuration.md)

15. Expand Media and you should now see your content:

  ![](../images/wp_site_migration/wp_site_migration_22.png)




