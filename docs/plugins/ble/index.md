---
layout: docs-plugins
title: ngCordova - Document and Examples - by the Ionic Framework Team

plugin-name: $cordovaBarcodeScanner
source: https://github.com/driftyco/ng-cordova/blob/master/src/plugins/ble.js
official-docs: https://github.com/don/cordova-plugin-ble-central
icon-apple: true
icon-android: true
icon-windows: false
---

This [Bluetooth Low Energy (BLE) Central Plugin](https://github.com/don/cordova-plugin-ble-central) plugin enables communication between a phone and Bluetooth Low Energy (BLE) peripherals.

```bash
cordova plugin add cordova-plugin-ble-central
```


#### Methods

* $cordovaBLE.scan
* $cordovaBLE.startScan
* $cordovaBLE.stopScan
* $cordovaBLE.connect
* $cordovaBLE.disconnect
* $cordovaBLE.read
* $cordovaBLE.write
* $cordovaBLE.writeWithoutResponse
* $cordovaBLE.startNotification
* $cordovaBLE.stopNotification
* $cordovaBLE.isEnabled
* $cordovaBLE.isConnected
* $cordovaBLE.showBluetoothSettings
* $cordovaBLE.enable

See the [official docs](https://github.com/don/cordova-plugin-ble-central) for an explanation of the methods. All of the methods return promises with the following exceptions which require function callbacks:

* $cordovaBLE.connect
* $cordovaBLE.writeWithoutResponse
* $cordovaBLE.startNotification
* $cordovaBLE.enable

#### Example 1 *(ES6)*

```javascript

var module = {};
module.controller('BLECtrl', function ($scope, $cordovaBLE) {

	//use your BLE peripheral's real values
	const SERVICE_UUID = "foo";
	const RX_CHARACTERISTIC = "bar";

	let bleUtil = {
		connect(deviceID) {
			return $q((resolve, reject) => {
				let successFn = () => {
					console.log('connected, about to subscribe to notifications');

					// subscribe for incoming data
					$cordovaBLE.startNotification(deviceID, SERVICE_UUID, RX_CHARACTERISTIC, (data) => this.onData(data), (err)=> this.onError(err));

					resolve();
				};

				// Note, connectionFailed could get called at any time in situations
				// where connectivity is lost or peripheral is switched off so we can't use the promise based approach of the ngCordova wrapper
				$cordovaBLE.connect(deviceID, successFn, (peripheral) => this.onConnectionFailed(peripheral));
			});
		},

		onData(data) { // data received from peripheral
			//print out binary bytes as hex
			if (data instanceof ArrayBuffer){
				let byteArr = new Uint8Array(data);
				let byteStr = "";
				for (let i = 0; i < byteArr.length; i++){
					byteStr += byteArr[i].toString(16) + " ";
				}
				console.log('bytes in: ' + byteStr);
			}
		},

		onError(reason) {
			console.warn("ERROR: " + reason); // real apps should use notification.alert
		},

		onConnectionFailed(peripheral){
			console.warn('Disconnected from peripheral unexpectedly');
		}

	};

	document.addEventListener("deviceready", function () {
		let deviceID = 'ABCD-1234-EFGH-5678-IJKLM-9010'; // This would be a real UUID obtained by using $cordovaBLE.scan
		bleUtil.connect(deviceID)
		.then(() => {
			console.log('continue doing something else');
		})
		.catch((err) => {
			console.error(err);
		})

	}, false);
});
```