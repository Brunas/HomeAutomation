## The problem

You have 
- Sonoff Mini baught around end of August 2020.
- followed instructions on Youtube/Internet/Tasmota site and using eWeLink app upgraded your device to the latest version - at the time of writing this it is 3.6.0 version.
- put DIY jumper on.
- created *sonoffDiy* Hotspot on your smartphone or on your Windows PC, but your device does not connect to that.
- downloaded Sonoff flashing tool from here https://github.com/itead/Sonoff_Devices_DIY_Tools/blob/master/tool/tool_01DIY85(3.3.0).exe and it does not see your device.
- no soldering skills or tools
- real desperation to leave Sonoff cloud and install Tasmota.

You are stuck and cannot find how to fix that problem. Don't cry yet - there is a way to install Tasmota.

## The solution 

1. Follow instructions how to enter DIY mode from https://github.com/itead/Sonoff_Devices_DIY_Tools/blob/master/SONOFF%20DIY%20MODE%20Protocol%20Doc%20v2.0%20Doc.pdf. This is the excerpt from it:

	1. Power on;
	2. Long press the button for 5 seconds for entering Compatible Pairing Mode (AP)
	User tips: If the device has been paired with eWeLink APP, reset the device is necessary by	long press the pairing button for 5 seconds, then press another 5 seconds for entering Compatible Pairing Mode (AP)
	3. The LED indicator will blink continuously;
	4. From mobile phone or PC WiFi setting, an Access Point of the device named **ITEAD-XXXXXXXX** will be found, connect it with default password **12345678**
	5. Open the browser and access http://10.10.7.1/
	6. Next, Fill in WiFi SSID and password that the device would have connected with
	7. Succeed, Now the device is in DIY mode.

Note: I needed to manually change IP address to 10.10.7.2, 255.0.0.0 with gateway 10.10.7.1 in adapter TCP/IPv4 settings to access that IP address.

3. Use Fing or any similar local network scanning app on your smartphone or PC to find IP address of your Sonoff Mini device. MAC Vendor most likely is **Espressif** and the device has **8081** port open.
4. Install **Rester** extension in Chrome or Firefox or any other preferred tool to perform REST API operations.
5. To test your device DIY mode create new request in **Rester**:
	1. Method: **POST**
	2. URL: http://<*IP of your device*>:8081/zeroconf/info
	3. Body: <code>{"data": {}}</code>
	4. You might need to add Header **Content-Type** with value **application/json**
	5. Press SEND
	6. If all is OK, status code *200* should be returned with bunch of data:
	<code>{
    "seq": 1,
    "error": 0,
    "data": {
        "switch": "off",
        "startup": "off",
        "pulse": "off",
        "pulseWidth": 2000,
        "ssid": "YourWiFi",
        "otaUnlock": false,
        "fwVersion": "3.6.0",
        "deviceid": "YourDeviceId",
        "bssid": "YourBSSId",
        "signalStrength": -52
    }
}</code>
	7. If that doesn't return *200*, try going back to 5s+5s reset above.
6. If all above works, let's unlock OTA:
	1. Method: **POST**
	2. URL: http://<*IP of your device*>:8081/zeroconf/ota_unlock
	3. Body: <code>{"data": {}}</code>
	4. You might need to add Header **Content-Type** with value **application/json**
	5. Press SEND
	6. You should get status code *200*
7. Sonoff's flashing tool should see your device now! 
8. Since Tasmota binary names have changed, download Tasmota-lite.bin from https://github.com/arendst/Tasmota/releases and flash it according to instructions everywhere on the Internet.
