# some change

- Output Bias Random Walk of gyro and acc, which slope is 0.5.
- change output of noise as Continuous-Time form.
- fix the noise parameter of acc, include Q/N/K/R, remove coefficients 60 or 3600 etc.

The main idea is to fit log Allan Variance value using Q / N / B / K / R and different power include -1, -0.5, 0, 0.5, 1.

# imu_utils

A ROS package tool to analyze the IMU performance. C++ version of Allan Variance Tool. 
The figures are drawn by Matlab, in `scripts`.

Actually, just analyze the Allan Variance for the IMU data. Collect the data while the IMU is Stationary, with a two hours duration.

## refrence

Refrence technical report: [`Allan Variance: Noise Analysis for Gyroscopes`](http://cache.freescale.com/files/sensors/doc/app_note/AN5087.pdf "Allan Variance: Noise Analysis for Gyroscopes"), [`vectornav gyroscope`](https://www.vectornav.com/support/library/gyroscope "vectornav gyroscope") and 
[`An introduction to inertial navigation`](http://www.cl.cam.ac.uk/techreports/UCAM-CL-TR-696.html "An introduction to inertial navigation").

```
Woodman, O.J., 2007. An introduction to inertial navigation (No. UCAM-CL-TR-696). University of Cambridge, Computer Laboratory.
```
Refrence Matlab code: [`GyroAllan`](https://github.com/XinLiGitHub/GyroAllan "GyroAllan")

## IMU Noise Values

Parameter | YAML element | Symbol | Units
--- | --- | --- | ---
Gyroscope "white noise" | `gyr_n` | <img src="https://latex.codecogs.com/svg.latex?{%5Csigma_g}"> | <img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B100%7D%20%5Cfn_jvn%20%5Csmall%20rad/s"> 
Accelerometer "white noise" | `acc_n` | <img src="https://latex.codecogs.com/svg.latex?{%5Csigma_a}"> | <img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B100%7D%20%5Cfn_jvn%20%5Csmall%20m/s%5E2"> 
Gyroscope "bias Instability" | `gyr_b` | <img src="https://latex.codecogs.com/svg.latex?{%5Csigma_b_g}"> | <img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B100%7D%20%5Cfn_jvn%20%5Csmall%20rad/s"> 
Accelerometer "bias Instability" | `acc_b` | <img src="https://latex.codecogs.com/svg.latex?{%5Csigma_b_a}"> | <img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B100%7D%20%5Cfn_jvn%20%5Csmall%20m/s%5E2"> 
Gyroscope “random walk” | `gyr_bw` | <img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B100%7D%20%5Cfn_jvn%20%5Csmall%20%5Csigma_%7Bwg%7D"> | <img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B100%7D%20%5Cfn_jvn%20%5Csmall%20rad/s"> 
Accelerometer “random walk” | `acc_bw` | <img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B100%7D%20%5Cfn_jvn%20%5Csmall%20%5Csigma_%7Bwa%7D"> | <img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B100%7D%20%5Cfn_jvn%20%5Csmall%20m/s%5E2"> 

* White noise is at tau=1 or N;
* Bias Instability is around the minimum;
* Random walk is at tau=3000 or K 

(according to technical report: [`Allan Variance: Noise Analysis for Gyroscopes`](http://cache.freescale.com/files/sensors/doc/app_note/AN5087.pdf "Allan Variance: Noise Analysis for Gyroscopes"))

## sample test

<img src="figure/gyr.jpg">
<img src="figure/acc.jpg">

* blue  : Vi-Sensor, ADIS16448, `200Hz`
* red   : 3dm-Gx4, `500Hz`
* green : DJI-A3, `400Hz`
* black : DJI-N3, `400Hz`
* circle : xsens-MTI-100, `100Hz`

## How to build and run?

### to build

```
sudo apt-get install libdw-dev
```

* download required [`code_utils`](https://github.com/gaowenliang/code_utils "code_utils");

* put the ROS package `imu_utils` and `code_utils` into your workspace, usually named `catkin_ws`;

* cd to your workspace, build with `catkin_make`;


### to run

* collect the data while the IMU is Stationary, with a two hours duration;

* (or) play rosbag dataset;

```
 rosbag play -r 200 imu_A3.bag
```

* roslaunch the rosnode;

```
roslaunch imu_utils A3.launch
```

Be careful of your roslaunch file:

```
<launch>
    <node pkg="imu_utils" type="imu_an" name="imu_an" output="screen">
        <param name="imu_topic" type="string" value= "/djiros/imu"/>
        <param name="imu_name" type="string" value= "A3"/>
        <param name="data_save_path" type="string" value= "$(find imu_utils)/data/"/>
        <param name="max_time_min" type="int" value= "120"/>
        <param name="max_cluster" type="int" value= "100"/>
    </node>
</launch>
```

### sample output:

```
%YAML:1.0
---
type: IMU
name: simulator
Gyr:
   unit: " rad/s"
   avg-axis:
      gyr_n: 1.4812687142891424e-02
      gyr_b: 9.7998795103765280e-04
      gyr_bw: 4.6509871480460975e-05
   x-axis:
      gyr_n: 1.4692399528685599e-02
      gyr_b: 9.8518793903601973e-04
      gyr_bw: 2.8490000231200879e-05
   y-axis:
      gyr_n: 1.4844275115273257e-02
      gyr_b: 1.0037833725431490e-03
      gyr_bw: 5.8666018195331909e-05
   z-axis:
      gyr_n: 1.4901386784715413e-02
      gyr_b: 9.5099254153378971e-04
      gyr_bw: 5.2373596014850146e-05
Acc:
   unit: " m/s^2"
   avg-axis:
      acc_n: 1.9109749723765068e-02
      acc_b: 3.1248109984496363e-03
      acc_bw: 4.0387852301552378e-04
   x-axis:
      acc_n: 1.9279401330095185e-02
      acc_b: 3.2669662466337092e-03
      acc_bw: 4.4151611213750250e-04
   y-axis:
      acc_n: 1.8992891739389096e-02
      acc_b: 3.1861302761859002e-03
      acc_bw: 4.6298800537015376e-04
   z-axis:
      acc_n: 1.9056956101810924e-02
      acc_b: 2.9213364725292996e-03
      acc_bw: 3.0713145153891502e-04
```

## dataset

DJI A3: `400Hz`

Download link: [`百度网盘`](https://pan.baidu.com/s/1jJYg8R0 "DJI A3")


DJI A3: `400Hz`

Download link: [`百度网盘`](https://pan.baidu.com/s/1pLXGqx1 "DJI N3")


ADIS16448: `200Hz`

Download link:[`百度网盘`](https://pan.baidu.com/s/1dGd0mn3 "ADIS16448")

3dM-GX4: `500Hz`

Download link:[`百度网盘`](https://pan.baidu.com/s/1ggcan9D "GX4")

xsens-MTI-100: `100Hz`

Download link:[`百度网盘`](https://pan.baidu.com/s/1i64xkgP "MTI-100")
