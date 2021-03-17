# Configuration Automation Station
_Setup and Configuration Settings for MBP Catalina_

Well at least one day this will be automated/automatable. The first step to this though is actually logging what goes where, how I set what and what order I do all these things in... That's what this repository is for. Then we can iteratively automate these steps. :)

## Basic Steps:
- Boot into `recovery` with `cmd` + `r`
- Install `macOS Catalina` (10.15.xx)
- After initial boot and user setup, restart again in `recovery`
- Open the `terminal` under the `utilities` recovery menu item
- Enter `csrutil status` and confirm it returns `enabled`
- Enter `csrutil disable` and confirm it says `System Integrity Protection disabled` _(or something to this effect)_
- Enter `shutdown -r now`
- After reboot and successful logon, open `terminal`
- Enter `csrutil status` and confirm it returns `disabled`
- Enter `git` to trigger the `xcode-select` prompt for install _(this line changes with various OS versions, so I find it best to simply trigger it with `git`)_
- After prompt declares successful install of `xcode-commandline-tools`, re-open terminal _(or open a new `shell`)_ and enter `git` again and confirm the `git command menu` returns _(typically starts with `usage: git [--`, as an automation note)_
  - IF you have a 3rd party automating the installation or use of profiles on the machine, for whatever reason, and you want to have those removed, for whatever reason, you can do that with the following steps:
    - assuming you were given a return of something like `jamf not found` when running the typical `sudo jamf -removeFramework` and/or you receieved a return of something like `some configuration profiles marked as non-removable` when running the typical `profiles -D` && `profiles -d`, proceed with the following:
      - open a new terminal/shell and enter `dsenableroot` enabling the root user and set a password
      - enter `su root` to switch into the active `root` user
      - enter `cd /var/db` to navigate to where the `ConfigurationProfiles` are kept
      - enter `mv -f ConfigurationProfiles _ConfigurationProfiles-OLD` _(We are assuming/assuring this still works in the case that the entity or program is smart enough to simply rename it if you merely append the end of the name of the `profile`. Most `mdm` management hosts/programs/scripts do not think to check the beginning of the profile directory name for changes.)_ 
      - enter `ls -la` to review all hidden files in a readable format and to confirm a return of the `ConfigurationProfiles` name change
      - enter `exit` and then `logout` in that shell, and then proceed to logout through the GUI as normal. _(or you can alternatively run this command 100% in the terminal, there are just a few bugs that can happen doing it exclusively in the terminal, when the GUI doesn't always keep up, esp. in account features)_
    - after successful re-entry or re-logon, open `System Preferences` and confirm there is nothing showing for the `profiles` pane _(the profiles pane shouldn't show up there at all anymore completely)_ and you can re-affirm this is set properly with a new terminal/shell and enter `sudo profiles -P` to ensure there are zero profiles set.
