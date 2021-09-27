#include "SD.h"
#include "esp_camera.h"
#include "soc/soc.h"
#include "soc/rtc_cntl_reg.h"
#include "FS.h"
#include "SD.h"

//SD Card Pins
#define sd_sck  14
#define sd_mosi 15
#define sd_ss   13
#define sd_miso  2

// read and write from flash memory
#include <EEPROM.h>

// define the number of bytes you want to access
#define EEPROM_SIZE 1

#define PWDN_GPIO_NUM     32
#define RESET_GPIO_NUM    -1
#define XCLK_GPIO_NUM      0
#define SIOD_GPIO_NUM     26
#define SIOC_GPIO_NUM     27

#define Y9_GPIO_NUM       35
#define Y8_GPIO_NUM       34
#define Y7_GPIO_NUM       39
#define Y6_GPIO_NUM       36
#define Y5_GPIO_NUM       21
#define Y4_GPIO_NUM       19
#define Y3_GPIO_NUM       18
#define Y2_GPIO_NUM        5
#define VSYNC_GPIO_NUM    25
#define HREF_GPIO_NUM     23
#define PCLK_GPIO_NUM     22

int pictureNumber = 0;
int mySN = 0;
char myFname1[4];
char myFname2[4];

void setup()
{
  WRITE_PERI_REG(RTC_CNTL_BROWN_OUT_REG, 0);
  
  Serial.begin(115200);
  delay(10);

  camera_config_t config;
  config.ledc_channel = LEDC_CHANNEL_0;
  config.ledc_timer = LEDC_TIMER_0;
  config.pin_d0 = Y2_GPIO_NUM;
  config.pin_d1 = Y3_GPIO_NUM;
  config.pin_d2 = Y4_GPIO_NUM;
  config.pin_d3 = Y5_GPIO_NUM;
  config.pin_d4 = Y6_GPIO_NUM;
  config.pin_d5 = Y7_GPIO_NUM;
  config.pin_d6 = Y8_GPIO_NUM;
  config.pin_d7 = Y9_GPIO_NUM;
  config.pin_xclk = XCLK_GPIO_NUM;
  config.pin_pclk = PCLK_GPIO_NUM;
  config.pin_vsync = VSYNC_GPIO_NUM;
  config.pin_href = HREF_GPIO_NUM;
  config.pin_sscb_sda = SIOD_GPIO_NUM;
  config.pin_sscb_scl = SIOC_GPIO_NUM;
  config.pin_pwdn = PWDN_GPIO_NUM;
  config.pin_reset = RESET_GPIO_NUM;
  config.xclk_freq_hz = 20000000;
  config.pixel_format = PIXFORMAT_JPEG;
  
  config.frame_size = FRAMESIZE_VGA;  // UXGA|SXGA|XGA|SVGA|VGA|CIF|QVGA|HQVGA|QQVGA
  config.jpeg_quality = 10;
  config.fb_count = 1;
  
  esp_err_t err = esp_camera_init(&config);
  if (err != ESP_OK) {
    Serial.printf("Camera init failed with error 0x%x", err);
    delay(1000);
    ESP.restart();
  }

   // initialize EEPROM with predefined size
    EEPROM.begin(EEPROM_SIZE);
    pictureNumber = EEPROM.read(0) + 1;
    EEPROM.write(0, pictureNumber);
    EEPROM.commit();

// LED Pin Mode
    pinMode(33, OUTPUT);

}

void loop() {
      digitalWrite(33,LOW);
      delay (1000);
      saveCapturedImage();
      
      digitalWrite(33,HIGH);

      delay(180*1000); //待機間隔 秒
}

void saveCapturedImage() {

  camera_fb_t * fb = NULL;
  
  // Take Picture with Camera
    Serial.println(" ");
    Serial.println("Take Picture ");
    fb = esp_camera_fb_get();  
  
  // Path where new picture will be saved in SD Card
    Serial.println("Save SD Card");

  // Get File Name
  if (mySN == 999){

    EEPROM.begin(EEPROM_SIZE);
    pictureNumber = EEPROM.read(0) + 1;
    EEPROM.write(0, pictureNumber);
    EEPROM.commit();

    mySN = 1;
    }else{
     mySN = mySN + 1;
    }

    sprintf(myFname1, "%03d",pictureNumber);
    sprintf(myFname2, "%03d",mySN);

    String path = "/p" + String(myFname1)+"_"+ String(myFname2) +".jpg";

    Serial.printf("Picture file name: %s\n", path.c_str());

      SPI.begin(sd_sck, sd_miso, sd_mosi, sd_ss);
      SD.begin(sd_ss);
  
    File file = SD.open(path.c_str(), FILE_WRITE);
    file.write(fb->buf, fb->len); 

    file.close();
   
    esp_camera_fb_return(fb); 
    Serial.println("complete");
}
