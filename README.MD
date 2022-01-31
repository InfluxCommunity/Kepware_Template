# Kepware and InfluxDB Integration

 **Kepware Version:** KEPServerEX | 6.11.718.0

**The goal:**
This repository will contain the how to guide and configs to get you started with both platforms. Kepware has provided a Factory simulator so you can trial the integration without connecting to your machines.

  

## Prerequisite

- Install KepwareServerEX, you can download a free trial by making an account [here](https://my.kepware.com/s/login/SelfRegister)
- Sign up for a free [InfluxDB Cloud account](https://www.influxdata.com/products/influxdb-cloud/) or download [InfluxDB OSS](https://portal.influxdata.com/downloads/#:~:text=Telegraf%20open%20source%20data%20collector).

## Setup

You have two options when intergrating Kepware and InfluxDB together. Direct and via Telegraf. There are pros and cons to both architectures, pick the version that is right for your usecase.

The below instructions will get you up and running. I highly recommend you check out "[PTC Kepware and InfluxDB: Collecting and Storing Your Automation Data](https://www.influxdata.com/blog/ptc-kepware-influxdb-collecting-storing-automation-data/)" for more infromation.


### Direct
|Pros|Cons  |
|--|--|
|No bridging software required |Limited Data model transformation and templating (has to be done in InfluxDB) |
|No code templating|| 

*Note: The below instructions will teach you how to deploy the Kepware Smart Factory simulation with is preconfigured with the IoT Gateway template. For more information check out the instructions [here](https://www.influxdata.com/blog/ptc-kepware-influxdb-collecting-storing-automation-data/).*


 1. Open KEPServerEX 6 configuration.
 2. (Optional) Load [factory_simulator](https://github.com/InfluxCommunity/Kepware_Template/blob/master/Factory_Simulator.opf) project file.  
 3. Right Click **IoT Gateway** and select **new agent**.
 4. Select **REST Client**.
 5. Enter the below InfluxDB URL and modify the following paramterars (base_url, organisation and bucket):
```
    {{base_url}}/api/v2/write?bucket={{bucket}}&precision=ms&org={{org}}
```
 6. Enter the token header:
```
Authorization: Token <TOKEN>
Content-Type: text/plain
```
 7. Right click the newely created agent.  Select **properties**.
 8. Select message. Change **template** to **advance template**. Add the new [template](https://github.com/InfluxCommunity/Kepware_Template/blob/master/Direct/template.txt).  
```
|#each VALUES|kepware,factory=germany |TAGNAME|=|VALUE| |TIMESTAMP||#unless @last||LINEFEED||/unless||/each|
```
Change the **template type** to **plain text**.

9. Add server tags to the new IoT gateway and restart the gatway. **IMPORTANT: tags cannot have spaces within there names.**
  

### Telegraf
|Pros|Cons  |
|--|--|
|Data model transformation and templating  |Second application to maintain|
|Stream Kepware data to multiple enterprise endpoints (Cloud, historians and enterpise platforms)||  

1. Download and install [Telegraf](https://github.com/influxdata/telegraf).
2. Decide whether to use the [MQTT](https://github.com/InfluxCommunity/Kepware_Template/blob/master/Telegraf/telegraf-mqtt.conf) or [OPC UA](https://github.com/InfluxCommunity/Kepware_Template/blob/master/Telegraf/telegraf-opcua.conf) Telegraf config. Note that MQTT requires that you use Kepware's IoT Gateway and setup an MQTT Broker like [Mosquitto](https://mosquitto.org/).
3. Complete the Telegraf configuration parameters. See [blog guide](https://www.influxdata.com/blog/ptc-kepware-influxdb-collecting-storing-automation-data/) for more information on each.
4. Start the Telegraf service,
```
sudo systemctl start telegraf 
```

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

