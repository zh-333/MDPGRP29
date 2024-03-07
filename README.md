### STM32
Contains STM32 program code for managing and functioning the robot

## SR: STM to RPi
1. Acknowledgement of command
   ```A```
   - acknowledges that command has been received and completed
2. Distance moved from last command 
   ```100```
   - 3 digit number, read 1 character at a time by RPi
   - see RS3 and RS5

## RS: RPi to STM 
1. Movement instructions from PC/Android: direction + forward/backward + distance
    ```SF010```
    - while RPi receives a list of commands from PC (see `NAVIGATION` messages - AR1 and PR1), it sends each 5 character command 1 at a time to STM
      - each command is a 5 character code: <L/R/S><F/B>XXX 
      - S/L/R: the first character indicates Straight / Left / Right 
      - F/B: the second character indicates Forward / Backward
      - XXX: the last 3 digits indicate distance in cm for S, or rotation angle for L/R
      - e.g. SB010 is move backwards 10cm, LF090 is turn 90 degrees to the left in the forward direction
    - on completion, STM acknowledges with A
2. Movement until ultrasonic sensor output indicates imminent obstacle
    ```UF100, VF100```
    - move Forward (this is the only valid direction since our ultrasonic sensor is front mounted)
    - end the movement based on the ultrasonic sensor
      - U is a bigger threshold used for the first part of task 2
      - V is a smaller threshold used for ensuring the robot fully enters the parking zone 
    - XXX: the last 3 digits indicate distance in cm which is the upper limit of how far to move if the ultrasonic sensor is not triggered
    - e.g. UF100 is to move foward 100cm, or until the ultrasonic sensor indicates that an obstacle is ahead
    - on completion, STM acknowledges with A
3. Read distance of movement until ultrasonic sensor output indicates imminent obstacle
    ```YF100```
    - move Forward (this is the only valid direction since our ultrasonic sensor is front mounted)
    - end the movement based on the ultrasonic sensor
    - measure the distance moved
    - XXX: the last 3 digits indicate distance in cm which is the upper limit of how far to move if the ultrasonic sensor is not triggered
    - e.g. YF100 is to move foward 100cm, or until the ultrasonic sensor indicates that an obstacle is ahead, and return the actual distance moved
    - on completion, STM acknowledges with a 3 digit distance in cm
4. Read distance of movement until ultrasonic sensor output indicates imminent obstacle
    ```XL100, XR100```
    - move forward until side-mounted IR sensor shows no obstacle to side
    - check for end of obstacle to Left or Right 
    - XXX: the last 3 digits indicate distance in cm which is the upper limit of how far to move if the ultrasonic sensor is not triggered
    - e.g. XL100 is to move foward 100cm, or until the left ultrasonic sensor indicates that there is no obstacle to the left, and return the actual distance moved
    - on completion, STM acknowledges with a 3 digit distance in cm