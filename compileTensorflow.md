Draft Steps to compiling Tensorflow 1.5 with GPU Support for a Mac.


There are published builds on the internet for compiling TensorFlow for a Mac.  The following utilizes the approach published on Tweakmind.

<url>https://tweakmind.com/tensorflow-1-5-macos-10-13-2/</url>



Notes on the configuration commands.

For most items, you will say no. 

For the cuDNN version, you need to match the file name of `libcudnn.7.dylib` stored in /usr/local/cuda. For the time being, that is a numeric 7. No decimal places.



```
computer:tensorflow m070316$ ./configure
You have bazel 0.9.0-homebrew installed.
Please specify the location of python. [Default is /Users/username/anaconda3/bin/python]: 


Found possible Python library paths:
  /Users/username/anaconda3/lib/python3.6/site-packages
Please input the desired Python library path to use.  Default is [/Users/username/anaconda3/lib/python3.6/site-packages]

Do you wish to build TensorFlow with Google Cloud Platform support? [Y/n]: n
No Google Cloud Platform support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Hadoop File System support? [Y/n]: n
No Hadoop File System support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Amazon S3 File System support? [Y/n]: n
No Amazon S3 File System support will be enabled for TensorFlow.

Do you wish to build TensorFlow with XLA JIT support? [y/N]: n
No XLA JIT support will be enabled for TensorFlow.

Do you wish to build TensorFlow with GDR support? [y/N]: n
No GDR support will be enabled for TensorFlow.

Do you wish to build TensorFlow with VERBS support? [y/N]: n
No VERBS support will be enabled for TensorFlow.

Do you wish to build TensorFlow with OpenCL SYCL support? [y/N]: n
No OpenCL SYCL support will be enabled for TensorFlow.

Do you wish to build TensorFlow with CUDA support? [y/N]: y
CUDA support will be enabled for TensorFlow.

Please specify the CUDA SDK version you want to use, e.g. 7.0. [Leave empty to default to CUDA 9.0]: 9.2


Please specify the location where CUDA 9.2 toolkit is installed. Refer to README.md for more details. [Default is /usr/local/cuda]: 


Please specify the cuDNN version you want to use. [Leave empty to default to cuDNN 7.0]: 7


Please specify the location where cuDNN 7 library is installed. Refer to README.md for more details. [Default is /usr/local/cuda]:


Please specify a list of comma-separated Cuda compute capabilities you want to build with.
You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
Please note that each additional compute capability significantly increases your build time and binary size. [Default is: 3.5,5.2]6.1


Do you want to use clang as CUDA compiler? [y/N]: n
nvcc will be used as CUDA compiler.

Please specify which gcc should be used by nvcc as the host compiler. [Default is /usr/bin/gcc]: 


Do you wish to build TensorFlow with MPI support? [y/N]: n
No MPI support will be enabled for TensorFlow.

Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native]: 


Add "--config=mkl" to your bazel command to build with MKL support.
Please note that MKL on MacOS or windows is still not supported.
If you would like to use a local MKL instead of downloading, please set the environment variable "TF_MKL_ROOT" every time before build.

Would you like to interactively configure ./WORKSPACE for Android builds? [y/N]: n
Not configuring the WORKSPACE for Android builds.

Configuration finished
```

Now compile based on the above configuration

```
bazel build --config=cuda --config=opt --copt=-msse4.2 --copt=-mpopcnt --copt=-maes --copt=-mcx16 --verbose_failures --action_env PATH --action_env LD_LIBRARY_PATH --action_env DYLD_LIBRARY_PATH //tensorflow/tools/pip_package:build_pip_package
```

Should the compile without error (yes, it will take some time), you should be able to build the Python wheel that can be install. This builds the wheel and saves it onto the desktop.

```
bazel-bin/tensorflow/tools/pip_package/build_pip_package ~/Desktop
```

Now install the wheel into your python directory. The pip3 assumes you are using Python 3.x. If you are using Anaconda, you can use just pip as they have aliased pip3 to pip for ease of use.  The name may have changed slightly based on options and/or python versions. The easiest option is to just use tab to autocomplete/expand the full file name.  sudo -H is to help with permissions and ensure you have access to install. This is essential if you are using the native python on a Mac (which is not recommended). 

```
sudo -H pip3 install ~/Desktop/tensorflow-1.5.0rc0-cp36-cp36m-macosx_10_13_x86_64.whl
```

At this point, one might as well install Keras to provide a test environment.

```
sudo -H pip3 install keras
```

Now, you can test the installations:

At the terminal, type python.

IMPORTANT: It is critical to always launch Python and later R from the terminal to have the envirnment variables realized. 

```
import tensorflow as tf
import keras

# check versions
print(tf.__version__)
print(keras.__version__)
# verify that keras indicates using TensorFlow as the backend.
```
