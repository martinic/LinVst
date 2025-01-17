# LinVst

LinVst adds support for Windows vst's to be used in Linux vst capable DAW's.

See LinVst-X (https://github.com/osxmidi/LinVst-X) for running vst plugins in a single Wine process so that plugins can communicate with each other or plugins that can use shared samples between instances will be able to communicate with their other instances.

See LinVst3 https://github.com/osxmidi/LinVst3 for running vst3 plugins.

## Quick Start Guide (see the Detailed-Guide folder for more info) 

(See the Wiki page for a visual guide https://github.com/osxmidi/LinVst/wiki)

Copy all of the lin-vst-serverxxxx files (files with lin-vst-server in their names) to /usr/bin.

Install the vst's.

The vst's will probably be installed by default to a Wine folder, something like ~/.wine/drive_c/Program Files/Steinberg/VSTPlugins (which is similar to where they are installed on Windows).

It's also possible with most plugins to make a folder and install the windows vst's into it.

Start linvstconvert (in the convert folder) and then select the linvst.so file.

Point linvstconvert to the folder containing the windows vst's and hit the Start (Convert) button.

Start up the linux DAW and point it to scan the folder containing the windows vst's.

If new vst plugins are added to a folder, then just run linvstconvert again on that folder.

More detailed install info is at https://github.com/osxmidi/LinVst/tree/master/Detailed-Guide which also includes possible performance details.

Install details for vsts are at https://github.com/osxmidi/LinVst/tree/master/Tested-VST-Plugins.

Make info is at https://github.com/osxmidi/LinVst/tree/master/Make-Guide

Binary LinVst releases are available at https://github.com/osxmidi/LinVst/releases

If a LinVst version error pops up then LinVst probably needs to be reinstalled to /usr/bin and the older (renamed) linvst.so files in the vst dll folder need to be overwritten (using linvstconvert).

Scripts are also avaliable as an alternative to linvstconvert in the convert and manage folders and also https://github.com/Goli4thus/linvstmanage

## Common Problems/Possible Fixes

For performance setup see https://github.com/osxmidi/LinVst/tree/master/Realtime-Audio-Config

If window resizing does not work, then after a resize the UI needs to be closed and then reopened for the new window size to take effect.

One of the problems in trying to intially load multiple vst's all at once is that when the Daw tries to load them, one or more plugins might not scan (and/or run) and the Daw scan might stop/hang.

Some plugins might not load for various reasons, and one of the basic ways to find problem plugins is to run the Daw from the terminal and look at the LinVst output for successful plugin loading as it tries to load each plugin.

It might require multiple Daw plugin rescans (maybe manual plugin rescans for some Daws) and also eliminating problem plugins by deleting their associated (renamed) .so files to get all working plugins scanned.

To be sure about what plugin might run and what plugin might not, it's probably best to proceed by adding one plugin at a time and/or by using the TestVst utility https://github.com/osxmidi/LinVst/releases which can also be run in batch mode to test multiple vsts.

TestVst can also be used to test dll overrides (wine dll's that are replaced by windows dll's) and if they are likely to work, without going through dll override trial and error using LinVst.

The terminal output of TestVst can sometimes be used to work out what dll override might be needed (if wine dll unimplemented function errors appear in the terminal output for instance).

Some plugins won't run due to various problems.

If a GLXBadFBConfig error occurs (check daw errors, it may happen with Kontakt and maybe with other plugins and certain video and mesa systems)

add MESA_GL_VERSION_OVERRIDE=4.5 to ~/.pam_environment (create the file if it doesn't exist) and then log out and in.

or by entering into a terminal

export MESA_GL_VERSION_OVERRIDE=4.5

and then start the daw from that terminal

----

To install a windows vst exe installation file use wine "path to the exe file"

The quotes also handle paths with spaces in their names.

```
For example, if the windows vst exe installation file is located in the Downloads folder and is named delay.exe, then (from the terminal) wine ~/"Downloads/delay.exe" will start the installation (~ stands for the user path ie /home/your-user-name). 

```

msi files can be clicked on to start their installation.

```
Usually by default vst installers install to a path something like ~/.wine/drive_c/Program Files/Steinberg/VSTPlugins and that would usually be the path/folder to use with linvstconvert and then that path would be set in the DAW's plugin search paths.

To change into the plugin directory (using the terminal) cd ~/".wine/drive_c/Program Files/Steinberg/VSTPlugins"

```

-----

If a LinVst version error pops up then LinVst probably needs to be reinstalled to /usr/bin and the older (renamed) linvst.so files in the vst dll folder need to be overwritten (using linvstconvert).

LinVst looks for wine in /usr/bin and if there isn't a /usr/bin/wine then that will probably cause problems.
/usr/bin/wine can be a symbolic link to /opt/wine-staging/bin/wine (for wine staging) for example.

Quite a few plugins need winetricks corefonts installed for fonts.

A large number of vst plugin crashes/problems can be basically narrowed down to the following dll's and then worked around by overriding/disabling them.

Quite a few vst plugins rely on the Visual C++ Redistributable dlls msvcr120.dll msvcr140.dll msvcp120.dll msvcp140.dll etc

Some vst plugins might crash if the Visual C++ Redistributable dlls are not present in /home/username/.wine/drive_c/windows/system32 for 64 bit vst's and /home/username/.wine/drive_c/windows/syswow64 for 32 bit vst's, and then overridden in the winecfg Libraries tab

Some vst plugins might not work due to Wines current capabilities or for some other reason.

Use TestVst for testing how a vst plugin might run under Wine.

Some vst plugins rely on the d2d1 dll which is not totally implemented in current Wine.

If a plugin has trouble with it's display then disabling d2d1 in the winecfg Libraries tab can be tried.

Some plugins need Windows fonts (~/.wine/drive_c/windows/Fonts) ./winetricks corefonts

Setting HKEY_CURRENT_USER Software Wine Direct3D MaxVersionGL to 30002 (MaxVersionGL is a DWORD hex value) might help with some plugins and d2d1 (can also depend on hardware and drivers).

wininet is used by some vst's for net access including registration and online help etc and sometimes wines inbuilt wininet might cause a crash or have unimplemented functions.

wininet can be overridden with wininet.dll and iertutil.dll and nsi.dll

The winbind and libntlm0 and gnutls packages might need to be installed for net access (sudo apt-get install winbind sudo apt-get install gnutls-bin sudo apt-get install libntlm0)
For Arch based distros sudo pacman -Sy gnutls lib32-gnutls samba

Occasionally other dlls might need to be overridden such as gdiplus.dll etc

Winetricks https://github.com/Winetricks/winetricks can make overriding dll's easier.

**For the above dll overrides**

```
winetricks vcrun2013
winetricks vcrun2015
winetricks wininet
winetricks gdiplus
```

Winetricks also has a force flag --force ie winetricks --force vcrun2013

cabextract needs to be installed (sudo apt-get install cabextract, yum install cabextract etc)

For details about overriding dll's, see the Wine Config section in the Detailed Guide https://github.com/osxmidi/LinVst/tree/master/Detailed-Guide

To enable 32 bit vst's on a 64 bit system, a distro's multilib needs to be installed (on Ubuntu it would be sudo apt-get install gcc-multilib g++-multilib)

Drag and Drop is enabled for the embedded LinVst version used with Reaper/Tracktion/Waveforn/Bitwig but it's only for items dragged and dropped onto the vst window and not for items dragged and dropped from the vst window to the DAW/host or to the Desktop window.
Usually the dragged item (dragged outside of the vst's window) will be saved as a midi or wav file in a location that is most likely to be located in one of the vst's folders ie a folder in My Documents or a folder that the vst installation has created. The midi or wav file can then be dragged to the DAW.
See MT-PowerDrumKit and EZDrummer2 and Addictive Drums 2 and SSD5 in the Tested VST's folder at https://github.com/osxmidi/LinVst/tree/master/Tested-VST-Plugins for some details.

Also, see the Tested VST's folder at https://github.com/osxmidi/LinVst/tree/master/Tested-VST-Plugins for some vst plugin setups and possible tips.

**Waveform**

For Waveform, disable sandbox option for plugins.

**Bitwig**

For Bitwig 2.5 and 3.x, In Settings->Plug-ins choose "Individually" plugin setting and check all of the LinVst plugins.
For Bitwig 2.4.3, In Settings->Plug-ins choose Independent plug-in host process for "Each plug-in" setting and check all of the LinVst plugins.

**Renoise**

Choose the sandbox option for plugins.

Sometimes a synth vst might not declare itself as a synth and Renoise might not enable it.

A workaround is to install sqlitebrowser

sudo add-apt-repository ppa:linuxgndu/sqlitebrowser-testing

sudo apt-get update && sudo apt-get install sqlitebrowser

Add the synth vst's path to VST_PATH and start Renoise to scan it.

Then exit renoise and edit the database file /home/user/.renoise/V3.1.0/ CachedVSTs_x64.db (enable hidden folders with right click in the file browser).

Go to the "Browse Data" tab in SQLite browser and choose the CachedPlugins table and then locate the entry for the synth vst and enable the "IsSynth" flag from "0" (false) to "1" (true) and save.

-----------

Optional Symlinks

A symlink can be used to access vst2 plugin folders from another more convenient folder.

Hidden folders such as /home/your-user-name/.wine/drive_c/Program Files/Steinberg/VSTPlugins can be accessed by the Daw by creating a symlink to them using a more convenient folder such as /home/your-user-name/vst2 for instance.

For example

ln -s "/home/your-user-name/.wine/drive_c/Program Files/Steinberg/VSTPlugins" /home/your-user-name/vst2/vst2plugins.so

creates a symbolic link named vst2plugins.so in the /home/your-user-name/vst2 folder that points to the /home/your-user-name/.wine/drive_c/Program Files/Steinberg/VSTPlugins folder containing the vst2 plugins.

The /home/your-user-name/.wine/drive_c/Program Files/Steinberg/VSTPlugins vst2 plugin folder needs to have had the vst2 plugins previously setup by using linvstconvert.

Then the Daw needs to have the /home/your-user-name/vst2 folder included in it's search path.

When the Daw scans the /home/your-user-name/vst2 folder it should also automatically scan the /home/your-user-name/.wine/drive_c/Program Files/Steinberg/VSTPlugins folder that contains the vst2 plugins (that have been previously setup by using linvstconvert).
