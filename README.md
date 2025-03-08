# Smart-bridge
#include <DHT.h>
#include <LiquidCrystal.h>

// Define pins
#define DHTPIN 7     // DHT11 sensor pin
#define DHTTYPE DHT11   // DHT11 sensor type
#define FANPIN 8     // DC fan pin (connected to transistor base)
const int lcdRS = 12, lcdEN = 11, lcdD4 = 5, lcdD5 = 4, lcdD6 = 3, lcdD7 = 2; // LCD pins

// Create objects
DHT dht(DHTPIN, DHTTYPE);
LiquidCrystal lcd(lcdRS, lcdEN, lcdD4, lcdD5, lcdD6, lcdD7);

// Threshold temperature for fan control
const int temperatureThreshold = 30; // Change this as needed

void setup() {
  Serial.begin(9600);
  dht.begin();
  lcd.begin(16, 2);
  pinMode(FANPIN, OUTPUT);
  digitalWrite(FANPIN, LOW); // Ensure fan is off initially
  lcd.print("Temp: ");
  lcd.setCursor(0, 1);
  lcd.print("Hum: ");
}

void loop() {
  // Read temperature and humidity
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  // Check for reading errors
  if (isnan(humidity) || isnan(temperature)) {
    Serial.println("Failed to read from DHT sensor!");
    lcd.clear();
    lcd.print("DHT Error");
    delay(2000);
    lcd.clear();
    lcd.print("Temp: ");
    lcd.setCursor(0, 1);
    lcd.print("Hum: ");
    return;
  }

  // Display temperature and humidity on LCD
  lcd.setCursor(6, 0);
  lcd.print(temperature);
  lcd.print(" C");
  lcd.setCursor(5, 1);
  lcd.print(humidity);
  lcd.print(" %");

  // Control fan based on temperature
  if (temperature > temperatureThreshold) {
    digitalWrite(FANPIN, HIGH); // Turn fan on
  } else {
    digitalWrite(FANPIN, LOW);  // Turn fan off
  }

  // Print data to serial monitor for debugging
  Serial.print("Humidity: ");
  Serial.print(humidity);
  Serial.print(" %, Temperature: ");
  Serial.print(temperature);
  Serial.println(" *C");

  delay(2000); // Delay before next reading
}
