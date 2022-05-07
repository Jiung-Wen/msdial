# MS-DIAL command line tutorial
The (unofficial) tutorial for running MS-DIAL (v4.80) on Linux cluster (or Mac)

[Update May 7, 2022] MS-DIAL installation on a Mac

## Download and "install" the MS-DIAL Console app on Linux
It took me a while to figure out how to install the MS-DIAL on Linux since the tutorial other than Windows version is less detailed. It turns out that I can run MS-DIAL app out-of-the-box and there's no installation step at all!

The first step is to download and unzip the MS-DIAL file:
```bash
wget http://prime.psc.riken.jp/compms/msdial/download/repository/Linux/MSDIAL%20ver.4.80%20Linux.zip
unzip MSDIAL\ ver.4.80\ Linux.zip
```
I prefer to replace all the spaces with underscores
```bash
mv MSDIAL\ ver.4.80\ Linux MSDIAL_ver.4.80_Linux
```

To add the execute permission to the app, run the following command:
```bash
chmod +x ./MSDIAL_ver.4.80_Linux/MsdialConsoleApp
```
And there you go! 

## How to install the MS-DIAL Console app on a Mac
The steps of installing MS-DIAL on a Mac are similar to that on a Linux, but a little bit tricky. The first several steps are the same as before:
```bash
wget http://prime.psc.riken.jp/compms/msdial/download/repository/macOS/MSDIAL%20ver.4.80%20OSX.zip
unzip MSDIAL\ ver.4.80\ OSX.zip
mv MSDIAL\ ver.4.80\ OSX/ MSDIAL_ver.4.80_OSX
chmod +x ./MSDIAL_ver.4.80_OSX/MsdialConsoleApp
```
Now here's the tricky part. When trying to launch the console app, you may see error messages like ```"Some.File.Name.Here” cannot be opened because the developer cannot be verified."``` Go to System preferences > Security & Privacy > General tab, and you will see ```macOS cannot verify the developer of “Some.File.Name.Here”. Are you sure you want to open it?``` Click Open Anyway. Getting MS-DIAL to work the first time on a Mac is a pain, you need to repeat this loop (launch -> error -> open anyway -> launch -> ...) several times until no error message is shown. However, once fixed, it runs fine!


## [Optional] Set up the $PATH variable (Linux)
```bash
vim ~/.bashrc
```

Press ```i``` to add the following line:
```bash
export PATH=/YOUR/PATH/TO/MSDIAL_ver.4.80_Linux:$PATH
```
Press ```Esc``` and then ```:wq``` to exit the text editor.

To read and export the path just added:
```bash
source ~/.bashrc
```

## Parameter file
I retrieved all the parameters and uploaded as ```gcms_param``` and ```lcms_param``` from its source code:
```bash
less ./MsdialWorkbench_Public/MsdialConsoleApp/Parser/ConfigParser.cs
```
Note that lines begin with ```#``` are considered comments and ```A | B``` indicate either A or B. Therefore, please delete/modify it accordingly.

## Input files
According to [MS-DIAL's official website](http://prime.psc.riken.jp/compms/msdial/consoleapp.html) (retrieved March 11, 2022), Both _gcms_ and _lcmsdda_ mode accept netCDF (.cdf), mzML (.mzml), or ABF (.abf) files, whereas _lcmsdia_ mode only accepts ABF (.abf) files.

I don't know how to convert vendor-format data files (e.g., .wiff or .d) into netCDF files. But you can convert them into mzML and ABF using [ProteoWizard](https://proteowizard.sourceforge.io/)'s msconvert and [Reifycs ABF Converter](https://www.reifycs.com/AbfConverter/), respectively. AFAIK, msconvert can be run on Windows and Linux, whereas ABF can be run only on Windows. I assume most of the readers here are looking for a way to convert files on Linux cluster, which I will introduce in the next section.

## Run msconvert on Linux cluster
If you are a root user or have sudo privileges, you can simply follow the instruction [here](https://hub.docker.com/r/chambm/pwiz-skyline-i-agree-to-the-vendor-licenses). However, if you are using an HPC cluster, chances are that you don't have that privileges and cannot easily install the msconvert app via Docker. Fortunately, you can achieve this by using Singularity, though it would be a little bit tricky. You may see an error message like "wine: /wineprefix64 is not owned by you" if you simply build the Singularity image from the Docker repo.

To install msconvert, first make sure you have singularity installed on the HPC cluster.

Make a directory called pwiz and go to pwiz:
```bash
mkdir pwiz && cd pwiz
```
Build a singularity sandbox (basically a dir) from Docker image:
```bash
singularity build --sandbox pwiz docker://chambm/pwiz-skyline-i-agree-to-the-vendor-licenses
```
Move the dir “wineprefix64” out of the sandbox (“pwiz” in this example):
```bash
mv pwiz/wineprefix64 ./
```
Build a singularity image file (.sif):
```bash
singularity build pwiz.sif pwiz 
```
Mount the dir “wineprefix64” and you can now execute the msconvert:
```bash
singularity exec -B wineprefix64:/wineprefix64 pwiz.sif wine msconvert [options]
```

