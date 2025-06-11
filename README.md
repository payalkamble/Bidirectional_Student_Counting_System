#define BLYNK_PRINT Serial
#define BLYNK_TEMPLATE_ID "TMPL3ZI2dCo4e"
#define BLYNK_TEMPLATE_NAME "iot"
#define BLYNK_AUTH_TOKEN "HIfRZQh1CpYQPEOfVJbLssjx0o4ByG0z"
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Blynk.h>
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>

 
#define SCREEN_WIDTH 128    // OLED display width, in pixels
#define SCREEN_HEIGHT 64    // OLED display height, in pixels
#define OLED_RESET -1       // Reset pin # (or -1 if sharing Arduino reset pin)
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

char auth[] = "Auth_token";       // You should get Auth Token in the Blynk App.
char ssid[] = "wifi_name";                       // Your WiFi credentials.
char pass[] = "wifi_password";
 
#define inSensor 14 //D5
#define outSensor 12 //D6
 
int inStatus;
int outStatus;
 
int countin = 0;
int countout = 0;
 
int in;
int out;
int now;
#define relay 0 //D3
WidgetLED light(V0);

 
void setup()
{
 
  Serial.begin(115200);
  Blynk.begin(auth, ssid, pass);
  delay(1000);  // wait a second
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C); //initialize with the I2C addr 0x3C (128x64)
  display.display();
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(WHITE);
  display.setCursor(0,0);
  delay(2000);
  pinMode(inSensor, INPUT);
  pinMode(outSensor, INPUT);
  pinMode(relay, OUTPUT);
  digitalWrite(relay, HIGH);

  Serial.println("Student Counter Demo");
  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(WHITE);
  display.setCursor(20, 20);
  display.print("Student");
  display.setCursor(20, 40);
  display.print("Counter");
  display.display();
  delay(3000);
}
 
void loop()
{
  Blynk.run(); // Initiates Blynk
  inStatus =  digitalRead(inSensor);
  outStatus = digitalRead(outSensor);
  if (inStatus == 0)
  {
    in = countin++;
  }
 
  if (outStatus == 0)
  {
    out = countout++;
  }
 
  now = in - out;
 
  if (now <= 0)
  {
    digitalWrite(relay, HIGH);
    light.off();
    display.clearDisplay();
    display.setTextSize(2);
    display.setTextColor(WHITE);
    display.setCursor(0, 15);
    display.print("No Student");
    display.setCursor(5, 40);
    display.print("Light Off");
    display.display();
    Serial.println("No Students! Light Off");
    delay(500);
  }
  else
  {
    digitalWrite(relay, LOW);
    light.on();
 
    display.clearDisplay();
    display.setTextColor(WHITE);
 
    display.setTextSize(1);
    display.setCursor(15, 0);
    display.print("Current Student");
    display.setTextSize(2);
    display.setCursor(50, 15);
    display.print(now);
 
    display.setTextSize(1);
    display.setCursor(0, 40);
    display.print("IN: ");
    display.print(in);
 
    display.setTextSize(1);
    display.setCursor(70, 40);
    display.print("OUT: ");
    display.print(out);
 
    display.display();
    Serial.print("Current Student: ");
    Serial.println(now);
    Serial.print("IN: ");
    Serial.println(in);
    Serial.print("OUT: ");
    Serial.println(out);
    delay(500);
  }
  Blynk.virtualWrite(V1, in); // Student In
  Blynk.virtualWrite(V2, out); // Student Out
  Blynk.virtualWrite(V3, now); // Current Students
  delay(1000);
}
