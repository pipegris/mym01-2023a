#include <WiFi.h>
#include <WebServer.h>
#include "max6675.h"

const char* ssid = "ESP32Demo";  // SSID de la red Wifi
const char* password = "esp32demo";  // Password de la red Wifi

// Crea instancia del servidor web escuchando el puerto 80 (HTTP)
WebServer server(80);

// Definición de puertos para el sensor de Temp
int thermoDO = 19;
int thermoCS = 23;
int thermoCLK = 5;
MAX6675 thermocouple(thermoCLK, thermoCS, thermoDO);

/*
   Estado inicial de los LEDs del ESP32
*/
uint8_t LED1pin = 13;
bool LED1Estado = LOW;
uint8_t LED2pin = 12;
bool LED2Estado = LOW;

// Gestión del servicio web
// Gestión del evento "conexión"
void handle_OnConnect() {
  LED1Estado = LOW; // 1
  LED2Estado = LOW; // 1
  Serial.println("GPIO4 Estado: OFF | GPIO5 Estado: OFF"); // 2
  server.send(200, "text/html", SendHTML(LED1Estado, LED2Estado)); // 3
}

// Gestión de la acción "Encender LED1"
void handle_led1on() {
  LED1Estado = HIGH; //1
  Serial.println("GPIO4 Estado: ON"); // 2
  server.send(200, "text/html", SendHTML(true, LED2Estado)); //3
}

// Gestión de la acción "Apagar LED1"
void handle_led1off() {
  LED1Estado = LOW;
  Serial.println("GPIO4 Estado: OFF");
  server.send(200, "text/html", SendHTML(false, LED2Estado));
}

// Gestión de la acción "Encender LED2"
void handle_led2on() {
  LED2Estado = HIGH;
  Serial.println("GPIO5 Estado: ON");
  server.send(200, "text/html", SendHTML(LED1Estado, true));
}

// Gestión de la acción "Apagar LED2"
void handle_led2off() {
  LED2Estado = LOW;
  Serial.println("GPIO5 Estado: OFF");
  server.send(200, "text/html", SendHTML(LED1Estado, false));
}

void handle_NotFound() {
  server.send(404, "text/plain", "La pagina no existe");
}

// Archivo servido HTML con estilos CSS
String SendHTML(uint8_t led1stat, uint8_t led2stat) {
  String ptr = "<!DOCTYPE html> <html>\n";
  
  ptr += "<head><meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0, user-scalable=no\">\n";
  ptr += "<meta http-equiv=\"refresh\" content=\"3\">";
  ptr += "<title>Control LEDs y Lectura de Temperatura</title>\n";
  
  // Estilos para los botones
  ptr += "<style>html { font-family: Helvetica; display: inline-block; margin: 0px auto; text-align: center;}\n";
  ptr += "body{margin-top: 45px;} h1 {color: #444444;margin: 45px auto 25px;} h3 {color: #444444;margin-bottom: 45px;}\n";
  ptr += ".button {display: block;width: 80px;background-color: #34db98;border: none;color: white;padding: 13px 25px;text-decoration: none;font-size: 25px;margin: 0px auto 35px;cursor: pointer;border-radius: 4px;}\n";
  ptr += ".button-on {background-color: #34db98;}\n";
  ptr += ".button-on:active {background-color: #29b980;}\n";
  ptr += ".button-off {background-color: #345e49;}\n";
  ptr += ".button-off:active {background-color: #2c503e;}\n";
  ptr += "p {font-size: 14px;color: #888;margin-bottom: 10px;}\n";
  ptr += "</style>\n";
  ptr += "</head>\n";
  ptr += "<body>\n";

  // Contenido
  ptr += "<div style=\"text-align:center;\"><img style=\"widh:auto;height:100px;\" src=\"https://sipac2.usc.edu.co/images/logoUSC.png\"/></div>";
  ptr += "<h1>ESP32 Web Server</h1>\n";
  ptr += "<h3>andres.grisales00@usc.edu.co</h3>\n";

  // Lógica para encendido/apagado de LEDs
  if (led1stat)
  {
    ptr += "<p>LED1 Estado: <span style=\"color:green;font-weight:bold\">ON</span></p><a class=\"button button-off\" href=\"/led1off\">OFF</a>\n";
  }
  else
  {
    ptr += "<p>LED1 Estado: OFF</p><a class=\"button button-on\" href=\"/led1on\">ON</a>\n";
  }

  if (led2stat)
  {
    ptr += "<p>LED2 Estado: <span style=\"color:red;font-weight:bold\">ON</span></p><a class=\"button button-off\" href=\"/led2off\">OFF</a>\n";
  }
  else
  {
    ptr += "<p>LED2 Estado: OFF</p><a class=\"button button-on\" href=\"/led2on\">ON</a>\n";
  }
  ptr += "<div class=\"button button-off\">"+String(thermocouple.readCelsius())+"C</div>";

  ptr += "</body>\n";
  ptr += "</html>\n";
  return ptr;
}

void setup() {
 // Configuración de interfaz serial 
  Serial.begin(115200);
  pinMode(LED1pin, OUTPUT);
  pinMode(LED2pin, OUTPUT);

  // Configuración del Wifi
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  Serial.println("");

  // Espera la conexión
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.print("Conectado a ");
  Serial.println(ssid);
  Serial.print("Direccion IP: ");
  Serial.println(WiFi.localIP());

  server.on("/", handle_OnConnect);
  server.on("/led1on", handle_led1on);
  server.on("/led1off", handle_led1off);
  server.on("/led2on", handle_led2on);
  server.on("/led2off", handle_led2off);
  server.onNotFound(handle_NotFound);

  // Inicia servidor
  server.begin();
  Serial.println("Servidor HTTP iniciado");
  
}

void loop() {
  server.handleClient();

  if (LED1Estado)
  {
    digitalWrite(LED1pin, HIGH);
  }
  else
  {
    digitalWrite(LED1pin, LOW);
  }

  if (LED2Estado)
  {
    digitalWrite(LED2pin, HIGH);
  }
  else
  {
    digitalWrite(LED2pin, LOW);
  }
}
