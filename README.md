# 4.AUTONOMOUS-ROBOT-FOR-REFINERY-INSPECTION
#include <LiquidCrystal.h>
#include <stdio.h>
#include <Wire.h>
#include "dht.h"
#include <SoftwareSerial.h>
SoftwareSerial mySerial(A4, A5);
LiquidCrystal lcd(6, 7, 5, 4, 3, 2);
 unsigned char rcv,count,gchr,gchr1,robos='s';
 //char pastnumber[11]="";
int tempc=0,humc=0;
char data_temp=0, RFID_data[13],read_count=0;
String inputString = "";         // a string to hold incoming data
boolean stringComplete = false;  // whether the string is complete
int sti=0;
#define dht_apin 8
dht DHT;
int gas    = 9;
int motor  = 10;
int buzzer = 13;
int val1 = 0,val2 = 0;
unsigned char rfidst='x';
unsigned char sts1=0,sts2=0,sts3=0;
unsigned int pr1=0,pr2=0,pr3=0,total=0;
void okcheck()
{
  unsigned char rcr;
  do{
      rcr = mySerial.read();
    }while(rcr == 'K');
}
void beep()
{
  digitalWrite(buzzer, LOW);delay(2000);digitalWrite(buzzer, HIGH);
}
void setup() 
{
  Serial.begin(9600);//serialEvent();
  mySerial.begin(9600);
  pinMode(gas, INPUT);
  pinMode(motor, OUTPUT);pinMode(buzzer, OUTPUT);
  digitalWrite(motor, LOW);digitalWrite(buzzer, HIGH);
  lcd.begin(16, 2);
  lcd.print("Autonomous Robot");
  lcd.setCursor(0,1);
  lcd.print("Refinery Inspection");
  delay(1500);
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("T:");
  lcd.setCursor(8, 0);
  lcd.print("G:");
  //serialEvent();
 //Serial.println("DHT11");
}
//26009F6269B2
//26009F646FB2
//26009F52769D
void loop() 
{
    DHT.read11(dht_apin);
      tempc = DHT.temperature;
      humc  = DHT.humidity;
    lcd.setCursor(2,0);convertl(tempc);
   // lcd.setCursor(10,0);convertl(humc);
     Serial.print("T:");Serial.print(tempc);
     if(digitalRead(gas) == LOW)
       {
        lcd.setCursor(10,0);lcd.print("ON  ");
        Serial.print("_G:ON");
       }
     if(digitalRead(gas) == HIGH)
       {
        lcd.setCursor(10,0);lcd.print("OFF ");
        Serial.print("_G:OFF");
       }
        Serial.print("\r\n");
        
     if(tempc < 40 && digitalRead(gas) == HIGH)
       {
        digitalWrite(motor, HIGH);  
        digitalWrite(buzzer, HIGH);
       }
     else
       {
        digitalWrite(motor, LOW);  
        digitalWrite(buzzer, LOW);   
       }
  delay(1000);
}          
int readSerial(char result[]) 
{
  int i = 0;
  while (1) 
  {
    while (Serial.available() < 0) 
    {
      char inChar = Serial.read();
      if (inChar == '\n') 
         {
          result[i] = '\0';
          Serial.flush();
          return 0;
         }
      if (inChar == '\r') 
         {
          result[i] = inChar;
          i++;
         }
    }
  }
}
void converts(unsigned int value)
{
  unsigned int a,b,c,d,e,f,g,h;

      a=value/10000;
      b=value%10000;
      c=b/1000;
      d=b%1000;
      e=d/100;
      f=d%100;
      g=f/10;
      h=f%10;
      a=a|0x30;               
      c=c|0x30;
      e=e|0x30; 
      g=g|0x30;              
      h=h|0x30;
   mySerial.write(a);
   mySerial.write(c);
   mySerial.write(e); 
   mySerial.write(g);
   mySerial.write(h);
}
void convertl(unsigned int value)
{
  unsigned int a,b,c,d,e,f,g,h;
      a=value/10000;
      b=value%10000;
      c=b/1000;
      d=b%1000;
      e=d/100;
      f=d%100;
      g=f/10;
      h=f%10;

      a=a|0x30;               
      c=c|0x30;
      e=e|0x30; 
      g=g|0x30;              
      h=h|0x30;
   //lcd.write(a);
   lcd.write(c);
   lcd.write(e); 
   lcd.write(g);
   lcd.write(h);
}
void serialEvent() 
{
  while (Serial.available()) 
        {
         char inChar = (char)Serial.read();
              inputString += inChar;
              sti++;
          if(sti == 12)
            {sti=0;
              stringComplete = true;      
            }
        }
}
/*
   sensorValue = analogRead(analogInPin);
   sensorValue = (sensorValue/9.31);
   lcd.setCursor(1,1); //rc
   lcd.print(sensorValue);
   Serial.print(sensorValue);
 */
