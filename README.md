# ArduiCode
The irrigation module uses soil moisture sensors to monitor water levels and trigger a pump only when needed — conserving water efficiently.
#include <Servo.h>
#include <LiquidCrystal.h>
Servo myServo;

int u_sensor1=0;
int touch_sensor=0;
int WET=0;
int DRY=0;
int u_sensor2=0, object=0, moisture=0;
long duration1, distance1, duration2, distance2, average;
long aver[3];
LiquidCrystal lcd(8,9,10,11,12,13);      //RS,EN,D4,D5,D6,D7
#define ir_sensor 4
#define moisture_sensor A0 // moisture
#define buzzer 2
#define touch_sensor 7

#define trigPin1 A4       ////right
#define echoPin1 A5

#define trigPin2 A2       //// front
#define echoPin2 A3
int onetime=0,onetime1=0 ;

int value= 0;


void setup()
{
 Serial.begin(9600);
 lcd.begin(16, 2);                  //initializing LCD
 lcd.setCursor(0,0); 
 lcd.print("Automatic WASTE");
 lcd.setCursor(0,1); 
 lcd.print("Segregation sys");
 delay(3000);
 pinMode(WET,INPUT);
 pinMode(DRY,INPUT);
 pinMode(ir_sensor, INPUT);
 pinMode(moisture_sensor, INPUT);
pinMode(touch_sensor, INPUT);
 pinMode(buzzer, OUTPUT);
 pinMode(trigPin1, OUTPUT);
 pinMode(echoPin1, INPUT);
 pinMode(trigPin2, OUTPUT);
 pinMode(echoPin2, INPUT);
 myServo.attach(3);           // attaches the servo on pin 3 to the servo object
 myServo.write(90);
 delay(3000); 
}
 





void loop() 
{
 moisture=analogRead(moisture_sensor);
 Serial.print("moisture = ");
 Serial.println(moisture);
 delay(500);
 /////////////////////////////////////////////////////
 u_sensor1 = distance1;
 delay(10);
 u_sensor2 = distance2;    
  delay(10);
  duration1 = pulseIn(echoPin1, HIGH);
  distance1 = (duration1/2) / 29.1;
  
  duration2 = pulseIn(echoPin2, HIGH);
  distance2 = (duration2/2) / 29.1;
  Serial.println(u_sensor1);
  Serial.println(u_sensor2);
  
  int lvl1=(16-u_sensor1)*6.5;
  int lvl2=(16-u_sensor2)*6.5;
   Serial.println(lvl1);
  Serial.println(lvl2);
   if(lvl1<0)
    {lvl1=0;}
  if(lvl2<0)
    {lvl2=0;}
  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("moisture: ");
  lcd.setCursor(12,0); 
  lcd.print(moisture);
  delay(1000);
  lcd.clear();
  lcd.setCursor(0,1); 
  lcd.print("DL= ");
  lcd.setCursor(4,1); 
  lcd.print(lvl1);
  lcd.setCursor(8,1); 
  lcd.print("WL= ");
  lcd.setCursor(13,1); 
  lcd.print(lvl2);
  delay(1000);
  if(lvl1 <= 100 && lvl1 >= 70)
  {      
   if(onetime==0)
   {
     lcd.setCursor(0,0); 
  lcd.print("-send msg-");
    digitalWrite(buzzer,HIGH); 
   
    digitalWrite(buzzer,LOW);
    onetime=1;
   }
  }
   
  else if (lvl2 <= 100 && lvl2 >= 70)
  {
    if(onetime1==0)
    {
      lcd.setCursor(0,0); 
  lcd.print("-send msg-");
     digitalWrite(buzzer,HIGH); 
     
     digitalWrite(buzzer,LOW);
     onetime1=1; 
    }
  }
else
{
 onetime=0;
 onetime1=0;
}
/////////////////////////////////////////////////////
  object=digitalRead(ir_sensor);
  moisture=analogRead(moisture_sensor);
  if(object==HIGH)
  {
   moisture=analogRead(moisture_sensor);
   lcd.setCursor(0,0); 
   lcd.print("-GARBAGE SENSE-");
   digitalWrite(buzzer,HIGH);
   delay(150);
   digitalWrite(buzzer,LOW);
   delay(1000);
   moisture=analogRead(moisture_sensor);
   Serial.print("moisture = ");
   Serial.println(moisture);
   delay(500);
   moisture=analogRead(moisture_sensor);
   Serial.print("moisture = ");
   Serial.println(moisture);
   delay(500);
   //delay(2000);
   if(moisture>=40)                         
   {
    lcd.setCursor(0,0); 
    lcd.print(" WET  GARBAGE");
    myServo.write(45);
    delay(5000);
    myServo.write(87);
    delay(2000);
    Serial.println("wet");
   }
   else if(moisture<100)
   {  
    lcd.setCursor(0,0); 
    lcd.print(" dry  GARBAGE");
    myServo.write(135); 
    delay(5000);
    myServo.write(87);
    delay(2000);
    Serial.println("moisture");
    lcd.clear();
   } 
  }
}
