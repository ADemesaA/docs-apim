# Configuring Geo Location Based Statistics

!!! note
    This documentation uses MySQL as an example for configuring the GEO_LOCATION_DATA database.

!!! info
    In order to generate Geolocation based statistics, you need to pass an `x-forwarded-for` header with the relevant IP in the API request.

1.  Use the Geo Location dataset that you created [here](../creating-geo-location-data-set/).
2.  Create the `GEO_LOCATION_DATA` database by executing one of the scripts in the `Geolocation Data/dbscripts` directory. In this example, `mysql.sql` is executed.

    !!! tip
        This can be done using the [MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

    For detailed instructions to run the database script, see [MySQL Documentation - The Workbench Scripting Shell](https://dev.mysql.com/doc/workbench/en/wb-scripting-shell.html).

3.  Restore data to the BLOCKS and LOCATION tables by importing data from the BLOCKS.csv and LOCATION.csv from the `.Geolocation Data/data` directory of the extracted zip using the commands given below.

    - Importing **Geolocation Data/data/LOCATION.csv**

        `mysqlimport -u root -p --ignore-lines=2 --fields-terminated-by=, --fields-optionally-enclosed-by='"' --local GEO_LOCATION_DATA <path_to_folder_location>/GeolocationData/data/LOCATION.csv`

    - Importing **Geolocation Data/data/BLOCKS.csv**

        `mysqlimport -u root -p --ignore-lines=2 --fields-terminated-by=, --fields-optionally-enclosed-by='"' --local GEO_LOCATION_DATA <Extracted_location>/GeolocationData/data/BLOCKS.csv`

        !!! tip
            For more information, see [MySQL Documentation - Data Export and Import](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import.html).

4.  Check whether your imported dataset is working properly by executing following query in the MySQL Command Line.

        SELECT loc.country_name,loc.subdivision_1_name
        FROM BLOCKS block , LOCATION loc
        WHERE block.network_blocks = '<Network_part_of_ipv4>'
        AND <Long_value_of_publilc_IP> BETWEEN block.network
        AND block.broadcast AND block.geoname_id=loc.geoname_id;

    **Example query** :

        SELECT loc.country_name,loc.subdivision_1_name
        FROM BLOCKS block , LOCATION loc
        WHERE block.network_blocks = '221.192'
        AND 3720398641 BETWEEN block.network
        AND block.broadcast AND block.geoname_id=loc.geoname_id;

5.  Download a JDBC provider depending on the database you are using (MySQL, in this example), and copy it to the `<APIM_ANALYTICS_HOME>/repository/components/lib` directory.
6.  Configure the datasource for the Geo location.

    A default datasource for Geo location is packed by default in the `<APIM_ANALYTICS_HOME>/conf/worker/deployment.yaml` file under data sources.
    You can edit the following data source to point to his own db and import the geo location data.

    ``` java
    - name: GEO_LOCATION_DATA
      description: "The data source used for geo location database"
      jndiConfig:
        name: jdbc/GEO_LOCATION_DATA
      definition:
        type: RDBMS
        configuration:
          jdbcUrl: 'jdbc:mysql://localhost:3306/GEO_LOCATION_DATA'
          username: 'root'
          password: '123'
          driverClassName: com.mysql.jdbc.Driver
          maxPoolSize: 50
          idleTimeout: 60000
          validationTimeout: 30000
          isAutoCommit: false
    ```

!!! note
    For information on how to configure alerts, see [Configuring Alerts](../../../managing-alerts-with-real-time-analytics/configuring-alerts/).
 

