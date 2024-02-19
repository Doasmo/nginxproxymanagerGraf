# Nginx Proxy Manager Graf

Welcome to the Nginx Proxy Manager Graf project! This tool integrates Nginx Proxy Manager logs with Grafana, allowing you to visualize your web traffic through beautiful dashboards. Utilize geographical data to analyze requests and monitor the performance of your web services in real-time.

## Prerequisites

Before you get started, there are a few things you need to have in place:

1. **InfluxDB Setup**: Create a database named `nginxproxymanagergraf` in InfluxDB to store the logs.
2. **Authentication**: Create a username and password for accessing the `nginxproxymanagergraf` database.
3. **GeoLite2-City Database**: Obtain the `GeoLite2-City.mmdb` file for IP geolocation. You can find this by searching online. Once obtained, upload it to a location accessible by the Docker container.
4. **Docker**: Ensure Docker is installed on the host where the Nginx Proxy Manager runs.

## Installation Steps

### Starting the Docker Container

Run the following Docker command on the same host where the Nginx Proxy Manager is running. Replace the placeholders with your actual data, such as the InfluxDB username, password, and the path to the `GeoLite2-City.mmdb` file.

```bash
docker run --name npmgraf -it \
-v /home/docker/nginx-proxy-manager/data/logs:/logs \
-v /path/to/your/GeoLite2-City.mmdb:/GeoLite2-City.mmdb \
-e HOME_IPS="192.168.0.*|192.168.10.*" \
-e INFLUX_USER=<Your_InfluxDB_Username> -e INFLUX_PW=<Your_InfluxDB_Password> \
-e INFLUX_DB=nginxproxymanagergraf \
-e INFLUX_HOST=<Your_InfluxDB_Host> \
-e INFLUX_PORT=8086 \
makarai/nginx-proxy-manager-graf
```

### Adding Data Source in Grafana

After starting the Docker container, add InfluxDB as a data source in Grafana:

1. Open Grafana and navigate to **Configuration > Data Sources**.
2. Click **Add data source**, then select **InfluxDB**.
3. Fill in the details for your InfluxDB instance, including the database `nginxproxymanagergraf`, user, and password you created earlier.

### Importing the Dashboard

To visualize the Nginx Proxy Manager logs:

1. In Grafana, go to **+ > Import**.
2. Upload the dashboard JSON file or enter the ID if it's hosted online.
3. Select the InfluxDB data source you added in the previous step.

## Visualizing Data

Once the dashboard is set up, you can start exploring your data. The world map visualization can be achieved with a query like:

```sql
SELECT count("IP") AS "counts" FROM "ReverseProxyConnections" WHERE $timeFilter GROUP BY "latitude", "longitude", "IP"
```

## Additional Tips

- Customize your dashboards to focus on the metrics that matter most to you.
- Regularly update the GeoLite2 database to ensure accurate geolocation data.
- Explore Grafana's alerting features to get notified of unusual traffic patterns or performance issues.

For more details on Nginx Proxy Manager, visit the official [GitHub page](https://github.com/jc21/nginx-proxy-manager).

![Nginx Proxy Manager Graf Dashboard](https://github.com/ma-karai/nginxproxymanagerGraf/blob/master/Screenshot%202021-02-14%20142221.png?raw=true)

*Screenshot of a Grafana dashboard visualizing Nginx Proxy Manager data.*
