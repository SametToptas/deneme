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

- Right click on Servers → Create Server
- Click on General and provide a name for your server (e.g. heroku).
- Go to the Connection tab, and enter the Heroku database credentials that you have previously obtained.
   - Host name/address: Host
   - Maintenance database: Database
   - Username: User
   - Password: Password (check the ‘Save password’ box for ease of login in future)
- Finally go the Advanced tab and state the name of the database next to DB restriction, so that only the relevant database would be visible on your screen.

**Connecting Heroku Database**

![Connecting Heroku database](https://wiki.osgeo.org/w/images/d/d3/Heroku_connection.png)
<img src="https://wiki.osgeo.org/w/images/d/d3/Heroku_connection.png" width="500" height="700">

It is now possible to create the tables. To open the query window, right click on the database and select the Query Tool.

