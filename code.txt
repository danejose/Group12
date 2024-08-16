#include <SoftwareSerial.h>
#include <Keypad.h>
#include<Servo.h>;
Servo servo;
SoftwareSerial ss(8,7); // RX, TX
 void sendGPSLocationViaSMS();
#define relay 3
String data;
int f=0,n,num=0;
String address;
const byte ROWS = 4; //four rows
const byte COLS = 4; //four columns

char keys[ROWS][COLS] = {
  {'1','2','3','A'},
  {'4','5','6','B'},
  {'7','8','9','C'},
  {'*','0','#','D'}
};

byte rowPins[ROWS] = {2,4,5,9}; //connect to the row pinouts of the keypad
byte colPins[COLS] = {10,11,12,13
};  //connect to the column pinouts of the keypad
#define IR1 A5
#define IR2 A4
Keypad keypad = Keypad( makeKeymap(keys), rowPins, colPins, ROWS, COLS );
void setup() 
{
  Serial.begin(9600);
  ss.begin(9600); 
  ss.println("AT"); 
  delay(1000);
  ss.println("AT+CMGF=1");
  delay(1000);

  pinMode(relay,OUTPUT);
  servo.attach(6);
  pinMode(IR1,INPUT);
  pinMode(IR2,INPUT);
}

void loop() 
{
  int sec1=digitalRead(IR1);
  int sec2=digitalRead(IR2);
  Serial.print("sec1:");
  Serial.println(sec1);
  Serial.print("sec2:");
  Serial.println(sec2);
  
   if(sec1==1&&sec2==0)
  {
    
   servo.write(0);
   delay(4000);
  }
  if(sec1==0&&sec2==1)
  {
    
    servo.write(180);
    delay(4000);
  }
  else 
  {
    servo.write(90);
  }
 
  if(f==0)
  {
  if(Serial.available()>0)
  {
    
    data=Serial.readStringUntil('\n');
    
         if(data=="a")
            {
              Serial.println(data);
              digitalWrite(relay,HIGH);
              
              address="adishankara";
              
              sendGPSLocationViaSMS();
              f=1;
              
            }
            Serial.println(f);
    }
  }
    
  
    
  else if(f==1)
  {
    
          char key = keypad.getKey();
          if(key)
          {
            Serial.print("Key Pressed : ");
            Serial.println(key);
            n=key;
            if(key>='0'&&key<='9')
                {
                  num=num*10+(n-48);
                }
            Serial.print("key pressed:");
            Serial.println(num);
           if(key=='#')
                {
                  if(num==123)
                 {
                   Serial.println("access");
                    digitalWrite(relay,LOW);
                    f=0;
                    num=0;
                  } 
                  else
                  {
                    num=0;
                  }
               }
            }    
          }



}

void sendGPSLocationViaSMS() {
    // String message = "Latitude: " + String(latitude, 6) + " Longitude: " + String(longitude, 6)+"password:123#";
    String message="Adishankara ,password:123#";
     Serial.println(message);
    int  flag=1;
      if(flag==1)
      {
        Serial.println("ac");
      // Sending SMS
      ss.print("AT+CMGF=1\r"); 
      delay(1000);
     
      ss.print("AT+CMGS=\"+919400050428\"\r"); 
      delay(1000);

    ss.println("GPS Location : " + message);
       // delay(100);
       
      ss.print((char)26);
      // delay(1000);
      
      
      
      flag=0;
      }
}
