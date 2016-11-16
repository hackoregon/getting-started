Connecting QGIS and PostGIS
================
M. Edward (Ed) Borasky <znmeb@znmeb.net>
2016-11-16

What you will need
------------------

1.  QGIS (Quantum GIS) 2.18.0 or later
2.  PostGIS 2.1 or later

Making the connection
---------------------

1.  Start your PostgreSQL management tool. You can use PgAdmin3 if you have it, otherwise use `psql`. Create a database and add the `postgis`, `postgis_topology` and `pgrouting` extensions to it. If you're on Windows, you have PgAdmin3 if you installed via the EnterpriseDB stack.
2.  The QGIS installer installs two GUI tools - the full QGIS and a simpler tool called the QGIS Browser. Open the QGIS Browser; the interface is much simpler.

    ![](Screenshots/QGIS-PostGIS/Screenshot%20from%202016-11-16%2014-55-20.png)
3.  Right-click on the `PostGIS` entry. A window will open up. Press the `New` button.

    ![](Screenshots/QGIS-PostGIS/Screenshot%20from%202016-11-16%2015-00-23.png)
4.  Fill out the form as shown here. Use your `Database` and `Username` where I have `znmeb`. Make sure the save password checkbox is cleared. Press the `Test Connection` button.

    ![](Screenshots/QGIS-PostGIS/Screenshot%20from%202016-11-16%2015-09-22.png)
5.  Huzzah! Press `OK` twice.

    ![](Screenshots/QGIS-PostGIS/Screenshot%20from%202016-11-16%2015-06-38.png)
