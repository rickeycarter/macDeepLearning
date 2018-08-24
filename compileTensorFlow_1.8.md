# Steps for Compiling TensorFlow 1.8 with GPU Support

Successful compiliation using 2017 Macbook Pro running High Sierra (10.13.6)


Resources

- https://www.tensorflow.org/install/install_sources

- https://gist.github.com/Willian-Zhang/a3bd10da2d8b343875f3862b2a62eb3b
This gist contains a necessary patch file to make the compilation work

Prerequisites

* Follow the instructions on the TensorFlow compilation to install Bazel and obtain the correct Xcode version needed to compile.

* Make a working directory to put the GIT clone into. For example, within your home drive, you can make a directory called `tf19`. While this is not technically required, you may find yourself trying to compile various versions of TensorFlow. Cloning the repository into this directory allows you to create parallel environments (and configuration files) for various versions of TensorFlow. Sure, git management can also make this happen, but this is much cleaner for someone new to GIT.


** Suggested workflow

```
cd ~
mkdir tf_1.8
cd tf_1.8
git clone https://github.com/tensorflow/tensorflow 
cd tensorflow
git tag
```
The last command lists out all versions of TensorFlow that are contained in the repository. These change with time so it is helpful to list out the versions prior to selecting (checking out) a version. 

Now, checkout the version you desire. We will use the final release of version 1.9.

```
git checkout v1.8.0
```

# Configure Bazel and Xcode

Xcode 9.x appears to be the suggested version for compiling. Information on configuring and installing Bazel is [here](https://docs.bazel.build/versions/master/install-os-x.html). 

* Check the Xcode version

From a terminal, run
```
xcodebuild -version
```

If this reports version 8.x.x, you need to change it.  If you did this as was described in the setup, you should have multiple versions of Xcode available and all that is needed is to select a later version. For example, to select version 9.2, enter the follow.

** Build 8/24/18 ** : was successful using Xcode version 9.2

```
sudo xcode-select -s /Applications/Xcode-9.2.app
```

* Upgrading Bazel

If after installing and configuring bazel using the bazel build link, you can install the latest version using:

```
brew update bazel
```

Notes on the configuration commands.

For most items, you will say no. 

For the cuDNN version, you need to match the file name of `libcudnn.7.dylib` stored in /usr/local/cuda. For the time being, that is a numeric 7. No decimal places.

