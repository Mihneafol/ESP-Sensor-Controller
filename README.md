  # ESP-Sensor-Controller
  Proiect ESP32/ESP8266 cu senzori de temperatură, umiditate, lumină și IR, plus control LED prin interfață web. Permite monitorizarea datelor senzorilor și controlul LED-ului de la distanță. Include și un buton fizic     pentru schimbarea stării LED-ului, afișând informațiile pe un LCD 16x2.
#ifdef ESP32
#include <WiFi.h>
#include <AsyncTCP.h>
#else
#include <ESP8266WiFi.h>
#include <ESPAsyncTCP.h>
#endif
#include <ESPAsyncWebServer.h>
#include <Wire.h>
#include <LiquidCrystal_PCF8574.h>
#include <DHT.h>

// Configurare WiFi
const char* ssid = "iPhone - Teodora"; 
const char* password = "teodoraa"; 

// Definire pini
#define DHTPIN 4        
#define DHTTYPE DHT11   
#define LIGHT_SENSOR_PIN 26  
#define IR_SENSOR_PIN 32  
#define LED_PIN 2        
#define BUTTON_PIN 4      

// Obiecte pentru senzori și LCD
DHT dht(DHTPIN, DHTTYPE);
LiquidCrystal_PCF8574 lcd(0x27);  // Adresa I2C 0x27 pentru LCD
AsyncWebServer server(80);

// Variabile
int ledState = LOW;
int buttonState, lastButtonState = LOW;
unsigned long lastDebounceTime = 0, debounceDelay = 50;

// HTML pentru interfața web
const char index_html[] PROGMEM = R"rawliteral(
<!DOCTYPE HTML>
<html>
<head>
  <title>ESP Web Server</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    html {font-family: Arial; text-align: center;}
    h2 {font-size: 2.0rem;}
    p {font-size: 1.5rem;}
    .switch {position: relative; display: inline-block; width: 60px; height: 34px}
    .switch input {display: none}
    .slider {position: absolute; background-color: #ccc; border-radius: 34px; width: 100%; height: 100%}
    .slider:before {content: ""; position: absolute; background: white; width: 26px; height: 26px; transition: 0.4s; left: 4px; bottom: 4px; border-radius: 50%}
    input:checked+.slider {background-color: #2196F3}
    input:checked+.slider:before {transform: translateX(26px)}
  </style>
</head>
<body>
  <h2>ESP Sensor Data</h2>
  <h3>LED State: <span id="ledState">%LEDSTATE%</span></h3>
  <label class="switch">
    <input type="checkbox" onchange="toggleLED(this)" %LED_CHECKED%>
    <span class="slider"></span>
  </label>
  <h3>Temperature: <span id="temperature">%TEMPERATURE%</span>°C</h3>
  <h3>Humidity: <span id="humidity">%HUMIDITY%</span>%</h3>
  <h3>Light Level: <span id="light">%LIGHT%</span></h3>
  <h3>IR Sensor: <span id="ir">%IRSTATE%</span></h3>

  <script>
    function toggleLED(element) {
      var xhr = new XMLHttpRequest();
      xhr.open("GET", "/update?state=" + (element.checked ? "1" : "0"), true);
      xhr.send();
    }
    setInterval(function() {
      var xhttp = new XMLHttpRequest();
      xhttp.onreadystatechange = function() {
        if (this.readyState == 4 && this.status == 200) {
          var data = JSON.parse(this.responseText);
          document.getElementById("temperature").innerText = data.temperature;
          document.getElementById("humidity").innerText = data.humidity;
          document.getElementById("light").innerText = data.light;
          document.getElementById("ir").innerText = data.ir ? "Detected" : "None";
          document.getElementById("ledState").innerText = data.led ? "ON" : "OFF";
        }
      };
      xhttp.open("GET", "/data", true);
      xhttp.send();
    }, 2000);
  </script>
</body>
</html>
)rawliteral";

// Funcție pentru a înlocui placeholder-ele din HTML
String processor(const String& var) {
  if (var == "LEDSTATE") return ledState ? "ON" : "OFF";
  if (var == "LED_CHECKED") return ledState ? "checked" : "";
  return String();
}

void setup() {
  Serial.begin(115200);
  dht.begin();
  Wire.begin(21, 22);  // Conexiune I2C pentru ESP32
  lcd.begin(16, 2);     // LCD de 16x2 caractere
  lcd.setBacklight(255);
  
  pinMode(LED_PIN, OUTPUT);
  pinMode(BUTTON_PIN, INPUT);
  pinMode(IR_SENSOR_PIN, INPUT);
  digitalWrite(LED_PIN, ledState);

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println(WiFi.localIP());

  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request) {
    request->send_P(200, "text/html", index_html, processor);
  });

  server.on("/update", HTTP_GET, [](AsyncWebServerRequest *request) {
    if (request->hasParam("state")) {
      ledState = request->getParam("state")->value().toInt();
      digitalWrite(LED_PIN, ledState);
    }
    request->send(200, "text/plain", "OK");
  });

  server.on("/data", HTTP_GET, [](AsyncWebServerRequest *request) {
    float temp = dht.readTemperature();
    float hum = dht.readHumidity();
    int light = analogRead(LIGHT_SENSOR_PIN);
    int ir = digitalRead(IR_SENSOR_PIN);
    
    String json = "{";
    json += "\"temperature\":" + String(temp) + ",";
    json += "\"humidity\":" + String(hum) + ",";
    json += "\"light\":" + String(light) + ",";
    json += "\"ir\":" + String(ir) + ",";
    json += "\"led\":" + String(ledState);
    json += "}";
    
    request->send(200, "application/json", json);
  });

  server.begin();
}

void loop() {
  int reading = digitalRead(BUTTON_PIN);  // Citește starea butonului

  // Verifică schimbarea stării butonului (debounce)
  if (reading != lastButtonState) {
    lastDebounceTime = millis();
  }

  // Verifică dacă a trecut timpul de debounce
  if ((millis() - lastDebounceTime) > debounceDelay) {
    if (reading != buttonState) {
      buttonState = reading;
      if (buttonState == HIGH) {
        ledState = !ledState;  // Schimbă starea LED-ului
        digitalWrite(LED_PIN, ledState);  // Actualizează LED-ul
      }
    }
  }

  lastButtonState = reading;  // Salvează starea butonului pentru următoarea iterație

  // Citește valorile de temperatură și umiditate
  float temp = dht.readTemperature();
  float hum = dht.readHumidity();

  // Actualizează LCD-ul
  lcd.clear(); // Șterge ecranul pentru a evita suprapunerea textului
  lcd.setCursor(0, 0);
  lcd.print("Temp: " + String(temp) + "C");
  lcd.setCursor(0, 1);
  lcd.print("Hum: " + String(hum) + "%");

  delay(2000); // Așteaptă 2 secunde înainte de a actualiza din nou
}
