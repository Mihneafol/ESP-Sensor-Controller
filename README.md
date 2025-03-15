  # ESP-Sensor-Controller
 Proiect ESP32  cu Senzori și Interfață Web
Acest proiect utilizează un ESP32 sau ESP8266 pentru a crea un server web care permite monitorizarea și controlul unui sistem de senzori și actuatori, inclusiv un senzor de temperatură și umiditate (DHT11), un senzor de lumină, un senor IR, și un LED. Sistemul poate fi accesat de pe orice dispozitiv conectat la rețea, oferind o interfață web pentru vizualizarea datelor și controlul LED-ului.

   # Componente hardware:
ESP32/ESP8266
Senzor de temperatură și umiditate DHT11
Senzor de lumină analogic
Senzor IR digital
LED
Buton pentru schimbarea stării LED-ului
LCD 16x2 cu I2C pentru vizualizarea datelor local
   # Funcționalități:
Monitorizarea temperaturii și umidității: Valorile senzorului DHT11 sunt afișate atât pe LCD cât și pe interfața web.
Controlul LED-ului: LED-ul poate fi aprins și stins de pe interfața web sau local, printr-un buton.
Senzor de lumină: Nivelul de lumină ambientala este citit printr-un senzor analogic și afișat pe interfața web.
Senzor IR: Detectează prezența sau absența obiectelor în apropierea senzorului IR.
Interfață Web: Permite vizualizarea și controlul senzorilor și LED-ului printr-o pagină web simplă.
   # Detalii tehnice:
WiFi: Proiectul folosește rețeaua WiFi pentru a trimite date către un server web și pentru a permite controlul LED-ului de la distanță.
HTML/JavaScript: Interfața web este construită folosind HTML și JavaScript pentru a actualiza datele într-un interval de 2 secunde, fără a necesita reîncărcarea paginii.
Debounce: Codul include o implementare de debounce pentru butonul fizic, evitând multiplele schimbări de stare neașteptate la apăsarea butonului.
#  Instalare:
  Instalare Biblioteci:
DHT pentru citirea senzorului DHT11.
LiquidCrystal_PCF8574 pentru utilizarea LCD-ului I2C.
ESPAsyncWebServer și AsyncTCP pentru serverul web asincron.
 # Configurare WiFi:
Modifică variabilele ssid și parola cu informațiile tale de rețea WiFi.
 # Compilare și Încărcare:
Selectează corect placa și portul în Arduino IDE sau PlatformIO.
Încarcă codul pe ESP32/ESP8266.
 # Accesare Web:
După ce ESP-ul se conectează la WiFi, adresa IP a dispozitivului va fi afișată în monitorul serial. Accesează această adresă IP dintr-un browser pentru a vizualiza și controla sistemul.
  Funcționare:
Pagina web va actualiza automat datele de temperatură, umiditate, nivel de lumină și stare senzor IR la fiecare 2 secunde.
Utilizatorul poate controla LED-ul printr-un switch din interfața web. Schimbările vor fi reflectate instantaneu și pe hardware.
Butonul local poate fi folosit pentru a controla LED-ul și fără acces la interfața web.
