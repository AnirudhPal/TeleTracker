# AR Telescope - CS590 Independent Study

## Abstract

Augmented/Virtual reality lets us explore worlds right from our living rooms. However, these are just virtual constructs. We often forgot the real environment around us, and in this case above the us. The night sky is quite fascinating and worth exploring. Unfortunately, the modernization of the world has let to substantial amount of light pollution. If you are in a major city, you would be luck to spot more than a dozen stars. In this independent study, I attempt to explore this often-underrated natural environment. I explore everything for constructing a telescope to star-pattern recognition and AR/VR integration. This multidisciplinary approach is enriching as it enabled me to integrate my academic field of computer graphics with my professional field of research manufacturing.

## Disclaimer

Right from the start, I wish to clarify that the information I present here is just an account of the work I did. It is by no means complete, optimal or ground-breaking. I ran into several technical challenges, some I was able to overcome, others I am still figuring out how to overcome. The biggest take-away that one can expect from this article is the telescope construction. The designs I came up with our quite a bit more mature than designs presented in other DIY articles. I use more precise industrial manufacturing techniques and draw a lot of inspiration from the world of research optics. Although I use industrial equipment to manufacture a lot of the parts for the project, the designs are quite adaptable to desktop 3D printing. 

## Star Detection

Before we get into the weeds of actual telescope construction, design and manufacturing; lets acknowledge that when we look up in the sky, most of us can appreciate the beauty of what we are looking at, but we are not as good at recognizing stars, celestial bodies and constellations. Knowing what we are looking at can further enhance our experience and can help us navigate the night-sky. Let’s look at an example:

<p align="center">
  <img src="assets/NightSkyBIDC.jpg">
</p>
<p align="center">
  Figure 1: Wide field shoot of the night sky. Beautiful, but there is more too it.
</p>

Here we see a picture of the night-sky. We see a lot of stars, but most of us do not really recognize what we are looking at. We also miss some significant stellar objects that we have managed to capture in the frame as they are drowned out in the sea of point lights. Let’s look at a processed image that was obtained by using a software tool called [Astrometry](http://astrometry.net/).

<p align="center">
  <img src="assets/NightSkyBIDCAnot.jpg">
</p>
<p align="center">
  Figure 2: Star detection using astrometry.
</p>

That gives us a lot more information about what we are looking at. This means that now we are able to make more interesting observations. Here is a crop of one of the spots on the previous picture. This might just look like a star, but in reality, we have captured a picture of the [Orion nebula](https://en.wikipedia.org/wiki/Orion_Nebula). We are able to make this determination because, with the help of the astrometry overlay, we know exactly where all stellar objects should be in the frame.

<p align="center">
  <img src="assets/NightSkyBIDCCrop.jpg" />
</p>
<p align="center">
  Figure 3: Orion nebula.
</p>

### Short Tutorial

In this tutorial I outline how to setup the astrometry local on a Windows 10 system. Please not that there is documentation available from astrometry.net, but their documentation is a little outdated compared to their current version of the software. This is a lot more simplified, but if you are interested here is the link to astrometry [documentation](http://astrometry.net/doc/build.html#build).

#### Install Windows Subsystem for Linux

The documentation for this process is can be found [here](https://www.windowscentral.com/install-windows-subsystem-linux-windows-10). Effectively, it adds a headless Ubuntu on your Windows 10 computer. This is important because the build process for Linux is much simpler than for Windows.

#### Update & Upgrade

If you installed the Windows Subsystem for the first time. Please run the following commands on the terminal.

> sudo apt-get update
> sudo apt-get upgrade

#### Install Astrometry

Now we can install astrometry through the package manager. This only install the executables for star detection, it does not have a star dataset to work with.

> sudo apt-get install astrometry.net

#### Get Index Files

Once astrometry is installed, we can now proceed to installing the necessary datasets for doing star pattern recognition. The terminal commands below are for widefield databases. However, there are more granular databases/index files available from astrometry on this [link](http://data.astrometry.net/debian/).

> wget http://data.astrometry.net/debian/astrometry-data-4208-4219_0.45_all.deb
> sudo dpkg -i astrometry-data-4208-4219_0.45_all.deb

#### Run Solver on Image URL

Now all the necessary files are installed, and we can use the solver on a sample image.

> solve-field [filepath]

Now if you image is located on your Windows File System, please add the following to the path

> solve-field /mnt/[full filepath]

#### Speed Optimization

There are several ways to speed up the star detection process. Here are the two optimization that I have tested and gotten the recognition to happen in seconds. The first you can apply universally to large image files. You can down sample the image by a factor by using this command.

> solve-field --downsample [downsample factor] [filepath]

After you solve the first image, you will know the field of view of your image. It will also tell you the index file that it used to recognize the pattern. I recommend that you only keep index files that are of the same field of view or smaller. The installed index files can be found in the following location:

> cd /usr/share/astrometry/

Use rm to remove index files that you don't need. The nomenclature of the index files are outlined [here](http://astrometry.net/doc/readme.html#getting-index-files).

## Physical Construction

The physical construction that I used is comprised of CNC machined parts made of steel and aluminum. I also use some off-the-shelf parts that are cheaply available. It is also possible to 3D print some of the parts. I provide descriptions of the parts and associated designs, so you can build this telescope for your own use. 

<p align="center">
  <iframe src="https://myhub.autodesk360.com/ue28e2e80/shares/public/SH56a43QTfd62c1cd968b35b710071d0253e?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
 </p>
<p align="center">
  Figure 4: CAD model of the telescope.
</p>

<p align="center">
  <img src="assets/Full_Pic.jpg" />
</p>
<p align="center">
  Figure 5: Telescope on a tripod.
</p>

The telescope tube can be sub-divided into three sub-assemblies.

### Back

The back provides the following functionality:
* Retain the primary mirror.
* Allow for collimation of the primary mirror.
* Connect to the middle sub-assembly.
* Heatsink for the primary mirror.

The mirror has to be suspended with minimal obstruction of the reflective surface. This is achieved through toe-clamp style fixturing in the four corners. Obstructions in the path of light will dim the image formed by the mirror and thus they would have to be minimized. It might be tempting to clamp on the mirror from the sides, but the fixturing of the mirror is not meant to be tight, as that can deform the mirror, and a side style clamping approach could result in the mirror falling out. Thus, toe-clamp is the best approach.

The parabolic mirror used has a very precise surface and needs to be positioned accurately in order to function properly. Unfortunately, it is very difficult to manufacture a retainment mechanism that can accurately position the mirror; thus, most optical setups include rotational adjustments that can be fine-tuned on the final assembly. This was achieved using a system of springs, a fulcrum and positioning screws.

The entire sub-assembly also needs to be linked up to the middle portion and maintain rigidity. I used straight beams because the telescope was small, but for a larger telescope one would need to use cross beams to increase the torsional rigidity of the frame. The metal also acts a passive heatsink for the mirror. The mirror changes its shape according to its temperature, this means it needs to be collimated while it is at its operating temperature. The metal helps this by allowing the temperature of the mirror to normalize with ambient temperature quickly.

<p align="center">
  <iframe src="https://myhub.autodesk360.com/ue28e2e80/shares/public/SH56a43QTfd62c1cd968ba4ba6a925e52e94?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
 </p>
<p align="center">
  Figure 6: CAD model of the back sub-assembly.
</p>

<p align="center">
  <img src="assets/Back_Pic.jpg" />
</p>
<p align="center">
  Figure 7: Back sub-assembly.
</p>

The key components in this sub-assembly are:

#### Backplate

It connects to the middle section (colored in red) using recess and screws. It also houses a pivot socket (colored in green) that a ceramic ball sits on. The weight of the structure is reduced by removing excess material (colored in blue). It also has spring retention sockets (colored in yellow) that provided tension on the pivot mechanism. There are also positioning screw holes (colored in purple) that pivot the mirror.

<p align="center">
  <iframe src="https://myhub.autodesk360.com/ue28e2e80/shares/public/SH56a43QTfd62c1cd968816ad51a74a58079?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
</p>
<p align="center">
  Figure 8: CAD model of backplate.
</p>

<p align="center">
  <img src="assets/Spring_Pic.jpg" />
</p>
<p align="center">
  Figure 9: Spring retention mechanism.
</p>

<p align="center">
  <img src="assets/Con_Pic.jpg" />
</p>
<p align="center">
  Figure 10: Connecting rod.
</p>

#### Primary Mirror Mount

The primary mirror mount has a recce for the mirror to sit in (colored in red). As well as a pivot socket (colored in green) for the ceramic ball. It also has spring retention sockets (colored in blue) similar to the backplate. There are also toe-clamp holes (colored in yellow) that are used to retain the mirror.

<p align="center">
  <iframe src="https://myhub.autodesk360.com/ue28e2e80/shares/public/SH56a43QTfd62c1cd968ce56d999e210499a?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
</p>
<p align="center">
  Figure 11: CAD Model of mirror mount.
</p>

<p align="center">
  <img src="assets/Piv_Pic.jpg" />
</p>
<p align="center">
  Figure 12: Pivoting socket with ceramic ball.
</p>

<p align="center">
  <img src="assets/TP_Pic.jpg" />
</p>
<p align="center">
  Figure 13: Toe-clamps.
</p>

### Middle

The middle provides the following functionality:
* Connection between the back and front sub-assemblies. 
* Mounting options to mount to an Arca Swiss compatible mount. 
* Allowing the center of mass of the telescope to be positioned on the mounting points. 

The back carries the primary mirror which needs to be connected to the front that houses the viewport, secondary mirror and eyepiece. It needs a stiff construction which is achieved through the use of solid steel rods. 

The telescope assembly also needs to connect to tripods, gimbal and other such mounts. This is achieved with the help of two movable dovetail connections that are compliment with the Arca Swiss mounting standard. 

Since, the connections are movable, they are also used to balance the mass of the telescope on the connection points. This inter reduces any moments of torque acting on those connection points, reducing the power required to drive the telescope. However, this only achieves what is known as static balance, which provides a few stable orientations. For heavier telescopes, one needs to achieve dynamic balance which will allow all telescope orientations to be stable. One way to achieve that is with the help of careful CAD modeling, where mass is accounted for accurately.

<p align="center">
  <iframe src="https://myhub.autodesk360.com/ue28e2e80/shares/public/SH56a43QTfd62c1cd968bd6389c20879c4a4?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
</p>
<p align="center">
  Figure 14: Cad model of the middle.
</p>

<p align="center">
  <img src="assets/Mid_Pic.jpg" />
</p>
<p align="center">
  Figure 15: Middle sub-assembly.
</p>

The key components of this sub-assembly are:

#### Rods

The rod is simple, but needs to be rigid, this part should not be 3D printed and at the very least should be made of a metal tube. I use solid steel with tapped holes (colored in red) for connections. Two rods together act as a guide rail for the movable Arca connections. 

<p align="center">
  <iframe src="https://myhub.autodesk360.com/ue28e2e80/shares/public/SH56a43QTfd62c1cd9686ad52dd276e1867a?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
</p>
<p align="center">
  Figure 16: CAD model of the rod.
</p>

<p align="center">
  <img src="assets/Rod_Pic.jpg" />
</p>
<p align="center">
  Figure 17: Rod connecting screw and hole.
</p>

#### Arca Connections

These connections consist of a dovetail portion (colored in red) that attaches to camera mounts. There are two sliding holes (colored in green) that slide on guide rods. It also has a pair of set screws (colored in blue) that immobilizes the connections at the balance point.

<p align="center">
  <iframe src="https://myhub.autodesk360.com/ue28e2e80/shares/public/SH56a43QTfd62c1cd968a792fe219f639985?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
</p>
<p align="center">
  Figure 18: CAD model of Arca Swiss mount.
</p>

<p align="center">
  <img src="assets/Sid_Pic.jpg" />
</p>
<p align="center">
  Figure 19: Sliding hole with setscrew.
</p>

### Front

 The front provides the following functionality:
* Retain the secondary mirror.
* Allow for collimation of the secondary mirror.
* Connect to the middle sub-assembly.
* Heatsink for the secondary mirror.
* Connection for the focuser. 

A lot of the functionality is carried over from the backplate, however the secondary mirror is much smaller, so the mechanism used in the backplate had to be miniaturized.

The connection to the focuser is made with the help of a series of connections. We have a straight connection to a M42 male thread. The male thread attaches to the female thread of the helical focuser. The focuser can then accept an eyepiece for manual observation. Or, you can attach a T mount style camera adapter that connects the focuser and a camera for digital observations. 

<p align="center">
  <iframe src="https://myhub.autodesk360.com/ue28e2e80/shares/public/SH56a43QTfd62c1cd968408b3642f0e9a590?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
</p>
<p align="center">
  Figure 20: CAD model of front.
</p>

<p align="center">
  <img src="assets/Front_Pic.jpg" />
</p>
<p align="center">
  Figure 21: Front sub-assembly.
</p>

The key components of this sub-assembly are:

#### Secondary Mirror Mount

The secondary mirror mount has the same features as the back sub-assembly, but it has been miniaturized.

<p align="center">
  <img src="assets/Sec_Pic.jpg" />
</p>
<p align="center">
  Figure 21: Secondary mirror mount.
</p>

#### Frontplate

The frontplate has mounting points (colored in red) to connect to the middle sub-assembly. It also has a view opening (colored in green) for light to enter the telescope. The secondary mirror mount also has a set-screw connection (colored in blue) for attachment. The focuser connects via press-fit port (colored in yellow). 

<p align="center">
  <iframe src="https://myhub.autodesk360.com/ue28e2e80/shares/public/SH56a43QTfd62c1cd968a476cb4af7430fb8?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
</p>
<p align="center">
  Figure 22: CAD model of front part.
</p>

<p align="center">
  <img src="assets/View_Pic.jpg" />
</p>
<p align="center">
  Figure 23: View port and press-fit connection.
</p>

#### Focuser

The helical focuser allows for fine manual adjustment of the focus. It also lets us mount eyepieces, collimating tool and camera mount to the telescope. 

<p align="center">
  <img src="assets/Eye_Pic.jpg" />
</p>
<p align="center">
  Figure 24: Focuser with eyepiece.
</p>

<p align="center">
  <img src="assets/Cam_Pic.jpg" />
</p>
<p align="center">
  Figure 25: Focuser with camera.
</p>

## VR Tracking Data

To integrate the VR headset with telescope, one of the things that I experimented with was getting the orientation data from the headset to drive a pan and tilt servo. The pan and tilt servo mirrors the orientation of the VR headset. I am using an Oculus Quest, so I was able to get the tracking data wirelessly using the ADB tool. Once I establish wireless ADB connection, I run the following code in Processing:

```c
// Import Libs
import java.io.*;
import processing.serial.*;

Serial myPort;  // Create object from Serial class
String val;     // Data received from the serial port

// Setup
void setup() {
  // Set Canvas
  size(640, 360, P3D);

  // Fill Object with Color
  fill(100);
  
   // Set Camera
  camera(0.0, 0.0, 200.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0); // (Pos, LookAt, Dir)
  
  String portName = Serial.list()[2]; //change the 0 to a 1 or 2 etc. to match your port
  println(portName);
  myPort = new Serial(this, portName, 9600);
}

// Render Loop
void draw() {
  // Get Orientation
  String ort = getOrient();

  // Send to Arduino
  sendArduino(ort);

  // Graph Cube
  graphCube(ort);
}

// Graph Cube
void graphCube(String rot) {
  // Split Component
  String[] rots = rot.split(",");

  // Extract Float
  //float A = float(rots[0]);
  float B = float(rots[1]);
  float C = float(rots[2]);

  // Set Lights
  lights();

  // Change Background
  background(0);
  
  // Rotate
  rotateX(radians(-B));
  rotateY(radians(C));

  // Create Box
  box(90);

  // Set Line Color
  stroke(255);

  // Create Lines
  line(-100, 0, 0, 100, 0, 0);
  line(0, -100, 0, 0, 100, 0);
  line(0, 0, -100, 0, 0, 100);
}

// Send Orientation PWM to Arduino
void sendArduino(String rot) {
  // Split Component
  String[] rots = rot.split(",");

  // Extract Float
  //float A = float(rots[0]);
  float B = float(rots[1]);
  float C = float(rots[2]);
  
  // Move to Positve
  B += 90.0;
  C += 90.0;
  
  // Trim Bottom
  if(B < 0.0)
    B = 0.0;
  if(C < 0.0)
    C = 0.0;
    
  // Trim Top
  if(B > 180.0)
    B = 180.0;
  if(C > 180.0)
    C = 180.0;

  // Map to PWM Char
  //char a = char(int(map(A, 0.0, 10.0, 0.0, 255.0)));
  char b = char(int(B));  // Pitch
  char c = char(int(C));  // Yaw

  // Send Data
  print("A");
  print(int(b));
  print("B");
  println(int(c));
  String out = "A" + b + "B" + c;
  myPort.write(out);
}

// Get VR Headset Orientation
String getOrient() {
  // Using ADB to Get Orientation
  String[] argv = {"adb", "shell", "dumpsys TrackingService | grep \"Rotation\" | head" };

  // Hold Response
  String std_output;

  // Try - Catch Block
  try {
    // Create Buffer and Stream Input from Command Line Execution
    BufferedReader in = new BufferedReader(new InputStreamReader(exec(argv).getInputStream()));

    // Read Line
    std_output = in.readLine();

    // Index of Euler
    int ind = std_output.indexOf("Euler");

    // Error Check
    if (ind < 0)
      throw new IOException();

    // Get Substring of Euler
    std_output = std_output.substring(ind);

    // Index of ( & )
    int ind1 = std_output.indexOf("(");
    int ind2 = std_output.indexOf(")");

    // Error Check
    if (ind1 < 0 || ind2 < 0)
      throw new IOException();

    // Get Substring of Rotation
    std_output = std_output.substring(ind1+2, ind2);

    // Close Input Buffer
    in.close();
  } 
  catch(IOException ee) {
    // Error (Return Null Orientation)
    std_output = "0.0, 0.0, 0.0";
  }

  // Return Orientation
  return std_output;
}
```

Here is a picture of the setup of the pan and tilt servo:

<p align="center">
  <img src="assets/Ard_Pic.jpg" />
</p>
<p align="center">
  Figure 26: Pan and tilt servo with Arduino.
</p>

## References

1. [Amateur Telescope Making Community](https://stellafane.org/tm/atm/index.html)
2. [Fusion 360 Learning Resources](https://www.autodesk.com/products/fusion-360/learn-support)
3. [Bechtel Center Gene Haas Labs](https://www.purdue.edu/bidc/)
4. [Thor Labs](https://www.thorlabs.com/)
