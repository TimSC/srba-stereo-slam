# SRBA-Stereo-SLAM
**Note:** *Preliminary version*

Library that performs stereo visual SLAM within a Sparser Relative Bundle Adjustment (SRBA) framework. 
More info about SRBA can be found [here](http://www.mrpt.org/srba)  

## Building from sources

### Prerequisites

* CMake
* [MRPT](https://github.com/MRPT/mrpt) (>=1.3.0 ?)
* OpenCV (>=2.4.0 ?)
* [SRBA](https://github.com/MRPT/srba)
* [stereo-vo](https://github.com/famoreno/stereo-vo)

Many of those can be installed in Ubuntu with:

    sudo apt-get install build-essential cmake libmrpt-dev libopencv-dev

**Note:** Better efficiency can be achieved if `MRPT` and `OpenCV` are compiled from sources instead of grabbed with `apt-get` by instructing the compiler to optimize for native architecture.

### Compiling

...
