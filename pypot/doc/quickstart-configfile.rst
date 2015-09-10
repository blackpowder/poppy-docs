.. _quickstart-configfile:

QuickStart: load a configuration file
============================================

What is a configuration file?
------------------------------------------------

In Pypot, there is a TODO LINK Robot object that contains the configuration of your robot: how many motors (with what IDs, on what port), their names, angle limits, and so on.

You can build a Robot object by hand, but it is much easier to launch a configuration from a configuration file. This text file contains a dictionnary, encoded in json.

This is an example of a minimal config file:

::

    {
      "controllers": {
        "head_controller": {
          "sync_read": true,
          "attached_motors": [
            "head",
          ],
          "port": "auto"
        }
      },
      "motorgroups": {
        "head": [
          "head_z",
          "head_y"
        ]
      },
      "motors": {
        "head_y": {
          "offset": 20.0,
          "type": "AX-12",
          "id": 37,
          "angle_limit": [
            -40,
            8
          ],
          "orientation": "indirect"
        },
        "head_z": {
          "offset": 0.0,
          "type": "AX-12",
          "id": 36,
          "angle_limit": [
            -100,
            100
          ],
          "orientation": "direct"
        }
      }
    }

It contains:

- two motors called head_y and head_z
- one motor group called head and containing the head_y and head_z motors
- one controller called head_controller, which controls teh motors of the motor group head. 
    A controller is associated to a serial port and therefore a USB2serial device. Even if you can theoretically plug more than 100 servos on one port, it is unadvised (for electrical losses) to have more than 15.
    
Each robot-specific library (poppy-humanoid for example) contains its own configuration file.

See TODO LINK for more details on the contents of teh configuration file and how to 

Test the Ergo Jr configuration
--------------------------------------------------

For test purposes, Pypot also contains a poppy Ergo Jr configuration, ready to use as a Python dictionnary.

::

    from pypot.robot.config import ergo_robot_config
    import pypot.robot

    my_config = dict(ergo_robot_config)
    print my_config["controllers"]
    print my_config["motorgroups"]
    print my_config["motors"]
    
If you convert this dictionnary to a robot, pypot checks if the motors are connected and tells you which motors it can't find. 
If all the motors are found, you can directly access the motors using their names and get and set their registers directly using their names:

::

    ergo_robot = pypot.robot.from_config(my_config)
    print ergo_robot.m2.present_position #get register present_position of motor m2
    
    ergo_robot.m6.compliant = False       #enable torque of the m6 motor
    ergo_robot.m6.goal_position = 20      #set goal position of motor m6 to 20 degree
    
    time.sleep(2)                                     #wait for the robot to move
    
    ergo_robot.m6.compliant = True       #disable torque of the m6 motor
    time.sleep(0.1)

Remark the time.sleep(0.1) in the last line: at the end of the script, the serial connection is closed and, if you don't wait a little bit, the connection may close before the last order (here: ergo_robot.m6.compliant = True) is sent.

See TODO LINK for more precisions on how to control a robot