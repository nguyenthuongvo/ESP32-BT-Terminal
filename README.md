# Changes

ESP32-BLE.ino as sample code for the terminal

0. go to "chrome://flags/" enable "Experimental Web Platform features"

1. you must run on local server 127.0.0.1- dont just click index

2. you must enable bluetooth on the webbrowser
chrome://flags

3. changes to the web page wont be modified unless
browser cache is cleared- Ctrl+F5 or ctrl+shift+del

4. cd current directory
py -m http.server
http://127.0.0.1:8000/

5. connecting to BLE-
cant filter by uuid

    return navigator.bluetooth.requestDevice({
		
	  filters: [{
        name: 'ESP32'
      }],
      optionalServices: ['6e400001-b5a3-f393-e0a9-e50e24dcca9e',
      '6e400002-b5a3-f393-e0a9-e50e24dcca9e',
      '6e400003-b5a3-f393-e0a9-e50e24dcca9e']
    }).

names and services must match on esp arduino sketch-

#define BLE_NAME "ESP32"
#define SERVICE_UUID        "6e400001-b5a3-f393-e0a9-e50e24dcca9e"
#define CHARACTERISTIC_UUID "6e400003-b5a3-f393-e0a9-e50e24dcca9e



# Web Bluetooth Terminal

[![NpmVersion](https://img.shields.io/npm/v/web-bluetooth-terminal.svg)](https://www.npmjs.com/package/web-bluetooth-terminal)
[![dependencies Status](https://david-dm.org/loginov-rocks/Web-Bluetooth-Terminal/status.svg)](https://david-dm.org/loginov-rocks/Web-Bluetooth-Terminal)
[![devDependencies Status](https://david-dm.org/loginov-rocks/Web-Bluetooth-Terminal/dev-status.svg)](https://david-dm.org/loginov-rocks/Web-Bluetooth-Terminal?type=dev)

![Favicon](https://raw.githubusercontent.com/loginov-rocks/Web-Bluetooth-Terminal/master/icons/favicon-16x16.png)
[https://loginov-rocks.github.io/Web-Bluetooth-Terminal](https://loginov-rocks.github.io/Web-Bluetooth-Terminal/) — try
it out, see how it works on [YouTube](https://www.youtube.com/watch?v=BNXN_931W_M), read tutorial on
[Medium](https://medium.com/@loginov_rocks/how-to-make-a-web-app-for-your-own-bluetooth-low-energy-device-arduino-2af8d16fdbe8)
(English) or on [Habr](https://habr.com/post/339146/) (Russian).

Web Bluetooth Terminal is a website that can **connect** with the remote devices which support **Bluetooth Low Energy**
(also called Bluetooth Smart) and **exchange data bidirectionally**. It can be installed on your homescreen as an
application and work offline.

**Killer feature:** the application establishes **serial communication** over BLE that is not provided by the
specification, but needed if you want to make your own BLE IoT devices using affordable bluetooth modules.

![Teaser](https://raw.githubusercontent.com/loginov-rocks/Web-Bluetooth-Terminal/master/misc/Teaser.png)

The application utilises BLE service (`0xFFE0`) and characteristic (`0xFFE1`) available in low cost BLE modules based
for example on CC2541 chip, such as HM-10, JDY-08, AT-09, CC41-A and other. Also, it bypasses 20 bytes limit specific
for GATT characteristics by keeping incoming messages in a buffer and waiting for the end of line characters.

Check [Bluetooth Low Energy (Smart) device](#bluetooth-low-energy-smart-device) and
[How to use this app as a base for my own project?](#how-to-use-this-app-as-a-base-for-my-own-project)
sections for a quick start and to find out how to make your own project. Also, I've made
[MeArm Controller](https://github.com/loginov-rocks/MeArm-Controller) as a showcase project.

## Features

**Accessible via browser** — just go to the [website](https://loginov-rocks.github.io/Web-Bluetooth-Terminal/) and
you'll get the full featured application, it is not needed to install anything.

**Cross-platform** — as long as the app is accessible via browser, you can use it with the desktop or with the smart
phone [browser](#browser).

**Installable** — if you don't want to remember the website address, you can add it to the homescreen.

**Works offline** after installation on your homescreen, since it is a Progressive Web Application.

And... it have **auto scrolling!** Enabled by default, but you can scroll the terminal to the top on more than a half of
the terminal window height to disable it. Scroll to the bottom to enable it again. Rocket science!

## Requirements

### Browser

One of browsers which supports Web Bluetooth API by default
([Chrome Platform Status](https://www.chromestatus.com/feature/5264933985976320),
[Can I use](https://caniuse.com/#feat=web-bluetooth)):

1. Chrome for desktop 56+
2. Chrome for Android 56+
3. Opera 43+
4. Opera for Android 43+

All this browsers support other necessary features, such as [ES6 classes](https://caniuse.com/#feat=es6-class) and PWA
capabilities ([Web App Manifest](https://caniuse.com/#feat=web-app-manifest) and
[Service Workers](https://caniuse.com/#feat=serviceworkers)), so I don't pay attention to it here.

### Bluetooth Low Energy (Smart) device

Different BLE devices implement their own services and characteristics to communicate with, but you can build your own
simple device: you just need a BLE module (e.g. HM-10, JDY-08, AT-09, CC41-A) and an Arduino Uno. Wire it and upload the
[bridge sketch](https://raw.githubusercontent.com/loginov-rocks/Web-Bluetooth-Terminal/master/misc/Arduino-Bridge/Arduino-Bridge.ino).

Pay attention to what voltage level your BLE module consumes, since it can vary from device to device! Read
specifications, you may need to connect your BLE module to the `3.3V` pin and use voltage level shifter between `TX` and
`RX` pins.

![Arduino Uno to BLE module wiring scheme](https://raw.githubusercontent.com/loginov-rocks/Web-Bluetooth-Terminal/master/misc/Arduino-Bridge/Scheme.png)

Open Serial Monitor in Arduino IDE, switch baudrate to `9600` and line endings to `Both NL & CR`. Next, launch the
[Web Bluetooth Terminal](https://loginov-rocks.github.io/Web-Bluetooth-Terminal/) and connect to your module. Now you're
able to make a small talk between the Terminal and the Serial Monitor!

#### BLE module configuration

When a BLE module is waiting for connection it can be configured with `AT` commands. So if you have troubles trying to
make BLE module work as expected you can use following commands, but again, read specifications! Here are some commands
I use with CC41-A module:

* `AT+DEFAULT` — resets the module to the defaults;
* `AT+RESET` — resets the module softly;
* `AT+ROLE` — gets the module working mode;
* `AT+ROLE0` — makes the module to work in slave mode, waiting for connection from other devices;
* `AT+NAME` — gets the module name;
* `AT+NAMESimon` — sets the module name to `Simon`;
* `AT+PIN` — gets the module pin (password);
* `AT+PIN123456` — sets the module pin to `123456`;
* `AT+UUID` — gets the module service UUID;
* `AT+CHAR` — gets the module characteristic UUID.

Commands can be case insensitive and may need to be terminated with `CR` (`\r`) and `LF` (`\n`).

## How to use this app as a base for my own project?

You can fork this repository and implement features specific to your needs. Don't forget that application should be
accessible via HTTPS protocol to enable Web Bluetooth API feature, so you can use
[GitHub Pages](https://pages.github.com/) switching the source to the `master` branch of your repository.

To use development capabilities, you'll need [Node.js](https://nodejs.org/), [npm](https://www.npmjs.com/) especially.
Install it, clone the repository and install `npm` dependencies:

```sh
git clone https://github.com/loginov-rocks/Web-Bluetooth-Terminal.git
cd Web-Bluetooth-Terminal
npm install
```

### Global install

Alternatively, you can delegate repository cloning to the package itself. Just install it globally:

```sh
npm install -g web-bluetooth-terminal
```

Having this package installed globally, you can use the following command to clone the repository into your current
directory:

```sh
web-bluetooth-terminal
```

Or you can specify directory name to clone into as an argument:

```sh
web-bluetooth-terminal MyProject
```

This command checks out the same version as you have installed globally. So if a new version is released, you can update
package with the following command:

```sh
npm update -g web-bluetooth-terminal
```

### Npm scripts

After installing `npm` dependencies, you can use some simple scripts that can be helpful:

* `npm run build` copies used vendors files and generates `css/style.css`;
* `npm run js:vendor` copies used vendors JavaScript files into the `js` directory;
* `npm run lint` lints JavaScript files;
* `npm run styles` generates `css/style.css` from SCSS sources placed in the `scss` directory;
* `npm run styles:vendor` copies used vendors stylesheets into the `css` directory;
* `npm run watch:styles` watches for changes made to the files placed in the `scss` directory and runs `npm run styles`
command.

### BluetoothTerminal.js API

Also, you can install [bluetooth-terminal](https://github.com/loginov-rocks/bluetooth-terminal) package or
[directly download the file](https://raw.githubusercontent.com/loginov-rocks/bluetooth-terminal/master/src/BluetoothTerminal.js)
containing `BluetoothTerminal` class written in ES6 and use it as you want. Here is a simple code snippet that can be
helpful for a quick start:

```js
// Obtain configured instance.
let terminal = new BluetoothTerminal();

// Override `receive` method to handle incoming data as you want.
terminal.receive = function(data) {
  alert(data);
};

// Request the device for connection and get its name after successful connection.
terminal.connect().then(() => {
  alert(terminal.getDeviceName() + ' is connected!');
});

// Send something to the connected device.
terminal.send('Simon says: Hello, world!');

// Disconnect from the connected device.
terminal.disconnect(); 
```

## Contribution

Please use the [dev](https://github.com/loginov-rocks/Web-Bluetooth-Terminal/tree/dev) branch and feel free to contribute!

## Reference

1. [Web Bluetooth Specification](https://webbluetoothcg.github.io/web-bluetooth/)
2. [Web Bluetooth Samples](https://googlechrome.github.io/samples/web-bluetooth/)
3. [Interact with Bluetooth devices on the Web](https://developers.google.com/web/updates/2015/07/interact-with-ble-devices-on-the-web/)
4. [Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/)
5. [Service Worker Toolbox](https://github.com/GoogleChromeLabs/sw-toolbox/)
