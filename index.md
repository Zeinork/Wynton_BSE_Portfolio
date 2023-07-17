# BSE Human-Directed Rolling Machine

The device outlined below is essentially a rover that is directed with an object of color, it will autonomously search its immediate surroundings to a directed color and then move away from it. Its primary directed function is aversion, in addition to recognizing a certain color and backing away, the machine also reacts to proximity stimuli by pinpointing the trigger site and responding accordingly. Along the process of creation, I was able to learn a lot about small-scale prototype electronics. With the utility of the Raspberry pi, it was quite easy to jumpstart what was an empty chassis to a moving rover. The biggest difficulty, which was overcome by the example of one of my BSE peers, was bridging the gap between using the camera to recognize colors and creating outputs that then could be used to direct the machine's movements.

You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:
```HTML 
<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->
```

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Wynton Z | Eaglecrest HS | Aerospace Engineering | Incoming Senior

**First picture that was taken on the Arducam.**

![Headstone Image](image.jpg)
  
# Milestone, Final

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Milestone, 2

**Second Milestone video Recorded**

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone 

# Milestone, 1

**First Milestone video recorded**

<iframe width="560" height="315" src="https://www.youtube.com/embed/CaCazFBhYKs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Hello everyone! This is my first milestone and this is what I've done so far:
-Programming
  -Hardware: Assembled Raspberry Pi computer, attached onboard camera
  -Testing: Recorded a video and took my first photo (unexpectedly)
  -Platform: Downloaded Python 2 and ran testing scripts
-Vehicle
  -Chasis: Wheels, motors, motor controllers, battery holder, and power switch assembled together

# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad because it can be done easily and for free in the browser. 

# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

```c++
# Disclaimer, the majority of the code was derived from Omeed, please see link below in page for his page
#Packages
from picamera.array import PiRGBArray    
from picamera import PiCamera
import RPi.GPIO as GPIO
import numpy as np
import time
import cv2
GPIO.setmode(GPIO.BCM)

# GPIO pins for the ultrasonic sensors
sensor_E0 = 17
sensor_T0 = 27

sensor_E1 = 26
sensor_T1 = 16

sensor_E2 = 13
sensor_T2 = 5

sensor_E3 = 34
sensor_T3 = 25

#GPIO pins for motors
ia_pin = 6  # GPIO pin for IA (speed control) of Motor A
ib_pin = 12  # GPIO pin for IB (direction control) of Motor A
ic_pin = 22  # GPIO pin for IC (speed control) of Motor B
id_pin = 23  # GPIO pin for ID (direction control) of Motor B

#LED_PIN=13  #If it finds the ball, then it will light up the led

# Set pins as output and input
GPIO.setup(sensor_T0, GPIO.OUT)
GPIO.setup(sensor_E0, GPIO.IN)

GPIO.setup(sensor_T1, GPIO.OUT)
GPIO.setup(sensor_E1, GPIO.IN)

GPIO.setup(sensor_T2, GPIO.OUT)
GPIO.setup(sensor_E2, GPIO.IN)

GPIO.setup(sensor_T3, GPIO.OUT)
GPIO.setup(sensor_E3, GPIO.IN)

# Set trigger to False (Low)
GPIO.output(sensor_T0, False)
GPIO.output(sensor_T1, False)
GPIO.output(sensor_T2, False)
GPIO.output(sensor_T3, False)

# Allow module to settle
def sonar(trig_pin, echo_pin):
      start=0
      stop=0
      # Set pins as output and input
      GPIO.setup(trig_pin,GPIO.OUT)  # Trigger
      GPIO.setup(echo_pin,GPIO.IN)      # Echo
     
      # Set trigger to False (Low)
      GPIO.output(trig_pin, False)
     
      # Allow module to settle
      time.sleep(0.01)
           
      #while distance > 5:
      GPIO.output(trig_pin, True)
      time.sleep(0.00001)
      GPIO.output(trig_pin, False)
      begin = time.time()
      while GPIO.input(echo_pin)==0 and time.time()<begin+0.05:
            start = time.time()
     
      while GPIO.input(echo_pin)==1 and time.time()<begin+0.1:
            stop = time.time()
     
      # Calculate pulse length
      elapsed = stop-start
      # Distance pulse travelled in that time is time
      # multiplied by the speed of sound (cm/s)
      distance = elapsed * 34000
     
      # That was the distance there and back so halve the value
      distance = distance / 2
     
      #print "Distance : %.1f" % distance
      # Reset GPIO settings
      return distance

#Set motor pins
GPIO.setup(ia_pin, GPIO.OUT)
GPIO.setup(ib_pin, GPIO.OUT)

GPIO.setup(ic_pin, GPIO.OUT)
GPIO.setup(id_pin, GPIO.OUT)

def forward():
      GPIO.output(ia_pin, GPIO.HIGH)
      GPIO.output(ib_pin, GPIO.LOW)
      GPIO.output(ic_pin, GPIO.HIGH)
      GPIO.output(id_pin, GPIO.LOW)
     
def reverse():
      GPIO.output(ia_pin, GPIO.LOW)
      GPIO.output(ib_pin, GPIO.HIGH)
      GPIO.output(ic_pin, GPIO.LOW)
      GPIO.output(id_pin, GPIO.HIGH)
     
def rightturn():
      GPIO.output(ia_pin,GPIO.LOW)
      GPIO.output(ib_pin,GPIO.HIGH)
      GPIO.output(ic_pin,GPIO.HIGH)
      GPIO.output(id_pin,GPIO.LOW)
     
def leftturn():
      GPIO.output(ia_pin,GPIO.HIGH)
      GPIO.output(ib_pin,GPIO.LOW)
      GPIO.output(ic_pin,GPIO.LOW)
      GPIO.output(id_pin,GPIO.HIGH)

def stop():
      GPIO.output(ia_pin,GPIO.LOW)
      GPIO.output(ib_pin,GPIO.LOW)
      GPIO.output(ic_pin,GPIO.LOW)
      GPIO.output(id_pin,GPIO.LOW)
     
#Image analysis work
def segment_colour(frame):    #returns only the yellow colors in the frame
    hsv_roi =  cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    mask_1 = cv2.inRange(hsv_roi, np.array([23,41,133]), np.array([40,150,255]))
    ycr_roi=cv2.cvtColor(frame,cv2.COLOR_BGR2YCrCb)
    mask_2=cv2.inRange(ycr_roi, np.array((0.,165.,0.)), np.array((255.,255.,255.)))

    mask = mask_1 | mask_2
    kern_dilate = np.ones((8,8),np.uint8)
    kern_erode  = np.ones((3,3),np.uint8)
    mask= cv2.erode(mask,kern_erode)      #Eroding
    mask=cv2.dilate(mask,kern_dilate)     #Dilating
    #cv2.imshow('mask',mask)
    return mask

def find_blob(blob): #returns the red colored circle
    largest_contour=0
    cont_index=0
    contours, hierarchy = cv2.findContours(blob, cv2.RETR_CCOMP, cv2.CHAIN_APPROX_SIMPLE)
    for idx, contour in enumerate(contours):
        area=cv2.contourArea(contour)
        if (area >largest_contour) :
            largest_contour=area
           
            cont_index=idx
            #if res>15 and res<18:
            #    cont_index=idx
                              
    r=(0,0,2,2)
    if len(contours) > 0:
        r = cv2.boundingRect(contours[cont_index])
       
    return r,largest_contour

def target_hist(frame):
    hsv_img=cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
   
    hist=cv2.calcHist([hsv_img],[0],None,[50],[0,255])
    return hist

#CAMERA CAPTURE
#initialize the camera and grab a reference to the raw camera capture
camera = PiCamera()
camera.resolution = (160, 128)
camera.framerate = 16
rawCapture = PiRGBArray(camera, size=(160, 128))
 
# allow the camera to warmup
time.sleep(0.001)
 
# capture frames from the camera
for image in camera.capture_continuous(rawCapture, format="bgr", use_video_port=True):
      #grab the raw NumPy array representing the image, then initialize the timestamp and occupied/unoccupied text
      frame = image.array
      frame=cv2.flip(frame,1)
      frame = cv2.rotate(frame, cv2.ROTATE_90_CLOCKWISE)
      frame = cv2.rotate(frame, cv2.ROTATE_90_CLOCKWISE)
      global centre_x
      global centre_y
      centre_x=0.
      centre_y=0.
      hsv1 = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
      mask_yellow=segment_colour(frame)     
      loct,area=find_blob(mask_yellow)
      x,y,w,h=loct
     
      #distance readings
      distance0 = sonar(sensor_T0,sensor_E0)
      distance1 = sonar(sensor_T1,sensor_E1)
      distance2 = sonar(sensor_T2,sensor_E2)
      distance3 = sonar(sensor_T3,sensor_E3)

             
      if (w*h) < 10:
            found=0
      else:
            found=1
            simg2 = cv2.rectangle(frame, (x,y), (x+w,y+h), 255,2)
            centre_x=x+((w)/2)
            centre_y=y+((h)/2)
            cv2.circle(frame,(int(centre_x),int(centre_y)),3,(0,110,255),-1)
            centre_x-=80
            centre_y=6--centre_y
            print (centre_x,centre_y)
      initial=300
      flag=0
    
      if(found==0):
            #if the ball is not found and the last time it sees ball in which direction, it will start to rotate in that direction
            if flag==0:
                  rightturn()
                  time.sleep(0.05)
            else:
                  leftturn()
                  time.sleep(0.05)
            stop()
            time.sleep(0.0125)
     
      elif(found==1):
            if(area<initial):
                  if(distance0<10):
                        #if ball is too far but it detects something in front of it,then it avoid it and reaches the ball.
                        if distance1>=8:
                              rightturn()
                              time.sleep(0.00625)
                              stop()
                              time.sleep(0.0125)
                              forward()
                              time.sleep(0.00625)
                              stop()
                              time.sleep(0.0125)
                              #while found==0:
                              leftturn()
                              time.sleep(0.00625)
                        elif distance2>=8:
                              leftturn()
                              time.sleep(0.00625)
                              stop()
                              time.sleep(0.0125)
                              forward()
                              time.sleep(0.00625)
                              stop()
                              time.sleep(0.0125)
                              rightturn()
                              time.sleep(0.00625)
                              stop()
                              time.sleep(0.0125)
                        else:
                              stop()
                              time.sleep(0.01)
                  else:
                        #otherwise it move forward
                        forward()
                        time.sleep(0.00625)
            elif(area>=initial):
                  initial2=6700
                  if(area<initial2):
                        if(distance0>10):

                              if(centre_x<=-20 or centre_x>=20):
                                    if(centre_x<0):
                                          flag=0
                                          rightturn()
                                          time.sleep(0.025)
                                    elif(centre_x>0):
                                          flag=1
                                          leftturn()
                                          time.sleep(0.025)
                              forward()
                              time.sleep(0.00003125)
                              stop()
                              time.sleep(0.00625)
                        else:
                              stop()
                              time.sleep(0.01)

                  else:

                        time.sleep(0.1)
                        stop()
                        time.sleep(0.1)  
      rawCapture.truncate(0)  # clear the stream in preparation for the next frame
         
      if(cv2.waitKey(1) & 0xff == ord('q')):
            break

GPIO.cleanup() #free all the GPIO pins



}
```

# Bill of Materials

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Raspberry Pi | Onboard computer, the "brains" of the robot | 35$ | <a href="https://www.raspberrypi.com/products/raspberry-pi-4-model-b/"> Link </a> |
| LS9110 Dual-Channel Motor Controller | Speed and directional wheel director | 1$ | <a href="https://www.elecrow.com/l9110-dualchannel-hbridge-motor-driver-module-12v-800ma-p-826.html"> Link </a> |
| HC-SR04 Ultrasonic Sensors | Proximity localization |  | <a href="https://www.amazon.com/HiLetgo-HC-SR04-Ultrasonic-Distance-MEGA2560/dp/B00E87VXH0/ref=sr_1_6?keywords=hc-sr04&s=industrial&sr=1-6"> Link </a> |
| Assorted Jumper Wires | Power supply management and overall connections | 7$ | <a href="https://www.amazon.com/EDGELEC-Breadboard-Optional-Assorted-Multicolored/dp/B07GD2BWPY/ref=sr_1_1_sspa?keywords=jumper+wires&s=industrial&sr=1-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1"> Link </a> |
| Raspberry Pi camera | Image recognition | 10$ | <a href="https://www.amazon.com/Arducam-Megapixels-Sensor-OV5647-Raspberry/dp/B012V1HEP4/ref=sr_1_3?keywords=raspberry+pi+camera&sr=8-3"> Link </a> |
| Breadboards | Used in conjunction with jumper wires, breadboards are used to streamline power supplication and connections | $10 | <a href="https://www.amazon.com/Breadboards-Solderless-Breadboard-Distribution-Connecting/dp/B07DL13RZH/ref=sr_1_5?keywords=breadboards&sr=8-5"> Link </a> |
| Smart Car Kit | Chasis, wheels, and overall body of the project | 14$ | <a href="https://www.amazon.com/Smart-Chassis-Motors-Encoder-Battery/dp/B01LXY7CM3/ref=sr_1_2?keywords=smart+car+chassis&sr=8-2"> Link </a> |
| Power Bank | Redundant and secondary power source  | 17$| <a href="https://www.amazon.com/EnergyQC-Portable-Ultra-Compact-Compatible-More-Black/dp/B09Z6T7FQ8/ref=sr_1_5?keywords=energy+qc&sr=8-5"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://projects.bluestampengineering.com/student-projects/winston-l/)
- [Example 2](https://komeed.github.io/Omeed-BSE-Portfolio/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

