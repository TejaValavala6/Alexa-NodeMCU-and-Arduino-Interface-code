# Alexa-NodeMCU-and-Arduino-Interface-code
Use this code to interface the alexa and nodeMCU and Arduino. You have to dump this code in ESP8266 (NodeMCU) in order to interface these three components.
//NODEMCU_CODE

#include<SoftwareSerial.h> //Included SoftwareSerial Library
SoftwareSerial s(12,13); // This function is used to interface the nodeMCU and Arduino Due in order to send and recieve the data
#ifdef ARDUINO_ARCH_ESP32
#include<WiFi.h>  
#else
#include<ESP8266WiFi.h> //This Header file is used to connect the ESP8266 to the WIFI
#endif

#include<Espalexa.h> //This Header File is uused to interface the Alexa and ESP8266

boolean connectWifi(); //Boolean function used to know whether the wifi is successfully connected or not

//CALL BACK FUNCTIONS
void patientCondition(uint8_t value);
void patientTemperature(uint8_t value);
void patientPulse(uint8_t value);

//DECLARING WIFI NAME AND PASSWORD
const char *ssid =  "Satya@";     // replace with your wifi ssid and wpa2 key
const char *password =  "satya815";


//DEVICE NAMES
String Device1="Patient one condition";
String Device2="Patient Temperature";
String Device3="Patient Pulse";

boolean wifiConnected = false;

Espalexa espalexa; //espalexa is the object of the Espalexa class

void setup() {
  // put your setup code here, to run once:
  Serial.begin(115200);                    //Declaring the baud rate
  s.begin(9600);

  wifiConnected = connectWifi();           //calling the connectWifi(); function


  //IF WIFI IS SUCCESSFULLY CONNECTED THE DIVICES TO ALEXA IS ADDED
  if(wifiConnected){
    espalexa.addDevice(Device1,patientCondition);   
    espalexa.addDevice(Device2,patientTemperature);
    espalexa.addDevice(Device3,patientPulse);
    espalexa.begin();
    }
  else{
    while(1){
    Serial.println("Cannot connect to WiFi. Please check data and reset the ESP.");  //If WIFI is not connected
    delay(2500);
    }
    }
    
}

void loop() {
  // put your main code here, to run repeatedly:
  espalexa.loop();
  delay(1);

  
}



//DEFINING CALLBACK FUNCTIONS
void patientCondition(uint8_t value){
    if(value == 255){ //CONTROLLING THE DEVICE 
      s.write(1); //SENDING DATA TO ARDUINO DUE THROUGH TRANSMITTER
      Serial.println("Patient one condintion ON");
      }
      else if(value == 0)
      {
       Serial.println("Patient one Condition OFF");
      }
  }
void patientTemperature(uint8_t value){
  if(value == 255){
      s.write(2); //SENDING DATA TO ARDUINO DUE THROUGH TRANSMITTER
      Serial.println("Patient one Temperature ON");
    }
  else if(value == 0){
      Serial.println("Patient one Temperature OFF");
    }
  }
void patientPulse(uint8_t value){
  if(value == 255){
      s.write(3); //SENDING DATA TO ARDUINO DUE THROUGH TRANSMITTER
      Serial.println("Patient one Pulse ON");
    }
  else if(value==0){
      Serial.println("Patient one Pulse OFF");
    }
  }

//CONNECTING WIFI
boolean connectWifi(){
  boolean state = true;
  int i=0;
  Serial.begin(9600);
  delay(10);

  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid,password);

  Serial.println("");
  Serial.println("Connecting to WiFi");
 
 // Wait for connection
  Serial.print("Connecting...");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
    if (i > 20) {
      state = false; 
      break;
      }
    i++;
  }
  Serial.println("");
  if (state) {
   Serial.print("Connected to ");
   Serial.println(ssid);
   Serial.print("IP address: ");
   Serial.println(WiFi.localIP());
  }
 else {
   Serial.println("Connection failed.");
  }
  return state; 
  } 
