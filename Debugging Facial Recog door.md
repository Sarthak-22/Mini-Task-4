## Facial Recognition Door - Debugged

### Problem Statement:
Operating a solenoid door lock to control the lock of a door using facial recognition methods by gathering data and training our own classifier using OpenCV package.

### Project Pipeline:

* Data gathering for face detection => Training our recognizer => Facial recognition (positive/negative) => signal sent to Rpi => signal sent by Rpi to solenoid lock.

 Hence, our pipeline essentially consists of two parts:
 
 * __Hardware__ - Rpi module, Rpi camera, solenoid lock, wires, resistors, LED's
 
 * __Software__ - Python code using OpenCV and uploading it to RPi.
 
### Debugging the pipeline:

 1. __Raspberry Pi module__ - The first step should be to check whether the Rpi camera is properly connected to the Rpi module. This is one of the most ignored and trivial mistake. You need to ensure that the camera is perfectly fitted and running. If the Rpi module starts to overheat or repeatedly switches off and on, then check for the power supply connection for the module. The rating of DC power supply should strictly be in the range of 7-12V. If you have accidentally burnt your board, you can buy a new one from the resources section below but make sure to not repeat the same mistake again.
 
 Connect the VCC and GND of the relay module to 5V and GND of Raspberry Pi. Then connect the signal pin of the relay module to the GPIO 26 of Raspberry Pi.
 
 2. __Rpi Camera__ - The Rpi camera comes with a short cable hence the camera might not be able to take a proper picture of the face. This problem can be solved by getting a reasonable third party cable with lengths between 5cm-1m. This will help the camera to be portable and flexible to use. RPi camera can give unusual results if not placed properly, hence ensure that the camera is properly adjusted near the door. Also ensure that the camera gets sufficient light for the photo to be captured. In dim light, an extra LED can be added near the camera to ensure optimum brightness in dim light.
 
 3. __Programming Algorithm__ - This is, by far and large, the most important debugging step. The hardware part of this project is simple to implement and should not be much pain in debugging. Several minute/major mistakes are possible which need to be rectified in a step-by-step manner. 
 
 The first step would be to check whether your classifier can actually detect faces. You should already have atleast 30 different photos of the face saved in a directory. Below is the code for the same:
 
 ```
 import cv2
from picamera.array import PiRGBArray
from picamera import PiCamera
import numpy as np 
import os
import sys

camera = PiCamera()
camera.resolution = (640, 480)
camera.framerate = 30
rawCapture = PiRGBArray(camera, size=(640, 480))

faceCascade = cv2.CascadeClassifier("haarcascade_frontalface_default.xml")

name = input("What's his/her Name? ")
dirName = "./images/" + name
print(dirName)
if not os.path.exists(dirName):
	os.makedirs(dirName)
	print("Directory Created")
else:
	print("Name already exists")
	sys.exit()

count = 1
for frame in camera.capture_continuous(rawCapture, format="bgr", use_video_port=True):
	if count > 30:
		break
	frame = frame.array
	gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
	faces = faceCascade.detectMultiScale(gray, scaleFactor = 1.5, minNeighbors = 5)
	for (x, y, w, h) in faces:
		roiGray = gray[y:y+h, x:x+w]
		fileName = dirName + "/" + name + str(count) + ".jpg"
		cv2.imwrite(fileName, roiGray)
		cv2.imshow("face", roiGray)
		cv2.rectangle(frame, (x, y), (x+w, y+h), (0, 255, 0), 2)
		count += 1

	cv2.imshow('frame', frame)
	key = cv2.waitKey(1)
	rawCapture.truncate(0)

	if key == 27:
		break

cv2.destroyAllWindows()
 ```

The execution of the above code would analyze whether the input from Rpi camera was a face using its algorithm. The next step would be to train the recognizer according to the data gathered in the previous step. Only if the above code works perfectly well, should you excute the below code. The below code analyzes the new input from the Rpi camera and analyzes it to check whether the face match index is greater than or equal to a threshold value.

```
import os
import numpy as np 
from PIL import Image 
import cv2
import pickle

faceCascade = cv2.CascadeClassifier("haarcascade_frontalface_default.xml")
recognizer = cv2.face.LBPHFaceRecognizer_create()

baseDir = os.path.dirname(os.path.abspath(__file__))
imageDir = os.path.join(baseDir, "images")

currentId = 1
labelIds = {}
yLabels = []
xTrain = []

for root, dirs, files in os.walk(imageDir):
	print(root, dirs, files)
	for file in files:
		print(file)
		if file.endswith("png") or file.endswith("jpg"):
			path = os.path.join(root, file)
			label = os.path.basename(root)
			print(label)

			if not label in labelIds:
				labelIds[label] = currentId
				print(labelIds)
				currentId += 1

			id_ = labelIds[label]
			pilImage = Image.open(path).convert("L")
			imageArray = np.array(pilImage, "uint8")
			faces = faceCascade.detectMultiScale(imageArray, scaleFactor=1.1, minNeighbors=5)

			for (x, y, w, h) in faces:
				roi = imageArray[y:y+h, x:x+w]
				xTrain.append(roi)
				yLabels.append(id_)

with open("labels", "wb") as f:
	pickle.dump(labelIds, f)
	f.close()

recognizer.train(xTrain, np.array(yLabels))
recognizer.save("trainer.yml")
print(labelIds)
```
In the next step, finally check for any syntax error in your code. 

### Resources

* [Raspberry Pi](https://robu.in/product/raspberry-pi-3-model-b-bcm2837b0-soc-iot-poe-enabled/)
* [Rpi camera](https://robu.in/product/raspberry-pi-camera-module/)
* [Solenoid door lock](https://robu.in/product/1240-24v-dc-0-8a-7-5w-solenoid-for-electric-door-lock/)

