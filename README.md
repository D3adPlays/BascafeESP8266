## CODE DE TEST

Ce code n'est pas final

```c++
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <WiFiClient.h>

const char* ssid = "wifi-eleves";
const char* password = "@bascan78";

//PIN D'ALLUMAGE MACHINE = D5
//URL INSTALLATION CARTE (https://arduino.esp8266.com/stable/package_esp8266com_index.json)
//TYPE DE CARTE LOLIN(WEMOS) D1 R2 & mini

//L' adresse de l'api web bascafe de l'équipe JAVA
String serverName = "http://192.168.1.106:6969/update-cafe";

//Cariable temporaire de limite de cycles de mise a jour
unsigned long lastTime = 0;

//Status de la machine 0 = éteinte et 1 = allumé
int currStatus = 0;

//Timer de mise a jour (En ms) de 5 secondes (5000 ms)
unsigned long timerDelay = 5000;

void setup() {
  //Démarrage de la console
  Serial.begin(115200);

  //Démarrage de la care WIFI 
  WiFi.begin(ssid, password);
  Serial.println("Connecting");

  while(WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.print("Connecte au wifi: ");
  Serial.println(WiFi.localIP());
 
  Serial.print("Timer pour lire les infos mis a: ");
  Serial.println(timerDelay);
}

void loop() {
  // Attendre de délai imparti de timerDelay
  if ((millis() - lastTime) > timerDelay) {
    //Check le status de la connection WIFI (Au cas ou vu que le wifi lycée est bancal)
    if(WiFi.status()== WL_CONNECTED){
      //Crécation des objets
      WiFiClient client;
      HTTPClient http;

      //URL de requete
      String serverPath = serverName + "?currStatus=" + currStatus;
      
      // Envoi de la requete avec l'objet CLIENT
      http.begin(client, serverPath.c_str());
      
      // demander le code de reponse html (https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
      int httpResponseCode = http.GET();
      
      if (httpResponseCode>0) {
        //Test du code reponse
        Serial.print("HTTP Response code: ");
        Serial.println(httpResponseCode);
        String payload = http.getString();
        Serial.println(payload);
      }
      else {
        //Test du code d'erreur
        Serial.print("Error code: ");
        Serial.println(httpResponseCode);
      }
      // Libération de resources de l'ESP8266
      http.end();
    }
    else {
      Serial.println("WiFi Disconnected");
    }
    lastTime = millis();
  }
}
```
