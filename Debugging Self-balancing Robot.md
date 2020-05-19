### Self Balancing Robot - Debugged

__Problem Statement__ - Building a self-balanced two wheel robot using a PIC32 microcontroller and a PID controller to maintain its balance.

__Project Pipeline__ - 

   Extracting data from IMU sensors => Filtering the values (converting the voltage values to corresponding physical values) through MCU => Feeding the data to PID loop(algorithm) => Sending signals to motors/drivers => Environment(robot) action => (Repeat)
   
   So, our Robot essentially consists of two parts-
   
   * __Hardware__ - IMU sensors, Microcontroller, I2C communication
   * __Software__ - PID algorithm, MCU programming
   
   Hence, we can debug the two parts seperately to find the possible solution.

__Debugging the Robot:__

  1. __Power Supply__ - After all the components have been properly attached and all the connections been made, the first step would be to check whether the circuit is provided with proper supply according to the rating of each components. For eg. It might be possible that different components need different supply of power (MCU with 3v, Motors with 5V). Hence, we need to ensure that the different components are provided with proper power supply according to their rating and grounded appropriately. To just easen up our process, we can use a glowing LED as an indicator for power supply(similar to orange LED in Arduino). This step might seem quite trivial but it is actually quite important as it can be easily ignored by anyone. 
  
  If the component does not work even on providing proper power supply, the circuit might have been shorted. An indication of shorting is the heating up of that component leading to a burn out. A new component should be ordered preferably from the resources section mentioned below.
  
 2. __Sensor modules__ - Starting sequentially according to the project pipeline, the next step should be debugging the sensor  connections. As for the IMU MPU6050, we can refer to its datasheet for its connection. The SDA and SCL pins of MPU-6050 are responsible for I2C communication with the MCU. It is suggested to use a multimeter at each pin of the sensor to ensure proper connection. For eg: the VCC pin should show somewhere around 5V in the multimeter and 0 for GND pin. If it does not show the desire voltage, then there might be a loose wire connection or a wrong wire connection which should be fixed. Ensure proper power and ground connection to the sensor. 
 
  If none of the above workout, then check for the orientation of the IMU. The X-axes and Y-axes direction(mentioned on the sensor) should be strictly perpendicular to the body of the robot i.e z-axis should be parallel to ground.
 
  If the problem still persist, then the component might be faulty or it might have been shorted and burnt out as well. The next step should be to order the same new component, preferably from the resources section mentioned below. 
   
 3. __Microcontroller__ - The MCU plays a vital role in the whole circuit. Hence, debugging the MCU is very important and should be done with utmost precision. The first step should be to check whether the MCU is getting overheated. If yes, then the possible issue can be wrong pin connections. The only solution is to replace the MCU with an new one.  If the MCU is not overheated but keeps switching on and off repeatedly, then it might not be getting adequate power supply. Hence,check for power connections to the MCU and ensure that it gets proper supply according to its rating. If the circuit is still not functional, use a multimeter to check each pin connection of the IC and compare the expected output with observed output. 
 
 If none of the above problem persist, then refer the next debugging step.
 
 4. __Debugging the code__ - It should be noted that only after all the physical debugging mentioned above is carried out, the next step of debugging the code should be inititated. It is quite evident that improper programming can lead to potential non-functionality of our machine. Hence, the code should be debugged several times before running to ensure the validity of our code. Our code uses PID algorithm to maintain balance. 
 
       First of all, we need to check whether the IMU sensor is providing the correct output. For eg: If the robot is totally still, the  sensor should output 0 acceleration(along X,y axes) and 0 angular velocity. __Serial monitor__ is the best way to debug the programmed  code. Serial monitor allows you to output the observed value of any component connected with the MCU and compare its value with the expected output. Below is the code to output the acceleratio and gyro values from the IMU sensor to the Serial monitor.
 
``` 
const int MPU = 0x68; // MPU6050 I2C address
float AccX, AccY, AccZ;
float GyroX, GyroY, GyroZ;
float accAngleX, accAngleY, gyroAngleX, gyroAngleY, gyroAngleZ;
float roll, pitch, yaw;
float AccErrorX, AccErrorY, GyroErrorX, GyroErrorY, GyroErrorZ;
float elapsedTime, currentTime, previousTime;
int c = 0;
void setup() {
  Serial.begin(19200);
  Wire.begin();                      // Initialize comunication
  Wire.beginTransmission(MPU);       // Start communication with MPU6050 // MPU=0x68
  Wire.write(0x6B);                  // Talk to the register 6B
  Wire.write(0x00);                  // Make reset - place a 0 into the 6B register
  Wire.endTransmission(true);        //end the transmission
  calculate_IMU_error();
  delay(20);
}
void loop() {
  // === Read acceleromter data === //
  Wire.beginTransmission(MPU);
  Wire.write(0x3B); // Start with register 0x3B (ACCEL_XOUT_H)
  Wire.endTransmission(false);
  Wire.requestFrom(MPU, 6, true); // Read 6 registers total, each axis value is stored in 2 registers
  //For a range of +-2g, we need to divide the raw values by 16384, according to the datasheet
  AccX = (Wire.read() << 8 | Wire.read()) / 16384.0; // X-axis value
  AccY = (Wire.read() << 8 | Wire.read()) / 16384.0; // Y-axis value
  AccZ = (Wire.read() << 8 | Wire.read()) / 16384.0; // Z-axis value
  // === Read gyroscope data === //
  previousTime = currentTime;        // Previous time is stored before the actual time read
  currentTime = millis();            // Current time actual time read
  elapsedTime = (currentTime - previousTime) / 1000; // Divide by 1000 to get seconds
  Wire.beginTransmission(MPU);
  Wire.write(0x43); // Gyro data first register address 0x43
  Wire.endTransmission(false);
  Wire.requestFrom(MPU, 6, true); // Read 4 registers total, each axis value is stored in 2 registers
  GyroX = (Wire.read() << 8 | Wire.read()) / 131.0; // For a 250deg/s range we have to divide first the raw value by 131.0, according to the datasheet
  GyroY = (Wire.read() << 8 | Wire.read()) / 131.0;
  GyroZ = (Wire.read() << 8 | Wire.read()) / 131.0;
  
  Serial.print(AccX);
  Serial.print("");
  Serial.print(AccY);
  Serial.print("");
  Serial.println(AccZ);
  Serial.print(GyroX);
  Serial.print("");
  Serial.print(GyroY);
  Serial.print("");
  Serial.println(GyroZ);
  }
  ```
   On getting the output on Serial monitor screen, we can compare the output values. For eg: when the robot is still(perpendicular to ground), acceleration along Y and Z should be zero and that along X should be approximately equal to g(9.8). Similarly, the output can be checked for different positions.
   
   
   If none of the above debugging step provide any vaueable information about the bug in the system, then one or many of the components might be faulty. Consider buying new components from the below resources section. 
   
   ### Resources
   
  * [MPU 6050](https://robu.in/product/mpu-6050-gyro-sensor-2-accelerometer/?gclid=CjwKCAjwwYP2BRBGEiwAkoBpAnCFNKDNL7hlFLU60GJX9Wi971pqqGnX0q__YpHHQyDJjibJfqbvARoC4kMQAvD_BwE)
  * [Arduino](https://www.amazon.in/Uno-ATmega328P-Compatible-ATMEGA16U2-Arduino/dp/B015C7SC5U/ref=sr_1_4?crid=AR5TA4SUBJDM&dchild=1&keywords=arduino+uno&qid=1589893793&sprefix=Ard%2Caps%2C265&sr=8-4)
 
