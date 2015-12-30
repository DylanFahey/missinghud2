## Overview
This repository is my experiment at getting MissingHud2 to work on the Linux version of Rebirth. I haven't used C++ in 5 or 6 years, so this is probably going to be a disaster. 

Since MissingHud2 uses a dll injector to work, a similar approach should be possible on Linux. I almost assume it would be easier in Linux than in Windows, using LD_PRELOAD. However, I believe libraries are included in the binaries of Steam games, so I'm not entirely sure if LD_PRELOAD works under those conditions. 

Furthermore, I do not own Afterbirth. I'm relatively terrible at BOI and bullet hell/roguelikes in general, so I can get a lot more mileage out of a game before I run out of content than someone who chews through content. Plus I bought the original BOI with Wrath of Lamb included, and I would have appreciated playing the easier game for a while first. I don't really want to make the same mistake. 

As such, I think my path will probably be as follows: 

1.) Read source code and identify any areas where windows-specific code is being run. Determine what the Isaac-specific dependencies are, and determine if these are any different in Linux and Windows. 

2.) Try to compile dll as a shared object.

3.) Try to eliminate as many errors as possible. Replace calls to windows-specific libraries with POSIX or other cross-platform libraries. Simplify the code if neccessary by removing Afterbirth support. 

4.) Once the dll has been compile without errors, try and inject the shared object using LD_Preload. 

5.) Evaluate progress. If the injection works, share with BOI modding community. If I get stuck I should probably ask for help either way. Ideally I'd get it working to a degree where other people could pitch in a bit.  

There are a few area's where I could probably use help:

1.) Determining what the differences are between the Linux and the Windows versions. 

2.) Testing the code on different platforms. I use FC23. I imagine most of the users will be using OSX, Ubuntu, or a Steam Machine of some kind. 

3.) Figuring out memory locations. I have no experience modding on Linux. I have used a memory tool to cheat on (local) games before, but I don't understand enough about the structure of Linux to understand what I'm doing with memory. 

4.) Checking my code. I haven't programmed in C++ in forever, and even then I don't think I ever learned memory management. 


##networkMe's notes:
Missing HUD 2 is an OpenGL powered informational overlay for the Binding of Isaac: Rebirth + Afterbirth.

The developers of the Binding of Isaac (Edmund McMillen, Nicalis) decided that one of their design decisions for the game would be to hide raw player statistics from the player as to not to overwhelm them. This project gives the player the choice to see their raw statistics if they choose to.

It is a transparent mod that **DOES NOT** disable achievements nor alter your Isaac game files in any way. **Note:** It can be used in Afterbirth daily runs with no repercussions.

It can be enabled and disabled at any point, during any run, with no lasting consequences. One can run other mods side-by-side with Missing HUD 2 with no issues.

Unlike other statistic based mods, it uses your live character statistics during a run. This translates to Missing HUD 2 remaining 100% accurate even after picking up items like [Experimental Treatment](http://bindingofisaacrebirth.gamepedia.com/Experimental_Treatment) and [Libra](http://bindingofisaacrebirth.gamepedia.com/Libra).

## Using
Missing HUD 2 aims to be nearly transparent to the user (and to Isaac itself).

You simply run the main executable (which acts as the DLL injector) and the HUD will be drawn onto an active Isaac process.
Note: The HUD only appears if you are in an active run. You must leave Missing HUD 2 open while you play the game.

If you wish to no longer see the HUD, just close the main executable and the HUD will disappear (the DLL will be unloaded).

The latest binary release can be found here:
https://github.com/networkMe/missinghud2/releases/latest

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

## Building
Missing HUD 2 has the below dependencies:

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
