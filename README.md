# macDeepLearning

Revised - September 10, 2018

# Setup and configuration help for external eGPUs on a Mac System. 

## Shopping List

- NVIDIA Titan Xp gpu (https://www.nvidia.com/en-us/titan/titan-xp/)

- AkiTio Node Pro enclosure (https://www.akitio.com/expansion/node-pro)

- Cabling: If using Thunderbolt 3 cables, the cable must be the high speed version (40 gbps). A USB-C end is not sufficient for high speed transfer.  Cables over 0.8m may not allow for 40gbps transfer speeds.  If using TB1/TB2, you need the TB3 -> TB2 adapter (https://www.apple.com/shop/product/MMEL2AM/A/thunderbolt-3-usb-c-to-thunderbolt-2-adapter) and a standard TB2 cable. (note: the TB3 plug goes into the eGPU and the 



There are two paths that can be considered –
*  Manual installation of NVIDIA web drivers and CUDA, but this approach still requires an OS patch to enable eGPU support. 

*  The second is to use on of the all-in-one scripts that are now available. Check the license agreements on the scripts to make sure they are consistent with your needs. 

This guide is going to use option 2, building upon the tremendous work happening at eGPU.io.  The script by learex is all encompassing and recommended as the first resource to try.

https://github.com/learex/macOS-eGPU


## Handy macOS commands

* Show hidden files: shift + command + period


# Step 0: Basic Installation of Software

## R 

Download the latest R version from https://www.r-project.org/.  At time of writing, version 3.5.1 was the latest release. 

## RStudio 

Download the lastest daily build from https://dailies.rstudio.com/. The daily builds ("beta" versions) of RStudio have advanced integration capabilities for Python into R. It is recommended that you download the latest daily build from RStudio. If you have issues on your computer, you can always try the latest public release. 

Launch RStudio and install some additional packages beyond the base.
```

install.packages("tidyverse")
install.packages("devtools")
install.packages("roxygen2")
install.packages("reticulate")
install.packages("tensorflow")
install.packages("keras")
install.packages("tfruns")

```

## Anaconda 

Install Anaconda / Python 3.6 from https://www.anaconda.com/download/#macos. The latest build of anaconda will likely have a version of Python newer than what is available in the default installation. The systems that have been built and tested use version 3.6.4. Once Anaconda has been installed, you can switch underlying python versions by the following command:

```
conda install python=3.6.4
```

It is suspected that many other 3.6.* versions would work. Some online resources suggest having just straight python installed.  Anaconda with the IDEs (spyder, Jupiter) will be helpful in learning python and getting started. It is recommended that this is where most users start.

As an aside on python, MacOS comes with a default version of python installed (2.7). This will be incompatiable with many modern packages. Anaconda will automatically mask out the old 2.7 installation on the Mac and make things like `pip install` work directly (instead of having to specify `pip3 install`). 

# Step 1: Back up

There will be several important changes to the OS and the configuration. You may elect to do a full system backup before beginning to a new external drive via Time Machine. If you have a previous Time Machine backup, this can be used, but sometimes, it is easier to have just one clean backup to work from. 

You can speed up the first back up by using this command in the Terminal:
```
sudo sysctl debug.lowpri_throttle_enabled=0
```

After the back up has been completed, re-enable the low priority task throttling
```
sudo sysctl debug.lowpri_throttle_enabled=1
```

# Step 2: disable system integrity protections (referred to as SIP frequently online)

In the terminal, enter
```
csrutil disable
```

Reboot

Verify status:
```
csrutil status
```
At this point, SIP should indicate it has been disabled.

# Step 3: Run the macOS-eGPU script

IMPORTANT: The eGPU should be disconnected from the computer at this point. You can have it all assembled, but do not plug it into the computer at this time.

Using the up-to-date documentation on @learex page, run the automated script. You can view and verify the code he uses on GitHub. This is a good practice to consider when running code at the administrator level on your computer.


* First a test run of the command

Note that this bash script has a -C (capital C) flag. Running this in this way will just check the system, install the macos-egpu program, but not install anything. It will do a series of checks with the flag.  After running this, use just the short cut command and flags as noted in the next step.

```
bash <(curl -s https://raw.githubusercontent.com/learex/macOS-eGPU/master/macOS-eGPU.sh) -C
```

* Run a second time but using no flags (automatic installation mode)

Since the program is already install, simply run the following

```
macos-egpu
```

And follow the instructions on the screen.

* Reboot and rerun

Depending on your system configuration, you may need to reboot and rerun the `macos-egpu` script multiple times. Older machines will need to have a TB1/TB2 patch. The system will automatically install the latest NVIDIA CUDA drivers and Web Drivers.   If you are using the AkiTio Node Pro, the chipset is T83, so you can indicate no to the question when prompted. 

* Run "Check" one more time

```
macos-egpu -C
```

Verify that all looks well and there are no scheduled updates.

# Step 4: Configure CUDA

* Install CUDA

If after running the check above you find that CUDA and the smaples are not installed, you can install them using the following commands

Execute the following command to have the GPU CUDA drivers installed 
```
macos-egpu --cudaDriver
```

Install the toolkit.
```
macos-egpu --cudaToolkit
```
Install the CUDA samples. They are helpful for testing the GPU once it is attached. 

```
macos-egpu --cudaSamples
```

The computer will have CUDA 9.2 installed. For the CUDA to be fully operational for deep learning, system parameters need to be set.

* Edit `.bashrc`

Change to your home directory (`cd ~` from a terminal). 

type
```
nano .bashrc
```

In the editor window, paste in the following parameters

```
export PATH=/Developer/NVIDIA/CUDA-9.2/bin${PATH:+:${PATH}}

export DYLD_LIBRARY_PATH=/Developer/NVIDIA/CUDA-9.2/lib\ ${DYLD_LIBRARY_PATH:+:${DYLD_LIBRARY_PATH}}

export LD_LIBRARY_PATH=$DYLD_LIBRARY_PATH

export DYLD_LIBRARY_PATH=/usr/local/cuda/lib:$DYLD_LIBRARY_PATH
```

If you are not used to using nano, use control o to write the file changes to disk. Then use control x to close (note: this is control and not the typical Mac command).

* Edit the `.bash_profile` file

```
nano .bash_profile
```

Then paste in this command
```
if [ -f $HOME/.bashrc ]; then
        source $HOME/.bashrc
fi

```
Command o followed by command x to save and exit the nano program.

* Reboot

While you might have success bashing the profile files, it is simplier to just reboot.

# Step 5: Install cuDNN

Access to NVIDIA deep learning libraries requires access to their developer site. They grant free access, but you must register.

Go to developer.nvidia.com and follow the steps for registration. cuDNN can be downloaded at <url>https://developer.nvidia.com/cudnn</url>. 

You will need to navigate through this page to download the correct version. At this time, that is cuDNN 7.1.4 for Mac OSX for CUDA 9.2.  

After downloading and uncompressing the files, follow the steps to copy the libraries over to the appropriate directories.
<url>https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html</url>

Briefly, 
```
cd ~/Downloads/
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib/libcudnn* /usr/local/cuda/lib
sudo chmod a+r /usr/local/cuda/include/cudnn.h 
sudo chmod a+r /usr/local/cuda/lib/libcudnn*
```

(the necessary environment variable for cuDNN was set above into the .bashrc file). So, you should be able to run this command without error. The system may report a warning, but an error is a problem. If you have an error, verify the paths above and reboot.

```
echo -e '#include"cudnn.h"\n void main(){}' | nvcc -x c - -o /dev/null -I/usr/local/cuda/include -L/usr/local/cuda/lib -lcudnn
```
You can also do a simple test to make sure CUDA is working properly.

```
kextstat | grep -i cuda
```

This should return something along these lines. An error indicates a problem with your CUDA installation. Check your environment variables once again.

```
 168    0 0xffffff7f80d2e000 0x2000     0x2000     com.nvidia.CUDA (1.1.0) E13478CB-B251-3C0A-86E9-A6B56F528FE8 <4 1>
```
 
# Step 6: Compile a few CUDA test programs

The CUDA toolkit comes with downloaded samples. Using the installation appraoch above, you will have the samples installed in `\Developer\NVIDIA\CUDA-9.2\samples`.

This directory is write-protected by default. You can copy this folder to your home drive if you would like, or, you could change the permissions on this directory only. Using finder, right click over the samples directory and select "get info". From here, you will see the permissions tab. Click the padlock in the lower right to unlock the permission. Click the "+" and add your username. Change the permission to read + write. There's one last step which is in the small gear: select "apply to enclosed items" to recursively apply the permissions.

At this point, compiling a few test samples is possible, but first, you need to verify the correct XCode versions. This documentation is available here:
<url>https://docs.nvidia.com/cuda/</url>

For CUDA 9.2, Xcode 9.2 is required. This likely means you will need to download version 9.2 from Apple. This, like NVIDIA, will require a free developer account to access to code.  You can have multiple versions of Xcode installed on your computer, so a naming strategy is required. Multiple versions of Xcode will be required for Tensorflow installation, which will be involved later in the process. 

Download Xcode to your ~/Downloads/ directory.  Double click on it to uncompress it. This is time consuming, be patient. Ensure your computer has good ventilation to avoid thermal throttling. 

After it has uncompressed, run the following commands to move (and rename) Xcode (version 9.2) and set it as your default compiler. These system commands run much faster than the UI copy and paste options. 
```
cd ~/Downloads/
mv Xcode.app Xcode-9.2.app
sudo mv Xcode-9.2.app /Applications
sudo xcode-select -s /Applications/Xcode-9.2.app
sudo xcodebuild -license
```

Now change directories to where the CUDA samples are stored and compile a few of them.
```
cd /Developer/NVIDIA/CUDA-9.2/samples
make -C 1_Utilities/deviceQuery
```

# Step 7: Connecting the eGPU

Once you compile the deviceQuery program, the output will be located here:
```
/Developer/NVIDIA/CUDA-9.2/samples/bin/x86_64/darwin/release
```

You can double click on the program and run it without the eGPU connected.  It should report a CUDA error indicating no device was found. This is normal.  

```
cudaGetDeviceCount returned 35
-> CUDA driver version is insufficient for CUDA runtime version
Result = FAIL
```


We are now ready to test the eGPU.

The approach to connect the eGPU appears to vary based on computer with MacBook Pros seemingly being more reliable than iMac. To start the process, try this approach.

Log out of the computer but do not restart (Apple - Log out). Once you are presented the log on fields, connect the eGPU to the computer. If all goes well, you should see and hear the eGPU / GPU come to life (indicator lights and fan noises). In prior eGPU releases, you would wait for the screen to flash indicating the drivers had flipped to the external GPU.  Give the computer about 30 seconds and then log into the computer. 

You should be able to open System Preferences (Apple - System Preferences) and then click on the CUDA app. The app should report a GPU being found. Now using the left arrow to return back to the main System Preferences panel. Then, click on the NVIDIA Device Manager app. This should report the Titan Xp GPU under a few tabs.

If you see the GPU in both places, try to rerun the deviceQuery app that was compiled above. If this run, your eGPU is now fully configured and the next steps are to update the Python installation and obtained (compile) a TensorFlow wheel with GPU support for the MacOS. It is worth noting, as will be shown, that everything needs to be synchronized with versions for Python, CUDA and OS. Compiling from the source helps with this. Downloading wheels off the internet will inevitably not work until you realize you have to be selective with what you find. 

Example of a step-by-step Compilation:
<url>https://gist.github.com/orpcam/73b208271856fa2ae7efc00a8768bd7c#gistcomment-2397350</url>


# Next Steps - Compile TensorFlow

TensorFlow specific builds and configurations are posted. These are a work in progress. Precompiled wheels are also made available as we have more success with compilation.
