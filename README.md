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

## MacPorts and PKG_SRC via Joyent
_[MacPorts is here](#) and the [PKG_SRC](#) we are looking for is a [NetBSD](#) variety put out by the team at [Joyent](#), who are now owned or otherwise officially a part/subsidy of [Samsung](#)_

- Download `XQuartz` [from their website](https://www.xquartz.org)
  - Follow necessary prompts with the installer _(let this run in background and do not hit final confirmation/logout button yet)_
- Download `MacPorts` installer [from their website](https://www.macports.org/install.php) _(of you can visit the repository [here](https://github.com/macports/macports-base))_
  - Follow necessary prompts with the installer _(let this run in background and do not hit final confirmation button yet)_
- Grab a copy of the `tarball` from [Joyent's website](https://pkgsrc.joyent.com)
  - For Catalina, it's `BOOTSTRAP_TAR="bootstrap-macos14-trunk-x86_64-20200716.tar.gz"` && `BOOTSTRAP_SHA="395be93bf6b3ca5fbe8f0b248f1f33181b8225fe"` && `curl -O https://pkgsrc.joyent.com/packages/Darwin/bootstrap/${BOOTSTRAP_TAR}` && `echo "${BOOTSTRAP_SHA}  ${BOOTSTRAP_TAR}" | shasum -c-` with a return of `bootstrap-macos14-trunk-x86_64-20200716.tar.gz: OK`
  - Then you enter `sudo tar -zxpf ${BOOTSTRAP_TAR} -C /` with your `sudo password` and then `eval $(/usr/libexec/path_helper)`
  - Then it's `sudo pkgin -y update` to setup
  - and then restart to allow taking of effect _(logout really doesn't do the trick, and I take this opportunity to perform a `PRAM` reset)_

## Initial Minor Software Installs
Ordinarily, I'm from the mindset of keep it simple and keep the taxing services on your machine as low as possible, but there are a couple very minor exceptions to that rule, and these steps cover those programs and settings. 

- [PlistEdit Pro](https://www.fatcatsoftware.com/plisteditpro/)


## Native/Shipped macOS BS Removal
- enter `sudo mount -uw /` _(to mount the read-only root file system as editable)_
- enter `sudo rm -rf /System/Applications/Chess.app /System/Applications/FaceTime.app /System/Applications/Home.app /System/Applications/Maps.app /System/Applications/Mail.app /System/Applications/News.app /System/Applications/Photo\ Booth.app /System/Applications/Photos.app /System/Applications/Stocks.app /System/Applications/TV.app /System/Applications/Reminders.app` _(feel free to add more to the list lol)_

## Highly Subjective UI Alterations
_(Quite of few of these end up serving as `aliases` later on, so you can also run them as additions or `echo >> .zshrc` or `echo >> .bashrc` for your profile and then just run the aliases after successful return of the profile edits...)_
- Change rows and columns for `springboard aka launchpad`: _(shows you which name won out for consumers)_
  - enter `defaults write com.apple.dock springboard-columns -int X`, where `X` is the number of columns *****
  - enter `defaults write com.apple.dock springboard-rows -int Y`, where `Y` is the number of rows *****
  - enter `defaults write com.apple.dock ResetLaunchPad -bool TRUE;killall Dock`, where you reset both the `dock` and the `springboard/launchpad` as they are both coupled in the IOKit
- Kill the stupid and really poorly updated `AppleSpell` program _(espeically useful for C/C++/C#/Perl/Haskell or practically any developer)_
  - run the [**AppleSpell Disabled Forever** micro script](https://github.com/felenk/config_mbp/blob/main/README.md#applespell-disabled-forever)
- Kill trackpad `force touch/click` with: `defaults write NSGlobalDomain com.apple.trackpad.forceClick false`
- Update trackpad scaling factor with: `defaults write NSGlobalDomain com.apple.trackpad.scaling 1.5`
- Add contextual menu for Web Inspector with: `defaults write NSGlobalDomain WebKitDeveloperExtras true`
- Set `celcius` as the default temperature guage/measure with: `defaults write NSGlobalDomain AppleTemperatureUnit "Celsius"`
- Whenever you want to really play with or store/backup your values you can just pipe them into a file, like I do, with: `defaults read NSGlobalDomain > ~/Profilers/DefaultsGlobal.xml` _(and I have it in an `XML` format, because I have a script that later goes in and makes mass changes and then compiles these options into a viewable format for a GUI to develop later)_
- `//todo, cluster defaults write commands into a group/script to run as batch`
- More commands with: `defaults write NSGlobalDomain ` _(later to serve as interactive script)_
  - `com.apple.finder _FXShowPosixPathInTitle true` _(should actually be: `defaults write com.apple.finder _FXShowPosixPathInTitle -bool true`)_
  - `defaults write com.apple.Finder AppleShowAllFiles -bool true`
  - `defaults write com.apple.finder FXEnableExtensionChangeWarning -bool false`
  - `defaults write com.apple.finder PathBarRootAtHome -bool TRUE` with reversal as `defaults delete com.apple.finder PathBarRootAtHome`
  - `defaults write com.apple.Finder FXPreferredViewStyle Nlsv` where options are `Flwv`, `Nlsv`, `clmv` and `icnv` as `Cover Flow`, `List`, `Column`, and `icon` respectively
  - `defaults write NSGlobalDomain NSScrollViewRubberbanding -int 0` _(disable stupid rubberband finder)_
  - `defaults write com.apple.finder CreateDesktop -bool false`
  - `defaults write com.apple.finder QuitMenuItem -bool true`
  - `defaults write com.apple.screencapture disable-shadow true; killall SystemUIServer`
  - `defaults write NSGlobalDomain NSAutomaticWindowAnimationsEnabled -bool false`
  - `defaults write -g QLPanelAnimationDuration -float 0`
  - `defaults write NSGlobalDomain NSWindowResizeTime -float 0.001`
  - `defaults write com.apple.finder DisableAllAnimations -bool true`
  - `defaults write com.apple.dock launchanim -bool false`
  - `defaults write com.apple.dock expose-animation-duration -float 0.1`
  - `defaults write com.apple.Dock autohide-delay -float 0`
  - `defaults write com.apple.Safari WebKitInitialTimedLayoutDelay 0.25`
  - `defaults write NSGlobalDomain KeyRepeat -int 0`
  - `defaults write com.apple.finder ShowStatusBar -bool true`
  - `defaults write com.apple.finder ShowTabView -bool true`
  - `defaults write com.apple.finder ShowPathbar -bool true`
  - `defaults write com.apple.finder NewWindowTarget -string 'PfHm'`
  - `defaults write com.apple.loginwindow.plist TALLogoutSavesState -bool false`


## MicroScripts

### AppleSpell Disabled Forever
```sh
sudo mv /System/Library/Services/AppleSpell.service/Contents/Resources \
        /System/Library/Services/AppleSpell.service/Contents/Resources.disabled \
        && defaults write NSGlobalDomain NSAutomaticSpellingCorrectionEnabled false \
        && defaults write NSGlobalDomain NSAutomaticTextCompletionEnabled false \
        && defaults write NSGlobalDomain NSAutomaticCapitalizationEnabled false \
        && defaults write NSGlobalDomain NSAutomaticDashSubstitutionEnabled false \
        && defaults write NSGlobalDomain NSAutomaticPeriodSubstitutionEnabled false \
        && defaults write NSGlobalDomain NSAutomaticQuoteSubstitutionEnabled false \
        && defaults write NSGlobalDomain NSAutomaticSpellingCorrectionEnabled false \

killall AppleSpell
```
#### Two Notes:
1. `NSGlobalDomain` statements can be written as either: `defaults write -g NSAutomaticCapitalizationEnabled -bool false` or `defaults write NSGlobalDomain NSAutomaticCapitalizationEnabled false` _(I try to write out the actual key in my scripts to make the code more readable for my memory sake and your viewing/reproduction sake. It's also easier to turn those into repeatable input variables, as is listed in my `//todos`)_
2. In order to re-enable the `NSAutomaticSpellingCorrectionEnabled` key, just run `defaults write NSGlobalDomain NSAutomaticSpellingCorrectionEnabled true` and same with the `NSAutomaticTextCompletionEnabled -bool`


_***** Note: I've found it's better to have a higher count for the columns and a lower count for the rows, if you want to have the optimal amount of space utilized, without as much of the "clear/whitespace" between the icons. Something that just drives me a little bananas. Mine is 9 by 7. Just a suggestion._
