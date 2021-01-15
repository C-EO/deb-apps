# Deb-Apps   
## Debian App Store for Open Source Projects


## To install Pi-Apps
Make sure `git` is installed with `sudo apt install git`, then run:
```
https://github.com/RaspberryRBX/deb-apps
~/deb-apps/install
```
The install script ensures YAD is installed, creates two menu buttons, and an autostarted updater. Nothing is modified outside your home directory.
## To run Deb-Apps
Menu -> Accessories -> Deb Apps Apps, or type `~/pi-apps/gui`.
### Useful links
https://discord.io/RaspberryRBX

### Basic usage
- This is the **main window**:  
![main window](https://github.com/Botspot/pi-apps/blob/master/icons/screenshots/main%20window.png?raw=true)  
Use the main window to quickly browse the selection of apps and easily install them.  
- If you double-click an app, or select and app and click Details, you will see the **Details window**.  
![details](https://github.com/Botspot/pi-apps/blob/master/icons/screenshots/details%20window.png?raw=true)  
- The **updater window** may pop up when you launch Pi-Apps, or when your Pi boots:  
![updates](https://github.com/Botspot/pi-apps/blob/master/icons/screenshots/updates%20available.png?raw=true)  
- Pi-Apps **Settings** can be configured by launching Menu -> Preferences -> Pi-Apps Settings.  
![settings](https://github.com/Botspot/pi-apps/blob/master/icons/screenshots/settings.png?raw=true)  
- If you click **New App** in Settings, you can easily create your own Apps using the Create App Wizard.
![create app](https://github.com/Botspot/pi-apps/blob/master/icons/screenshots/create%20app.png?raw=true)  
It helps you select an icon, create & debug install/uninstall scripts, write a description, and more.  
## To-do
- [X] Make app creation system. (completed with the `createapp` script)  
- [X] Add Pi-Apps to Twister OS. (completed on 11/2/2020 via the Twister 1.8.5 patch.)  
- [X] Make 32-bit and 64-bit install scripts.  
- [X] Allow multiple apps to be selected from the app list and be installed simultaneously.  
- [X] Add a search function to the app list. It's still experimental: to enable it, switch to **xlunch** in **Pi-Apps Settings** -> **App List Style**.

## How it works
 - Each 'App' is simply a small `install` script, `uninstall` script, two icon sizes, and two text files containing the description and a website URL.
 - Each App is stored in its own separate directory. `~/deb-apps/apps/` holds all these app directories. The Zoom app, for example, would be located at `~/deb-apps/apps/Discord/`.
 - Because of the contained nature of each app folder, it's really easy to 'package' your own apps: just put the folder in a ZIP file and send it to friends. (or upload it as a [new issue](https://github.com/RaspberryRBX/deb-apps/issues/new) so your app can be added to Pi-Apps)
 - When you click Install, the selected App's `install` script is executed. (Or, in some cases, the `install-32` or `install-64` script is executed.)
 - When you click Uninstall, the selected App's `uninstall` script is executed.
## Terminal usage
 - The `manage` script is similar to `apt-get` - it handles installing apps, uninstalling them, keeping them updated, and more. `Manage` does not include a GUI, though in some cases, a dialog may appear to ask you a question.
   - To **install** an app, run this:
`~/pi-apps/manage install Zoom`
   - To **uninstall** an app:
`~/pi-apps/manage uninstall Discord`
### Directory tree
 - `~/deb-apps/` This is the main folder that holds everything. In all scripts, it is represented as the `${DIRECTORY}` variable.
   - `COPYING` This file contains the GNU General Public License v3 for Pi-Apps.
   - `createapp` GUI script - this is run when you click "Create App" in Settings.  
   ![create app](https://github.com/Botspot/pi-apps/blob/master/icons/screenshots/create%20app.png?raw=true)
   - `gui` The main GUI window. This script  is responsible for displaying the App list and the Details page.
   ![main window](https://github.com/Botspot/pi-apps/blob/master/icons/screenshots/main%20window.png?raw=true)
   - `install` This script is used to install Pi-Apps. Adds a couple menu launchers, and makes sure YAD is installed.
   - `manage` This script handles installing, uninstalling, and updating Apps. It does not check or update any files outside the `apps/` directory.
   - `pi-apps.desktop` This file is a .desktop launcher, exactly the same as the main Pi-Apps launcher in Menu.
   - `pkg-install` If an App requires some `apt` packages in order to run, its `install` script will run `pkg-install`. Pkg-install records which app installed what (in the installed-packages folder BTW), so when you uninstall an App, those packages will be removed.
   - `preload` This script generates the app list for the `gui` script. If no files have been modified since last launch, `preload` won't regenerate the app list, but instead will return a previously saved version of the list. This approach reduces Pi-Apps's launch time by around 1 second.
   - `purge-installed` This does exactly the opposite of `pkg-install` This script is run when an App is being uninstalled. Purge-installed will uninstall all packages the app installed.
   - `README.md` You are reading this file right now!
   - `settings` This GUI script is executed when you launch 'Pi-Apps Settings' from the Menu.
   ![settings](https://github.com/Botspot/pi-apps/blob/master/icons/screenshots/settings.png?raw=true)
   - `uninstall` Uninstalls Pi-Apps and removes the menu launchers. Asks permission to uninstall YAD.
   - `updater` This GUI script is executed every time  the `gui` script is launched. Updater first compares today's date against the `last-update-check` file. If it's time to check for updates, `updater` first checks for App updates, then checks for other files/folders that have been modified or created. If anything can be updated, a dialog will open and ask permission to update:  
   ![updates](https://github.com/Botspot/pi-apps/blob/master/icons/screenshots/updates%20available.png?raw=true)
   - `data/` This folder holds all local data that should not be overwritten by updates.
     - `settings/`This stores the current settings saved by the 'Pi-Apps Settings' window. Each file contains one setting. For example, the file `settings/Preferred text editor` contains "geany" by default.
     - `status/` This folder stores all installation information for all apps
     "latest" means that app is up to date.
     "new" means that app is new from the repository. (in other words, it does not exist locally)
     "local" means that app does not exist on the repository.
     "updatable" means the repository's version and the local version don't match.
     - `preload/` This directory is used by the `preload` script to improve Pi-Apps' launch time.
       - `timestamps` This file stores timestamps for the most recently modified app, the most recently modified setting, and the most rencently modified status file.
       If any of these entries don't match when `preload` is called, then the app list will be regenerated.
       - `LIST` This file holds the app list. The entire file's contents is piped into the YAD dialog box.
     - `installed-packages/` This keeps track of any/all APT packages each app installed. This folder is written to from the `pkg-install` script.
     For example, if Pi Power Tools installs `xserver-xephyr` and `expect`, then the `installed-packages/Pi Power Tools` file will contain "xserver-xephyr expect"
   - `etc/` This folder is basically an extension of the main `pi-apps/` folder. Its contents don't need to clutter up the main directory, but they can't go in `data/` because these files should be kept up-to-date.
     ```
     #Donate automatically to Botspot every time Pi-Apps is launched
     Yes
     No
     ```
     Now, the next time Settings is opened, you will see:  
     ![auto-donate](https://i.ibb.co/nzBNgFT/auto-donate.png)
     With this file-based approach, adding new settings (and/or parameters) is much easier to do and in a standardized way. (As opposed to adding new settings by editing a bash script)
     - `git_url` This simple file stores this link: https://github.com/Botspot/pi-apps
     If you fork this repository and make changes, you will want Pi-Apps checking for updates from your repository, not this main one. Simply change the URL in this file to switch to your repository.
   - `icons/` This stores all the icons that are embedded into various dialogs.
     - `screenshots/` Stores screenshots of various dialogs, mainly used as an image hosting service, though I suppose they could come in handy if an offline help dialog was made.
   - `update/` This folder holds the latest version of the entire Pi-Apps repository. It's contents is re-downloaded every time the `updater` script checks for updates. It is used to compare file hashes, detect when an app or file can be updated, and is used to copy new file versions into the main `pi-apps/` directory during an update.
## Badge
### Q&A with RaspberryRBX
 - Why did you develop Pi-Apps?  
> I Was On Debian In A VM Using Botspots YTBuddy App Then Got Idea To Turn PiApps Into Deb Apps
And Asked To Do This And Keep Botspot Updated Then Started Coding It.

 - How long did it take to program this?  
> It Took Me 5 Days Changing Botspot Code And Install To Make It Compatible With Debian.
>
