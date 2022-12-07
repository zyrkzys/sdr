# sdr
I've got an XTRX from Fairwaves and I'm trying to figure out how to use every bell and whistle, and to come up with some interesting projects.

# Background

## Hardware
The XTRX is a powerful software-defined radio (SDR) from Massachusetts-based Fairwaves. As a product, it's aimed at the low-cost commercial projects, high-end amateurs, and professionals with specific requirements. It offers a range of commercial deployment-ready capabilities, with 2x2 mulitple-input multiple-output (MIMO), from 30 MHz (30000000 Hz) to 3.8 GHz (3800000000 Hz). It includes an onboard GPS, a Xilinx Artix 7 field-programmable gate array (FPGA), and attachments for either a mini Peripheral Component Interconnect Express (PCIE) (x4) interface to be slotted direcly on a motherboard or a little pod for USB access in a more austere environment. It  was not cheap (I paid a full $600 for the full kit).

### Installation
I went the PCIe route, installing in the provided mini PCIe x4 sled, and mounting that on my motherboard. I didn't encounter any issues there. I do have a beefier power supply than I needed, but that's my GPU (RTX 8000). I doubt this project will consume really any watts at all, comparatively. 

## Software
I am running Ubuntu 20.05 LTS, because that is the version most people have had success with, it would seem. It works for me now, after the below finagling. I have no other specialization I can think of that matters, but it did take me a while to get it working (user error!). I will detail the packages I installed as prerequisities for both SoapySDR and libxtrx:

```sudo apt-get install build-essential libusb-1.0-0-dev cmake dkms python3 python3-pip gpsd gpsd-clients pps-tools libboost-all-dev git qtbase5-dev libqcustomplot-dev libqcustomplot1.3 libqt5printsupport5 doxygen swig cheetah3```

```sudo apt-get install cmake g++ libpython3-dev python3-numpy swig```

# Dependencies

## SoapyXTRX
The first thing I had to do to up and running was install SoapySDR with Python bindings. I skipped this step initially, going out of order, which did not work, because libxtrx customizes the install for Soapy. Here is the link: https://github.com/pothosware/SoapySDR/wiki/BuildGuide

## libxtrx
Next, with Soapy installed, we can install libxtrx. Or, in my case, reinstall it once you've got Soapy working. I followed the exact instructions from the project's page at https://github.com/xtrx-sdr/images. It's important to include the "DENABLE_SOAPY=YES" flag when you cmake it, and I also turned the UDEV rules on, e.g.:

% cmake -DENABLE_SOAPY=NO -DINSTALL_UDEV_RULES=ON .. 

Next, if you're using the PCIe connect like I did, make sure you do the "PCIe kernel driver compilation" steps. 

## Rebuild Soapy with libxtrx installed
Now we can make sure Soapy knows libxtrx is there, so we rebuild as described here https://github.com/pothosware/SoapySDR/wiki/BuildGuide

# Testing the Equipment

## Basic Functionality
I used the included test script from the main libxtrx repo - which I've included in this one as "test_xtrx.sh" - to ensure the kernel sees it, mounts the /dev/xtrx device

## GPS Functionality
Beyond the device being present in software confirming the drivers are good to go, I also have the "test_gps.sh" bash script which will launch ```gpsmon``` as fed by the newly-mounted /dev/ttyXTRX0 device, which should show you your GPS coordinates after a few seconds. I taped my antenna (one of the included components) by my window, and haven't had any issues. You will have to connect one of the SMA (SubMiniature version A) connectors from the 'GPS' spot on the XTRX to one of the mini PCIe card's output points on the 'inside' side (relative to the computer case) and connect the GPS antenna to the corresponding 'outside' port. Then, ```sudo apt install gpsmon``` and run the script. It is one line but I like to record these things.

## Antenna Functionality
The same bit about attaching SMA wires from the XTRX to the external-facing ports applies. All required were included, for me. To test the antenna availability, I employed the completed install of SoapySDR in Python. See the Jupyter notebook 'XTRX x SoapySDR.ipynb' for more details. Everything should now be working! 


