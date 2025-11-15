🌱 SMART AGRICULTURE SYSTEM – IoT Based Monitoring & Automation

A real-time IoT system that monitors soil moisture, temperature, humidity, rain detection, and gas levels while automating irrigation using a water pump.

⭐ Overview

The Smart Agriculture System is an IoT-based farming solution that collects real-time environmental data using sensors like DHT11, Soil Moisture Sensor, Rain Sensor, Gas Sensor (MQ-2) and automates irrigation using a water pump controlled through a relay.

All sensor data is sent to the Blynk IoT App, allowing farmers to monitor field conditions remotely and make smarter agricultural decisions.

This project integrates:

IoT hardware

Real-time monitoring

Cloud connectivity

Automation

Data visualization

🎯 Project Objectives

Reduce manual monitoring efforts

Prevent overwatering & save water

Detect weather conditions like rain

Alert farmers on critical gas leaks

Provide full remote access via mobile app

Create a small prototype farm model to visualize real behavior

🧠 Features
✔ Real-Time Environment Monitoring

Soil moisture percentage

Temperature & humidity

Gas leak detection

Rain detection

✔ Smart Irrigation

Auto mode → Pump turns ON/OFF based on soil moisture

Manual mode → Control pump via mobile app

✔ Alerts

Gas leak alert

Rain alert

Pump running notifications

✔ Live Dashboard

All data visible on Blynk App widgets.

🛠 Hardware Components

ESP8266 / NodeMCU

DHT11 (Temperature & Humidity)

Soil Moisture Sensor

MQ-2 Gas Sensor

Rain Sensor Module

Relay Module (for pump control)

Water Pump (5V/12V)

Prototype mini farm setup

Jumper wires + Breadboard

🛰 Software & Tools

Arduino IDE

Blynk (IoT App)

C/C++ for ESP

Firebase / Thingspeak (optional for cloud logs)

👨‍💻 Circuit Connections
Component	ESP8266 Pin
DHT11	D2
Soil Moisture	A0
MQ-2 Gas Sensor	D5
Rain Sensor	D6
Water Pump (Relay)	D7
📲 Blynk App Setup
Virtual Pin	Function
V0	Temperature
V1	Humidity
V2	Soil Moisture
V3	Gas Alert
V4	Rain Status
V5	Pump Control
V6	Auto/Manual Mode
🧩 Code (ESP8266 + Blynk Full Working Code)

👉 The complete source code is available in /code/smart_agri_iot.ino
(Paste the code file separately in your repo — I can create your folder structure too.)

🌾 Prototype Explanation

A mini farm model was created using:

Soil patch

Plant area

Rain sensor mounted on top

Gas sensor beside the model

Water pipe connected to pump

ESP8266 placed at the center

The prototype helps visualize:

Live sensor values

Automatic water supply

Real-time alerts

📊 Working

Sensors continuously read values

ESP8266 sends data to Blynk app

If soil moisture < threshold → Pump turns ON

If rain is detected → System disables pump

Alerts sent to mobile for gas & weather

User can manually override pump anytime
