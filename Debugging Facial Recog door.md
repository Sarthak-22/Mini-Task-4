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
 
 2. __Rpi Camera__ - 



