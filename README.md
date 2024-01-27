# Anduino
 - 아두이노에서 온습도 값을 받아서 습도에 따라 가습기가 자동으로 작동하게 안드로이드 앱을 구현하였다.


# 디자인
![졸업논문-15](https://github.com/mmg1111-spec/Anduino/assets/93562291/6f4e3b67-a652-4d28-a6a4-fb2d768dd5c7)
 - 안드로이드 앱 디자인은 다음과 같이 구현하였다.
 - 블루투스 찾기 버튼을 통해 아두이노 블루투스 모듈을 연결할수 있도록 구현하였다.
 - on, off 버튼을 통해 가습기 모듈을 작동하게 구현하였다.
 - 습도 % 버튼은 온습도 센서에 습도 값이 습도 %버튼에 설정한 값보다 낮으면 자동으로 작동하게 구현하였다.
 - 온습도 센서에서 측정한 온도, 센서값은 LCD에서 나오게끔 구현하였다.
# 아두이노
![졸업논문-9](https://github.com/mmg1111-spec/Anduino/assets/93562291/53afa88d-ff91-4857-aa37-bc3f252bc2ab)
 #### 아두이노에 연결한 모듈
 - 아두이노 UNO 보드
 - 블루투스 HC06 모듈
 - 온습도 DHT11 센서
 - LCD I2C 모듈
 - 가습기 모듈

# 아두이노 코드
 ```c
 #include <SoftwareSerial.h>
#include <DHT11.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

SoftwareSerial hc06(2,3);
 
byte i2cAddress = 0;
int count =0;
int humidify = 7;
char val = 'z';

LiquidCrystal_I2C lcd(0x27,16,2);
DHT11 dht11(4); // dht11 set pin 2

char charinit = 'z';
int intinit = 0;
 
void initSerial() {
  Serial.begin(9600);
  hc06.begin(9600);
  while(!Serial);
 
 
}

void inithc06() {
  if (hc06.available()){
    Serial.write(hc06.read());
  }
  if (Serial.available()) {
    hc06.write(Serial.read());
  }
 
}



void valinit() {
  if(hc06.available()) {
    while(hc06.available() > 0) {
      Serial.println(hc06.available());
      char temp1 = hc06.read();
      val = temp1;
      Serial.println(val);
    }
  } else {
   
    Serial.println(hc06.available());
    delay(1000);
  }
}
 
void initI2cDevices() {
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0,0);
  lcd.print("LCD SETTING");
  lcd.setCursor(0,1);
  lcd.print("ANDUINO");
  delay(2000);
  lcd.clear();
}
 

 
void pLCD(char *string1, float temp, char *string2, float humi)
{
  lcd.setCursor(0,0);
  lcd.print(string1);
  lcd.print(temp);
  lcd.print(" C");
  lcd.setCursor(0,1);
  lcd.print(string2);
  lcd.print(humi);
  lcd.print(" %");
}

void pLCD2(char *string1, char *string2, char *string3, float humi)
{
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print(string1);
  lcd.print(string2);
  lcd.setCursor(0,1);
  lcd.print(string3);
  lcd.print(humi);
  lcd.print(" %");
}

void pLCD3(char *string1, char *string2)
{
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print(string1);
  lcd.setCursor(0,1);
  lcd.print(string2);
}


 
void setup() {
  pinMode(humidify, OUTPUT);
  initSerial();
  initI2cDevices();
//  initI2cLCD();

 
}


 
void getDHT11()
{
  int err;
  float temp, humi;
 
  if((err=dht11.read(humi, temp)) == 0) {
//    Serial.print("Temperature:");
//    Serial.print(temp);
//    Serial.print(", Humidity:");
//    Serial.print(humi);
//    Serial.println("%");
    if(val == 'z') {
      pLCD2("T_HUMI: ", "NULL", "HUMI: ", humi);
    }
    if(val == 'a') {
      digitalWrite(humidify,HIGH);
      Serial.println(humi);
      lcd.backlight();
      pLCD2("HUMI POWER ", "ON", "HUMI: ", humi);
    } else if (val == 'b') {
      if(humi < 40) {
        digitalWrite(humidify,HIGH);
        pLCD2("T_HUMI: ", "40%", "HUMI: ", humi);
      } else {
        digitalWrite(humidify,LOW);
        pLCD2("T_HUMI: ", "40%", "HUMI: ", humi);
      }
    } else if (val == 'c') {
      if(humi < 45) {
        digitalWrite(humidify,HIGH);
        lcd.backlight();
        pLCD2("T_HUMI: ", "45%", "HUMI: ", humi);
      } else {
        digitalWrite(humidify,LOW);
        lcd.backlight();
        pLCD2("T_HUMI: ", "45%", "HUMI: ", humi);
      }
    } else if (val == 'd') {
      if(humi < 50) {
        digitalWrite(humidify,HIGH);
        lcd.backlight();
        pLCD2("T_HUMI: ", "50%", "HUMI: ", humi);
      } else {
        digitalWrite(humidify,LOW);
        lcd.backlight();
        pLCD2("T_HUMI: ", "50%", "HUMI: ", humi);
      }
    } else if (val == 'e') {
      if(humi < 55) {
        digitalWrite(humidify,HIGH);
        lcd.backlight();
        pLCD2("T_HUMI: ", "55%", "HUMI: ", humi);
      } else {
        digitalWrite(humidify,LOW);
        lcd.backlight();
        pLCD2("T_HUMI: ", "55%", "HUMI: ", humi);
      }
    } else if (val == 'f') {
      digitalWrite(humidify,LOW);
      Serial.println(humi);
      lcd.backlight();
      pLCD2("HUMI POWER ", "OFF", "HUMI: ", humi);
    }

   
  } else {
    Serial.println("");
    Serial.print("Err no:");
    Serial.print(err);
    Serial.println("");
  }
 
  delay(DHT11_RETRY_DELAY);
}
 
void loop() {

//  inithc06();  
//  charinitsetup();
  valinit();
 
  getDHT11();
 
  delay(1000);
}
```
# 아두이노 앱
안두이노 앱 : <https://drive.google.com/file/d/1RU06V8XCLF0lVkhasC7CJXc3GEGYt_ez/view?usp=drive_link>
