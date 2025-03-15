# ESP-Sensor-Controller
ESP32 Project with Sensors and Web Interface

This project uses an ESP32 or ESP8266 to create a web server that allows the monitoring and control of a sensor and actuator system, including a temperature and humidity sensor (DHT11), a light sensor, an IR sensor, and an LED. The system can be accessed from any network-connected device, offering a web interface for data visualization and LED control.

  # Hardware Components:
ESP32/ESP8266
DHT11 Temperature and Humidity Sensor
Analog Light Sensor
Digital IR Sensor
LED
Button to change the LED state
16x2 LCD with I2C for local data display
  # Features:
Temperature and Humidity Monitoring: DHT11 sensor values are displayed both on the LCD and the web interface.
LED Control: The LED can be turned on or off via the web interface or locally, using a button.
Light Sensor: The ambient light level is read using an analog light sensor and displayed on the web interface.
IR Sensor: Detects the presence or absence of objects near the IR sensor.
Web Interface: Allows monitoring and control of sensors and the LED through a simple web page.
  # Technical Details:
WiFi: The project uses WiFi to send data to a web server and enable remote control of the LED.
HTML/JavaScript: The web interface is built using HTML and JavaScript to update data every 2 seconds, without requiring a page reload.
Debounce: The code includes a debounce implementation for the physical button, preventing multiple unintended state changes when the button is pressed.
  # Libraries:
DHT for reading the DHT11 sensor.
LiquidCrystal_PCF8574 for using the I2C LCD.
ESPAsyncWebServer and AsyncTCP for the asynchronous web server.
  # WiFi Configuration:
Modify the ssid and password variables with your WiFi network credentials.
  # Compile and Upload:
Select the correct board and port in Arduino IDE or PlatformIO.
Upload the code to your ESP32/ESP8266.
  # Web Access:
After the ESP connects to WiFi, its IP address will be displayed in the serial monitor. Access this IP address from a browser to view and control the system.
  # Operation:
The web page will automatically update temperature, humidity, light level, and IR sensor status every 2 seconds.
The user can control the LED through a switch on the web interface. Changes will be reflected instantly on the hardware.
The local button can also be used to control the LED without accessing the web interface.
