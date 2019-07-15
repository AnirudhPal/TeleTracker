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
<img src="https://www.renesas.com/img/misc/engineer-school/fig3-a-bldc-monitor-en.gif" />
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

#### H-Bridge Driver

#### Feather M0 for RT Procs

#### Rasberry Pi for Image Processing and Stellarium

#### Pi Camera NoIR

### Software

#### 10 arc-min Resolution with Encoder

#### 1 arc-min Resolution with Image Processing

## Test Bench

## Experiments

## References
