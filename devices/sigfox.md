---
description: >-
  This section explains what Sigfox infrastructure is and how to integrate their
  devices with Thinger.io platform in a very simple way.
---

# SIGFOX

![](../.gitbook/assets/sigfox_thinger.png)

## Introduction

Sigfox is a French company founded in 2009 that builds wireless networks to connect low-energy objects such as electricity meters, smartwatches, and washing machines, which need to be continuously on and emitting small amounts of data. Sigfox employs a proprietary technology that enables communication using the Industrial, Scientific and Medical ISM radio band which uses 868MHz in Europe and 902MHz in the US. It utilizes a wide-reaching signal that passes freely through solid objects, called "ultra narrowband" and requires little energy, being termed "Low-power Wide-area network \(LPWAN\)". The network is based on one-hop star topology and requires a mobile operator to carry the generated traffic. The signal can also be used to easily cover large areas and to reach underground objects.

Sigfox has partnered with a number of firms in the LPWAN industry such as Texas Instruments, or Silicon Labs. The ISM radio band support bidirectional communication. The existing standard for Sigfox communications supports up to **140 uplink messages a day**, each of which can carry a payload of **12 Bytes** \(Excluding message header and transmission information\) and up to 4 downlink messages per day, each of which can carry a payload of 8 Bytes. If you want more details about Sigfox, please visit the [Sigfox Developer Portal](http://makers.sigfox.com/about/).

This documentation will describe how to integrate SigFox devices and its data into the Thinger.io Platform. In the firsts steps, we will review how to configure Thinger.io resources, and then, in the Sigfox side we will configure the communication with the platform for pushing our sensors data.

## Integrating a Sigfox Device with Thinger.io

This process is carried out in two parts, on the one hand, the preparation of Thinger.io to receive data from Sigfox and, on the other hand, the configuration of Sigfox cloud callback that will send the information to Thinger.io. During the next sections we will explain both parts, starting with Thinger.io side steps: 

There are two ways to configure Thinger.io to work with Sigfox devices. The best option is by deploying the "Sigfox Plugin", which will manage the integration, providing advanced features such as devices auto-provisioning, Uplink/Downlink payload processing and device management, but this option is only available for subscribed developers. Freemium accounts can also integrate Sigfox devices following the "tradicional integration" section below:

### **Advanced Integration \(with Sigfox plugin\)**

{% page-ref page="../plugins/sigfox.md" %}

### Traditional integration \(without plugins\)

We need to configure some resources in our Thinger.io account, like defining a place where the Sigfox data will be stored, and grating access to the Sigfox platform to store data in our account. The required steps are defined in the following subsections.

#### Create a Data Bucket

Data buckets are data storage that will keep the information received from the Sigfox devices. So, wee need to create a data bucket to store the information from our Sigfox device. We can use one data bucket to store data from multiple devices, but it is preferred a single data bucket for each device. This way, we can create dashboards with time-series data from each bucket representing a single device or sensed entity.

Then, open the Buckets section in your cloud console, and create a new Data Bucket. We will need some details, like:

1. `Bucket Id`, a unique bucket identifier in your account that will used later in the integration with Sigfox. The identifier in this example will be `SmartEverything`. Keep this identifier in hand, as it will be used in the Sigfox side.
2. `Bucket Name`,  which can be used as a more user-friendly representation of the identifier.
3. `Bucket Description`, where we can place anything we want to remember the bucket purpose. 
4. `Enabled`, that needs to be true to allow bucket writing.
5. `Data Source`, that needs to bee set to `From Write Call`, as the Sigfox devices won't be connected directly to the platform, but will push data over the REST API Interface.

![](../.gitbook/assets/create_sigfox_bucket.png)

Once the form is filled, just click on `Add Bucket` to create you bucket.

#### Create an Access Token

At this moment we have created a data bucket to store our Sigfox Data, but, as any Thinger.io resource, they are protected by our account credentials. This way, we need to issue an access token that will allow Sigfox back-end to interact with our data bucket. In this example, we will create an access token that will grant access only to our data bucket, and moreover, access to the the write operation. This way, if the token is leaked in some way, we are not exposing other resources to the attacker.

Then, open the Access Tokens section in your cloud console, and create a new Access Token. We will need some details, like:

1. `Token Id`, a unique token identifier in your account.
2. `Token Name`,  which can be used as a more user-friendly representation of the identifier.
3. `Enabled`, that needs to be true to allow using the token. It can be disabled in any moment if you want.
4. `Token permissions`, that will be configured for accessing our Bucket resource, `SmartEverything` in this case, with the `WriteBucket` action.

![](../.gitbook/assets/create_sigfox_token.png)

Once the form is filled, just click on Add Token to create your token. In this moment, the token value will appear. In our case, the token generated is the following. Keep this token in hand, as it will be used in the Sigfox side.

```text
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJTbWFydEV2ZXJ5dGhpbmciLCJ1c3IiOiJhbHZhcm9sYiJ9.0Qb48c_ToBiIVcCOdvXU2Kn51mTnGLDcN44shVRzNls
```

## Sigfox Cloud Configuration

After making all the configuration that is required to get Thinger.io ready for receiving data, the next step is to configure the Sigfox Backend for pushing data to it, using our token identifier, and the token we have generated.

### Creating Sigfox Callback

In this step we will create a Sigfox callback that will push the information from our Sigfox device to our Thinger.io data bucket. In our example, a callback is just and endpoint that is called when the Sigfox device send data over the network, so we will configure the callback pointing to our data bucket.

To create a callback in Sigfox, just follow this steps:

1. Go to [https://backend.sigfox.com](https://backend.sigfox.com) and login into your account. We assume that you already have registered your device with the platform.
2. Click on `Device Type` tab on the top, and then click on the device type name you want fo configure. Alternatively, you can go to the `Device` tab, and click on the `Device type` column of your device.
3. Click on `Callbacks` on left menu, and then create a new one. 

In this step, you need to select that you want to create a `Custom Callback`, as we need to call an endpoint that is not directly supported by the Sigfox back-end.

![](../.gitbook/assets/create_sigfox_callback.png)

Then, we need to configure the callback to write to our data bucket. If you want to see how the configuration looks like, here you have our configuration. But you have the details of every field after the screenshot.

![](../.gitbook/assets/sigfox_callback.png)

The configuration in our example is the following:

1. `Type` is `DATA` with `UPLINK`, as we want to send our device data.
2. `Channel` is of type `URL`, as we will be calling an HTTP endpoint.
3. `Send duplicate` as disabled to avoid writing duplicate messages received by different base station.
4. `Custom payload config` will completely depend on the payload sent by your device. In our case, our device will be sending the temperature and humidity as 32 bit floats, so we have configured the payload as `temp::float:32:little-endian hum::float:32:little-endian`, where we define the `temp` and `hum` parameters as 32 bit floats in little endian. Notice that Sigfox only supports 12 bytes of payload per message, so you must optimize this space, like sending temperature and humidity as integers if it is not required decimal accuracy. For the example, this will work.
5. `Url pattern` must be configured according to your Thinger.io user id, and our bucket name.
   * The pattern should be like `https://api.thinger.io/v1/users/{user_id}/buckets/{bucket_id}/data`. 
   * You must to change the `{user_id}` and `{bucket_id}` according to your account. In our example, the final url pattern will be `https://api.thinger.io/v1/users/alvarolb/buckets/SmartEverything/data`.
   * Notice that you can also use Sigfox variables to compose your url, i.e., for storing the data from each device to a different bucket, we could create a url like: `https://api.thinger.io/v1/users/alvarolb/buckets/{device}/data`. 
6. `HTTP Method` should be set to POST.
7. In `Headers` we must include an `Authorization` header with our device token in order to authenticate the bucket write request.
   * Header name should be `Authorization`
   * Header value should be `Bearer {access_token}`, where the `{access_token}` is the token you have generated in the previous stems.
   * In our case, our final header value is as the following. Notice the space between the `Bearer` word and the token itself.

     ```text
     Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJTbWFydEV2ZXJ5dGhpbmciLCJ1c3IiOiJhbHZhcm9sYiJ9.0Qb48c_ToBiIVcCOdvXU2Kn51mTnGLDcN44shVRzNls
     ```
8. The final step is to configure the `Body` and its `Content type`. For content type, we well set `application/json` as de bucket can store arbitrary JSON data. The body then will contain all the information we want to store formatted in JSON format. In Sigfox you can define your body according to available variables, both defined by the platform itself \(like device id, link quality, or device location\), or defined by your payload config. In our case, we defined variables `temp`, and `hum`, that will be included with other Sigfox variables. For this example, our payload will look like the following:

   ```javascript
    {
       "device" : "{device}",
       "snr" : {snr},
       "rssi" : {rssi},
       "station": "{station}",
       "latitude": {lat},
       "longitude": {lng},
       "temperature" : {customData#temp},
       "humidity" : {customData#hum}
    }
   ```

   Notice that we are mixing Sigfox variables, like `{device}`, with our own custom data in the payaload, like `{customData#temp}`. This body is then processed on every message reception, and the variables will be replaced with the current values. So, the server will receive a JSON payload with the device identifier, device temperature, humidity, coarse location \(km accuracy\), and signal quality.

After these steps, we should have now a callback completely configured to push data to our data bucket.

### Programming Sigfox Devices

Now it is time to program our Sigfox Device that will be sending data to our buckets. In this case, we provide examples for the [SmartEverything](http://www.smarteverything.it/) device, and the [Arduino MKRFOX1200](https://www.arduino.cc/en/Main.ArduinoBoardMKRFox1200).

#### Arduino MKRFOX1200

Arduino MKRFOX1200 has been designed to offer a practical and cost effective solution for makers seeking to add SigFox connectivity to their projects with minimal previous experience in networking. It is based on the Microchip SAMD21 and a ATA8520 SigFox module. Can run for over six months on 2 AA 1.5V batteries with typical usage. The design includes the ability to power the board using two 1.5V AA or AAA batteries or external 5V.

![](../.gitbook/assets/arduino_mkrfox1200.jpeg)

**Initial Setup**

To program this device, we will use the [Arduino IDE](https://arduino.cc). In this case, it is necessary to install or update the board toolchain, that can be done directly from the Boards Manager, searching for `mrk`, and selecting the Arduino SAMD Boards.

![](../.gitbook/assets/mkrfox1200_install.png)

You will need to install also the `Arduino SigFox for MKRFox1200` library that is available from the Library Manager, and it is also **NECESSARY** to install the `Arduino Low Power`, and the `RTCZero` libraries.

![](../.gitbook/assets/arduino_sigfox_library.png)

After a successful installation, we can now select the Board in the Arduino IDE. Just select the Arduino MKRFOX12000. You must also select, as any other Arduino board, the port where de device is connected.

![](../.gitbook/assets/arduino_mkr1200_board_selection.png)

You can check that everything is up and running by flashing the following example that will provide information about your module, like the board ID and PAC. This information is necessary for registering the device in Sigfox.

```cpp
#include <SigFox.h>

void setup() {
  Serial.begin(9600); 

  while(!Serial) {};

  if (!SigFox.begin()) {
    Serial.println("Shield error or not present!");
    return;
  }

  String version = SigFox.SigVersion();
  String ID = SigFox.ID();
  String PAC = SigFox.PAC();

  // Display module informations
  Serial.println("MKRFox1200 Sigfox first configuration");
  Serial.println("SigFox FW version " + version);
  Serial.println("ID  = " + ID);
  Serial.println("PAC = " + PAC);

  Serial.println("");

  Serial.print("Module temperature: ");
  Serial.println(SigFox.internalTemperature());

  Serial.println("Register your board on https://backend.sigfox.com/activate with provided ID and PAC");

  delay(100);

  // Send the module to the deepest sleep
  SigFox.end();
}

void loop() {
  // put your main code here, to run repeatedly:
}
```

**Notice:** from now on, we assume that you already registered the board if your Sigfox account. If not, please, check the [First Configuration](https://www.arduino.cc/en/Tutorial/SigFoxFirstConfiguration) tutorial from Arduino.

**Pushing data to Sigfox**

Now that we have our toolchain running, it is time to code something to push data to the Sigfox Backend. Before presenting the code, **remember** that in the callback we have defined in the Sigfox, we stablished a payload config that is expecting to receive two floats representing both temperature and humidity. So, our payload must match with this definition:

```text
 temp::float:32:little-endian hum::float:32:little-endian
```

In our code, this payload can be easily represented by a `struct` that holds two floats. Obviously, you can define your own structs with different data types \(but take care of structure padding, and architecture\), but the Sigfox payload must be reconfigured to properly decode the fields you are sending.

```cpp
 struct data{
  float temp;
  float hum;
 };
```

So, the code will finally look like the following. In this case we are using the Arduino MKRFOX1200 along with a DHT sensor providing temperature and humidity required for the callback we have configured in the Sigfox back-end. If you do not have a DHT sensor, you can try using the internal temperature sensor of the board, by calling `SigFox.internalTemperature()`, and setting the humidity value to zero or any other value.

```cpp
 #include <SigFox.h>
 #include <SimpleDHT.h>
 #include <ArduinoLowPower.h>

 #define DHT11_PIN 0

 void setup() {
   Serial.begin(9600);
   pinMode(LED_BUILTIN, OUTPUT);
 }

 void blink(unsigned int count, unsigned long ms){
   for(int i=0; i<count; i++){
     digitalWrite(LED_BUILTIN, HIGH);
     delay(ms);
     digitalWrite(LED_BUILTIN, LOW);    
     delay(ms);
   }
 }

 void send_data(){
   // initialize sigfox module
   SigFox.begin();
   delay(100);

   // Enable debug led and disable automatic deep sleep
   SigFox.debug();

   // clears all pending interrupts
   SigFox.status();
   delay(1);

   // define sigfox payload data structure
   struct data{
     float temp;
     float hum;
   };

   // read temperature and humidity from DHT sensor connected at pin DHT11_PIN
   SimpleDHT11 dht11;
   byte temp, hum;
   dht11.read(DHT11_PIN, &temp, &hum, NULL);

   // NOTE! it is not quite efficient sending bytes as floats over the net, but this is just for illustrative purposes
   struct data reading;
   reading.temp = temp;
   reading.hum = hum;

   // send the structure to sigfox (8 bytes)
   Serial.println("Sending SigFox message!");

   // start a packet
   SigFox.beginPacket();

   // write our buffer
   SigFox.write((const char*)&reading, sizeof(reading));

   // send buffer to SIGFOX network
   int ret = SigFox.endPacket();  
   if (ret > 0) {
     Serial.println("No transmission");
     // 3 quick blink on error
     blink(3, 500);
   } else {
     Serial.println("Transmission ok");
     // 1 blink on success
     blink(1, 1000);
   }

   SigFox.end();
 }

 void loop() {
   send_data();
   delay(10*60*1000);
   // you can deep sleep the device if you want
   //LowPower.sleep(10*60*1000);
 }
```

**Notice**, you can uncomment the `LowPower.sleep` function call and comment the `sleep` one if you want to deep sleep your Arduino MRKFOX1200, i.e., when running from batteries. You can also avoid using the `Serial`, and the `SigFox.debug()` that is there just for debugging purposes. In sleep mode, the device requires a manual reset before flashing it again.

#### SmartEverything

SmartEverything is an IoT device specially designed for rapid prototyping, as it has full Arduino compatibility, with multiple sensors ready to use, like MEMS Pressure Sensor, Proximity and Ambient Light Sensor, iNEMO 9-axis inertial module, humidity and temperature sensors, and even NFC NTAG, or a GPS/GNSS integrated antenna. If these features are quite interesting by themselves, this board also integrates a Bluetooth Low Energy \(BLE\), and of course a Sigfox Module \(Telit LE51-868 S 868MHz module\).

![](../.gitbook/assets/sigfox_smarteverything_thinger.jpg)

With these awesome features, we can use the board for multiple purposes, like vehicle tracking with the GPS, building a micro meteorological station, registaring vibrations and impacts with the accelerometers, o any other use case. For this example, we will register just the temperature and humidity. This way, we have created a simple code that will be registering temperature and humidity every 10 minutes.

**Initial Setup**

To program this device, we will use the [Arduino IDE](https://arduino.cc). In this case, it is necessary to install the board toolchain, that can be done directly from the Boards Manager, searching for `smarteverything`, and selecting the Arrow Boards by Axel Elettronica.

![](../.gitbook/assets/smarteverything_install_arduino.png)

After a successful installation, we can now select the Board in the Arduino IDE. Just select the SmartEverything Fox \(Native USB Port\). You must also select, as any other Arduino board, the port where de device is connected.

![](../.gitbook/assets/smarteverything_sigfox_board.png)

**Pushing data to Sigfox**

Now it is time to write a simple sketch to send our sensor readings to Sigfox. The provided sample sketch will basically initialize, in the setup, the Sigfox Modem, the sensors, and the USB Serial port for some debugging. Then, in the loop, our sketch will read both the temperature and humidity and will transmit the data to Sigfox. It will also check if the transmission is OK to blink a green led on success or a red led otherwise. After that, it will sleep for 10 minutes, as we mentioned in the introduction, Sigfox will allow only 140 messages a day.

Before presenting the code, **remember** that in the callback we have defined in the Sigfox, we stablished a payload config that is expecting to receive two floats representing both temperature and humidity. So, our payload must match with this definition:

```text
temp::float:32:little-endian hum::float:32:little-endian
```

In our code, this payload can be easily represented by a `struct` that holds two floats. Obviously, you can define your own structs with different data types \(but take care of structure padding, and architecture\), but the Sigfox payload must be reconfigured to properly decode the fields you are sending.

```cpp
struct data{
 float temp;
 float hum;
};
```

So, the code will finally look like the following. **Notice** that this code has not been optimized for battery powered use cases. You can use the power saving mode to the device if you want, but this is out of scope of this example.

```cpp
#include <Wire.h>
#include <SmeSFX.h>
#include <Arduino.h>
#include <HTS221.h>

void setup() {
  // init temp & hum sensor
  Wire.begin();
  smeHumidity.begin();

  // init serial
  SerialUSB.begin(115200);

  // init sigfox module
  sfxAntenna.begin(19200, &SigFox);
  sfxAntenna.setSfxDataMode(); 
}

void send_data(){
  // define sigfox payload data structure
  struct data{
    float temp;
    float hum;
  };

  // read sensor data into the struct
  struct data reading;
  reading.temp = smeHumidity.readHumidity();
  reading.hum = smeHumidity.readTemperature();

  // send the structure to sigfox (8 bytes)
  SerialUSB.println("Sending SigFox message!");
  sfxAntenna.sfxSendData((const char*)&reading, sizeof(reading));
}

void loop() {
  // send sigfox data
  send_data();

  // wait for a response
  bool response=false;
  do{
    if (sfxAntenna.hasSfxAnswer()) {
      switch (sfxAntenna.sfxDataAcknoledge()) {
      case SFX_DATA_ACK_OK:
          ledGreenLight(HIGH);
          SerialUSB.println("Answer OK! :)");
          delay(2000);
          ledGreenLight(LOW);
          response = true;
          break;
      case SFX_DATA_ACK_KO:
          ledRedLight(HIGH);
          SerialUSB.println("Answer KO :(");
          delay(2000);
          ledRedLight(LOW);
          response = true;
          break;
      }
    }
  }while(!response);

  // sleep ten minutes for the next message
  delay(10*60*1000);
}
```

## Checking Sigfox Setup

After we have both the device code running, the Sigfox callback configured, and the data bucket created, we should check now that everything is up and running.

We can start by checking that the Sigfox platform is receiving our messages. Just go to your device in the Sigfox back-end, and open the `Messages` section that is on the left panel. We should see something like the following screenshot, where some messages have been received. You can also see the payload being sent \(in hexadecimal\), and some other information like link quality, timestamp, or callback result.

![](../.gitbook/assets/sigfox_messages.png)

It is interesting here to check that our callback response is success, as the callback icon change from green to red depending on the result. In our case, our callbacks are in green, so the request was ok. You can click on the icon to see the server response, that is a 200 OK HTTP response.

![](../.gitbook/assets/sigfox_callback_response.png)

Then we can also check that our data bucket is being populated with the data received from Sigfox. So, opening your data bucket in Thinger.io, should look like the following. Nice! We have our data now being stored. **Notice** that the columns in the bucket are just the fields we configured in the Sigfox callback body.

![](../.gitbook/assets/sigfox_bucket_data.png)

## Building a Dashboard

Now that we have our data in the bucket, we can just create a real-time dashboard from our Sigfox data. You can just create the widgets selecting your bucket as the data source, and that's all!

![](../.gitbook/assets/sigfox_dashboard.png)

