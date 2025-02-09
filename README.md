

# FFmpegKit for Python - V6.0.2

`FFmpegKit` is a collection of tools to use `FFmpeg` in `Android` applications.

It includes scripts to build `FFmpeg` native libraries, a wrapper library to run `FFmpeg`/`FFprobe` commands in
applications.     

Video Tutorial: https://youtu.be/z-RKGXo6Qys  
     
## 1. Android (+Python)

### 1. Features
- Supports `API Level 26+` on Main releases.
- Includes `arm-v7a`, `arm-v7a-neon`, `arm64-v8a`, `x86` and `x86_64` architectures
- Can handle Storage Access Framework (SAF) Uris
- Builds shared native libraries (.so)

Building is at the end of the documentation, Building yourself doesn't seem to work btw.                                                              

### 3. Using With Python

        
#### NOTE: If you are having lower quality exports, add "-q:v 1 -q:a 1" before your output file/path. 1 for highest.  
 
#### 1) Installing NDK r25c using sdkmanager.     
Install sdkmanager using your package manager (as ROOT/sudo).      
`sudo apt/dnf install sdkmanager` or `sudo pacman -S sdkmanager`      
then, Install the right ndk using - `sudo sdkmanager --install "ndk;r25c"`
Note down the path the ndk installs to, it is required in next step.

##### 2)   In Buildozer spec file add Basic requirements/library and the ndk path of last command. (Alongside what you already have)
```
# Basic Requirements
requirements =  pyjnius, jnius
android.api = 32
android.minapi = 26

# Adding the right (and working NDK); Your path would/should be different, but here is what my Path is: /opt/android-sdk/ndk/25.2.9519653 
android.ndk_path = /Path/to/sdk-ndk/ 

# Adding the library.
android.gradle_dependencies = com.arqies:ffmpeg-kit-python:6.0-2.LTS@aar, com.arthenica:smart-exception-common:0.2.1@jar, com.arthenica:smart-exception-java:0.2.1@jar 
android.add_gradle_repositories = maven {url 'https://Arqies-Repositories.github.io/Gradle-FFmpeg-Kit-Python/'}
android.enable_androidx = True

```      
The minimum api number can be anything above 25.    

##### 3)   Using Pyjnius, declare the variables in Python (https://pyjnius.readthedocs.io/en/stable/)
```
#IMPORTING jnius
from jnius import autoclass
from jnius import * 
#Declaring Variable so it can be used
FFMPEG = autoclass('com.sahib.pyff.ffpy')
```

BOTH (FFMPEG and FFPROBE) RETURN OUTPUT OF THE COMMAND         
##### 3) To Use FFMPEG 
```
#EXECUTED FFMPEG COMMAND, COMMAND IS STRING
ffmpegCommand = FFMPEG.Run("COMMAND")

#PRINTS RETURN (OUTPUT OF THE COMMAND)
print(ffmpegCommand)
```

##### 4) To use FFProbe
```
#EXECUTED FFProbe COMMAND, COMMAND IS STRING
probeCommand = FFMPEG.RunProbe("Command")

#PRINTS RETURN (OUTPUT OF THE COMMAND)
print(probeCommand)

```
NOTE - FILTER_COMPLEX can not be used. (IDK why, if you have solution tell me please)      

### If it Still does not work for you:    
Create a new issue, Make sure to include your buildozer.conf and buildlogs. 


#### EXAMPLES      
##### 1) FFMPEG     
```
from jnius import autoclass
from jnius import * 
#Declaring Variable so it can be used
FFMPEG = autoclass('com.sahib.pyff.ffpy')


#THIS COVERTS VIDEO INTO an audio file (MP4 TO WAV)
d = FFMPEG.Run(str("-i video.mp4 -ab 160k -ac 2 -ar 44100 -vn TEMP/audio.wav"))

#THIS PRINTS THE OUTPUT (I don't think you even need output in ffmpeg unless for trouble shooting)
print(d) 

```


##### 2) FFProbe
```
from jnius import autoclass
from jnius import * 
#Declaring Variable so it can be used
FFMPEG = autoclass('com.sahib.pyff.ffpy')


#Gets Framerate of video
frameRate = FFMPEG.RunProbe("-v error -select_streams v -of default=noprint_wrappers=1:nokey=1 -show_entries stream=r_frame_rate video.mp4")
#This command outputs 30/1 at 30 frames

frameRate = frameRate.split("/")[0]
#this makes it 30 instead of 30/1

frameRate = int(frameRate)
print(frameRate)
#Converts framerate to integer and prints it

```    





### Building (Skip this part, its for myself lol)

Run `android.sh` at project root directory to build `ffmpeg-kit` and `ffmpeg` shared libraries. 

Please note that `FFmpegKit` project repository includes the source code of `FFmpegKit` only. `android.sh` needs 
network connectivity and internet access to `github.com` in order to download the source code of `FFmpeg` and 
external libraries enabled.

#### 2.1 Prerequisites

`android.sh` requires the following tools and packages.

##### 2.1.1 Android Tools
   - Android SDK Build Tools
   - Android NDK r22b or later with LLDB and CMake (See [#292](https://github.com/arthenica/ffmpeg-kit/issues/292) if you want to use NDK r23b)
   - Java (openjdk)

##### 2.1.2 Packages

Use your package manager (apt, yum, dnf, brew, etc.) to install the following packages.

```
sdkmanager autoconf automake libtool pkg-config curl cmake gcc gperf texinfo yasm nasm bison autogen git wget autopoint meson ninja
```

##### 2.1.3 Environment Variables 



Set `ANDROID_SDK_ROOT` and `ANDROID_NDK_ROOT` environment variables before running `android.sh`. (not NDK-bundle, Preferably install using sdkmanager)      
sdkmanager command:
```
sdkmanager --install "build-tools;24.0.3" "tools;24.4.1" "platform-tools;24.0.0" "ndk;r22b" "cmake;3.22.1" "ndk-bundle;r22b"
```

```
export ANDROID_SDK_ROOT=<Android SDK Path>
export ANDROID_NDK_ROOT=<Android NDK Path>
```

#### 2.2 Options

Use `--enable-<library name>` flag to support additional external or system libraries and
`--disable-<architecture name>` to disable architectures you don't want to build.

```
./android.sh --enable-fontconfig --disable-arm-v7a-neon
```

Run `--help` to see all available build options.


