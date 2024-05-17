# ZC702 Test Pattern Generator Flow
+ This article covers generating test pattern generator images through ADV7511 HDMI, including resolution conversion functionality, for ZC702

**Last update: 2024/05/18**

+ Vivado Version: 2021.1

+ This process is referenced from [Xilinx Video Series](https://support.xilinx.com/s/question/0D52E00006hpsS0SAI/xilinx-video-series-and-blog-posts?language=en_US) 做為修改

## Build ZC702 Block Design on Vivado

### 1. Create Block Design and perform IP design

+ ZYNQ7 Processing System

<img src="Images/TPG1.png"/>

+ Video Test Pattern Generator

<img src="Images/TPG2.png"/>

+ AXI4-Stream to Video Out

<img src="Images/TPG3.png"/>

+ Video Timing Controller

<img src="Images/TPG4.png"/>

<img src="Images/TPG5.png"/>

+ Clocking Wizard

<img src="Images/TPG6.png"/>

<img src="Images/TPG7.png"/>

148.5 𝑀ℎ𝑧=(2200×1125×60)/10^6 

+ AXI IIC

<img src="Images/TPG8.png"/>

+ Begin connecting the above IPs to start the integration process

<img src="Images/TPG9.png"/>

<img src="Images/TPG10.png"/>

<img src="Images/TPG11.png"/>

<img src="Images/TPG12.png"/>

<img src="Images/TPG13.png"/>

<img src="Images/TPG14.png"/>

<img src="Images/TPG15.png"/>

<img src="Images/TPG16.png"/>

<img src="Images/TPG17.png"/>

<img src="Images/TPG18.png"/>

<img src="Images/TPG19.png"/>

Data width inconsistency

<img src="Images/TPG20.png"/>

<img src="Images/TPG21.png"/>

We solve it by adding an AXI-4 Stream Subset Converter

<img src="Images/TPG22.png"/>

<img src="Images/TPG23.png"/>

<img src="Images/TPG24.png"/>

<img src="Images/TPG25.png"/>

### 2. Add constraints detailing how HDMI is connected to the ADV7511's pins. You can download the zip file from this directory and locate the XDC file at XVES_0019\src\constr\ZC702.xdc

<img src="Images/TPG26.png"/>

+ Modify the constraints to match the port naming in the Block Design

<img src="Images/TPG27.png"/>

### 3. Create HDL Wrapper & Generate Bitstream

<img src="Images/TPG28.png"/>

### 4. Export Hardware，This step will output XSA to Vitis for writing C code to control the FPGA

<img src="Images/TPG29.png"/>

## Build ZC702 Application on Vitis

### 5. Open Vitis, import the XSA, and create a platform

<img src="Images/TPG30.png"/>

+ After creating the platform, proceed to build it to generate the linkage files

<img src="Images/TPG31.png"/>

### 6. Create an application and import the files from the "src" folder in the zip file

<img src="Images/TPG32.png"/>

<img src="Images/TPG33.png"/>

<img src="Images/TPG34.png"/>

### 7. Delete the original helloworld.c, then open tpg_hdmi_zc702.c and add the code highlighted in the diagram below

<img src="Images/TPG35.png"/>

### 8. ZC702 Hardware Configuration

<img src="Images/TPG36.png"/>

### 9. Right-click on Application and select Run as 1 Launch Hardware to view the results

<img src="Images/TPG37.png"/>

<img src="Images/TPG38.png"/>

# ZC702 Test Pattern Generator Output Multiple Resolution Flow 

## Modify IP on Vivado

### 1. Make adjustments to IP configurations in the aforementioned Block Design

+ Video Timing Controller - Enable AXI-4 Lite for control by C code in Vitis

<img src="Images/TPG39.png"/>

+ Clocking Wizard

Enable Dynamic Reconfiguration to update clocks from AXI-4 Lite through Vitis

<img src="Images/TPG40.png"/>

Check the desired clock values corresponding to different resolutions for output

25.2 𝑀ℎ𝑧=480𝑃

74.25 𝑀ℎ𝑧=720𝑃

148.5 𝑀ℎ𝑧=1080𝑃

<img src="Images/TPG41.png"/>

Enable MMCM Override

<img src="Images/TPG42.png"/>

Remember the following division values, which will be used by Vitis

<img src="Images/TPG43.png"/>

Go back and deselect the other two output clocks. Remember to modify the corresponding resolutions in the VTC

<img src="Images/TPG44.png"/>

+ Connect the above IPs together

<img src="Images/TPG45.png"/>

<img src="Images/TPG46.png"/>

### 2. Generate the bitstream as before and export the XSA

## Build ZC702 Application on Vitis

### 3. Update the previously created platform by importing the new XSA, and rebuild it as before

<img src="Images/TPG47.png"/>

### 4. Create the application, import the previously modified code, and make further changes in tpg_hdmi_zc702.c as follows:

<img src="Images/TPG48.png"/>

If not changing the resolution, comment out the two lines in the Clocking Wizard Configuration

<img src="Images/TPG49.png"/>

If not changing the resolution, comment out the three lines in the VTC Configuration

<img src="Images/TPG50.png"/>

Note that simply not changing the original resolution or clock speed does not require reconfiguration, but if there is an AXI-4 Lite, you need to initialize and enable that IP in Vitis for it to function

### 5. Run as 1 Launch Hardware，Check the results

+ 480P

<img src="Images/TPG51.png"/>

+ 720P

<img src="Images/TPG52.png"/>

<img src="Images/TPG53.png"/>

<img src="Images/TPG54.png"/>

+ 1080P

<img src="Images/TPG55.png"/>

<img src="Images/TPG56.png"/>

<img src="Images/TPG57.png"/>

## Reference

+ [ZedBoard HDMI1.4 Working #Principle with FPGA](https://www.youtube.com/watch?v=BstMo5OwsjI&ab_channel=Nielfotech)

+ [Video Series 21: TPG Application on ZC702](https://support.xilinx.com/s/article/922324?language=en_US)
