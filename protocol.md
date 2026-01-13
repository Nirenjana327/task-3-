# UART PROTOCOL
UART = Universal Asynchronous Receiver Transmitter
It is a serial communication protocol used to send data one bit at a time between two devices (like Arduino ↔ Arduino, Arduino ↔ PC).
Connections : Sender TX - Receiver RX
              Receiver RX - Sender TX
              GND - GND
TX-transmitter (sends data)
RX-receiver(receives data)
GND-common ground

Sender code:
void setup() { 
    Serial.begin(9600); // Start UART 
}
void loop() { 
    Serial.println("HELLO"); // Send message 
    delay(1000); 
}

Receiver code:
void setup() {
  Serial.begin(9600); 
}

void loop() {
  if (Serial.available() > 0) {   // Check if data arrived
    char c = Serial.read();        // Read one character
    Serial.print(c);               // Print it
  }
}

OUTPUT:HELLOHELLO

# I2C PROTOCOL
I²C = Inter-Integrated Circuit
I²C is a communication method that lets many electronic chips talk to each other using only two shared wires.
Connections :A4-A4(SDA-Data)
             A5-A5(SCL-Clock)
             GND-GND
             5V-5V
             
MASTER CODE:
#include <Wire.h>

void setup() {
  Wire.begin();           // I2C as Master
}

void loop() {
  Wire.beginTransmission(8);  // Address of Slave
  Wire.write("HELLO");        // Send word
  Wire.endTransmission();     // Send data
  delay(1000);
}

SLAVE CODE:
#include <Wire.h>

void setup() {
  Serial.begin(9600);
  Serial.println("SLAVE STARTED");

  Wire.begin(8);                  // I2C slave address
  Wire.onReceive(receiveEvent);   // Register receive handler
}

void receiveEvent(int howMany) {
  Serial.print("Received: ");
  while (Wire.available()) {
    char c = Wire.read();
    Serial.print(c);
  }
  Serial.println();
}

FLOW :Arduino starts
Prints SLAVE STARTED
Waits 
Master sends data
receiveEvent() runs automatically
Data is printed on Serial Monitor

OUTPUT:
SLAVE STARTED
HELLO
SLAVE STARTED
HELLO

# SPI PROTOCOL
SPI = Serial Peripheral Interface
SPI is a communication protocol used when one main device (Master) talks to one or more helper devices (Slaves).
Connections :D13-D13
             D12-D12
             D11-D11
             D10-D10
             GND-GND
             5V-5V
MOSI → OUTPUT
SCK → OUTPUT
MISO → INPUT 
Slave only talks when CS is LOW

MASTER CODE:
#include <SPI.h>

const char message[] = "HELLO";

void setup() {
  pinMode(10, OUTPUT);       // CS pin
  digitalWrite(10, HIGH);    // Deselect slave
  SPI.begin();               // Start SPI as Master
}

void loop() {
  digitalWrite(10, LOW);     // Select Slave

  for (int i = 0; i < 5; i++) {
    SPI.transfer(message[i]);  // Send one character
    delay(10);                 // Small delay
  }

  digitalWrite(10, HIGH);    // Deselect Slave
  delay(2000);
}

SLAVE CODE:
#include <SPI.h>

void setup() {
  Serial.begin(9600);
  pinMode(MISO, OUTPUT);   // Required for slave
  SPCR |= _BV(SPE);  // Enable SPI
  SPI.attachInterrupt(); // enables interrupt
}


ISR (SPI_STC_vect){
    char c = SPDR;         // Read received byte
    Serial.print(c);       // Print character
  }
void loop() {
}

OUTPUT:
HELLO
HELLO
HELLO

# SPI MULTIPLE NODES 
Here their is one master and two slaves
Connections :D13-D13
             D12-D12
             D11-D11
             D10-D10
             D9-D9
             GND-GND
             5V-5V
             
MASTER CODE: 
#include <SPI.h>

#define CS1 10   // Slave 1
#define CS2 9    // Slave 2

void setup() {
  Serial.begin(9600);

  pinMode(CS1, OUTPUT);
  pinMode(CS2, OUTPUT);

  digitalWrite(CS1, HIGH);
  digitalWrite(CS2, HIGH);

  SPI.begin();   // Master mode
}

void loop() {

  // Talk to SLAVE 1
  digitalWrite(CS1, LOW);
  byte r1 = SPI.transfer(11);
  digitalWrite(CS1, HIGH);

  Serial.print("Slave 1 replied: ");
  Serial.println(r1);

  delay(1000);

  // Talk to SLAVE 2
  digitalWrite(CS2, LOW);
  byte r2 = SPI.transfer(15);
  digitalWrite(CS2, HIGH);

  Serial.print("Slave 2 replied: ");
  Serial.println(r2);

  delay(2000);
}

SLAVE1 CODE:
#include <SPI.h>

volatile byte received;

void setup() {
  Serial.begin(9600);
  pinMode(MISO, OUTPUT);
  SPCR |= _BV(SPE);       // Enable SPI
  SPI.attachInterrupt();
}

ISR (SPI_STC_vect) {
  received = SPDR;
  SPDR = received + 1;   // Reply
}

void loop() {
  Serial.print("Slave 1 got: ");
  Serial.println(received);
  delay(500);
}

SLAVE2 CODE:
#include <SPI.h>

volatile byte received;

void setup() {
  Serial.begin(9600);
  pinMode(MISO, OUTPUT);
  SPCR |= _BV(SPE);
  SPI.attachInterrupt();
}

ISR (SPI_STC_vect) {
  received = SPDR;
  SPDR = received + 2;   // Different reply
}

void loop() {
  Serial.print("Slave 2 got: ");
  Serial.println(received);
  delay(500);
}

OUTPUT:
Slave 1 replied: 12
Slave 2 replied: 17
Slave 1 replied: 12
Slave 2 replied: 17

Master controls clock & CS
Slaves stay silent unless selected
SPI sends data byte-by-byte
Each slave can behave differently
Same MOSI/MISO/SCK shared by all
