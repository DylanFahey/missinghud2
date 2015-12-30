## Overview
This repository is my experiment at getting MissingHud2 to work on the Linux version of Rebirth. I haven't used C++ in 5 or 6 years, so this is probably going to be a disaster. 

Since MissingHud2 uses a dll injector to work, a similar approach should be possible on Linux. I almost assume it would be easier in Linux than in Windows, using LD_PRELOAD. However, I believe libraries are included in the binaries of Steam games, so I'm not entirely sure if LD_PRELOAD works under those conditions. [I understand that there is a Steam runtime](https://github.com/ValveSoftware/steam-runtime), and that I might be able to use this. However, I think you may be able to buy BOI without Steam, so that might not be a reasonable assumption.

Furthermore, I do not own Afterbirth. I'm relatively terrible at BOI and bullet hell/roguelikes in general, so I can get a lot more mileage out of a game before I run out of content than someone who chews through content. Plus I bought the original BOI with Wrath of Lamb included, and I would have appreciated playing the easier game for a while first. I don't really want to make the same mistake. 


## Project Path
I think my path will probably be as follows: 

- [ ] Read source code 
 - [ ] Identify any areas where windows-specific code is being run. 
 - [ ] Determine what the Isaac-specific dependencies are
  - [ ] Determine if these are any different in Linux and Windows. 
 - [ ] Determine if memory addresses in Linux and Windows are different (RebirthItemTracker code might have some hints)
- [ ] Try to compile dll as a shared object.
- [ ] Try to eliminate as many errors as possible. Replace calls to windows-specific libraries with POSIX or other cross-platform libraries. Simplify the code if neccessary by removing Afterbirth support. 
  - [ ] Attempt injection of the shared object using LD_Preload. 

Evaluate progress after each step and modify as neccessary

If the injection works:
- [ ] Share with BOI modding community.
 - [ ] Ask for help testing
  - [ ] Linux
  - [ ] OSX

If I get stuck I should probably ask for help. Ideally I'd get it working to a degree where other people could pitch in a bit.

I should probably try running this on Windows at some point to see how it works on the system it was developed for. 

## Areas of Weakness
I think I will probably run into problems in the following areas:

1. Determining what the differences are between the Linux and the Windows versions. 
2. Testing the code on different platforms. I use FC23. I imagine most of the users will be using OSX, Ubuntu, or a Steam Machine of some kind. 
3. Figuring out memory locations. I have no experience modding on Linux. I have used a memory tool to cheat on (local) games before, but I don't understand enough about the structure of Linux to understand what I'm doing with memory. 
4. Checking my code. I haven't programmed in C++ in forever, and even then I don't think I ever learned memory management. 

## Pitfalls 
Other than my specific areas of weakness, I think there are a few things that will make this very difficult:

* LD_PRELOAD only works on the current user account I believe. I'm nto sure if steam runs on the current user account for everyone. I think starting BOI with LD_Preload would solve that problem.
*  The easiest way to make LD_PRELOAD work is probably to create a generic bash script. I'm not entirely sure that BOI installs to teh same directory on other people's computers, even relative to the ~ directory. This would probably also get super messed up by sudo. Alternatively it could just be unpacked into the BOI install folder and run on the local path.


## Resources
1. Steam Libraries
[List of packages in the steam runtime](https://github.com/ValveSoftware/steam-runtime/blob/master/packages.txt)

2. Porting and Crossplatform code
[IBM: Windows to Unix Porting](http://www.ibm.com/developerworks/aix/library/au-porting/)
[Creating a shared library with GCC](http://www.adp-gmbh.ch/cpp/gcc/create_lib.html)
[Shared Libaries FAQ](http://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html)

3. Developer notes from NetworkMe
[Reddit User Page](https://www.reddit.com/user/networkme) Almost all of his comments are about missing hud
[Reddit comments from MissingHud2 release](https://www.reddit.com/r/themoddingofisaac/comments/3lswm7/missing_hud_2_rebirth_stats_overlay/)

4. Other Sources
[Rebirth Item Tracker](https://github.com/Hyphen-ated/RebirthItemTracker) Has cross-platform support. May be useful to crib notes from. (sidenote: Python is much easier to read than C++)

##Dependencies
NetworkMe listed the following dependencies for the dll
```
1. Main executable (DLL injector)
  * [Qt5](http://www.qt.io/) (static version directory set manually in CMake)
  * [EasyLogging++](https://github.com/easylogging/easyloggingpp)
  * [Boost.Interprocess](http://www.boost.org/doc/libs/1_59_0/doc/html/interprocess.html)
  * [Google's Protobuf](https://github.com/google/protobuf)
2. Injected DLL
  * [GLEW](https://github.com/nigels-com/glew)
  * [SOIL2](https://bitbucket.org/SpartanJ/soil2)
  * [Boost.Interprocess](http://www.boost.org/doc/libs/1_59_0/doc/html/interprocess.html)
  * [Google's Protobuf](https://github.com/google/protobuf)
  
It uses the CMake build system to compile.
The easiest Windows MinGW environment to compile it on is the MSYS2 enviroment.
```
If LD_Preload works, I don't see why the main executable would be necessary. Regardless, each of the dependecies is cross-platform. Fudnamentally, I would probably aim to replace QT5 with GTK, since that is included in the Steam libraries.

All of the dependencies of the injected object are cross-platform as well. The DLL appears to use windows.h. Perhaps this can be replaced with a POSIX library, or perhaps a boost library. Boost is already being used in part, so that would be a somewhat elegant solution. 

##networkMe's notes:
## Using
Missing HUD 2 aims to be nearly transparent to the user (and to Isaac itself).

You simply run the main executable (which acts as the DLL injector) and the HUD will be drawn onto an active Isaac process.
Note: The HUD only appears if you are in an active run. You must leave Missing HUD 2 open while you play the game.

If you wish to no longer see the HUD, just close the main executable and the HUD will disappear (the DLL will be unloaded).

**Note:** The **latest version of Missing HUD 2** is designed to be used on the **latest Steam version** of the game.
If you are crashing or seeing weird stat values that are clearly incorrect, you most likely are not running the latest version of the game and/or Missing HUD 2. Pirated copies of the game are not officially supported by Missing HUD 2.

## Current features
* Works in fullscreen and windowed mode (as it's a direct OpenGL implementation)
* Shows how your raw statistics change as you pick up items and use pills, real-time
* Allows you to choose at what precision you see the raw statistics (default is 2 decimal place)
* Statistics HUD on the left of the Isaac viewport shows:
  * Total tears fired in run (optional)
  * Speed
  * Range
  * Tear firerate (Tear delay, lower is faster)
  * Shot speed
  * Shot height (optional)
  * Damage
  * Luck
  * Deal with the Devil % chance
  * Deal with the Angel % chance
