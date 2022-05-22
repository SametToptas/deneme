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

# Web Interface

This section describes how to visualise the data stored in the database on the web. First, the section provides a brief introduction on the required technologies, including NodeJS, Visual Studio Code and GitHub. Second, the code is presented that can be used to visualise the collected field data.

## 13.Technologies

### NodeJS

NodeJS is an open-source, cross-platform, JavaScript runtime environment that executes JavaScript code outside of a browser. It can be downloaded from [here](https://nodejs.org/en/).

### Visual Studio Code

Visual Studio Code is a source code editor that can be used with a variety of programming languages. It is developed by Microsoft for Windows, Linux and macOS. It can be downloaded from [this link](https://code.visualstudio.com/). The version used for this tutorial is 1.40.1.

**Git** Git is a software development version control system. In order to effectively upload our project to GitHub, Git must be downloaded from this link.

## 14.Create Desktop App

Create an empty folder on the computer.

Open Visual Studio Code. Go to **File → Open folder**.

Then go to **Terminal → New Terminal**.

It opens a terminal screen below the screen. Write **npm init** on the terminal screen end press enter. It asks some questions to create **package.json**, which holds various metadata relevant to the project.

Press enter for all of the questions. The metadata file **package.json** is created at the end of this process. Once all the default values are accepted, the **package.json** looks like:

**The metadata file: package.json**

![The metadata file: package.json](https://wiki.osgeo.org/w/images/a/a7/Web_package_json.jpg)

Some JavaScript packages need to be installed. These packages will be installed via npm (Node Package Manager). Packages to install are listed as follows:

- **Express Module**

Express is a minimal and flexible Node.js web application framework that provides a robust set of features to develop web and mobile applications.

In terminal write the following and press enter:

```javascript
npm install express --save
```

- **File System Module**

This allows to work with the file system on your computer.

In terminal write the following and press enter:

```javascript
 npm install fs --save
```

- **Bluebird Module**

Bluebird is a fully featured library with focus on innovative features and performance.

In terminal write the following and press enter:

```javascript
 npm install bluebird --save
```

- **Pg-Promise Module**

Pg-promise is a PostgreSQL interface for NodeJS.

In terminal write the following and press enter:

```javascript
 npm install pg-promise --save
```

The package.json will be updated once these packages are installed, and will look this. Notice the **dependencies**:

**Updated package.json**

![Updated package.json](https://wiki.osgeo.org/w/images/a/a7/Web_package_json.jpg)

Project settings are ready.

Now, we need to establish the database connection. Click the **New File** button, and add **appConfig.js** file.

**Adding the appConfig.js file**

![Adding the appConfig.js file](https://wiki.osgeo.org/w/images/d/d5/Web_appconfig.jpg)

The appConfig.js would include the database connection settings (for now it is the localhost) and the entire content of the file should be:

```javascript
connectionString = "postgres://tjlaxzkwaztdgd:ccbc4712d91f24c1c92149a8734e9fe765f6705b8721d582f63c9962a8da8801@ec2-52-215-225-178.eu-west-1.compute.amazonaws.com:5432/de51cfpita1udc";

module.exports = {
    connectionString: connectionString
```

The only statement that requires to be changed regarding the credentials of the Heroku database is the **connectionString**.

![web connection](https://wiki.osgeo.org/w/images/0/0c/Web_connection_string.jpg)

This statement should be updated based on the credentials of the Heroku database, which can be found through **Settings → View Credentials**.

**Obtaining Credentials of the Heroku database**

<img src="" width="500" height="700">

After that, data stored in the database must be retrieved. For this, we need to create a new file **database.js**:

**Create new file: database.js**

![database](https://wiki.osgeo.org/w/images/5/5c/Web_database_js.jpg)

The connection is applied from appConfig.js and the query is applied in this script. The important thing is the query at this point. Check that your table name and queries correspond to those in your database. The content of the **database.js** should look like:

```javascript
const promise = require('bluebird');
const { append } = require('express/lib/response');
const CONFIG = require('./appConfig');
const pgp = require('pg-promise')(options);
const config ={
  connectionString: CONFIG.connectionString,
  max:30,
  ssl:{rejectUnauthorized: false}
};
let DATABASE_PGB = pgp(config);

module.exports = {
       getAllLocations: getAllLocations
};

var options = {
    promiseLib: promise
};

function getAllLocations(cb) {
      DATABASE_PGB.any('SELECT ST_X(loc) as longitude, ST_Y(loc) as latitude , m_type as market_type , m_name as market_name,"Photo" as pht from markets')
      .then(function (data) {
         cb(null, data);})
       .catch(function (err) {
          cb(err)});
}
```

Finally, the web site can be created. Press **New File** and create the **index.html** file.

**Create new file: index.html**

![indexhtml](https://wiki.osgeo.org/w/images/f/f6/Web_index_html.jpg)

The background map is obtained from **OpenLayers**. The query response returns a GeoJSON file. Therefore, **Jquery** library will be used to receive the query response. Also, the security policy is added to run the web site page as HTTPS protocol. Copy the following code as an HTML file:

```javascript
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="description" content="">
        <meta name="viewport" content="width=device-width, initial-scale=1">    
        <link href="https://fonts.googleapis.com/css?family=Montserrat:500&display=swap" rel="stylesheet">
</head>
<style>
.map{
  position: fixed;
  top: 150px;
  right: 150px;
  width: 88%;
  height: 65%;
  box-shadow: 0 5px 10px rgba(0, 0, 0);
  padding: 15px;
  border-radius: 20px;
  border:10px solid #ccc;
  bottom:10px;
}
body{
  font-family:"Montserrat", sans-serif ;
  justify-content: flex-end;
  align-items: flex-end;
  background: #ccc;
}
*{
  margin:0;
  padding:0;
  box-sizing: border-box;
  font-family:"Montserrat", sans-serif  ;
}
li, a, button{
    font-family: "Montserrat", sans-serif;
    font-weight: 500;
    font-size:25px;
    color: #fff;
    text-decoration: none;

}


header {
  position: relative;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30px 10%;
  background-color: #3586ff;
}
footer {
  position: absolute;
  background-color: #3586ff;
  width:100%;
  color:white;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  font-weight: bolder;
  opacity: 0.75;

}
footer {
  height: 16vh;
  bottom:0;
}

footer .social_icon,
footer .menu_1 {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 10px 0;
  flex-wrap: wrap;

}

footer .social_icon li,
footer .menu_1 li
{
 
  list-style: none;
}

footer .social_icon li a,
footer .menu_1 li a
{
  font-size:2em;
  color: rgb(0, 0, 0);
  margin: 0 10px;
  display: inline-block;
  transition: 0.5s;
}


footer .social_icon li a:hover {
  transform: translateY(-10px);

}

footer .menu_1 li a
{
  font-size:1.2em;
  color: rgb(0, 0, 0);
  margin: 0 10px;
  display: inline-block;
  transition: 0.5s;
  opacity: 0.75;
  font-weight: bolder;
}

footer .menu_1 li a:hover
{
  opacity: 1;
}

footer p
{
  color: rgb(0, 0, 0);
  text-align: center;
  margin-top: 15px;
  margin-bottom: 10px;
  font-size: 1.1em;
}

footer .wave{
  position:absolute;
  top: -100px;
  left: 0;
  width: 100%;
  height: 100px;
  background: url(https://github.com/Berke0609/Osgeo-cdn/blob/main/wave.png?raw=true);
  background-size: 1000px 100px;
}

footer .wave#wave1{
  z-index: 1000;
  opacity: 1;
  bottom: 0;
  animation: animateWave 4s linear infinite;

}

footer .wave#wave2{
  z-index: 999;
  opacity: 0.5;
  bottom: 10px;
  animation: animateWave_02 4s linear infinite;

}



footer .wave#wave3{
  z-index: 1000;
  opacity: 0.2;
  bottom: 15px;
  animation: animateWave 3s linear infinite;

}

footer .wave#wave4{
  z-index: 999;
  opacity: 0.7;
  bottom: 20px;
  animation: animateWave_02 3s linear infinite;

}

@keyframes animateWave
{
  0%
  {
    background-position-x: 1000px;
  }
  100%
  {
    background-position-x:0px ;
  }
}

@keyframes animateWave_02
{
  0%
  {
    background-position-x: 0px;
  }
  100%
  {
    background-position-x:1000px ;
  }
}
.logo {
  display:inline-block;
  margin-left:30px;
  margin-top:15px;

}
.logo {
    text-decoration: none;
    font-size: 50px;
    font-family: "Montserrat", sans-serif;
    color:rgb(0, 0, 0);
    font-weight: bolder;
    opacity: 0.75;
}

.nav__links a,
.cta,
.overlay__content a {
  font-family: "Montserrat", sans-serif;
  font-weight: 500;
  color: #202020;
  text-decoration: none;
  font-weight: bolder;
  opacity: 0.75;
}

.nav__links {
  list-style: none;
  display: flex;
}

.nav__links li {
  padding: 0px 35px;
}

.nav__links li a {
  transition: color 0.3s ease 0s;
}

.nav__links li a:hover {
  color: rgb(0, 0, 0);
}

.cta {
  padding: 9px 25px;
  background-color: #ccc;
  border: none;
  border-radius: 50px;
  cursor: pointer;
  transition: background-color 0.3s ease 0s;
}

.cta:hover {
  background-color: rgba(0, 136, 169, 0.8);
}

/* Mobile Nav */

.menu {
  display: none;
}

.overlay {
  height: 100%;
  width: 0;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  background-color: #2b46ce;
  overflow-x: hidden;
  transition: width 0.5s ease 0s;
}

.overlay--active {
  width: 100%;
}

.overlay__content {
  display: flex;
  height: 100%;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.overlay a {
  padding: 15px;
  font-size: 50px;
  display: block;
  transition: color 0.3s ease 0s;
}

.overlay a:hover,
.overlay a:focus {
  color: #0d0d0e;
}
.overlay .close {
  position: absolute;
  top: 20px;
  right: 45px;
  font-size: 60px;
  color: #edf0f1;
}

@media screen and (max-height: 450px) {
  .overlay a {
    font-size: 30px;
  }
  .overlay .close {
    font-size: 40px;
    top: 15px;
    right: 35px;
  }
}

@media only screen and (max-width: 800px) {
  .nav__links,
  .cta {
    display: none;
  }
  .menu {
    display: initial;
  }
}

.box {
  position: relative;
  padding: 45px 20px;
  background-color: #3586ff;
  font-size: 20px;
  font-family: "Montserrat", sans-serif;
  box-shadow: 0 1px 4px rgba(0, 0, 0);
  padding: 15px;
  border-radius: 20px;
  border:10px solid #ccc;
  bottom:10px;
  
  
  
}
.box .box-title {
  font-size: 25px;
  font-family: "Montserrat", sans-serif;
  color:#202020;
  opacity: 0.75;
  
}

.box .text {
  font-size: 20px;
  font-family: "Montserrat", sans-serif;
  color:#202020;
  opacity: 0.75;
}



</style>
<body>
    <header>
        <img class="logo" src="https://github.com/Berke0609/Osgeo-cdn/blob/main/MARKETS%20INFORMATION_free-file%20(2).png?raw=true" alt="logo"
            <nav>
                <ul class="nav__links">
                    <li><a href="#">Supermarkets</a></li>
                    <li><a href="#">Buffets</a></li>
                    <li><a href="#">About</a></li>
                </ul>
            </nav>
            <a class="cta" href="#">Contact</a>
            <p class="menu cta">Menu</p>
    </header>
    <footer>
      <div class="waves">
        <div class="wave" id="wave1"></div>
        <div class="wave" id="wave2"></div>
        <div class="wave" id="wave3"></div>
        <div class="wave" id="wave4"></div>
      </div>
      <ul class="social_icon">
        <li><a href="#"><ion-icon name="logo-linkedin"></ion-icon></a></li>
        <li><a href="#"><ion-icon name="logo-twitter"></ion-icon></a></li>
        <li><a href="#"><ion-icon name="logo-facebook"></ion-icon></a></li>
      </ul>
      <ul class="menu_1">
        <li><a href="#">Home</a></li>
        <li><a href="#">About</a></li>
        <li><a href="#">Services</a></li>
        <li><a href="#">Team</a></li>
      </ul>
      <p>&copy; Designed By Team1</p>
    </footer>
    <div id="mobile__menu" class="overlay">
        <a class="close">&times;</a>
        <div class="overlay__content">
            <a href="#">Supermarkets</a>
            <a href="#">Buffet</a>
            <a href="#">About</a>
        </div>
    </div>
    <div id="mapdiv" class="map"></div>
        <script src="http://www.openlayers.org/api/OpenLayers.js"></script>
        <script src="http://code.jquery.com/jquery-2.1.1.min.js"></script>
        <script type="module" src="https://unpkg.com/ionicons@5.5.2/dist/ionicons/ionicons.esm.js"></script>
        <script nomodule src="https://unpkg.com/ionicons@5.5.2/dist/ionicons/ionicons.js"></script>
        <script>

  

    $.getJSON('/api/data', function (data) {
        map = new OpenLayers.Map("mapdiv");
        map.addLayer(new OpenLayers.Layer.OSM());
        
        var veri1=data[0].longitude;
        var veri2=data[0].latitude;
                
                
        var point;
        var length = data.length

        // Data dizisini haritaya ekleme işlemi
        for (var point = 0; point < data.length; point++) {
            var lon = data[point].longitude;
            var lat = data[point].latitude;
            var type = data[point].market_type;
            var name = data[point].market_name;
            var photo = data[point].pht;
            var lonLat = new OpenLayers.LonLat(lon, lat).transform(new OpenLayers.Projection("EPSG:4326"), // transform from WGS 1984
                map.getProjectionObject() // to Spherical Mercator Projection
                    )
            var zoom = 12;

            var markers = new OpenLayers.Layer.Markers("Markers");
            
            map.addLayer(markers)
            
            
                       
            var marker = new OpenLayers.Marker(lonLat)
            marker.lat = lat;
            marker.lon = lon;
            marker.type = type;
            marker.name = name;
            marker.photo = photo;
            
            
            var popup;
            marker.events.register("click", marker, function() {
            // Reset all markers.
          

                if (popup) {
                    map.removePopup(popup);
                }       // closing the popup when click the other
                  popup = new OpenLayers.Popup("popup",
                    new OpenLayers.LonLat(this.lonlat.lon,this.lonlat.lat), 
                    new OpenLayers.Size(400),
                        `<div class="box" style= "color:#d4d0d0">
                        <h6 class="box-title" style="margin-left:15">MARKET INFORMATION</h6>
                        <br>
                        <p><b class="text" style=>Latitude: </b>${this.lat}</p>
                        <br>
                        <p><b class="text">Longitude: </b>${this.lon}</p>
                        <br>
                        <p><b class="text">Market Name: </b>${this.name}</p>
                        <br>
                        <p><b class="text">Market Type: </b>${this.type}</p>
                        <br>
                        <p><b class="text">Market Photo: </b><img src="https://github.com/Berke0609/Osgeo-cdn/blob/main/${this.photo}?raw=true"  style='width:100%;height:auto'></p>
                        </div>`,
                        true);
                        
                        
                        map.addPopup(popup);
                             
            });
            
            
            markers.addMarker(marker)
            if (type=='Supermarket') {
              marker.setUrl('https://github.com/Berke0609/Osgeo-cdn/blob/main/supermarkets.png?raw=true');
            }
            else if (type=="Buffet"){
              marker.setUrl('https://github.com/Berke0609/Osgeo-cdn/blob/main/shop%20(1).png?raw=true');
            }
            markers.setOpacity(10);
            
            
                               
           map.setCenter(lonLat, zoom)
            } 
        });


    </script>
</body>
</html>
```

Finally, the website needs to be published through NodeJS. Click the **New File** and create the **index.js** file.

**Create new file: index.js**

![indexjs](https://wiki.osgeo.org/w/images/3/3b/Web_new_index_js.jpg)

This file provides code to publish the website. It reads the HTML page and it sends a response. The content of the file should be:

```javascript
var express = require('express');
var fs = require('fs');
var DATABASE = require('./database.js');
var app = express();
app.get('/', function (req, res, next) {
res.writeHead(200, { 'Content-Type': 'text/html' });
var myReadStream = fs.createReadStream(__dirname + '/index.html',
'utf8')
myReadStream.pipe(res);
});
app.use('/api/data', function (req, res) {
DATABASE.getAllLocations(function (err, data) {
if (err) {
res.sendStatus(500);
} else {
res.send(data);
}
})
});
app.listen(process.env.PORT || 4000, function(){
console.log("Express server listening on port %d in %s mode",
this.address().port, app.settings.env);
});
```

It can be checked whether the website is working or not. Go to terminal and write **node index.js**, then press enter.

**Initiating the project**

![Initiating the project](https://wiki.osgeo.org/w/images/c/ca/Web_node_index_js.jpg)

Open a web browser and type: [site](https://team1gis.herokuapp.com/#). Once the browser opens, the data stored on the database would be retrieved and displayed on the map as shown below.

**Visualising collected data on localhost**

![Visualising collected data on localhost](https://github.com/SametToptas/osgeo_report_images/blob/main/sitee.png?raw=true)

## 15.Deploying the Project on Heroku

a
