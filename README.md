# Windows to Raspberry Pi Remote C++ Cross-Compilation with Eclipse

## Introduction
This guide provides step-by-step instructions for setting up a Windows environment to cross-compile C++ code for Raspberry Pi using Eclipse IDE. It covers installing the GCC cross-compiler, configuring Eclipse, setting up CMake, and debugging with GDB.

## Prerequisites
- Windows PC (32-bit or 64-bit)  
- Raspberry Pi with Raspbian OS (e.g., Bookworm, Bullseye, or Buster)  
- Internet connection  
- Git installed for cloning repositories  

## 1. Install the GCC Cross-Compiler
Download and install the GCC cross-compiler based on your Windows OS and target Raspberry Pi OS:

**For 32-bit Windows**  
Download from [gnutoolchains.com/raspberry](http://gnutoolchains.com/raspberry/):  
- Raspbian GNU/Linux 12 (Bookworm): `raspberry-gcc12.2.0-r2.exe` (399 MB)  
- Raspbian GNU/Linux 11 (Bullseye): `raspberry-gcc10.2.1-r2.exe` (408 MB)  
- Raspbian GNU/Linux 10 (Buster): `raspberry-gcc8.3.0-r4.exe` (761 MB)  
- Raspbian GNU/Linux 10 (Buster, armhf): `raspberry-gcc8.3.0-r3.exe` (749 MB)  
- Raspbian GNU/Linux 10 (Buster, full): `raspberry-gcc8.3.0.exe` (288 MB)  
- Raspbian GNU/Linux 9 (Stretch, full): `raspberry-gcc6.3.0-r5.exe` (448 MB)  

**For 64-bit Windows**  
Download from [gnutoolchains.com/raspberry64](https://gnutoolchains.com/raspberry64/):  
- Raspbian GNU/Linux 12 (Bookworm): `raspberry64-gcc12.2.0.exe` (409 MB)  
- Raspbian GNU/Linux 11 (Bullseye): `raspberry64-gcc10.2.1.exe` (702 MB)  

Install the toolchain to `C:\SysGCC\raspberry` (32-bit) or `C:\SysGCC\raspberry64` (64-bit).

## 2. Install SmarTTY
1. Download SmarTTY from [sysprogs.com/SmarTTY/download](https://sysprogs.com/SmarTTY/download/).  
2. Install to `C:\Program Files (x86)\Sysprogs\SmarTTY`.  
3. Synchronize the Raspberry Pi sysroot:  
   - **For 32-bit**:  
     ```bash
     "C:\Program Files (x86)\Sysprogs\SmarTTY\SmarTTY.exe" /UpdateSysroot:C:\SysGCC\raspberry\arm-linux-gnueabihf\sysroot
     ```  
   - **For 64-bit**:  
     ```bash
     "C:\Program Files (x86)\Sysprogs\SmarTTY\SmarTTY.exe" /UpdateSysroot:C:\SysGCC\raspberry64\aarch64-linux-gnu\sysroot
     ```

## 3. Clone the Project Repository
Clone your project repository to your local machine:  
```bash
git clone <your-repository-url>
```

## 4. Configure Eclipse IDE
1. Open Eclipse IDE for Embedded C/C++ Developers.  
2. **Import the project**:  
   - Go to `File > Import... > C/C++ > Existing Code as Makefile Project`.  
   - Select the cloned project directory and name the project.  
3. **Configure the toolchain**:  
   - Go to `Project > Properties > C/C++ Build > Builder Settings`.  
   - Check `Generate Makefiles automatically` but do not apply yet.  
   - Go to `C/C++ Build > Settings`.  
   - Set the cross-toolchain prefix to `arm-linux-gnueabihf-` (32-bit) or `aarch64-linux-gnu-` (64-bit).  
   - Set the toolchain binaries path, e.g., `C:\SysGCC\raspberry\bin` (32-bit) or `C:\SysGCC\raspberry64\bin` (64-bit).  
   - Return to `Builder Settings` and uncheck `Generate Makefiles automatically`.  
4. **Configure the Build Output Parser**:  
   - Go to `Window > Preferences > C/C++ > Build > Settings > Discovery Tab > CDT GCC Build Output Parser`.  
   - Alternatively, for project-specific settings: `Project > Properties > C/C++ General > Preprocessor Include... > Providers > CDT GCC Build Output Parser`.  
   - Set the compiler pattern to `(.*gcc)|(.*g\+\+)|(clang)` to handle cross-compiler output (e.g., `arm-linux-gnueabihf-gcc`).  
5. **Configure SSH connection**:  
   - Go to `Debug > Debug Configurations > Connection > New Connection > SSH`.  
   - Set up the connection and browse the remote file path on the Raspberry Pi.

## 5. Build and Test
1. Build the project: `Project > Build Project`.  
2. SSH into the Raspberry Pi and run the executable:  
   ```bash
   chmod +x your_executable
   ./your_executable
   ```

## 6. Debug with GDB
1. Go to `Run > Debug Configurations > C_-Hello-World Debug > Debugger tab`.  
2. Set the GDB debugger to `C:\SysGCC\raspberry\bin\arm-linux-gnueabihf-gdb.exe` (32-bit) or `C:\SysGCC\raspberry64\bin\aarch64-linux-gnu-gdb.exe` (64-bit).  
3. Apply and test the connection.

## 7. Set Up CMake
1. **Install the cmake4eclipse plugin**:  
   - Go to `Help > Eclipse Marketplace`.  
   - Search for `cmake4eclipse`, select the plugin, and install. Restart Eclipse.  
2. **Add toolchain to PATH**:  
   - Right-click `This PC > Properties > Advanced system settings > Environment Variables`.  
   - Under `System Variables`, find `Path`, click `Edit`, and add `C:\SysGCC\raspberry\bin` (32-bit) or `C:\SysGCC\raspberry64\bin` (64-bit).  
3. **Install Ninja**:  
   - Download `ninja.exe` from [github.com/ninja-build/ninja/releases](https://github.com/ninja-build/ninja/releases).  
   - Place it in `C:\Ninja` and add `C:\Ninja` to the system `Path`.  
4. **Install CMake**:  
   - Download the latest `.msi` installer (e.g., `cmake-3.30.2-windows-x86_64.msi`) from [cmake.org/download](https://cmake.org/download/).  
   - Install and add CMake to the system `Path`.  
5. **Create a new project**:  
   - Go to `File > New > C/C++ Project > cmake4eclipse Managed Build`.  
   - Name the project, select the directory, and ensure `CMakeLists.txt` is present.  
6. **Example CMakeLists.txt**:  
   ```cmake
   cmake_minimum_required(VERSION 3.10)
   project(ExampleProject)
   set(CMAKE_CXX_STANDARD 11)
   add_executable(your_executable main.cpp)
   ```  
7. **Configure the core build toolchain**:  
   - Go to `Window > Preferences > cmake4eclipse > Build Tool Kits`.  
   - Add the path to the build tools and CMake executable.  
8. **Set up run configurations**:  
   - Add the `C/C++ Application` path to `your_executable`.  
   - Configure the remote connection for deployment.

## Additional Resources
- [StackOverflow: Configure GCC Cross-Toolchain in Eclipse](https://stackoverflow.com/questions/27180771/how-to-configure-a-particular-gcc-cross-toolchain-in-eclipse-cdt/27180772#27180772)
