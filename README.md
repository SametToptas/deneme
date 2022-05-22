# OSGEO PROJECT - MARKETS INFORMATION
## Project Scenario
As in the past, people need markets and buffets for the supply of food and similar products in order to survive today. The aim of the project is to provide access to the location information of the market or buffet they want to reach, together with the name, type and photo. Based on this situation, the coordinates of the markets and buffets to be used in the project were transferred to the database by both marking and photographing via QField, and then displayed in QGIS.

## Acquired Knowledge
After reviewing the implementation steps, you will be able to understand the process of developing a mobile geospatial data collection platform using technologies such as QField, QGIS, Postgres/PostGIS, and Syncthing, and build your own website on heroku, which is open source.

**Comparison of Mobile Data Collection Platforms**

![Comparison of mobile data collection platforms](https://wiki.osgeo.org/w/images/e/e3/01_overview.jpg)

### Setting Up the Environment
This section describes the required steps to set up the development environment. In order to realise the steps described in subsequent sections in a smooth manner, it is recommended that the users:

- Open a Heroku account 
- Install PostgreSQL and PostGIS on their local computer
- Connect the Heroku database from QGIS
- Set up the project in QGIS
- Transfer the project from desktop PC (QGIS) to mobile data collection platform (QField).

## Application Steps
### 1.Opening a Heroku Account

A database server should be available in the web to initiate a mobile geographic data collection project. There are several solutions that offer a free solution to this. One of the platforms supporting Postgres and PostGIS is Heroku, which is a platform as a service (PaaS) that enables developers to build, run, and operate applications entirely in the cloud. Therefore, this tutorial relied on Heroku to set up the database on the cloud.

The sign up page of [Heroku](https://signup.heroku.com/login?redirect-url=https%3A%2F%2Fid.heroku.com%2Foauth%2Fauthorize%3Fclient_id%3Dd2ef2b24-e72c-4adf-8506-28db2218547d%26response_type%3Dcode%26scope%3Dglobal%252Cplatform%26state%3DSFMyNTY.g3QAAAACZAAEZGF0YW0AAAAxaHR0cHM6Ly9kYXNoYm9hcmQuaGVyb2t1LmNvbS9hdXRoL2hlcm9rdS9jYWxsYmFja2QABnNpZ25lZG4GANL7onluAQ.eDiBIjGpk8wBx82K2Ej2tBwAitNPBGNQoMMDLoySy78) requires some personal information such as name, surname and email address.

**Sign up page of Heroku**

![Sign up page of Heroku](https://wiki.osgeo.org/w/images/0/0e/1_Heroku_Login.jpg)

Once the form is submitted, an email is sent to verify the account. These credentials is used to create a new app.

The second step is to create a Heroku app with a Postgres database. Steps are as follows:

- Log in to your Heroku account.
- Click "New" → "Create New App" (→ symbol is a left click and ⇒ is a right-click)
- Create New App
  - Provide a unique app name. Heroku acknowledges if the provided app name is unique.
  - There are two regions: "United States" (default) and "Europe". You may leave it as it is.
  - Finally, click "Create app".
- Go to https://dashboard.heroku.com/apps and choose the app you just created.
- Click on "Resources" tab.
  - Under "Add-ons" type "Heroku Postgres" and click on it. Finally, complete the Postgres deployment by clicking on the "Provision" button.
- To see the database credentials:
  - Click on the settings symbol located on the top-right corner.
  - Click "Data".
  - Select your application.
  - Go to "Settings" → "View Credentials".

### 2.Accessing the Heroku Database

A user interface can be used to manipulate a Postgres database such as [pgAdmin](https://www.pgadmin.org/) or [DBeaver](https://dbeaver.io/) .

This tutorial relied on pgAdmin 4, and one it is installed, we can connect to our Heroku database with the following steps:

- Right click on **Servers → Create Server**
- Click on **General** and provide a name for your server (e.g. heroku).
- Go to the **Connection** tab, and enter the Heroku database credentials that you have previously obtained.
   - Host name/address: Host
   - Maintenance database: Database
   - Username: User
   - Password: Password (check the ‘Save password’ box for ease of login in future)
- Finally go the **Advanced** tab and state the name of the database next to **DB restriction**, so that only the relevant database would be visible on your screen.

**Connecting Heroku Database**

![Postgre create table](https://github.com/SametToptas/osgeo_report_images/blob/main/postgre%20connection.png?raw=true)

![Postgre create table](https://github.com/SametToptas/osgeo_report_images/blob/main/postgre%20connection%202.png?raw=true)

It is now possible to create the tables. To open the query window, right click on the database and select the **Query Tool.**

The scenario for this tutorial requires three tables: i) markets and ii) buffets are tables with a spatial aspect, whereas market_type does not have a geometry and is used to streamline the data collection process. Also, multiple languages are supported in a more convenient way.

First, let's create the market_type table. The following code creates two different types of markets (supermarket and buffet).

**Creating the market_type table**

![Postgre create table](https://github.com/SametToptas/osgeo_report_images/blob/main/postgre%20code1.png?raw=true)

The other two tables possess a spatial component. Therefore, the first step is to activate the extension **postgis**, which is achieved using the create extension postgis statement. 

The SQL statements to create the **markets** tables are as follows.

**Creating the markets tables**

![Postgre create table](https://github.com/SametToptas/osgeo_report_images/blob/main/postgre%20code2.png?raw=true)

The following attributes are included within the created tables:

- markets
- m_type is the type of a market.
- id is the unique identifier of a market.
- loc is the location of a market in WGS 84.
- observation_time is the date-time of an observation.

markets table has a point geometry indicating the latitude and longitude of the observed market as well as the market type and observation time. 

## 3.Connecting the Database with QGIS

The Postgres database created in the previous subsection can be included directly into a QGIS project, thanks to the seamless linkage between QGIS and Postgres. 

Once PostGIS is right-clicked, and New Connection is clicked, the following interface opens in which you can provide database credentials of the Heroku database.

**Linking the Postgres database with QGIS**

<img src="https://wiki.osgeo.org/w/images/5/58/Postgres2qgis.jpg" width="500" height="700">

Details you have to provide:

- An arbitrary **Name**
- **Host, Port** and **Database** should exactly be the same as it appears on the Heroku page.
- **SSL mode** should be allow.
- Go to **Basic** tab under **Authentication**, and you store the **user name** and **password** in order to ease the process of real time data collection.
- Do not forget to check the box **Also list tables with no geometry** to obtain all the tables of the database.

If all the credentials of the Heroku database are entered correctly, clicking the Test Connection button pops up a window indicating the success of the connection.

Finally, once the OK button is clicked, the connection appears on the Browser panel as shown below, and the user can add all the relevant tables to the layer panel by double-clicking on the table.

**Adding the tables in Heroku to QGIS**

<img src="" width="500" height="700">

##  4.Setting Up the Value Relations 

in QGIS In order to ease the process of data collection, it is useful to explicitly state the value relationships between the feature layers and to improve the user experience by hiding some of the details. For instance, a student can only select one of the pre-defined market types instead of typing it. This can be achieved by adjusting the **Attribute Form** specified under the **Properties** of the markets layer as shown below.

**Relating the tree type in trees layer with pre-defined tree_types**

<img src="" width="500" height="700">

The market type attribute m_type has a value relation to the market_types table. In order to realise this relation. The following settings are specified:

- **Widget type:** Value Relation
- **Layer:** market_type
- **Key column:** name_en
- **Value column:** name_en

In addition, some of the attributes might better be hidden from the users such as the id attribute of the market as it is a serial number that auto increments as a market is recorded. Therefore, this attribute shall not be editable. In addition, it can be hidden from the user to reduce clutter in the GUI of the QField. This process is illustrated as follows.

- Uncheck **Editable**
- **Widget type:** Hidden

**Removing clutter from the GUI by hiding some attributes**

<img src="" width="500" height="700">

Once an attribute is hidden, it means that during data collection that field is not displayed to the user. However, a user could still update it under the attribute table. In order to prevent this as well, we have unchecked Editable.

The last attribute of the markets layer is the observation time. It might be important to record the observation time in order to facilitate further spatio-temporal analysis.

Update the default value of the observation_time attribute to be now() as shown in the figure below. In this way, whenever a market is recorded, its observation time is also recorded.

**Assigning default observation time to the corresponding attribute**

<img src="" width="500" height="700">

Actions:

- **Widget Type**: Date/Time
- **Default value**: now()

If the mobile data collection platform assumes internet connection, then including OpenStreetMap in the QGIS project may ease data collection process. Install the **OpenLayers** plugin, and include **OpenStreetMap** in your project.

**Including OpenStreetMap in the project to ease data collection**

<img src="" width="500" height="700">

## 5.Transferring the Project to QField

Once all the layers are added, the project is saved as a QGS file. This means that the file is a legible XML document. All of the details of the project are included in this document including the credentials of the Heroku database. Anyone possessing this QGS file can start collecting field data, which would have immediate effect on the Heroku database. In this way, multiple users can effectively collect field data into the same database. This file can directly be transferred to the smart phone or tablet possessing QField.

**Data collection on QField**

<img src="" width="500" height="700">

Once the QGS project file is transferred to the mobile device, it can be opened using QField as shown in (a). The layers appear just like they do in QGIS,as shown in (b). In order to start editing, the relevant layer is selected and then the pen icon is toggled in the upper-right corner of the screen. Once the horizontal lines button on the upper left corner is pressed, the user can start collecting data. The location could either be set by panning around the map and clicking on the '+' button located at the bottom-right of the screen or by clicking the locate button at the left-hand side of the screen, which uses the GNSS receiver of the phone to locate the position of the user. New features are added just like they are added in QGIS with the functionality of hiding some attributes (e.g. id in markets) or by offering a drop-down list to the user (e.g. user can select one of the pre-defined market types).

### Collecting Photos in the Field

Photos can be used in mobile data collection projects to better describe a spatial object. Storing photos in a database is not trivial, and most of the time the path to the photo is stored. For this reason, an additional platform is needed to share photos where multiple users capture photos and synchronise them.

## 6.Setting Up the Snycthing Environment

[Syncthing](https://syncthing.net/) is a continuous file synchronisation program. It synchronizes files between two or more devices. The location of the shared folder is specified on Syncthing. It requires internet to synchronise the files. The aim of using Syncthing in this project is synchronising photo folders between mobile device and computer.

Firstly, Syncthing must be installed on all devices. It can be downloaded for Windows from [here](https://github.com/canton7/SyncTrayzor/releases/tag/v1.1.24). There are two options as x64 and x86. The suitable option can be downloaded. Note that SyncTrayzor is the name of the software on the desktop.

Likewise, the app **Syncthing** must be installed on the mobile device. It can be [downloaded](https://play.google.com/store/apps/details?id=com.nutomic.syncthingandroid) to android devices through Google Play.

Having installed Syncthing on PC and mobile devices, they are required to be connected. Open **SyncTrayzor** on your desktop. Go to **Actions → Show ID** as shown below.

**Obtaining the ID of the PC where each photo will be uploaded**

![Obtaining the ID of the PC where each photo will be uploaded](https://wiki.osgeo.org/w/images/7/7e/Syncthing_showid.jpg)

The ID of a device is presented as a text and QR code as shown below.

**ID of a Snycthing device**

![ID of a Snycthing device](https://wiki.osgeo.org/w/images/b/bf/Syncthing_id.jpg)

This code must also be entered to the mobile device. Press the '+' button in the **Devices** menu as shown below.

**Synching the mobile device with PC**

![Synching the mobile device with PC](https://wiki.osgeo.org/w/images/2/2a/Syncthing_add_device.jpg)

Device options will be shown. Enter the connection ID and connection name. Also, QR code can be used to add the PC as illustrated below.

**Adding the PC to the mobile device**

![Adding the PC to the mobile device](https://wiki.osgeo.org/w/images/6/6d/Syncthing_add_PC.jpg)

Once the PC is added to the mobile, this action sends a request to the PC stating the details of the connection. Press **Add Device** as shown below.

**Accepting the request sent from the mobile device**

![Accepting the request sent from the mobile device](https://wiki.osgeo.org/w/images/7/7e/Syncthing_confirmation_add_device.jpg)

After acceptance, connections will be established between the mobile device and the PC, which can be seen in SyncTrayzor as follows. The mobile device used in this tutorial is MI 8 SE.

**Connected devices – PC view**

![Connected devices – PC view](https://wiki.osgeo.org/w/images/b/b6/Syncthing_connected_devices.jpg)

Also, Syncthing on the mobile device displays the connection information as shown here:

**Connected devices – mobile device view**

![Connected devices – mobile device view](https://wiki.osgeo.org/w/images/2/21/Syncthing_connected_devices_mobile_device_view.jpg)

Having reached this step means that the mobile device and PC are synchronised. One may add further mobile devices with the same procedure. Once the connection is established between the devices, the details regarding file synchronization must be provided.

## 7.Updating the QGIS Project

The path of the captured photos must also be stored in the database. There are two different ways to add a 'photo' attribute to the 'markets' layer. First, the attribute can be added when creating the table in the database. The data type can be chosen as text, whih corresponds to the URL of the image. Second, it can be added from the QGIS project. This tutorial relies on the latter option.

Open the attributes table. Right click the 'markets' layer and click 'Open attributes table'. Then follow these steps:

- Press the 'Toggle Editing Mode' which is shown with button '1'.
- Press the 'New Field' button which is shown with button '2'.

**Adding a new attribute from QGIS**

<img src="" width="500" height="700">

After that, enter the column name and select the data type. Select type as 'Text, unlimited length (text)', and then click OK.

**Adding a new field in QGIS**

<img src="" width="500" height="700">

Finally, click on 'Toggle Editing Mode' to end editing, and press save.

The following adjustments have to be satisfied to display photos on QGIS:

- Right click to 'markets' layer and press **properties**
- Go to **Attributes Form**
- Select the Photo column
- Select **Widget Type** as **Attachment**
- Enter default path of DCIM folder
- Select **Relative paths** and leave the default option checked **Relative to Project Path**

**Configuring the photos field – part 1**

<img src="" width="500" height="700">

Continue with the following steps:

- Under **Storage Mode** select **File paths**
- Check **Display Resource Path**
- Check **Display button to open file dialog**
- Finally, select the **Integrated Document Viewer** as **Image**

Press the apply button. The project is now ready to display photos of trees.

**Configuring the photos field – part 2**

<img src="" width="500" height="700">

Transfer the project folder containing:

- The updated QGS file (gisteam1_w_photo_2_qfield.qgs) and
- the DCIM folder that you will now share

to your mobile device.

**Transfer the project folder to the mobile device**

<img src="" width="500" height="700">

To synchronise the photo folder with the connected devices, press **Add Folder** in the SyncTrayzor as shown below.

**Adding the folder in SyncTrayzor for sharing photos with multiple users**

<img src="" width="500" height="700">

Enter the folder name and folder path. It is important to enter the file location correctly as shown below.

**Setting the file synchronisation folder**

<img src="" width="500" height="700">

Select the mobile devices you want to synchronize the folder that contains photos in the **Sharing** option and press **Save** button.

**Selecting the devices to share the directory containing photos**

<img src="" width="500" height="700">

This sends a request to the mobile device(s) selected for acknowledgment. Please note that

- This request appears **only** in the pull-down notification bar as shown below and
- It might take a while since mobile devices can easily get disconnected.

**Folder share request sent to mobile device**

![Folder share request sent to mobile device](https://wiki.osgeo.org/w/images/1/16/Drop_down.jpg)

**Accept the request**

![Accept the request](https://wiki.osgeo.org/w/images/e/ef/Photo_ackw_mobile.jpg)

**Select** the DCIM folder that you previously transferred to finalise the synchronisation setup on your mobile device as shown below. Note that it is the same folder that you transferred to the mobile device (i.e. **DCIM** folder under the **?????Değişcek?????** folder).

**Setting up the mobile device to accommodate synched photos**

<img src="" width="500" height="700">

Now the synched mobile devices are ready to take photos. The photos will be synched with the PC as well as with other synched mobile devices.

## 8.Collecting Photos on the Field

Open the updated QGIS project file (gisteam1_w_photo_2_qfield.qgs) in QField and record an observation. The following screen will pop-up:

**Taking a photo of an observation with QField**

<img src="" width="500" height="700">

Once the camera icon is clicked, camera opens an a photo can be captured. If there is a problem at this point, make sure that the **Use native camera function** is turned off under QField Settings as shown below:

**Taking a photo of an observation with QField**

![Taking a photo of an observation with QField](https://wiki.osgeo.org/w/images/e/e4/Photo_native_camera.jpeg)

The captured photos will be synched with all the connected devices. Consequently, a captured photo of a tree can also be visualised in QGIS:

**Visualising a Field Photo in QGIS**

<img src="" width="500" height="700">

## 9.Export Project

The first part is to export the project so that all the database is backed up to a Geopackage file (gpkg) that QField can update.

The required steps are as follows:

- Under **Plugins → QFieldSync**, select **Preferences** as shown in (a) below. Clicking on this button opens the window presented in (b).
  - Set the **Default Import Directory** to be the path of the project.
  - Set the **Default Export Directory** to be the path of the files that are to be exported. This directory must be copied to the mobile device.

**QFieldSnyc plugin**

<img src="" width="500" height="700">

- Select **Project Configuration**, which opens a window as shown below listing the available layers and actions that can to be taken for each of them.
  - For OpenStreetMap, select **no action**.
  - The other layers should be in the **offline editing** mode.

**QField project synchronisation details**

![QField project synchronisation details](https://wiki.osgeo.org/w/images/5/5e/Qfield_sync_details.jpg)

Select **Package for QField**, which opens the following window. Select the export directory, which can be the same directory as previously. Once the **Create** button is clicked, the plugin creates the required Geopackage file and associates it with the QGS file. In this way, data are stored in a Geopackage file.

**Creating the package for QField**

<img src="" width="500" height="700">

**Note** that once the export folder is successfully created as shown below, it **may** disrupt the project file. For example, the drop down list to select the market type, which otherwise works when the QGS file is transferred directly to QField, does not work any more.

Open the new project file in QGIS and **make sure** that all of the settings are correct.

Finally, open a **DCIM** folder, which will be used to synchronise photos that are captured offline.

**Make sure settings are correct in the exported QGIS file**

<img src="" width="500" height="700">

## 10.Capturing Photos in the Field

Continue with the following steps while you have internet connection:

- Transfer the project to the mobile devices. Multiple users can collect offline data.
- Open SyncTrayzor (PC) and share the DCIM folder with other devices.
- Syncthing will ask you to accept the request on your mobile device. Accept it and select the DCIM under the export folder. In this way, all the photos captured in QField will be stored under the DCIM folder, which would then be synchronised.

## 11.Data Collection

At this stage, we do not have internet connection any more. Nevertheless, we can collect field data with the following steps:

- Open the project available under the **export** directory in QField.
- Collect field data.
- You can also **update** existing records

Now we are ready to synchronise data we have just collected. For this, we need to have internet connection.

## 12.Synchronisation

Once you have internet connection again, do the followings to synchronise:

- Transfer the export folder in your mobile device back to your PC.
- Open GIS.
- Under QFieldSync plugin select Synchronize from QField, which opens up a window as illustrated below.
- Once the Synchronize button is clicked, all of the content of the Geopackage is read and the Heroku database is updated accordingly.

**Synchronize project**

<img src="" width="500" height="700">

- Save the QGS file.
- You can repeat the process of synchronising for other devices by repeating this last process.

