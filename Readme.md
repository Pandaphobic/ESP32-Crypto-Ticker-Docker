# ESP32 Crypto Ticker

Simple ESP32 based crypto ticker that depends on a locally deployed express server to make calls to the Coingecko API. The server provides a JSON array with up to 4 rows of coins to be displayed the 20x4 LCD Display.

## Hardware

For this project, I went with an ESP-WROOM-32. This board can connect to Wifi and is a breeze to use for things like http requests. The display I chose is a standard 20x4 LCD with a factory installed PCF8574 ic to provide I2C interface. For the enclosure, I had a spare black plastic Hammond project box (120mm x 100mm x 35mm) that a friend 3d printed a faceplate for.

### Parts List

- ESP-WROOM-32
- 20x4 LCD w/ PCF8574 I2C
- Black Plastic Hammond Enclosure (120mm x 100mm x 35mm)
- 3ft Micro USB
- female to female jumpers

#### Planned upgrades:

- reset button
- brightness adjustment

## Server

The server is very straight forward as far as express apps go. It makes a single call to the conigecko api using a formatted url. The coins in the config.toml are used to created the url and make the request. Once the data is received, it is formatted for the ESP32 and sent to it.

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
`⚡️[Ticker-Server]: Server is running at https://localhost:1337`

You can test the API in the browser by typing in https://localhost:1337/ticker-2004. Should should get a response similar to this:

## ESP32 Setup

First, make sure you have properly connected the I2C display to your board. For me, it was:

```
ESP32   PCF8574
V5------VCC
GND-----GND
G21-----SDA
G22-----SCL
```

- Plug your board into your PC - note the display should turn on, regardless of code on the board.

- Open the "pio" folder in VS Code with Platform IO installed. Use the PIO gui to open the project and then build it. This will make sure that all the libraries are downloaded and that you have the appropriate files needed to continue.

- The serverName and Wifi credentials must be set before flashing the ESP32 - you will want to setup the server first before doing this part so that you know what your url will be.

```

// Wifi Setup
const char* ssid = "figureitout";
const char* password = "idontknow";
// Server call string
const char* serverName = "http://localhost:1337/ticker-2004";

```

# Project Pics
