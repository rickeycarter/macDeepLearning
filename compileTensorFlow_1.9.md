# Steps for Compiling TensorFlow 1.9 with GPU Support (Draft)


**IMPORTANT:** Read the links guide carefully along with the TensorFlow documentation. There are subtle requirements such as Xcode and Bazel versions that need to be satisfied. Compiling TF version 1.5 requires different versions than TF version 1.9. 

Resources

- https://www.tensorflow.org/install/install_sources



Prerequisites

* Follow the instructions on the TensorFlow compilation to install Bazel and obtain the correct Xcode version needed to compile.

* Make a working directory to put the GIT clone into. For example, within your home drive, you can make a directory called `tf19`. While this is not technically required, you may find yourself trying to compile various versions of TensorFlow. Cloning the repository into this directory allows you to create parallel environments (and configuration files) for various versions of TensorFlow. Sure, git management can also make this happen, but this is much cleaner for someone new to GIT.


** Suggested workflow

```
cd ~
mkdir tf19
cd tf19
git clone https://github.com/tensorflow/tensorflow 
cd tensorflow
git tags
```
The last command lists out all versions of TensorFlow that are contained in the repository. These change with time so it is helpful to list out the versions prior to selecting (checking out) a version. 

Now, checkout the version you desire. We will use the final release of version 1.9.

```
git checkout v1.9.0
```

# Configure Bazel and Xcode

Xcode 9.x appears to be the suggested version for compiling. Information on configuring and installing Bazel is [here](https://docs.bazel.build/versions/master/install-os-x.html). 

Notes on the configuration commands.

For most items, you will say no. 

For the cuDNN version, you need to match the file name of `libcudnn.7.dylib` stored in /usr/local/cuda. For the time being, that is a numeric 7. No decimal places.

