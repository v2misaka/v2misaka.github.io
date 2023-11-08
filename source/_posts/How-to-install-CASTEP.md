---
title: How to install CASTEP.
date: 2021/12/28 22:47
categories: 
- Code and Software
- CASTEP
---
This post is ***not*** a substitute to official tutorial, and is just a simplified version.  
If you have obtain CASTEP, after unpacking, you can read the official install tutorial: **README.INSTALL**

<!-- more -->
A cost-free worldwide source code license to CASTEP and NMR CASTEP for academic use is availible.
Before reading this post, you can get CASTEP through the website:
<center>http://www.castep.org/CASTEP/GettingCASTEP </center>  
<br/>

# System Environment

These are required to compile CASTEP:  
**<font size="4">1. Fortran 2003 compiler. </font>**  
Here I recommend GNU Fortran (4.9.1, 5.4.0, 6.3-4, 7.2-7.5, 8.*, 9.2, 9.3.0, 10.1.0).  
**<font size="4">2. Numerical libraries. (implementing the LAPACK and BLAS functionality)</font>**  
Intel MKL is recommended as it also contains an optimised FFT library. I use mkl included in Intel oneAPI.  
**<font size="4">3. GNU make version 3.81 or later.</font>**  
**<font size="4">4. Python.</font>**  
**<font size="4">5. Optimised FFT library</font>**  
Intel MKL

While using HPC of WHU, you should load **scl/gcc4.9** and **intel/oneapi/2021** first while install and in the *.sbatch* file.

# Install

We use CASTEP-21.11 as an example.

First of all:  

    tar -zxf CASTEP-21.11.tar.gz
    cd CASTEP-21.1
    make
Then open the file: *Makefile*  
Modify the value of **FFT** and **MATHLIBS** to `mkl`. Save and quit.  

Then install through code:

    make install
After installation, you can check through code:

    make check FFT=mkl MATHLIBS=mkl

# Calling the programme

You can call the programme through code:

    castep.serial <seedname>
If you have not (or cannot) add castep into *path*, use the code:

     *...\linux_x86_64_ifort\castep.serial <seedname>

# Online Tutorials

<p align="left">Online and official tutorial is availible through the website:  </p>
<center>http://www.castep.org/CASTEP/OnlineTutorials </center>
