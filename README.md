# IoT-Based-Beverage-Quality-Monitoring-System
#include <Wire.h>
#include <EEPROM.h>
#include "GravityTDS.h"
float ph;
int turbidity;
#define TdsSensorPin A1
GravityTDS gravityTds;
float temperature = 25,tdsValue = 0;
void setup() {
 // put your setup code here, to run once:
 Serial.begin(9600);
 gravityTds.setPin(TdsSensorPin);
 gravityTds.setAref(5.0); //reference voltage on ADC, default 5.0V on Arduino UNO
 gravityTds.setAdcRange(1024); //1024 for 10bit ADC;4096 for 12bit ADC
 gravityTds.begin(); //initialization
}
void loop() {
 // put your main code here, to run repeatedly:
 ph = phRead(A0);
 gravityTds.setTemperature(temperature); // set the temperature and execute temperature compensation
 gravityTds.update(); //sample and calculate
 tdsValue = gravityTds.getTdsValue(); // then get the value
 turbidity = turbidityRead(A2);
 
 Serial.print(ph);
 Serial.print(",");
 Serial.print(tdsValue,0);
 Serial.print(",");
 Serial.print(turbidity);
 Serial.println();
 delay(2000);
 
}
float phRead(int phpin){
 float calibration_value = 21.70-0.7;
 int phval = 0; 
 unsigned long int avgval; 
 int buffer_arr[10],temp;
 float ph_act;
 for(int i=0;i<10;i++) 
{ 
buffer_arr[i]=analogRead(phpin);
delay(30);
}
for(int i=0;i<9;i++)
{
12
for(int j=i+1;j<10;j++) {
if(buffer_arr[i]>buffer_arr[j]) {
temp=buffer_arr[i];
buffer_arr[i]=buffer_arr[j];
buffer_arr[j]=temp; }}}
avgval=0;
for(int i=2;i<8;i++)
avgval+=buffer_arr[i];
float volt=(float)avgval*5.0/1024/6; 
 ph_act = 
-5.70 * volt + calibration_value;
return ph_act; }
int turbidityRead(int tbpin){
 float sense=analogRead(tbpin);
 int turbidity1 = map(sense, 0,640, 100, 0);
 return turbidity1; }
