---
layout: post
title:  "Pybullet Installation Steps"
date:   2016-12-02 17:00:04 -0700
categories: jekyll update
---

Pybullet is a python wrapper for [Bullet Physics SDK](https://github.com/bulletphysics/bullet3) -  real-time collision detection and multi-physics simulation for VR, games, visual effects, robotics, machine learning etc. The absence of a formal procedure to install pybulellet encourages me to write this post. Pybullet is still in development and the installation steps are expected to change over time.  

#### Pre-Installation  
1. Ensure that Anaconda is not your default python environment. A quick way to ensure that is by changing the python path variable in `~/.bash_profile`. Comment it out to use default python.
2. [Install cmake](https://cmake.org/install/).
3. Install Xcode for MAC OS.  

#### Installing Pybullet
1. `git clone https://github.com/bulletphysics/bullet3` in the directory where you wish to keep bullet binaries (prefer ~)
2. `cd bullet3`
3. Run `sh build_and_run_cmake_pybullet_double.sh` to start the installation
4. `cd build_cmake/examples/pybullet/`
5. Run `ln -s pybullet.2.85.dylib pybullet.so` to create a symlink for pybullet. 
6. `python -c 'import pybullet as pb'` should now run without any errors in the pybullet directory
7. `export PYTHONPATH=[cwd for step 1-5]` to call pybullet from outside this directory. 
8. Test importing Pybullet from a different directory. 


Enjoy Pybullet : )
