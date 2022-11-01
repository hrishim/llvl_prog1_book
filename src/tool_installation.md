# Installation of Required Tools

You will need the following software tools for following along this book

- GCC for ARM: A compiler to compile your programs into binary.
- QEMU: This is a software emulator that we will use to emulate the Raspberry Pi
- Make tool for Windows 10
- Git: A version control software. You will use this to get a copy of the code in this book from our Git repository.

## Windows 10

### GCC compiler

The compiler is available as a free download from [here](https://developer.arm.com/downloads/-/gnu-a). The specific version you need for **Windows 10** is under the "Windows mingw" section and the "aarch64-none-elf" version. Download the *.tar.xz* file.

This is a compressed archived file containing all the compiler, assembler and disassembler tools needed for this book. To extract these files you will need a program like [7-zip](https://www.7-zip.org/). Once you install 7-Zip you can open the tar.xz file using that program. 

Opening the archive file will give you a directory. Move that directory to your home directory (in windows this will be C:\Users\<Your_User_Name>)

### QEMU

Preferably running 64-bit Windows 10

1. Download [QEMU](https://www.qemu.org/download/#windows) and click on 64-bit version (select the 32-bit version if you are using 32-bit Win 10)
2. Download the latest released installer executable
3. Run installer and follow instructions to install

### Make

1. Download **Make for Windows** from [here](http://gnuwin32.sourceforge.net/packages/make.htm). 
2. Down load the installer executable by selecting **Complete package, except sources** option.
3. Run installer and follow instructions to install

### Git

**Git** is a source control management system. Download the installer from [here](https://git-scm.com/download/win) and follow the instructions in the installer.

The installation includes an application named **Git bash**. **Git Bash** is a Linux-based command line environment. It allows users to type various **Git commands** that make source code management easier. 

After the installation of Git is complete, open the **Git Bash** application using the **Start** menu. This will open a terminal window with a command prompt and it current directory by default will be your home directory.   

Create a subdirectory named **low_level_prog** and navigate into it by running following commands in the **Git bash** terminal window.

```
mkdir low_level_prog
cd low_level_prog
```

Now, type the following to checkout code from the repository:

```
git clone https://github.com/hrishim/llvl_prog1_code.git
```

### Setting up the environment

1. Navigate into the code checout:
```
cd llvl_prog1_code
```

2. Open **dotbashrc** file using vim editor (included as part of Git bash installation):
```
vim dotbashrc
```
**NOTE:** : If you prefer to use a different editor like **notepad**, you can replace **vim** with the editor name of your preference. For example: *notepad dotbashrc* 

3. Fill up the installation paths of Qemu, GCC and Make in space provided within the dotbashrc file:


```
# Please fill the actual installation path of Qemu below:
# For example:
# export LOCAL_QEMU_PATH="/c/Program Files/qemu"
export LOCAL_QEMU_PATH=""

# Please fill the actual installation path of ARM GCC below:
# For example:
# export LOCAL_GCC_PATH="$HOME/gcc-arm-10.2-2020.11-mingw-w64-i686-aarch64-none-elf"
export LOCAL_GCC_PATH=""

# Please fill the actual installation path of make below:
# For example:
# export LOCAL_MAKE_PATH="$HOME/make-3.81-bin"
export LOCAL_MAKE_PATH=""
```

**NOTE:** 
While specifying any path in this file, following minor modifications to Windows paths must be made:
* Replace backslashes(\\) in Windows paths with forward slashes(/).
* Remove the first colon after the drive letter, and add a slash at the beginning.
* **$HOME** can be used to refer to the home directory
* For example: (1) Windows path: "C:\Program Files/qemu" would become "/c/Program Files/qemu". (2) "C:\Users\<Your_User_Name>\make" would become "$HOME/make"

4. Exit the editor and source the **dotbashrc** file to setup the environment
```
source dotbashrc
```

5. Test if it is working by typing the following command in the terminal window:

```
qemu-system-aarch64 --version
```

You should see an output like below. The version you see may be different and that is OK.

```
QEMU emulator version 6.0.0 (v6.0.0-11869-g800a25ea45-dirty)
Copyright (c) 2003-2021 Fabrice Bellard and the QEMU Project developers
``

**Additional help (external link):** [Installing QEMU on Windows](https://www.youtube.com/watch?v=SHMUMeEzSS0) (see the path setting)
 
