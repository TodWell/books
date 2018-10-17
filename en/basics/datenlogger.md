# Data logger - Storing to SD card

In the following short manual, we describe the readout of data from sensors and storing it on a SD card.

## Materials
- Arduino Uno
- senseBox-Shield
- microSD card
- Sensor(s) (as required)

## Basics

The [senseBox-Shield](shields.md) is equiped with a microSD card slot.
The sensor data are read and subsequenly written into a `.csv` (comma-seperated-value) file.

## Template
The following code template contains all components needed for writing on a SD card.
Now, only the readout has to be integrated.

```arduino
/*
  senseBox data logger template
  The readaout of the respective sensor has to take place in the loop. For that, it has to be adjusted manually.
*/

#include <SPI.h> // important libraries for storing on SD cards
#include <SD.h>

const int chipSelect = 4;

void setup() {
  Serial.begin(9600);
  while (!Serial) { ; }

  Serial.print("Initialisiere SD-Karte");

  // Check, if a sd card is pluged in.
  if (!SD.begin(chipSelect)) {
    Serial.println("Card not found");
    return;
  }
  Serial.println("Card initlaized successfully");
}

void loop() {
  // Readout of the sensor. Please, adjust!
  float measurement = //......;


  // öffne die datei datalog.txt auf der SD-Karte mit schreibrechten
  File dataFile = SD.open("datalog.txt", FILE_WRITE);


  if (dataFile) {
    // If file is open, write measurement on sd card. Then close the file again.
    dataFile.print(measurement); // Write measurement into open file
    dataFile.println(";"); // Define seperation character
    dataFile.close();
  } else {
    // If file can not be opened, show error message
    Serial.println("Error while opening file!");
  }
  delay(1000);
}
```

## Example: HDC100X

The readout of sensors should take place within the `loop()`-function after opening the file.
The information for reading out files can be found in [Datenblättern](../downloads.md).
Adjust the code according to the templates provided by the manufacturers.
Mesurements are writen into the CSV-file with `dataFile.print(Measurement);`.
`ln` creates a line break.
With the command `delay(Mikrosekunden)`, you can adjust the storing interval.

In the following, you find a template for the HDC100x.

```arduino
/*
  senseBox HDC100x data logger
  Connecting HDC100x via I2C to Arduino
  SDA - A4, SCL - A5, VCC - 5V, GND - GND
*/

#include <SPI.h> // important libraries for storing on SD cards
#include <SD.h>
#include <Wire.h> //I2C Library
#include <HDC1000.h> //Library for HDC100x

HDC1000 mySensor(0x43); //IC2 adress of HDC1000

const int chipSelect = 4; //

void setup() {
  Serial.begin(9600);
  while (!Serial) { ; }

  Serial.print("Initialisiere SD-Karte");

  // Check, if a sd card is pluged in.
  if (!SD.begin(chipSelect)) {
    Serial.println("Card not found");
    return;
  }
  Serial.println("Card initlaized successfully");

  mySensor.begin();
}

void loop() {

  // Readout of the sensor and writing into the file.
  File dataFile = SD.open("datalog.txt", FILE_WRITE);

  float temp = mySensor.getTemp(); // Read out temperature
  float humi = mySensor.getHumi();
  Serial.println(temp); // Show temperature in the serial monitor
  Serial.println(humi);

  if (dataFile) {
    // If file is open, write measurement
    dataFile.print(temp);
    dataFile.print(";");
    dataFile.print(humi);
    dataFile.println(";");
    dataFile.close();
  } else {
    // If file can not be opened, show error message
    Serial.println("Fehler beim Öffnen!");
  }

  delay(1000); //Interval of readout and storing
}
```
## Sensors for capturing temperature, humidity and ari pressure
These instruments write their measurements into a csv-file on an sd card.
The sensors are connected via I²C to Arduino.
The code can be found [here](https://raw.githubusercontent.com/sensebox/resources/master/code/senseBox_Datenlogger_T_H_P.ino).
