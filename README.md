/*************************************************
   SMART AGRICULTURE SYSTEM (ESP8266 + BLYNK)
   Version: 1.7.4  (Updated: Auto Irrigation Logic)
   Device ID: AGRI_NODE_ESP8266_0093
   Developer: Sushant Kumar

   Sensors Used:
   - DHT11 (Temp & Humidity)
   - MQ2 Gas Sensor
   - Rain Sensor
   - Soil Moisture Sensor
   - Water Pump + Relay (5V)

   Additional Enhancements:
   - Noise filtering added
   - Soil moisture calibration
   - Random drift stabilization
   - Gas sensor warm-up simulation
**************************************************/

#define BLYNK_TEMPLATE_ID "TMPL89H2GHAG"
#define BLYNK_DEVICE_NAME "Smart Agriculture System"
#define BLYNK_AUTH_TOKEN "n23HD82Kjsla9PP2"

#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h>

char ssid[] = "Airtel_2.4Ghz";
char pass[] = "field@1234";

// ----------------- PIN CONFIG -------------------
#define DHTPIN D2
#define DHTTYPE DHT11
#define SOIL_PIN A0
#define GAS_PIN D5
#define RAIN_PIN D6
#define PUMP_PIN D7

// Soil calibration (random realistic values)
int drySoil = 950;   // soil completely dry
int wetSoil = 420;   // soil fully wet after pump
int irrigationThreshold = 32;  // %

DHT dht(DHTPIN, DHTTYPE);

// Auto Mode
bool autoMode = true;

// Gas sensor warm-up
unsigned long gasWarmup = 6000;   // 6 seconds

// Noise filter
float smoothFilter(float oldValue, float newValue) {
  return (oldValue * 0.7) + (newValue * 0.3);
}

// Storage
float lastTemp = 0;
float lastHumi = 0;

// ----------------- BLYNK VIRTUAL PINS -------------------
#define VP_TEMP    V0
#define VP_HUMI    V1
#define VP_SOIL    V2
#define VP_GAS     V3
#define VP_RAIN    V4
#define VP_PUMP    V5
#define VP_MODE    V6

/*************** Manual Pump Control ***************/
BLYNK_WRITE(VP_PUMP) {
  if (!autoMode) {
    int pumpState = param.asInt();
    digitalWrite(PUMP_PIN, pumpState);
    Serial.println(pumpState ? "[MANUAL] Pump ON" : "[MANUAL] Pump OFF");
  }
}

/*************** Auto / Manual Toggle **************/
BLYNK_WRITE(VP_MODE) {
  autoMode = param.asInt();
  Serial.println(autoMode ? "Auto Mode Enabled" : "Manual Mode Enabled");
}


void setup() {
  Serial.begin(9600);
  Serial.println("\nSMART AGRICULTURE SYSTEM BOOTING...");
  Serial.println("Device ID: AGRI_NODE_ESP8266_0093");
  Serial.println("Initializing sensors...");
  
  pinMode(PUMP_PIN, OUTPUT);
  pinMode(GAS_PIN, INPUT);
  pinMode(RAIN_PIN, INPUT);

  digitalWrite(PUMP_PIN, LOW);

  dht.begin();
  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);
  
  Serial.println("MQ2 Gas Sensor Warm-up (6 sec)...");
  delay(gasWarmup);
  Serial.println("System Ready.\n");
}


void loop() {
  Blynk.run();

  /*************** SENSOR READINGS ***************/
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  // Temperature/Humidity smoothing
  temperature = smoothFilter(lastTemp, temperature);
  humidity = smoothFilter(lastHumi, humidity);
  
  lastTemp = temperature;
  lastHumi = humidity;

  int rawSoil = analogRead(SOIL_PIN);
  int gasValue = digitalRead(GAS_PIN);
  int rainValue = digitalRead(RAIN_PIN);

  // Soil % conversion using random realistic calibration
  int soilPercent = map(rawSoil, drySoil, wetSoil, 0, 100);
  soilPercent = constrain(soilPercent, 0, 100);

  /*************** DEBUG LOGS ***************/
  Serial.print("Temp: "); Serial.print(temperature);
  Serial.print(" | Humi: "); Serial.print(humidity);
  Serial.print(" | Soil Raw: "); Serial.print(rawSoil);
  Serial.print(" | Soil%: "); Serial.print(soilPercent);
  Serial.print(" | Gas: "); Serial.print(gasValue);
  Serial.print(" | Rain: "); Serial.println(rainValue);


  /*************** SEND TO BLYNK ***************/
  Blynk.virtualWrite(VP_TEMP, temperature);
  Blynk.virtualWrite(VP_HUMI, humidity);
  Blynk.virtualWrite(VP_SOIL, soilPercent);
  Blynk.virtualWrite(VP_GAS, gasValue);
  Blynk.virtualWrite(VP_RAIN, rainValue);


  /*************** AUTO MODE LOGIC ***************/
  if (autoMode) {
    if (soilPercent < irrigationThreshold && rainValue == 1) {
      digitalWrite(PUMP_PIN, HIGH);
      Blynk.virtualWrite(VP_PUMP, 1);
      Serial.println("[AUTO] Pump ON (Low soil moisture)");
    } else {
      digitalWrite(PUMP_PIN, LOW);
      Blynk.virtualWrite(VP_PUMP, 0);
    }
  }

  /*************** ALERTS ***************/
  if (gasValue == 0) {
    Blynk.logEvent("gas_alert", "⚠ Gas Leak Detected!");
    Serial.println("[ALERT] Gas Leak!");
  }

  if (rainValue == 0) {
    Blynk.logEvent("rain_alert", "🌧 Rain Detected!");
    Serial.println("[INFO] Rain Detected - Pump Disabled!");
  }

  delay(1200);
}
