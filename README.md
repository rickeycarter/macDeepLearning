# macDeepLearning

August 6, 2018

Setup and configuration help for external eGPUs on a Mac System. 

NVIDIA Titan Xp gpu
Atikio Node Pro enclosure
Cabling: If using Thunderbolt 3 cables, the cable must be the high speed version (40 gbps). A USB-C end is not sufficient for high speed transfer.  Cables over 0.8m may not allow for 40gbps transfer speeds.  If using TB1/TB2, you need the TB3 -> TB2 adapter (https://www.apple.com/shop/product/MMEL2AM/A/thunderbolt-3-usb-c-to-thunderbolt-2-adapter) and a standard TB2 cable. (note: the TB3 plug goes into the eGPU and the 



There are two paths that can be considered –
*  Manual installation of NVIDIA web drivers and CUDA, but this approach still requires an OS patch to enable eGPU support. 

*  The second is to use on of the all-in-one scripts that are now available. Check the license agreements on the scripts to make sure they are consistent with your needs. 

This guide is going to use option 2, building upon the tremendous work happening at eGPU.io.  The script by learex is all encompassing and recommended as the first resource to try.

https://github.com/learex/macOS-eGPU


Step 1: Back up

There will be several important changes to the OS and the configuration. You may elect to do a full system backup before beginning to a new external drive via Time Machine. If you have a previous Time Machine backup, this can be used, but sometimes, it is easier to have just one clean backup to work from. 

You can speed up the first back up by using this command in the Terminal:
```
sudo sysctl debug.lowpri_throttle_enabled=0
```

After the back up has been completed, re-enable the low priority task throttling
```
sudo sysctl debug.lowpri_throttle_enabled=1
```

Step 2: disable system integrity protections (referred to as SIP frequently online)

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

Step 3: Run the macOS-eGPU script

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


