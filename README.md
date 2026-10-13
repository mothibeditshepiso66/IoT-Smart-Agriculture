# Smart Agriculture IoT System

A virtual IoT smart agriculture project that uses an ESP32 simulation in
Wokwi to collect environmental and soil readings, publish sensor data
through MQTT, and display the information in a Node-RED dashboard.

The project is designed to help users monitor growing conditions and
observe when soil moisture may require watering. It can be demonstrated
without physical hardware by running the Wokwi simulation.

## Project Features

-   Reads temperature and humidity from a DHT22 sensor.
-   Reads pressure from a BMP180/BMP085-compatible barometric sensor.
-   Reads soil moisture from a potentiometer configured as a simulated
    soil-moisture sensor.
-   Displays readings on an I2C LCD, if included in the circuit.
-   Uses an ESP32 and MQTT to send readings to Node-RED.
-   Displays incoming sensor readings on a Node-RED dashboard.
-   Uses an LED as a simulated pump indicator when pump-control logic is
    enabled.

> **Important:** The soil-moisture potentiometer is a simulation input,
> not a real soil probe. The LED represents a pump; it does not operate
> a physical water pump.

## Project Files

Place these files in the root of your GitHub repository:

  -----------------------------------------------------------------------
  File                                Purpose
  ----------------------------------- -----------------------------------
  `flows.json`                        Exported Node-RED flow, including
                                      the dashboard and MQTT nodes

  `diagram.json`                      Wokwi circuit layout and component
                                      connections

  `sketch.ino`                        ESP32 Arduino code

  `README.md`                         Project description and setup
                                      instructions

  `processed_sensor_dataset.csv`      Optional recorded or processed
                                      sensor dataset, if you are
                                      including it in the project
  -----------------------------------------------------------------------

Your filenames may differ if you saved your files under different names.

## System Overview

1.  The Wokwi ESP32 simulation reads the configured sensor values.
2.  The ESP32 formats the readings as a message, typically JSON.
3.  The ESP32 publishes the message to an MQTT topic.
4.  Node-RED subscribes to that topic and processes the incoming
    message.
5.  The Node-RED dashboard presents the readings in widgets such as text
    values, gauges, or charts.

``` text
Wokwi sensors
     |
     v
ESP32 (sketch.ino)
     |
     | MQTT sensor messages
     v
MQTT broker
     |
     v
Node-RED flow (flows.json)
     |
     v
Node-RED dashboard
```

## Requirements

-   A web browser and internet connection.
-   A Wokwi account or access to the Wokwi simulator.
-   Node.js and Node-RED installed locally, or access to an existing
    Node-RED instance.
-   An MQTT broker that both the ESP32 simulation and Node-RED can
    reach.
-   The Arduino libraries used by the sketch, such as:
    -   `PubSubClient`
    -   `Adafruit BMP085 Library` (if used by the sketch)
    -   `DHT sensor library`
    -   `Adafruit Unified Sensor` (required by many DHT library setups)
    -   `ArduinoJson`
    -   `LiquidCrystal I2C` (if the LCD is included)

Install the libraries through the Arduino Library Manager or the library
manager supported by your development environment. Use the versions
compatible with your sketch.

## 1. Open the Wokwi Project

1.  Open your Wokwi project.
2.  Check that `diagram.json` contains the correct components and
    wiring.
3.  Open `sketch.ino` and confirm that the pin definitions match the
    circuit.
4.  Check the sensor and output connections before starting the
    simulation.
5.  Start the simulation and open the Serial Monitor to check for
    startup messages and sensor readings.

### Pin configuration

Confirm these pins against your actual `diagram.json` and `sketch.ino`
before running:

  Component                            ESP32 pin / connection
  ------------------------------------ ------------------------
  I2C SDA                              GPIO 21
  I2C SCL                              GPIO 22
  DHT22 data                           GPIO 15
  Soil-moisture potentiometer output   GPIO 34
  Pump indicator LED                   GPIO 26

The I2C sensor and LCD may share GPIO 21 and GPIO 22. If your current
project uses different pins, keep the wiring and code consistent rather
than copying this table blindly.

## 2. Configure MQTT

The ESP32 and Node-RED must use the same MQTT broker address and topic.

1.  Open `sketch.ino`.
2.  Find the Wi-Fi, MQTT broker, port, and topic settings.
3.  Set them to values supported by your Wokwi simulation and Node-RED
    environment.
4.  In Node-RED, configure the MQTT input node to use the same broker
    and subscribe to the same topic.
5.  Deploy the Node-RED flow and check that the MQTT input node
    connects.

**Networking note:** `localhost` or `127.0.0.1` refers to the device
running the client and usually will not refer to your computer from
inside a separate simulator. Use a broker address that is reachable from
both sides. Do not publish passwords, tokens, or other private
credentials in a public repository.

## 3. Import the Node-RED Flow

1.  Open the Node-RED editor in your browser.
2.  Select the menu in the top-right corner.
3.  Choose **Import**.
4.  Select or paste the contents of `flows.json`.
5.  Import the flow into your workspace.
6.  Check the MQTT broker settings and any dashboard nodes.
7.  Select **Deploy**.

If the dashboard nodes are missing, install the dashboard package
required by the flow. Older flows may use `node-red-dashboard`; newer
flows may use a different dashboard package. Install the package that
matches the node types shown in your imported flow.

## 4. Open the Node-RED Dashboard

The dashboard URL depends on your Node-RED setup and dashboard package.
Common defaults are:

-   **Classic `node-red-dashboard`:** `http://localhost:1880/ui`
-   **Other dashboard packages:** use the dashboard path configured by
    that package or by your Node-RED instance.

Replace `localhost` with the address of the computer or server running
Node-RED if you are opening it from another device.

If the dashboard is blank or shows no values:

1.  Confirm that the flow has been deployed.
2.  Check that the MQTT broker is connected.
3.  Verify that the ESP32 publishes to the topic used by the MQTT input
    node.
4.  Open the Node-RED debug sidebar and inspect incoming messages.
5.  Confirm that the dashboard widgets are connected to the correct
    processing nodes.

## 5. Run the Full Simulation

1.  Start the Wokwi simulation.
2.  Check the Serial Monitor for sensor readings and MQTT connection
    messages.
3.  Start or confirm the MQTT broker.
4.  Deploy the Node-RED flow.
5.  Open the dashboard in your browser.
6.  Change the soil-moisture potentiometer in Wokwi to simulate
    different soil conditions.
7.  Observe the dashboard values and the simulated pump indicator, if
    pump-control logic is implemented.

The dashboard can only display values that the ESP32 actually publishes
and the Node-RED flow successfully processes. Sensor names and JSON
property names must match between the sketch and the flow.

## Dataset

If you have a file such as `processed_sensor_dataset.csv`, add it to the
repository to document the recorded or processed readings. The CSV
dataset is separate from the live MQTT stream unless you explicitly add
a Node-RED or application feature that reads and displays the CSV.

When documenting the dataset, include: - What each column represents. -
The units used for each measurement. - Whether values are simulated,
measured, or processed. - Any cleaning or preprocessing performed.

Do not describe simulated readings as measurements from real farm
hardware.

## Troubleshooting

  -----------------------------------------------------------------------
  Problem                             What to check
  ----------------------------------- -----------------------------------
  Wokwi code does not compile         Check library names, installed
                                      versions, header files, and syntax
                                      errors

  Sensor shows an invalid reading     Check the component wiring, pin
                                      definitions, and sensor
                                      initialization

  MQTT does not connect               Verify broker address, port,
                                      connectivity, and credentials

  Node-RED receives no messages       Confirm the ESP32 topic matches the
                                      MQTT input topic

  Dashboard does not open             Confirm Node-RED is running and use
                                      the correct dashboard URL

  Dashboard widgets remain empty      Inspect the Debug sidebar and
                                      verify JSON property names

  Pump indicator does not change      Check the GPIO pin and the
                                      soil-moisture threshold logic
  -----------------------------------------------------------------------

## Safety and Scope

This is an educational simulation. The simulated LED is not a real pump
controller, and the project should not be treated as a field-ready
irrigation system without additional hardware, testing, and safety
controls.

## Future Improvements

-   Store readings in a database for historical analysis.
-   Add alerts for low soil moisture or unusual environmental
    conditions.
-   Add time-series charts and CSV export to the dashboard.
-   Evaluate irrigation thresholds using crop-specific requirements.
-   Connect tested physical sensors and a properly protected pump driver
    for a real deployment.

## License

Choose a license before redistributing the project. If you do not want
to grant reuse permissions yet, leave this section out until you have
decided.

## Author

Add your name, course, institution, and project date here if required
for your submission.
