# AR Telescope - CS590 Independent Study under Dr. Voicu Popescu

## Index

## Abstract

We live in a time of great technological innovation. Augmented/Virtual reality lets us explore worlds right from our living rooms. However, these are just virtual constructs. We often forgot the real enviroment around us, and in this case above the us. The night sky is quite fascinating and worth exploring. Unfortunately, the modernization of the world has let to substantial amount of light pollution. If you are in a major city, you would be luck to spot more than a dozen stars. In this independent study, I attempt to explore this often underrated natural environment. I explore everything for constructing a telescope to star-pattern recognition and AR/VR integration. This multidisciplinary approach is enriching as it enabled me to integrate my academic field of computer graphics with my proffesional field of research manufacturing.

## Disclaimer

Right from the start, I wish to clarify that the information I present here is just an account of the work I did. It is by no means complete, optimal or ground-breaking. I ran into several technical challenges, some I was able to overcome, others I am still figuring out how to overcome. The biggest take-away that one can expect from this article is the telescope construction. The designs I came up with our quite a bit more mature than designs presented in other DIY articles. I use more precise industrial manufacturing tecniques and draw a lot of inspiration from the world of research optics. Although I use industrial equipment to manufacture a lot of the parts for the project, the designs are quite adaptable to desktop 3D printing. 

## Star Detection

Before we get into the weeds of actual telescope construction, design and manufacturing; lets acknowledge that when we look up in the sky, most of us can appreicate the beauty of what we are looking at but we are not as good at recognizing stars, celesetial bodies and constelations. Knowing what we are looking at can further enhance our experience and can help us navigate the night-sky. Lets look at an example:

<p align="center">
  <img src="assets/NightSkyBIDC.JPG" />
</p>
<p align="center">
  Figure 1: Wide-field shoot of the night sky. Beautiful, but there is more too it.
</p>

Here we see a picture of the night-sky. We see a lot of stars, but most of us do not really recognize what we are looking at. We also miss some significant stellar objects that we have managed to capture in the frame as they are drowned out in the sea of point lights. Lets look at a processed image that was obtained by using a software tool called [Astrometry](http://astrometry.net/).

<p align="center">
  <img src="assets/NightSkyBIDCAnot.jpg" />
</p>
<p align="center">
  Figure 2: Star detection using astrometry.
</p>

That gives us a lot more information about what we are looking at. This means that now we are able to moke more intresting observations. Here is a crop of one of the spots on the previous picture. This might just look like a star, but in reality we have captured a picture of the [Orion nebula](https://en.wikipedia.org/wiki/Orion_Nebula). We are able to make this determination because, with the help of the astrometry overlay, we know exactly where all stellar objects should be in the frame.

<p align="center">
  <img src="assets/NightSkyBIDCCrop.jpg" />
</p>
<p align="center">
  Figure 3: Orion nebula.
</p>

### Short Tutorial

In this tutorial I outline how to setup the astromtery local on a Windows 10 system. Please not that there is documentation available from astrometry.net, but their documentation is a little outdated compared to their current version of the software. This is a lot more simplified, but if you are interested here is the link to astrometry [documentation](http://astrometry.net/doc/build.html#build).

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

Once astrometry is installed, we can now proceed to installing the neccessary datasets for doing star pattern recognition. The terminal commands below are for widefield databases. However, there are more granular databases/index files available from astrometry on this [link](http://data.astrometry.net/debian/).

> wget http://data.astrometry.net/debian/astrometry-data-4208-4219_0.45_all.deb
> sudo dpkg -i astrometry-data-4208-4219_0.45_all.deb

#### Run Solver on Image URL

Now all the neccesary files are installed and we can use the solver on a sample image.

> solve-field [filepath]

Now if you image is located on your Windows File System, please add the following to the path

> solve-field /mnt/[full filepath]

#### Speed Optimization

There are several ways to speed up the star detection process. Here are the two optimization that I have tested and gotten the recognition to happen in seconds. The first you can apply universally to large image files. You can downsample the image by a factor by using this command.

> solve-field --downsample [downsample factor] [filepath]

After you solve the first image, you will know the field of view of your image. It will also tell you the index file that it used to recognize the pattern. I recommend that you only keep index files that are of the same field of view or smaller. The installed index files can be found in the following location:

> cd /usr/share/astrometry/

Use rm to remove index files that you don't need. The nomenclature of the index files are outlined [here](http://astrometry.net/doc/readme.html#getting-index-files).

## Physical Construction

## Star Tracking

## VR/AR Integration
