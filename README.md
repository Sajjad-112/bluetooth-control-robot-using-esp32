# bluetooth-control-robot-using-esp32
#include <BluetoothSerial.h>
BluetoothSerial btSerial;
#define BT_NAME "ESP32BT-Sajjad" // Set bluetooth name
#define BUILTIN_LED 2 // LED is active low
boolean btConnected = false;
int left_wheelA = 27; int left_wheelB = 26;
int right_wheelA = 25; int right_wheelB = 33;
int EN_L = 14; int EN_R = 32;
const int freq = 5000;
const int Channel_1 =0;
const int Channel_2 = 1;
const int resolution=8;
int IR_SENSOR_L = 18;
int IR_SENSOR_C = 19;
int IR_SENSOR_R = 21;
void setup(){
pinMode(left_wheelA, OUTPUT); pinMode(left_wheelB, OUTPUT);
pinMode(right_wheelA, OUTPUT); pinMode(right_wheelB, OUTPUT);
// configure PWM functionalitites
ledcSetup(Channel_1, freq, resolution);
ledcSetup(Channel_2, freq, resolution);
// attach the channel to the GPIO to be controlled
ledcAttachPin(EN_L, Channel_1);
ledcAttachPin(EN_R, Channel_2);
delay(2000);
pinMode(BUILTIN_LED, OUTPUT);
Serial.begin(115200);
btSerial.begin(BT_NAME);Serial.println("ESP32 Bluetooth Mobile Robot");
Serial.println();
digitalWrite(BUILTIN_LED, HIGH);}
void loop(){
int times = 0;
if (btSerial.available()) {
int inChar = (char)btSerial.read();
if (btConnected == false) {
btConnected = true;
Serial.println("Bluetooth connected.");}
Serial.println(String("Char recevied: ")+inChar);
if (inChar == 49) {
Drive_Far();
// times = 1;
} else if (inChar == 50) {
Drive_Back();
//times = 2;
} else if (inChar == 51) {
//times = 4;
Turn_Right();
}else if (inChar == 52) {
Turn_Left();
//times = 8;
} else {
digitalWrite(BUILTIN_LED, LOW);}
Serial.println(String("Times: ")+times);
for (int i = 0; i<times; i++){
digitalWrite(BUILTIN_LED, LOW); // Turn on led
delay(500);
digitalWrite(BUILTIN_LED, HIGH);
delay(500);}}
}void Drive_Far(){
digitalWrite(left_wheelA, HIGH);
digitalWrite(left_wheelB, LOW);
digitalWrite(right_wheelA, HIGH);
digitalWrite(right_wheelB, LOW);
ledcWrite(Channel_1, 160);
ledcWrite(Channel_2, 190);
}
void Drive_Back(){
digitalWrite(left_wheelA, LOW);
digitalWrite(left_wheelB, HIGH);
digitalWrite(right_wheelA, LOW);
digitalWrite(right_wheelB, HIGH);
ledcWrite(Channel_1, 160); //130 on EN_L where as 150 on EN_R makes the
speed of both motors equal
ledcWrite(Channel_2, 190);
}
void Stop(){
digitalWrite(left_wheelA, LOW);
digitalWrite(left_wheelB, LOW);
digitalWrite(right_wheelA, LOW);
digitalWrite(right_wheelB, LOW);
delay(500);}
void Turn_Right(){
ledcWrite(Channel_1, 160); //to turn right speed of left wheel is increased
ledcWrite(Channel_2, 115);
digitalWrite(left_wheelA, HIGH);
digitalWrite(left_wheelB, LOW);
digitalWrite(right_wheelA, HIGH);
digitalWrite(right_wheelB, LOW);
// delay(200);
}
void Turn_Left(){
ledcWrite(Channel_1, 115); //to turnleft speed of Right wheel is increased
ledcWrite(Channel_2, 160);
digitalWrite(left_wheelA, HIGH);
digitalWrite(left_wheelB, LOW);
digitalWrite(right_wheelA, HIGH);
digitalWrite(right_wheelB, LOW);
//delay(200);
}
void Turn_Axial_L(){
ledcWrite(Channel_1, 115); //to turnleft speed of Right wheel is increased
ledcWrite(Channel_2, 140);
digitalWrite(left_wheelA, HIGH);
digitalWrite(left_wheelB, LOW);digitalWrite(right_wheelA, LOW);
digitalWrite(right_wheelB, HIGH);
}
void Turn_Axial_R(){
ledcWrite(Channel_1, 130); //to turn right speed of left wheel is increased
ledcWrite(Channel_2, 100);
digitalWrite(left_wheelA, LOW);
digitalWrite(left_wheelB, HIGH);
digitalWrite(right_wheelA, HIGH);
digitalWrite(right_wheelB, LOW);
}
void LFR(){
if(digitalRead(IR_SENSOR_L)==HIGH&&
digitalRead(IR_SENSOR_C)==LOW &&
digitalRead(IR_SENSOR_R)==HIGH){
Drive_Far();
Serial.print(digitalRead(IR_SENSOR_L));
Serial.print(" ");
Serial.print(digitalRead(IR_SENSOR_R));
Serial.println("Striaght !!");
}
// Stop();
if(digitalRead(IR_SENSOR_L)==HIGH &&
digitalRead(IR_SENSOR_C)==HIGH &&
digitalRead(IR_SENSOR_R)==LOW){
Turn_Axial_L();Serial.print(digitalRead(IR_SENSOR_L));
Serial.print(" ");
Serial.print(digitalRead(IR_SENSOR_R));
Serial.println("TURN RIGHT!!");
}
//Stop();
if(digitalRead(IR_SENSOR_L)==LOW &&
digitalRead(IR_SENSOR_C)==HIGH &&
digitalRead(IR_SENSOR_R)==HIGH){
Turn_Axial_R();
Serial.print(digitalRead(IR_SENSOR_L));
Serial.print(" ");
Serial.print(digitalRead(IR_SENSOR_R));
Serial.println("TURN LEFT!!");
}
}
void U_Turn_R(){
ledcWrite(Channel_1, 130); //to turn right speed of left wheel is increased
ledcWrite(Channel_2, 100);
digitalWrite(left_wheelA, HIGH);
digitalWrite(left_wheelB, LOW);
digitalWrite(right_wheelA, HIGH);
digitalWrite(right_wheelB, LOW);
// delay(200);
}
