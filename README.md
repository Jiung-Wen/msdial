# MS-DIAL
The (unofficial) tutorial for running MS-DIAL (v4.80) on Linux cluster


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


## [Optional] Set up the $PATH variable 
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

