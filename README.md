# Compucell-Mac-Scripts
Scripts for compiling and installing Compucell in mac

Installing Compucell on Mac is fairly a straight forward process but it does require a set of pre-requisite libraries to be installed on your Mac. A working set of the third party dependencies that are needed for compucell to be compiled and working is as following:

*	Qt
*	PyQt
*	PyQwt
*	Qscintilla
*	VTK
*	Roadrunner
*	CMake
*	GCC
*	Sip


All of the above prerequisites with the exception of VTK and Roadrunner can be directly installed from brew. This document also assumes that xcode has been installaed on your Mac as this process extensively uses install-name-tool and otool commands.###### Install Brew
Any installation on Mac typically requires that the user have admin privileges but there is work around to have the brew installed locally on to a non admin user’s home directory on mac. We are going to install brew in a directory called Code in the home folder.
	cd ~
	mkdir Code
	cd Code
	git clone https://github.com/mxcl/homebrew.git

Now to be able to use the brew command we should export the bin path to the PATH variable

	export PATH=${HOME}/Code/homebrew/bin:${PATH}

This export is local to the terminal and once the terminal is closed it needs to be done again.

##### Install the prerequisites using brew

	brew install qt
	brew install pyqt
	brew install pyqwt
	brew install qscintilla2
	brew install sip
	brew install cmake
	brew install swig

##### Install VTK
VTK is an integral part of Compucell and is not readily available for installation from brew and needs to be compiled using cmake and native compilers. At the time of writing this documentation VTK 7.0.0 is used. The process of installing VTK is as below: Download the source of VTK from here<http://www.vtk.org/download/>. I have used VTK-7.0.0.tar.gz. The source can be uncompressed to a folder called VTK_src by using the following command.	gunzip -c VTK-7.0.0.tar.gz | tar xopft –	mv VTK-7.0.0 VTK_src	mkdir VTK_build

Open Cmake gui from the applications folder and point the source ![Cmake image](/images/vtkcmake.png)
Point VTK_src for the Source location and VTK_build for the build of binaries.Change the `CMAKE_INSTALL_PREFIX` values to the current directory where VTK_src and VTK_build are located and add the directory VTK_install. For example if VTK_src is located in your desktop then the flag value should be /Users/uname/Desktop/VTK-installVerify that `VTK_WRAP_PYTHON, BUILD_SHARED_LIBS and BUILD_EXAMPLES` flags are checked.Click on configure and see that it completes normally without any glitches. Once completed, click on Generate to complete the process. If asked for compilers check on use native compilers and continue.If GUI is not present the same can be done using the command ccmake. The flag values should remain same but to be able to point to the appropriate build and source directories	cd /Users/uname/Desktop/VTK_build	ccmake ../VTK_srcChange the values and hit c to configure and g to generate and quit.Once the build is done, go to the build directory in the terminal and execute the following commands	make clean	make –j 4(-j is to specify the number of cores to be used for the compilation and this process might take a while)	make installOnce this is done, check that python2.7/site-packages directory is present in VTK-install/lib directory.Once the installation of all the prerequisites have been installed, we need to consolidate the prerequisites in to a single directory as explained in the below steps so that the compilation script for the compucell can work.Let us assume that we are going to install in CC3D in Desktop.	mkdir CC3D	cd CC3DCurrent directory is ~/Desktop/CC3D	mkdir DepsNow copy the PyQt4 from the homebrew installation directory to the Deps. 	cp -r ~/Code/homebrew/Cellar/pyqt/4.11.4/lib/python2.7/site-packages/PyQt4 Deps/Copy and Merge the PyQt directory in the installed PyQwt directory structure.	cp -r ~/Code/homebrew/Cellar/pyqwt/5.2.0/lib/python2.7/site-packages/PyQt4/ Deps/PyQt4/ Copy Qt library into the Deps directory	cp -r ~/Code/homebrew/Cellar/qt/4.8.7_2/lib/ Deps/Copying Qscintilla2 libraries in to the Dependencies is a two step process. Execute the below two commands.	cp -r ~/Code/homebrew/Cellar/qscintilla2/2.9.3/lib/*.dylib Deps/	cp -r ~/Code/homebrew/Cellar/qscintilla2/2.9.3/lib/python2.7/site-packages/PyQt4/ Deps/PyQt4/Once this is done copy the three supplied rpathModifier python scripts into the Deps folder and execute the scripts as following.	python rpathModifierPyQt4.py	python rpathModifierQt4.py	python rpathModifierQt4_qscintilla.pyThese scripts will in turn create three shell scripts `rpathizerPyQt4.sh, rpathizerQt4_qsci.sh and rpathizerQt4.sh` that needs to be executed inside the Deps directory. We are going to change the permissions for the scripts to be executable by the following commands.	Chmod +x rpathizerPyQt4.sh	Chmod +x rpathizerQt4_qsci.sh	Chmod +x rpathizerQt4.shExecute the steps as below	sudo ./rpathizerPyQt4.sh	sudo ./rpathizerQt4_qsci.sh	sudo ./rpathizerQt4.shNow We need to create a directory structure that holds the dependencies in the way the compiler script understands. Execute the following script in the CC3D directory.
	./CreateDirStruct.sh
This will create a directory `macdeps` within which it will create `Deps, Deps/Deps, Deps/lib, Deps/player` directories.Copy the `PyQt4` directory from the above steps to the `player` directory


##### Roadrunner installation
Download the pylibroadrunner for mac from [sourceforge page](https://sourceforge.net/projects/libroadrunner/files/libroadrunner-1.3/). Unzip the package and run the setup.py
	python setup.py install
copy the `roadrunner` directory into the `rr-install/site-packages` ##### Copy the gcc folder to macdeps
copy the gcc-5.3 from the repository to macdeps.
##### Run the build.sh to compile and install