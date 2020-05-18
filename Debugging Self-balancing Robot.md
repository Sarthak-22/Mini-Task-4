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
 
 First of all, we need to check whether the IMU sensor is providing the correct output. For eg: If the robot is totally still, the sensor should output 0 acceleration(along X,y axes) and 0 angular velocity. __Serial monitor__ is the best way to debug the programmed  code
 
