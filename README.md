# Ioniq-BSO-Remote-Monitor
### Monitor the charge of your Ioniq PHEV remotely from your smartphone

![Screenshot_IONIQ_BSO](https://user-images.githubusercontent.com/50306926/59957631-8d6b3a80-949a-11e9-94d0-610644a23630.jpg)

![yt](https://user-images.githubusercontent.com/50306926/59966678-19737580-9520-11e9-961e-71a477c88894.jpg) Visita [Mi lista de Youtube](https://www.youtube.com/playlist?list=PLTzjC-Oy2s-5wAB1-0_I-gAGDHgAlO9_A) para ver algunos videos de ejemplo

---------------------------------------------------------------

# ENGLISH VERSION
**As soon as I finish writing in Spanish, I will try to write it in English. Meanwhile, Google Translate will help you.**

- [What is it for?](#para-que-sirve)
- [How does it work?](#como-funciona)
- [Features](#funcionalidades)
- [What I need?](#qué-necesito)
- [ Previous questions about the project](#preguntas-previas-sobre-el-proyecto)
  - [How do I know the OBD will work?](#como-sé-que-el-obd-va-a-funcionar)
  - [But will I be able to use the OBD in other programs?](#Pero-voy-a-poder-usar-el-OBD-en-otros-programas)
  - [WTF!!! Can I mount Torque Pro or Hybrid Assistant on my Ioniq media?](#Puedo-montar-Torque-Pro-o-Hybrid-Assistant-en-mi-multimedia)
  - [But why not use a WiFi OBD directly?](#Pero-porqué-no-utilizar-un-OBD-WiFi-directamente)
  - [But why not use a Bluetooth OBD directly?](#Pero-porqué-no-usar-un-OBD-Bluetooth-directamente)
  - [Why Lolin NodeMCU and not another ESP family board?](#porqué-Lolin-NodeMCU-y-no-otra-placa-de-la-familia-ESP)
  - [ESP-LINK software installation](#instalación-del-software-esp-link)

- [ESP-LINK software installation](#Instalación-del-software-ESP-LINK)
- [OBD Modification](#modificación-del-OBD)
- [Configuring the MQTT service](#Configuración-del-servicio-MQTT)
- [Installing Ioniq BSO Remote Monitor on ESP8266](#Instalación-de-Ioniq-BSO-Remote-Monitor-en-ESP8266)
    - [Configuration of `Config.h`](#Configuración-de-Config.h)
    - [Wi-Fi and OBD setup](#Configuración-de-WiFi-y-OBD) 
    - [MQTT broker configuration](#Configuración-del-broker-MQTT])
    - [Telegram Settings](#Configuración-de-Telegram])
    - [DDNS Settings](#Configuración-de-DDNS)
    - [Other settings](#Otras-configuraciones)
    
  - [Libraries](#librerías)  
  - [Compilation and installation](#Compilado-e-instalación)
  - [View data on your smartphone or browser](#Visualización-de-datos-en-tu-smartphone-o-navegador)
  - [Screenshots](#Screenshots)


## What is it for?
First of all I want to say that although professionally I have dedicated myself to programming and have been related to electronics, Arduino, ESP8266 and the C++ language is not my world. I've done my best, but I'm sure you'll find bugs and better ways to do many of the steps I've programmed. Be benevolent :pray:. Thanks.

Well, Hyundai Ioniq PHEV or EV vehicles sold in Europe do not include any monitoring system for the battery or the charging process, so I decided to make a system that would allow me to leave my Ioniq charging and through my smartphone I could see the status and notify me at the end of the load. In addition, I wanted the system to be as simple as possible, although programming knowledge and some basic electronics are required. It is not comparable to the BlueLink system supplied in the USA or Korea, but it performs the basic functions for which it has been programmed.

An important point is that I am not responsible for the manipulation you do to your OBD or your Ioniq. Connecting an OBD to the car does not void the warranty, but if you make a mistake, do it wrong or produce a short circuit, it is your sole responsibility.


## How does it work?
Although it may seem complicated, the system is simpler than it seems. Using an OBD2 reader modified to work with WiFi as a client, a board from the ESP8266 family connects to OBD2 and collects the necessary data that it sends to an MQTT broker. It has been tested using the free Adafruit and CloudMQTT servers, although any other can be used. Later from our smartphone we will see the status of our vehicle in real time through a web page that is copied to our device, and that does not require installing any application or contracting external web services or databases. In addition, if the user has minimal knowledge of the web and javascript, he can create the screen to suit him. Go ahead and modify the HTML's to your liking and visit [my Youtube playlist](https://www.youtube.com/playlist?list=PLTzjC-Oy2s-5wAB1-0_I-gAGDHgAlO9_A) to see some example videos.

## Features
The application allows monitoring the following data:
* Current battery charge in % (SOC Display)
* Battery charge or discharge power
* Battery voltage
* Charge or discharge intensity
* Prediction of remaining kilometers in electric mode
* Maximum, minimum and average temperature of the battery
* Battery fan status
* Charging connector connected or disconnected
* Amount of energy charged
* Estimated upload completion time
* Report via Telegram
* DDNS client
* Possibility to link with IFTTT to turn off chargers or other ideas. Your imagination commands...


## What I need?
* First of all patience and read the instructions. If you get tired of reading, don't continue.

* 2 ESP8266 family boards. I use a Lolin MCU V3 for the OBD and a WiFi Kit 8 from [Heltec Automation](https://heltec.org/project/wifi-kit-8/) for collecting and sending data. You can really use whatever boards you want, as the software should work just fine. For tests I have also used Wemos D1 mini. The WiFi Kit 8 has a 0.91″ OLED screen and an automatic lithium battery charger. This means that when the car is charging, the plate has a battery for several days. You can put a power bank or any other system you can think of since the Ioniq's USB's do not have power during charging.

* 1 OBD2 unit **that works with the Hyunday Ioniq** PHEV and is Bluetooth or WiFi.

* The [ESP-LINK](https://github.com/jeelabs/esp-link) software from [JeeLabs](https://github.com/jeelabs) which you will find at https://github.com/jeelabs /esp-link . This software allows you to create a gateway between the OBD UART and our WiFi and you must install it on the first of the ESP boards, in my case the Lolin NodeMCU. JeeLabs has all the documentation about it. Please read it in detail in order to install it.

* A 2, 3 or 4G router in your vehicle to be able to send the data to the MQTT server, or your own smartphone with WiFi sharing activated. I have a D-Link DWR-730 router bought second-hand for just over €20, but the minimal data traffic makes any use.

## Previous questions about the project

### How do I know my OBD will work?
I'm sorry, but you will have to do previous tests to know what works. I still cannot give you a correct purchase link or a specific manufacturer, since I bought mine many years ago and it works perfectly, but after numerous tests and purchases of different OBD's we know that those with firmware version 1.5 have a higher chance of working, though not all. Old OBD's usually work, more than 3 or 4 years old. You will also need to remove the Bluetooth or WiFi board that it has to make the necessary modification, so it cannot be a single-board OBD, or one that does not allow its manipulation. If the OBD you have has a detachable Bluetooth or WiFi board, before handling it you should try Torque Pro and the PID files from [JejuSoul](https://github.com/JejuSoul/OBD-PIDs-for-HKMC-EVs ) which you will find at https://github.com/JejuSoul/OBD-PIDs-for-HKMC-EVs. I'm not going to lie to you... it's a lottery to find one that works, but Chinese products are like that. Above all, do not trust the exterior with the images that I will put since it is not a guarantee that it will work. If the PIDs from [JejuSoul](https://github.com/JejuSoul/OBD-PIDs-for-HKMC-EVs) works for you and you can read battery parameters, it is almost certainly a guarantee of success.
<br/>
<br/>

### But will I be able to use the OBD in other programs?
Yes, I continue to use it with Torque or Hybrid Assistant from my smartphone or, better yet, from the multimedia of my Ioniq. For that reason I use two ESP8266 and thus be able to use other programs. That if, I can not use multiple APP's and my application simultaneously. Attached capture of Torque pro in my Ioniq multimedia:
<br/>
<br/>

![IMG_20190322_233611_294](https://user-images.githubusercontent.com/50306926/59950477-57b45a80-9476-11e9-8835-0e7f98892dc2.jpg)
<br/>
<br/>

### WTF!!! Can I mount Torque Pro or Hybrid Assistant on my Ioniq media?
Yes, but this is not where I will explain that to you. Visit [Ioniq Spain](https://ioniqspain.wordpress.com/2019/02/20/acceso-completo-a-la-capa-android/), the blog of our colleague Smoje79, and you will be able to see how it is done.

### But why not use a WiFi OBD directly?
The WiFi OBD's on the market work as an access point (AP) and not as a station (STA). The board in charge of collecting the data would connect without problems to the OBD, but it would not be able to go out to the Internet to send the data. For that reason I modify the OBD by putting an ESP8266. If anyone has any other ideas, they are welcome.
<br/>
<br/>

### But why not use a Bluetooth OBD directly?
That was my first idea, using an ESP32 to connect to the OBD and from there send the data to the MQTT broker. Sorry, I was not able to use classic Bluetooth successfully through the Arduino IDE. Using Espressif's ESP-IDF it seems that it would be possible, but my knowledge of ESP32 is not sufficient at the moment.
<br/>
<br/>

### Why Lolin NodeMCU and not another ESP family board?
Very simple, it's the one I had on hand. A very good option is Wemos D1 Mini since it is small, just as cheap and works perfectly. Possibly replace Lolin with this plate. I'd appreciate it if you tell me which one you use.
<br/>
<br/>

-------------------------------------------------- -------------


## Installation of the ESP-LINK software
After doing different tests with my own software, [ESP-LINK](https://github.com/jeelabs/esp-link) has seemed to me the best option. It is a fluid software, easy to manage and that allows us a multitude of options. As I have said before, follow the installation instructions and check that it works, although I am attaching the configuration used by my colleague Ángel from the NodeMCU firmware programmer:

![firmwareESPLINK](https://user-images.githubusercontent.com/50306926/59951787-40776c00-947a-11e9-9f69-225841a29907.jpg)

<br/>
<br/>

Once the ESP-LINK is installed, connect to the Wi-Fi that the plate will raise and that you will see that it is without security, to later go to the IP 192.168.4.1 via the web. You will have to change the connection speed of the UART from the menu option *"μC CONSOLE"*, leaving it at 38400 bauds:

![photo5793915804792762996](https://user-images.githubusercontent.com/50306926/59952544-e2985380-947c-11e9-9bc5-ad3b606f6fc7.jpg)

<br/>
<br/>

I recommend you disable the log from *"Debug log"* leaving it OFF:

![photo5793915804792762994](https://user-images.githubusercontent.com/50306926/59952545-e2985380-947c-11e9-86ba-340452ddb4fa.jpg)

<br/>
<br/>

Finally you can put a fixed IP so that the board in charge of collecting the OBD data can find the ESP-LINK- This IP together with port 2323 is what you will have to configure later on the second ESP8266 board:

![photo5793915804792762995](https://user-images.githubusercontent.com/50306926/59952543-e2985380-947c-11e9-91af-2c1fb08e84d6.jpg)

<br/>
<br/>

# OBD Modification
First of all, thanks to Ángel for his idea. He was the first to modify his OBD and to explain to me what he had done :-)
In principle, the modifications are simple, but require skill in small size welds. My original OBD was like this:

![IMG_20190126_114531](https://user-images.githubusercontent.com/50306926/59947687-33548000-946e-11e9-9c94-68ce4eea7137.jpg)

<br/>
<br/>

You have to desolder the Bluetooth board to be able to connect the ESP8266 pins. In my model the connection is as follows, but depending on how your OBD is, you will have to investigate which pins to use. I can't explain it to you if it's different.

![pineout](https://user-images.githubusercontent.com/50306926/59955764-85a49980-948c-11e9-807d-163d0b577c75.jpg)
<br/>
<br/>


Yes, I have tried other smaller boards like the Wemos D1 Mini that works perfectly, or the ESP-01 that fit perfectly inside the OBD. In order not to overload the 3.3v voltage regulator that the OBD itself has, I installed a regulator that was powered from 5 volts, as I do with the Lolin NodeMCU, but the temperature of the voltage regulator did not convince me. In the following image you will see how the assembly with ESP-01 looked:
![ESP01](https://user-images.githubusercontent.com/50306926/59949205-b4ae1180-9472-11e9-8be2-156f9dbae6aa.jpg)

<br/>
<br/>

**IMPORTANTE:** Antes de comprobar que funciona revisa el cabelado 2 veces por lo menos, especialmente la alimentación de 5 voltios a y GND para evitar cortocircuitos que puedan dañar a los circuitos del OBD, del ESP8266 o del propio coche. Mi recomendación es que hagas la prueba con una fuente de laboratorio y conectes 12 voltios al OBD mediante conectores. Los pines que tienes que conectar son el 16 con 12V+ y el 4 y 5 con el negativo (GND). Si todo funciona correctamente el OBD y el ESP8266 arrancará y podrás acceder a él:

![Pineout_OBD](https://user-images.githubusercontent.com/50306926/59956185-049ad180-948f-11e9-984f-49ede28667f0.jpg)
<br/>
<br/>


If everything is correct, you can try to connect to the OBD from a browser and access the *"μC CONSOLE"* menu. From there type the command *ATZ* and you should get the response of the OBD firmware version, in my case *ELM 327 v1.5*. If so, everything works correctly and the hardest part of the assembly is already done.

![atz](https://user-images.githubusercontent.com/50306926/59956657-cbb02c00-9491-11e9-886a-4882cf2a8678.jpg)
<br/>
<br/>


# Installation of Ioniq BSO Remote Monitor on ESP8266

## Configuration of `Config.h`
Now we can move on to installing the Ioniq BSO Remote Monitor software on our second ESP8266 board. As I said before, you can use whichever you want, but you will need to configure the `Config.h` before compiling the code. In these first 4 lines you can choose whether or not to use the Telegram bot, DDNS client (Dynamic DNS), enable the test mode for tests without the OBD and if you use an ESP WIFI Kit 8 from Heltec or any other ESP. If you don't have that plate, leave it commented since you can use any of them.

```c#
#define ENABLE_TELEGRAM;          //Disconnect if you do not want notifications by Telegram bot
//#define ENABLE_DDNS;              //Disconnect if not use dynamic DNS
//#define ENABLE_TEST_MODE        //If enabled, for send data tests
//#define ENABLE_HELTEC_WIFI_Kit_8  //If defined, enable for compile HELTEC_WIFI_Kit_8
```
<br/>
<br/>


### WiFi and OBD setup
`Config.h` carries the ability to use two different WiFi and OBD configurations. The first is the one used if you enable `TEST_MODE`. This mode allows you to work without an OBD, sending manually generated MQTT frames that allow you to test the application and its operation on your smartphone or in a browser on your computer. You can modify, add or remove test strings from the `testString` function at the end of the code. You'll see that the `TEST_MODE` includes an OBD IP, but it doesn't need to actually exist. You must disable `TEST_MODE` in order for it to work normally. Note that the port is 2323, which is together with 23, the ones that ESP-LINK uses.

```c#
// ***************************************************************************
// IMPORTANT: Enable wifi and OBD fot test or work in Ioniq
// ***************************************************************************
#ifdef ENABLE_TEST_MODE  ///Enable wifi for test in your LAN
    int testLine = 1; 
    const char* ssid = "YourSSIDForTest";
    const char* password = "YourPasswordForTest";
    int port= 2323; // Port OBD device
    IPAddress serverOBD(192,168,1,200);  //IP address OBD device
    
#else  //Enable wifi and IP for OBD in your Ioniq
    int testLine = 0; 
    const char* ssid = "YourSSIDIoniq";
    const char* password = "PasswordWifiIoniq";
    int port= 23; // Port OBD device
    IPAddress serverOBD(192,168,0,2);  //IP address OBD device
#endif
```
<br/>
<br/>
<br/>

### MQTT broker configuration
Don't you know MQTT? Well again Google can help you, but you can visit [What is MQTT? Its importance as an IOT protocol](https://www.luisllamas.es/que-es-mqtt-su-importancia-como-protocolo-iot/) of the fireproof [Lluis Llamas](https://www.luisllamas.es /) which has this and other very interesting articles.

If you are more or less up to date with MQTT and if you are registered with any service, you can visit [Cloudmqtt.com](https://cloudmqtt.com) and register for free. Enable a `Cute Cat` plan with which you have enough for this service. When you are registered, enable a user from *Users and ACL* and later a *node* or as they call it *ACL*. You can create the user and node as you like, but if you don't want to change the settings I've put in, use the `ioniq` user and the `bso` node, and enable read and write. It should be like this:
<br/>

![cloud](https://user-images.githubusercontent.com/50306926/59964449-3ea4bb80-9501-11e9-95bd-a284a6d38ed1.jpg)

The configuration in `Config.h` carries two examples of MQTT servers, with the one in the Adafruit configuration being commented out. As I said, I use [CloudMQTT](https://www.cloudmqtt.com/). All the data is sent in an array to a single topic, so with the *Cute Cat* plan I can work without problem. In addition [CloudMQTT](https://www.cloudmqtt.com/) provides *persistence*, which allows the last data sent to be displayed even though the board is turned off. Adafruit doesn't allow that feature without taking intermediate actions.

Of course you can use the MQTT server that you like the most. I'll be waiting your comments about it.
```c#
/*
// ***************************************************************************
// Very interesting to be used with IFTTT, but it has the inconveneinte that
// does not support persistence in MQTT
// ***************************************************************************
const char* mqtt_server = "io.adafruit.com";
const int mqttPort = 1883;
const char* mqttUser = "YourUsername";                           // Your Username
const char* mqttPassword = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";   // Your Active Key Key
const char* nodemqtt= "Username/f/ioniq";  //                    // Your feed
*/


// ***************************************************************************
// Config server mqttt cloudmqtt.com. Support persistence data.
// ***************************************************************************
const char* mqtt_server = "m24.cloudmqtt.com";   // Your server in CloudMQTT
const int mqttPort = 14357;                      // Your port NOT SSL
const char* mqttUser = "UserCloudMQTT";          // Your user in CloudMQTT
const char* mqttPassword = "PasswordCloudMQTT";  // Your password in CloudMQTT
const char* nodemqtt= "ioniq/bso";               // Your topiq in CloudMQTT
```
<br/>
<br/>


### Telegram Settings
The next block is the configuration of the Telegram bot, which allows receiving the upload reports when it is finished:

![telegram](https://user-images.githubusercontent.com/50306926/59963526-59bcfe80-94f4-11e9-8425-da87d4fcc1f0.jpg)

The configuration is very simple, but you will say... How the hell do I create and configure the Telegram bot? Well, first of all, Google helps you again and there you will find answers. Anyway, here are some links that may be of interest:
- [How do I create a bot?](https://core.telegram.org/bots#3-how-do-i-create-a-bot)
- [How to create an Telegram Bot Token & Get Chat ID?](https://www.youtube.com/watch?v=2jdsvSKVXNs)

In the `Config.h` section you only have to put the language of the message, token and the corresponding chatId.
```c#
String telegramLang= "ESP"  // ESP= Spanish |  ENG = English
String BOTtoken = "botxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";  //token format is botXXXXXXXXXXXXXXXXXXXXXXXXXXX
String Chat_id = "123456789"; // Chat_id
IPAddress telegramServer(149, 154, 167, 200); // IP de api.telegram.org
```
<br/>
<br/>

###DDNS Settings
If you want, you can configure the DDNS service. What do you need it for? Well, for example, if you want to access your OBD from abroad or you need to know the IP of your vehicle. I have left it prepared since I am interested in the near future for modifications that I have in mind. I use the [NOIP.com](https://www.noip.com/) service, but you will see that you can use others.

```c#
// ***************************************************************************
// Config DDNS
// ***************************************************************************
String ddnsService = "noip"; // Enter your DDNS Service Name - "duckdns" / "noip" / "dyndns" / "dynu" / "enom".
String ddnsDomain ="YourName.ddns.net"; 
String ddnsUsername ="UserDDNS";
String ddnsPassword ="PasswordDDNS";
int ddnsUpdate = 10000; // Check for New Ip Every 10 Seconds.
```

<br/>

### Other settings
In this section is the name of the vehicle that is sent in the notification via Telegram, the size of the battery of our Ioniq and the time zone settings. I have yet to automatically calculate if it is summer or winter.
```c#
String vehicleID = "My IONIQ";   // VehicleID is sended in telegram message
float kWhBattery = 8.9;          // For Ioniq PHEV
//float kWhBattery = 38,3;       // For Ioniq EV
int timeZone = 1;                // Your time zone
bool daylightSaving = true;      // False for winter time, true for summer time.
```

## Libraries
In the `NTPClient.h` and `EasyDDNS.h` libraries I add the download URLs to facilitate the installation.
```c#
#include <time.h>              //
#include <NTPClient.h>         // https://github.com/arduino-libraries/NTPClient
#include <WiFiUdp.h>           //
#include <WiFiClientSecure.h>  //
#include <EasyDDNS.h>          // https://github.com/ayushsharma82/EasyDDNS
#include <ESP8266WiFi.h>  
#include <PubSubClient.h>      // IMPORTANT: Modify #define MQTT_MAX_PACKET_SIZE 256 in PubSubClient.h file from library directory
```
<br/>
<br/>

**IMPORTANT:**
It is necessary to highlight an important point about the `PubSubClient.h` library, which is in charge of sending the MQTT packets to the broker. As it is sent in a single *topic* and the size exceeds the maximum 127 bits it allows, the size of `MQTT_MAX_PACKET_SIZE` must be changed to 256. You will have to look for the file `PubSubClient.h` which should be in *Arduino/libraries/PubSubClient /src* and edit it:

```c#
// MQTT_MAX_PACKET_SIZE : Maximum packet size
#ifndef MQTT_MAX_PACKET_SIZE
#define MQTT_MAX_PACKET_SIZE 256 //<---- modificar aquí 
#endif
```
<br/>
<br/>

# Compilation and installation
If you have followed the given steps, you can compile and install. I recommend that you enable `TEST_MODE` so you can see how it works through the serial console so you can see the data in your MQTT broker in real time.
![console](https://user-images.githubusercontent.com/50306926/59964207-02bc2700-94fe-11e9-987e-66417af0bc00.jpg)

<br/>
<br/>

# Visualization of data on your smartphone or browser
Now it's time to open the distribution's HTML folder. You must open the `index.html` file with a text editor and locate the following lines, where you must place the connection data to the MQTT broker server:

```javascript
/// CONFIGURATION MQTT BROKER
topic = 'ioniq/bso';  // Configure your topic
usuario = 'YourUser';
contrasena = 'YourPassword';
var client = new Paho.MQTT.Client("m24.cloudmqtt.com", 12345, clientId);  // Edit port number
```
<br/>
<br/>

Now open `index.html` with a browser and if you have the board with the Ioniq BSO Remote Monitor running and in `TEST_MODE`, you should see the data update. If so, copy the `HTML` folder to your smartphone using some of the existing programs in the APP Store and leave that folder somewhere accessible, such as the *Downloads* folder. Now you can open the `index.html` from your mobile browser and see that everything works.
<br/>

We're done.... Comment out the corresponding line in `Config.h` to disable `TEST_MODE`, compile and send the program back to your ESP board. Remember to put the WiFi data of your vehicle so that it works, either sharing from your smartphone or through a router. Plug the modified OBD into the Ioniq, feed the ESP board the program todo and you should see your vehicle data.

Works? I hope so.
<br/>
<br/>
<br/>
<br/>


# Screenshots
## **SIN CARGAR**

![Screenshot_IONIQ_BSO_not_charge](https://user-images.githubusercontent.com/50306926/59957781-2d759380-949c-11e9-91c6-4f71c3bdcddf.jpg)
<br/>
<br/>
<br/>

## **DURANTE LA CARGA**

![Screenshot_IONIQ_BSO_charging](https://user-images.githubusercontent.com/50306926/59957752-c9eb6600-949b-11e9-9f2a-82c575926ec2.jpg)
<br/>
<br/>
<br/>

## **EJEMPLO DE PERSONALIZACIÓN**

![Screenshot_IONIQ_BSO_example](https://user-images.githubusercontent.com/50306926/59941543-b91bff80-945d-11e9-8704-061b4d2b4040.png)
<br/>
<br/>
<br/>

## **OTHER CUSTOMIZATION**

![Screenshot_IONIQ_BSO_example1](https://user-images.githubusercontent.com/50306926/59941542-b91bff80-945d-11e9-92df-750d3fc649ae.png)
<br/>
<br/>
<br/>

## Disclaimer
*Ioniq BSO Remote Monitor ("The Software") is provided as is. Use the software at your own risk. The authors make no warranty of performance or fitness for a particular purpose, or any other express or implied warranties. No oral or written communication from or authorization of the authors will create a guarantee. Under no circumstances shall the authors be liable for direct, indirect, special, incidental, or consequential damages resulting from the use, misuse, or inability to use the software, even if the author has been advised of the possibility of such damage. These exclusions and limitations may not apply in all jurisdictions. You may have additional rights and some of these limitations may not apply to you. This software is intended for scientific use only.*

<br/>
<br/>
----------------------------------------------------------
<br/>
<br/>
# ENGLISH (Soon)

## Formal disclaimer
*Ioniq BSO Remote Monitor (“The Software”) is provided as is. Use the software at your own risk. The authors make no warranties as to performance or fitness for a particular purpose, or any other warranties whether expressed or implied. no oral or written communication from or information provided by the authors shall create a warranty. Under no circumstances shall the authors be liable for direct, indirect, special, incidental, or consequential damages resulting from the use, misuse, or inability to use the software, even if the author has been advised of the possibility of such damages. These exclusions and limitations may not apply in all jurisdictions. You may have additional rights and some of these limitations may not apply to you. This software is only intended for scientific usage.*
