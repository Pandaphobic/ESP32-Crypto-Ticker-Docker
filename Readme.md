# ESP32 Crypto Ticker

Simple ESP32 based crypto ticker that depends on a locally deployed express server to make calls to the Coingecko API. The server provides a JSON array with up to 4 rows of coins to be displayed the 20x4 LCD Display.

## Hardware

For this project, I went with an ESP-WROOM-32. This board can connect to Wifi and is a breeze to use for things like http requests. The display I chose is a standard 20x4 LCD with a factory installed PCF8574 ic to provide I2C interface. For the enclosure, I had a spare black plastic Hammond project box (120mm x 100mm x 35mm) that a friend 3d printed a faceplate for.

### Parts List

- ESP-WROOM-32
- 20x4 LCD w/ PCF8574 I2C
- Black Plastic Hammond Enclosure (120mm x 100mm x 35mm)
- 3ft Micro USB
- 4 female to female jumpers

#### Planned upgrades:

- reset button
- brightness adjustment

## Server

The server is very straight forward as far as express apps go. It makes a single call to the conigecko api using a formatted url. The coins in the config.toml are used to create the url and make the request. Once the data is received, it is formatted for the ESP32 and sent to it.

## Code Process Overview

1. request sent to SERVERADDRESS:PORT/ticker-2004
2. server makes Coingecko API request
3. parses the incoming data and formats it for safe travel
4. esp32 receives data, removes and replaces some characters and
5. print to display

# Deploy Intsructions

## Server Setup

I chose to _Dockerize_ the server app so that I could easily deploy it to my home server (via Portainer) once I was done. [This tutorial is great!](https://www.youtube.com/watch?v=CsWoMpK3EtE&t=336) Upload the image to your Docker account, build it, run it, expose internal port 1337 to the outside (I left it on random) - done.

If instead you'd like to run it on your local machine,

1. Clone this repo
2. cd into the root directory (the folder with package.json in it)
3. run the following commands:

```
  // if you are using yarn
  yarn
  yarn build
  yarn start

  // if you are using npm
  npm install
  npm run build
  npm run start
```

If done correctly, you should see
`??????[Ticker-Server]: Server is running at https://localhost:1337`

You can test the API in the browser by typing in https://localhost:1337/ticker-2004. Should should get a response similar to this:
![browser_img](https://user-images.githubusercontent.com/11794877/172496023-8cba0b94-19b0-45a6-b642-0df73c5100c9.png)

## ESP32 Setup

First, make sure you have properly connected the I2C display to your ESP32. For me, it was:

```
ESP32   PCF8574
V5------VCC
GND-----GND
G21-----SDA
G22-----SCL
```

- Plug your ESP32 into your PC - note the display should turn on, regardless of code on the ESP32.

- Open VS Code with Platform IO installed. Use the PIO gui to open the project folder "pio" and then compile it. This will make sure that all the libraries are downloaded and that you have the appropriate files needed to continue.

- The serverName and Wifi credentials must be set before flashing the ESP32 - you will want to setup the server first before doing this part so that you know what your url will be.

```
// Wifi Setup
const char* ssid = "figureitout";
const char* password = "idontknow";
// Server call string
const char* serverName = "http://localhost:1337/ticker-2004";

```

- Flash the ESP32 and wait about 15 seconds to see the first output on the display.

# Project Pics

![286215299_892385228821410_2181109550074132449_n](https://user-images.githubusercontent.com/11794877/172496313-c100eadd-b17d-441f-8a51-41213d321ce8.jpg)

![285789627_495974835547427_1162574691762965265_n](https://user-images.githubusercontent.com/11794877/172496263-3480584c-25d1-4ca6-96ae-cd6d4dfe2473.jpg)
