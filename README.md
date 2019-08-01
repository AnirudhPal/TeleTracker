# TeleTracker - CS590 Independent Study

## Index

> Under Construction

## Abstract

The objective of the independent study is to find a **simple and inexpensive** method of making a telescope tracking mount and studying the software and harware challenges associated with such an endeavour. The following sections cover the motivations, design aspect, software and experiments assoicated with the project.

## Motivation

Let say you wanted to take a picture of the Orion Nebula from your backyard. Even if you get a more that capable telescope for 100-200$, you will not be able to pull this off because:

1. The earth rotates at a constant rate, making the sky shift.
2. Its hard to locate a dim object in the night sky accurately.
3. Light pollution makes it difficult to see the stars and take pictures.

I wanted to address these problems by making my own tracker system which performs the following basic features:

1. Be able to locate objects in the sky.
2. Compensate for any vibrations and shifting of the sky.
3. Take long exposure shots of objects with a Raspberry Pi Camera.

Here are some pictures to illustrate this problem. They were taken by me a couple years ago from my back yard.

<p align="center">
<img src="https://scontent-ort2-1.xx.fbcdn.net/v/t1.0-9/525062_3727071190456_1042449301_n.jpg?_nc_cat=103&_nc_oc=AQlguguw0SMG8pTJCGN6XgaaduHyrVkAAMS40QW_3db-TzTsL6O-ovVl9ZSm72FpCvc&_nc_ht=scontent-ort2-1.xx&oh=64e954685378ce7211f316dbb00a2190&oe=5DB0898A" />
</p>
<p align="center">
Fig 1: Trailing of stars due to earth's rotation.
</p>

<p align="center">
<img src="https://scontent-ort2-1.xx.fbcdn.net/v/t1.0-9/12199_3716048354892_782774520_n.jpg?_nc_cat=105&_nc_oc=AQkNWdqRMD98O5dVlMhobNRv-7UFfqp3rl7ya1th_2Ab6z4UUzSqSUYSoswuviywtFs&_nc_ht=scontent-ort2-1.xx&oh=1a86d85800efed7bf9c56aaa8c46bb27&oe=5DB534DB" />
</p>
<p align="center">
Fig 2: Unprocessed tracked image.
</p>

<p align="center">
<img src="https://scontent-ort2-1.xx.fbcdn.net/v/t1.0-9/61791_3716048874905_1899857854_n.jpg?_nc_cat=106&_nc_oc=AQkYm-2CTDsBiDCv0U1nxebUSDaBuNTKk03w7cqOEJYGzhREg1UKnHjVAT9PiH4ZUEc&_nc_ht=scontent-ort2-1.xx&oh=d1b81f024ab7a74e5557142ebb79ef6a&oe=5DA22906" />
</p>
<p align="center">
Fig 3: Processed tracked image.
</p>

## Design Specification

I set some design specifications for the project. The specifics are discussed below along with why they are apporpriate for my application. 

### Hardware

The hardware had to inexpesive and easy to procure so others can replicate my results. This would also allow me to make my tracker at a low price point. It is possible to by trackers with the same capabilty at 600-1000$ so it makes sense to make sure my tracker cost less than 600$.

#### BLDC Direct Drive

The tracker requires an actuator to move, in this case that will be a motor. One can use a motor in conjunction with a gears, belt or pulley system to make it more cost effective, but I decided not to do this beacuse such systems introduce backlash error and other forms of errors due to machining tolerances. Instead I choose to do a direct drive, which in simple terms means that I will be mounting the apparatus directly to the motor drive shaft. For this to work the load has to be well balanced inorder to not apply any significant torque on the motor.

Furthermore I had to be able to control the postion of the motor accurately. One might conclude that the approriate choice for such an application would be a stepper motor. Unfortunately stepper motors have a fixed number of steps per revolution which is far coarser than what I needed for my application. Here is an image of a stepper motor. 

<p align="center">
<img src="https://cdn.sparkfun.com//assets/parts/1/1/0/6/7/13656-01.jpg" />
<img src="https://circuitdigest.com/sites/default/files/inlineimages/u/Stepper-Motor-Internal-Structure.png" />
</p>
<p align="center">
Fig 4: NEMA Stepper Motor.
</p>

Although the stepper motor wouldn't be appropriate for my applications, its mechanism of controling postion by energizing different phases led me to the motor type that can get me the resolution I need. BLDC motor are similar to steppers in the sense that they have 3-phases that can be controlled. But unlike stepper motors you can modulated each of the phases to get many fine position with the motor. They are also relatively inecpesive due to their simple brushless construction and heavy use in RC Model Aviation. Here is an image of a BLDC motor.

<p align="center">
<img src="https://cdn-global-hk.hobbyking.com/media/catalog/product/cache/1/image/660x415/17f82f742ffe127f42dca9de82fb58b1/legacy/catalog/45369.jpg" />
<img src="https://3l4sbp4ao2771ln0f54chhvm-wpengine.netdna-ssl.com/wp-content/uploads/2018/07/Brushed-vs-BLDC-Motor.jpg" />
</p>
<p align="center">
Fig 4: BLDC Gimbal Motor.
</p>

Despite BLDC motor's simple construction their drive mechanism is rather complicated. The require three 120 deg phase shifted sinosodial signals on the phases to rotate. The later sections discuss how I plan on acheiving this. The ability to do this process (refered to as commutation) is the making and breaking point of this project. 

<p align="center">
<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cc/3_phase_AC_waveform.svg/300px-3_phase_AC_waveform.svg.png" />
</p>
<p align="center">
Fig 5: BLDC Drive Phases.
</p>

#### Absolute Rotary Encoder

Although I can commutate the motor using simple senserless techniques described in many research papers, it is dificult to do so accurately in an open control scheme. To deal with this I will pair the motor with an absolute rotary encoder that can be used for closed loop control. I decided to go with AS5048A, which is a 14 bit hall-effect absolute rotary encoder. It uses a small magnet to get its absolute rotary postion. The reason I went with this kind of an encoder is simple, it was the cheapest one I could find that had the resolution that I needed.

<p align="center">
<img src="https://media.digikey.com/Photos/Austria%20Microsystems/AS5048A-AB-1.0.jpg" />
</p>
<p align="center">
Fig 5: AS5048A Evalution Board.
</p>

This sensor works on an interesting principle which I will talk briefly about. It uses the the hall effect which can be illustrated as the shifting of flowing electrons due to a magnetic field. This shift can be detected as a potential difference. If you have multiple elements like this you can map the relative magnitudes of the voltage to magnitude of magnetic flux density and translate that to a vector, that gives you the absolute rotary postion. Thankfully all of this is handled internaly by the IC and it gives us a absolute position through and SPI interface.

<p align="center">
<img src="https://www.ablic.com/en/semicon/wp-content/uploads/2018/09/img-whats-hall-effect-ic-en1.png" />
</p>
<p align="center">
Fig 6: Illustration of Hall Effect.
</p>

Notes on the SPI Interface Registers

|Register Address|Register Purpose|Notes|Type|
|:-:|:-:|:-:|:-:|
|0x0016|Zero Postion 6-13 bit|Bits 0-7 are used for this.|Read/Write|
|0x0017|Zero Postion 0-5 bit|Bits 0-5 are used for this.|Read/Write|
|0x3FFD|Internal Sensor Signal Gain Value|Bits 0-7 are used for this.|Read|
|0x3FFE|Absolute Postion without Zero Correction|14 bit value.|Read|
|0x3FFF|Absolute Postion with Zero Correction|14 bit value.|Read|

Notes on the SPI Interface Packet

|Packet Scheme|||
|:-:|:-:|:-:|
|15 bit|14 bit|13-0 bits|
|Even Parity Bit|Read/Write Bit|Data/Register Address|

Notes on the SPI Interface Read

|Master|Direction|Slave|Purpose|
|:-:|:-:|:-:|:-:|
|Make Packet|||Construct Packet with Parity and Read Register Address|
|Pull Chip Select High|->||Make Slave Listen|
||<-|Send Data Packet|Slave Responds|
|Get Packet|||Receive the Packet|
|Pull Chip Select Low|->||Make Slave Stop Listening|
|Decode Packet|||Translate the Data Packet to Usable Data|

Notes on the SPI Interface Write

> Under Construction

#### H-Bridge Driver

As discussed above driving a 3 phase BLDC is quite complicated. This results in the driving hardware to be substantialy expensive compared to other types of motors. BLDCs in the market are generally designed for high rpm application and consiquetly the drivers for low RPM applications are expensive. I looked at a couple of options like ICs from a company called Trinamic Motion Control. All of these ICs integrate several features like back EMF sensing, current sensing, break before make etc; to have more accurate control on the motor. As much as I would have liked to have such features on my final project, it isnt exactly inexpensive. So I decided to go with the simplest driver possible. Here is the simplified version of the driver I am using.

<p align="center">
<img src="https://www.allaboutcircuits.com/uploads/articles/BLDC_DC_Motor_with_Hall_Effect_Sensors-1.jpg" />
</p>
<p align="center">
Fig 7: BLDC Driver.
</p>

Ideal this would require three half H-Bridges which are similar to switches for each coil. Unfortunately I only had access to a Dual Full H-Bridge, the L298N driver chip. This allowed me to drive up to 4 phases using the driver board. It has some good and bad attributes. One of the good attributes is the fact that it has ability to drive high currents up to 1.5 A per H-Bridge. The down side is that it has limited current sensing capabilities, which I do plan on using to make my control algorithm more effective.

<p align="center">
<img src="https://images-na.ssl-images-amazon.com/images/I/61VkNn0PcaL._SX425_.jpg" />
</p>
<p align="center">
Fig 8: L298N Driver Board.
</p>

#### Feather M0 for RT Procs

The Featger M0 is an Atmel SAMD21 developmemnt board which has a ARM Cortex M0. I am using it in conjenction with Arduino bootloader. This has made quick prototyping quite easy and the chip is more than capable of handling the control loop. It also has the advantage of having a higher clock speed as well as the ability to do higher frequency PWM. I can also speed up the code my modifying the timer registers directly. Later I describe how that works. Since the chip doesn't have any underlying operating system, we have to rely on interupts for time delays and servicing data sent by Raspberry Pi.

<p align="center">
<img src="https://cdn-shop.adafruit.com/970x728/2772-01.jpg" />
</p>
<p align="center">
Fig 9: Feather M0 from Adafruit.
</p>

PWM is Pulse Width Modulation. In a nutshell, it allows us to emulate an analog signal by sending a digital signal with varying width of digital high and low. If we do this rapidly, then the inductor treates the switching signal as similar to an analog average. To make this happen, the SAMD21 has keep tight track of time. It is obviouse that one can do this in software (bit-banging) by using the internal clock but that would make it difficult to handle multiple PWM outputs and perform other tasks as well. To facilitate this, the chip has several internal clocks that can be dispatched to perfomr the task of generating the PWM signal.

<p align="center">
<img src="https://microchip.wdfiles.com/local--files/32arm:samd21-mcu-overview/samd21-block-diagram.png" />
</p>
<p align="center">
Fig 10: SAMD21 Layout.
</p>

#### Rasberry Pi for Image Processing and Yale Bright Star Catalogue

The reason I choose to work with the Rasberry Pi was because it easy accessible and has plenty of support material online. This would make my work easier and make it possible for others to easily replicate my results. OpenCV takes about an hour to install on a Pi that is running Raspbian OS. I reccomend using [this](https://www.pyimagesearch.com/2018/09/26/install-opencv-4-on-your-raspberry-pi/) tutorial. Unfortunately, the image processing cannot be CUDA accellerated as Raspberry Pi doesnt have the neccessary hardware. This however is not that big of a problem because the image processsing I am attempting to do is not very compute intensive. Infact, one doesnt even need to use OpenCV. Here is a rough algorithm that one can use in any language of there choosing:

|Algorithm|Specifics|
|:--|:--|
|Set camera parameters like ISO, focus, exposure etc.|Most of these have to be set based on experimentation. But if one is using this for the night sky, the parameters will stay consistent accross multiple nights.|
|Aquire image data from camera.|The Pi Camera acts as a DMA device with the help of the Pi GPU. This means aquiring frames are quite easy.|
|Agrigate RGB into brightness map.|This can be done easily with array manipulation and is a good candidate for concurrency.|
|Locate the brightest spot on map.|This can be done by searching for the largest brightness value. I am concidering two different approaches to perform this concurrently. In one case each line of data can be assigned to a thread and the max value storage can be protected from data corruption with the help of locks. In another scenario we dont need locks. We just find the brightess point in each line concurrently and then find the brightess point in those bright point.|
|Compute delta.| Find out the postion of the brightest spot in relation to the center. This will require some filtering. Maybe a Kalman filter. Maybe apply kernel convolution in the RGB to grayscale step.|

Apart from using image processing for stabalization. We first need to get the object in frame. Despite there being heavy collaboration between astronomers around the world, I couldn't find a consolidated database for all stellar/celestial objects. I was able to find files from [universitites](http://tdc-www.harvard.edu/catalogs/bsc5.html) that stored mutiple objects that one can observe. The in realtion ship with GPS, time and orientation can be used to compute the location of objects in the sky. Moreover, advance simulation environments like [Stellarium](http://stellarium.org/) can also compensated for motions of different objects in the sky.

One things that I am still considering is running the real-time portion of the code on the Raspberry Pi. There are some provisions within Linux that claim to provide real-time like performance. [LinuxCNC](http://linuxcnc.org/) was a reference that seem to indicate that this is possible. There is also the option of using a real-time OS like [ChibiOS](http://www.chibios.org/dokuwiki/doku.php) and the trouble of porting all of the neccessary features to a real-time OS.

PUT RPI

#### Pi Camera NoIR

Pi Camera was an obvious choice because I am using a Raspberry Pi. The NoIR variant was a choice I made based on my application. To understand we need talk a little bit about how digital cameras work. The have a CCD or a Charge Coupled Device which is an array of small light sensors that capture levels of RGB in a scene. But one down side is that they are sensitive to IR light which throws of the color of the produced image. To fix this a camera often has an IR filter which blocks out IR light. This is useful for standard cameras but is less optimal for astro-photography. You want your sensor to get as much light as possible to you reduce ISO and get less noisy images. You can also reduce exposure time which can improve sharpness as vibrations in the system don't easily turn into artifacts in the image. So I went with the NoIR variant. The focus has to be manually adjusted for the Pi Camera. But this is a good thing as we can adjust it once and not have to worry about it later. 

The camera also works with the GPU to have frames available to the CPU using Direct Memeory Access (DMA). This can simplyfy the process of image aquasition and is ilustrated [here](https://picamera.readthedocs.io/en/release-1.13/fov.html).

PUT NOISE EXPOSURE IR PI CAMERA

### Software

The software is limited by the hardware available. But I have tried to keep it simple so it can be more readily used or ported to other hardware.

#### PWM approximation of Sine Wave

In the hardware section I talked about how the BLDC motor requires 3 phase 120 deg offset sine wave to control its rotor position. However, it is cost prohibitive to have the hardware produce a sine wave using a Digital to Analog Convertor (DAC). To work around this problem we can approximate a sine wave's analog levels with a Pulse Width Modulated (PWM) signal. PWM is a digital signal that is comprised of frames in which the pulse width can be modulated to get different duty cycles. Increasing the pulse width leads to the signal behaving more like a full duty-cycle signal and reducing the pulse width leads to the signal behaving more like a empty duty-cycle signal.

In my intial experimentation there were two PWM limiting factors; the frequency and resolution. The frequency is the number of frames the hardware can produce in a second. This was causing audible noise in the coils that is a function of the PWM frequency and to get rid of it I would have to push the frequency into the in-audible range of audio frequencies. The resolution is the number of individual pulse width sizes available in a given frame. This would have an effect on the theoretical resolution of the motor position. I would have to increase this enough to not run into resolution problems. To achieve this one has to set parameters for a timer in the SAMD21 and the process is illustrated [here](https://cdn.sparkfun.com/datasheets/Dev/Arduino/Boards/Atmel-42181-SAM-D21_Datasheet.pdf) in Section 30 on page 651.

Sine appoximation can be done using a look-up table that store the PWM values for a sine wave. I found this [calculator](http://elabz.com/wp-content/uploads/2011/11/sin_funct.zip) online that provided the values which can then be used to to drive the motor. It might still be possible to drive the motor using a more true sine waves using audio hardware which is relatively in-expensive. 

#### Kalman Filter

The kalman filter is intended to zero in on the true value of the sensor by taking multiple observations. It is relatively simple to implement for a single sensor and you can find multiple libraries that have the filter already implemented. The basic idea is that previous observation and approximation is used to evaluate the trueness of the current observation to derive a new approximation. I found the idea similar to old RTT estimation method in TCP. 

#### 10 arc-min Resolution with Encoder

This resolution will be attained by using a database to obtain absolute position of the object in the sky. Then computing the relative postion of the telescope to that object. That data can be fed into the targets of the PIDs that controls the BLDC and gets feedback from the encoder.

#### Zero Torque Problem

As the BLDC reaches the intended postion. The torque on the motor nears zero, which means it can hold that position. This is a problem as we want to keep the system steady at that position. To get around this problem we can oscilate the target postion at a high frequency around the intended postion so that the rotor has a holding torque. This however seems to be overcomplicated solution to a simple problem. If we for-go the sine approximation and just switch between the 6 orientations acheivable by a digital signal in the motor. We can have simpler solution to the zero torque problem. 

#### 1 arc-min Resolution with Image Processing

Once the object is in the field of view we switch from the encoder to the image processing algorithm to control the motor using PID.

## Test Bench

> Under Construction

## Experiments

This is the log of all the expriments I have run so far.

### AS5048A

Rotary Encoder Independent Test

#### Steady State

#### Steady State with Kalman

#### Linear Change

#### Linear Change with Kalman

### BLDC

## Areas to Explore

> Under Construction

### Hardware

> Under Construction

#### TMC6200

> Under Construction

#### TMC4671

> Under Construction

#### Axial Flux Motor

> Under Construction

#### VESC

> Under Construction

### Software

> Under Construction

#### Reinforcement Learning Control Algorithms

> Under Construction

#### Neural Flight Mod

> Under Construction

#### Braking

> Under Construction

#### Velocity Control

> Under Construction

#### Acceleration Control

> Under Construction

## References

> Under Construction
