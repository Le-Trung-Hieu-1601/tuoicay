#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
BlynkTimer timer;
char auth[] = "4O73ZsNaPsfQoCwGr1LwieM_WhT92qqN";
char ssid[] = "301";
char pass[] = "daihocbachkhoahanoi2.0";
#define ledr D5
#define ledg D6
#define ledy D7
#define analogPin A0
#define relay D2
int value1,value2,analogVal,button,Ref1,Ref2;
int phantram;
WidgetLED appledr(V10);
WidgetLED appledg(V11);
WidgetLED appledy(V12);
void setup()
{
 Serial.begin(9600);
 Blynk.begin(auth, ssid, pass,"sv.bangthong.com",8080);
 pinMode(ledr,OUTPUT);
 pinMode(ledy,OUTPUT);
 pinMode(ledg,OUTPUT);
 pinMode(relay,OUTPUT);
 Blynk.syncVirtual(V2);
 Blynk.syncVirtual(V3);
 Blynk.syncVirtual(V4);
 Blynk.syncVirtual(V9);
 
}
BLYNK_WRITE(V2) { // điều chỉnh chế độ
 value1 = param.asInt();
}
BLYNK_WRITE(V3) { // Lấy giá trị ngưỡng trên
 Ref1=param.asInt();
}
BLYNK_WRITE(V4) { // Lấy giá trị ngưỡng duoi
Ref2=param.asInt();
}
BLYNK_WRITE(V9) { // Nut nhan bat tat relay
 button=param.asInt();
}
void loop()
{
 Blynk.run();
 timer.run();
 analogVal = analogRead(analogPin); // 0 --> 1023
 phantram=map(analogVal, 0, 1023, 100, 0); // chuyen sang phantram
 Blynk.virtualWrite(V1,phantram);
 Blynk.virtualWrite(V8,String(phantram)+"%");
 if (value1 == 1){
 if (phantram > Ref1){
 appledr.off();
 appledg.off();
 appledy.on();
 digitalWrite(ledr,LOW);
 digitalWrite(ledg,LOW);
 digitalWrite(ledy,HIGH);
digitalWrite(relay,LOW);

 Blynk.virtualWrite(V6,"Độ ẩm cao"); //hiển thị trên LCD dòng 1
 }
 else if (phantram < Ref2){
 appledr.on();
 appledg.off();
 appledy.off();
 digitalWrite(ledr,HIGH);
 digitalWrite(ledg,LOW);
 digitalWrite(ledy,LOW);
 digitalWrite(relay,HIGH);

 Blynk.virtualWrite(V6,"Đang tưới nước");
 }
 else {
 appledr.off();
 appledg.on();
 appledy.off();
 digitalWrite(ledr,LOW);
 digitalWrite(ledg,HIGH);
 digitalWrite(ledy,LOW);
digitalWrite(relay,LOW);
 Blynk.virtualWrite(V6,"Độ ẩm BT");
 }
 }
 else {
 if (button == 1){
 digitalWrite(relay,HIGH);
 }
 else {
 digitalWrite(relay,LOW);
 } 
 }
}

