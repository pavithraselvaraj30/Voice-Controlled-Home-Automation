# Voice-Controlled-Home-Automation

## OVERVIEW:

The Voice-Controlled Home Automation System is designed to provide users with a hands-free and convenient method to control household appliances using spoken commands. This project integrates speech recognition technology, IoT hardware, and microcontroller-based control circuits to automate common home functions such as switching lights, fans, and other electrical devices.

## FEATURES:

1. Hands-Free Operation
2. Real-Time Voice Recognition
3. Appliance Control
4. IoT Integration
5. User-Friendly Interface
6. Energy Efficiency
7. Multi-Platform Support
8. Expandability

## PROJECT ARCHITECTURE:

### 1. User Layer (Voice Command Input)

This is where the user interacts with the system.

#### Components:

Google Assistant / Siri / Alexa
IFTTT / Voice Shortcut / Custom App
Web Browser (optional)

#### Function:

User issues voice commands such as
“Turn on the light”
“Switch off the fan”
Voice assistant converts speech → text → triggers a web request to ESP32 URLs.

#### Example:

For light ON → http://<ESP_IP>/lighton
For fan OFF → http://<ESP_IP>/fanoff

### 2. Cloud / Automation Layer (Optional Based on Setup)

If using Google Assistant + IFTTT or a similar automation tool:

#### Role:

Converts voice command into an HTTP GET request.
Sends the request to the ESP32 WebServer over WiFi.
This layer is optional but common in voice-controlled IoT projects.

### 3. Communication Layer (WiFi Network)

#### Your ESP32 connects to the home WiFi:

WiFi.begin(ssid, password);

#### Responsibilities:

Provide IP address to ESP32
Allow devices (phones/assistants) to access ESP32 through local network

#### Transfer HTTP requests like:
/lighton, /lightoff, /fanon, /fanoff

### 4. Device Control Layer (ESP32 WebServer)

#### ESP32 runs a web server at port 80:

WebServer server(80);
It listens for HTTP requests and executes actions.

#### Example routes:

/lighton → Relay1 → Light ON
/lightoff → Relay1 → Light OFF
/fanon → Relay2 → Fan ON
/fanoff → Relay2 → Fan OFF

#### Responsibilities:

Decode incoming HTTP requests
Control GPIO pins
Send response messages (“Light ON”, “Fan OFF”, etc.)

### 5. Hardware Control Layer (Relay & Appliances)

#### Components:

Relay1 → GPIO 5 → Light
Relay2 → GPIO 18 → Fan

#### Logic:

ESP32 sets relay pins LOW/HIGH
Relay toggles AC appliances safely

#### Your code initializes:

pinMode(RELAY1, OUTPUT);
pinMode(RELAY2, OUTPUT);
digitalWrite(RELAY1, HIGH);  // OFF state
digitalWrite(RELAY2, HIGH);


LOW → Appliance ON
HIGH → Appliance OFF

### WORKING PRINCIPLE:
1.The user gives a voice command (e.g., “Turn on the light”).

2.The voice is captured by the input device and processed by speech recognition.

3.The interpreted command is sent to the microcontroller through Bluetooth, Wi-Fi, or serial input.

4.The microcontroller identifies the command and sends a signal to the relay module.

5.The relay switches the corresponding appliance ON or OFF.

6.The system may give feedback to the user confirming the action.

### PROGRAM:

```
#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "YourWiFiSSID";
const char* password = "YourWiFiPassword";

WebServer server(80);

#define RELAY1  5
#define RELAY2  18

void setup() {
  Serial.begin(115200);
  pinMode(RELAY1, OUTPUT);
  pinMode(RELAY2, OUTPUT);
  digitalWrite(RELAY1, HIGH);
  digitalWrite(RELAY2, HIGH);

  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConnected!");
  Serial.println(WiFi.localIP());

  server.on("/lighton", []() {
    digitalWrite(RELAY1, LOW);
    server.send(200, "text/plain", "Light ON");
  });
  
  server.on("/lightoff", []() {
    digitalWrite(RELAY1, HIGH);
    server.send(200, "text/plain", "Light OFF");
  });

  server.on("/fanon", []() {
    digitalWrite(RELAY2, LOW);
    server.send(200, "text/plain", "Fan ON");
  });

  server.on("/fanoff", []() {
    digitalWrite(RELAY2, HIGH);
    server.send(200, "text/plain", "Fan OFF");
  });

  server.begin();
  Serial.println("Server started");
}

void loop() {
  server.handleClient();
}
```

## CONCLUSION:
The Voice-Controlled Home Automation system successfully demonstrates how modern IoT technology and wireless communication can be combined to create a convenient, efficient, and user-friendly smart home solution. By using voice commands processed through a web-based interface and WiFi connectivity, the system allows users to control household appliances such as lights and fans with ease.

The ESP32-based web server design ensures reliable communication and quick response to user commands, while the relay module provides safe switching of electrical loads. This hands-free automation approach not only enhances comfort but also supports accessibility for elderly and differently-abled users.

