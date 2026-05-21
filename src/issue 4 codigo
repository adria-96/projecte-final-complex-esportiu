#include <WiFi.h>
#include <WebServer.h>
#include <Wire.h>
#include "Adafruit_CCS811.h"

Adafruit_CCS811 ccs;
WebServer server(80);

// WIFI
const char* ssid = "TU_WIFI";
const char* password = "TU_PASSWORD";

// LED
const int LED_PIN = 2;

// Umbral CO2
const int UMBRAL_CO2 = 600;

// Variable global
int co2 = 0;


// -------- WEB PAGE --------
void handleRoot() {
  String estadoLED = digitalRead(LED_PIN) ? "ENCENDIDO" : "APAGADO";

  String html = "<!DOCTYPE html><html>";
  html += "<head>";
  html += "<meta http-equiv='refresh' content='3'>";
  html += "<meta charset='UTF-8'>";
  html += "<title>Monitor CO2 ESP32</title>";
  html += "<style>";
  html += "body{font-family:Arial;text-align:center;margin-top:40px;}";
  html += "h1{color:#333;}";
  html += ".dato{font-size:40px;color:green;}";
  html += ".led{font-size:30px;color:red;}";
  html += "</style>";
  html += "</head><body>";

  html += "<h1>Monitor CO2</h1>";
  html += "<p class='dato'>" + String(co2) + " ppm</p>";
  html += "<p class='led'>LED: " + estadoLED + "</p>";

  html += "</body></html>";

  server.send(200, "text/html", html);
}
// --------------------------


void setup() {

  Serial.begin(115200);

  pinMode(LED_PIN, OUTPUT);
  digitalWrite(LED_PIN, LOW);

  // I2C
  Wire.begin(21, 22);

  // Sensor
  if (!ccs.begin()) {
    Serial.println("Error iniciando CCS811");
    while (1);
  }

  while (!ccs.available());

  // WIFI
  WiFi.begin(ssid, password);

  Serial.print("Conectando WiFi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi conectada");
  Serial.print("IP: ");
  Serial.println(WiFi.localIP());

  // WEB SERVER
  server.on("/", handleRoot);
  server.begin();

  Serial.println("Servidor web iniciado");
}


void loop() {

  server.handleClient();

  if (ccs.available()) {
    if (!ccs.readData()) {

      co2 = ccs.geteCO2();

      Serial.print("CO2: ");
      Serial.println(co2);

      if (co2 > UMBRAL_CO2) {
        digitalWrite(LED_PIN, HIGH);
      } else {
        digitalWrite(LED_PIN, LOW);
      }
    }
  }

  delay(2000);
}
