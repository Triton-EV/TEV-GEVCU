# Throttle Calibration and Mapping

Created: June 30, 2022 10:43 AM
Last Edited Time: June 30, 2022 12:02 PM

## Index

1. About
2. How GEVCU detects throttle position
3. Throttle Calibration
4. Throttle Mapping
5. Conclusion

---

 

## About

**Throtlle Pedal :**  It is a way to control an engine's power by regulating the amount of field or air entering the engine.
.

**CENTRAL function of GEVCU** is to translate throttle pedal position into motor control signals.

It is also the  **most complicated concept**  to grasp as we need to accommodate as many combinations of pedal position, acceleration, and regenerative braking as possible to accommodate a variety of vehicles and driver preferences.

## 1) How GEVCU Detects Throttle Position

- GEVCU detects throttle position **by voltage.**
- **Throttles may be** potentiometers, hall effect devices, and of either one signal or two.
- GEVCU provides these sensors with a **5v supply** and a **ground reference return** and receives from it a signal or signals indicating pedal position by voltage.
- This signal is optically isolated, buffered, filtered, and scaled **from the 0 to 5 volt signal from the pedal to a 0-3.3v signal** that can be read by the multiprocessor.
- This signal is then sampled by the ADC and presented as a number between **0 and 4096 digitally.**
- Sampling issues, noise on the wires, electromagnetic interference all have an effect on such small signals. So the software **“averages”**
this input value continuously.
- Programmatically, it then converts this **digital value** to a ***“throttle percentage”*** between 0 and 100 to some precision for use by the various program elements.

Eg **:**

**Throttle at Rest :** might put out a voltage of 0.83, It is scaled and converted to a digital value of 95 —> which is then defined in software as 0% throttle.

**Throttle at Pedal Fully Depressed :** might put out a voltage of 4.62volts, It is scaled and converted to a digital value of 3785 —> which is then defined in software as 100% throttle.

## 2) Throttle Calibration

- Whatever sensor is selected or used, the output read at the analog input to the GEVCU is going to vary **depending on wire lengths, manufacturing variations etc**. So it is important to have a means of calibrating the
throttle.

To get the numeric values, we have to use our USB serial port terminal program to view the digital value “read” by GEVCU for the throttle positions.

Enter the letter L and the serial monitor will begin to provide a text read out that is updated every few seconds.

**For single input throttles two variables are provided** – T1MN and T1MX

**For two wire throttles, a second input is provided,** T2MN and T2MX.

### So calibrating the throttle becomes quite easy to do manually:

1. Enter L to get the screen logging shown above.
2. Ensure throttle is at rest (idle).
3. Note numeric value appearing in AIN0.
4. Press throttle to full acceleration.
5. Note new numeric value appearing in AIN0.
6. Enter the first value using the command T1MN=xx where xx is the value
displayed.
7. Enter the second value noted using the command T1MX=xx where xx is the
value displayed.

![Untitled](Throttle%20Calibration%20and%20Mapping%201ec69ef958ec4ffe8e47e58827b13770/Untitled.png)

→By convention, we usually wire the primary throttle signal to AIN0 and the
secondary to AIN1 with brake inputs on AIN2.

→The next line actually indicates the current status of digital inputs 0 through 3 as DIN0: etc.

→ Next line will provide information on the current status of the eight digital
MOSFET outputs DOUT0 through DOUT7.

→ For dual signal throttles, this procedure can be repeated for AIN1 using the
variables T2MN and T2MX.

## 3) **Throttle Mapping**

Throttle calibration simply establishes the “end points” of the throttle map. We
establish the actual digital readings for an idle and max throttle condition. This is then mapped to a normalized 0 to 100% throttle value.

GEVCU actually provides a rich set of variables to map that 0-100% in an almost endless number of combinations to ***“tune the curve***” or ***tune your throttle action*** to vary the feel of the car while driving.

The basic list of variables includes

- TCREEP
- TRGNMIN
- TRGNMAX
- TMAP
- TFWD

![Untitled](Throttle%20Calibration%20and%20Mapping%201ec69ef958ec4ffe8e47e58827b13770/Untitled%201.png)

**Regenerative Braking** is a function of the forward motion of the car, fed through the axles and transmission to the motor, is used to generate electricity.

There will  be no regenerative braking of the wheels are not turning (no matter what we set our variables) 

- So in the diagram depicted, the first 25% of the pedal does NOT cause us to go in reverse. It is simply dead pedal.
- In starting off, TFWD defines the point in the pedal where forward motion begins. (around at 27% throttle)
- Torque is then mapped from 27% to 100% throttle with one modification. It is scaled nonlinearly by the variable TMAP which defines the percentage of throttle where 50% torque occurs.

We have nothing defined at all from 25% our minimum regen point, and 27% , the beginning of forward acceleration torque. This provides a little **“coast” zone** where no forward torque is applied and no regen torque is produced.

## 4) Conclusion

- These variables are at first confusing and seem unnecessarily complicated.
Worse, it will take you a number of “tuning drives” where you go drive the car, come back and change some variables, go drive the car, come back and repeat.
- But you will quickly see that these variables allow you to achieve a custom level of feel and nuance in the way the vehicle drives that is really quite the central feature of GEVCU.