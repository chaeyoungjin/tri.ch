# tri.ch
cd Downloads/
git clone https://github.com/everylumi/Adafruit_Python_DHT.git
cd Adafruit_Python_DHT/
sudo python3 setup.py install
sudo python setup.py install

import RPi.GPIO as GPIO
import Adafruit_DHT as dht
import time

Button_PIN = 21
A1A_PIN = 23
A1B_PIN = 24
DHT_PIN = 19

pressed = False

def button_pressed_callback(channel):
    global pressed

    pressed = 1 - pressed
    
    if pressed:
        print('버튼 On')
        A1A.start(0)                
        #A1A.ChangeDutyCycle(70)
    else:
        print('버튼 Off')
        A1A.stop() 

GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)
GPIO.setup(A1A_PIN, GPIO.OUT)
GPIO.setup(A1B_PIN, GPIO.OUT)
GPIO.setup(Button_PIN, GPIO.IN)
GPIO.add_event_detect(Button_PIN, GPIO.FALLING, 
            callback=button_pressed_callback, bouncetime=10)

PWM_FREQ = 50
A1A = GPIO.PWM(A1A_PIN, PWM_FREQ)
A1B = GPIO.PWM(A1B_PIN, PWM_FREQ)

try:
    while 1:
        humidity,temperature = dht.read_retry(dht.DHT11, DHT_PIN)
       
        if humidity<=50:
            relative_temperature = temperature
        elif humidity<=60:
            relative_temperature = temperature +1
        elif humidity<=70:
            relative_temperature = temperature +2
        elif humidity<=80:
            relative_temperature = temperature +3
        elif humidity<=90:
            relative_temperature = temperature +4
        print(relative_temperature)

        if pressed ==1:
            if relative_temperature > 28:
              print('상대온도 28도씨 조과')
              A1A.start(0)                
              A1A.ChangeDutyCycle(95)
            elif relative_temperature > 25:
              print('상대온도 25도씨 조과')
              A1A.start(0)                
              A1A.ChangeDutyCycle(70)
            elif relative_temperature > 23:
              print('상대온도 23도씨 조과')
              A1A.start(0)                
              A1A.ChangeDutyCycle(50)
        else :
          A1A.stop()

        

finally:
    GPIO.cleanup()
