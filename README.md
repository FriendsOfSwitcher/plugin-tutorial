# Plugin tutorial

Welcome! This tutorial describes how to create a Switcher plugin.

## How difficult is creating a plugin?

For most games, you don't need any programming experience to create a plugin (see #codeless-plugins). Creating a plugin could take less than 20 minutes, and it can then be made available for the whole community to use!

## Introduction to plugins

Different games save their graphics/keypmap settings in a different way. Plugins are used to allow Switcher to interact with the settings of each game. 

Plugins can support any, or all, of three features for a game:

- Graphics.
- Keymaps.
- Game saves.

### Codeless plugins

For most games, a plugin can be created without writing any code. This is called a "codeless" plugin, which simply consists of a text file called `plugin.yaml". The text file contains information which allows Switcher to locate the relevant settings.

You should be able to create a codeless plugin if:

- The game saves graphics and keymap settings in separate files.

### Python plugins

Plugins can also be implemented in Python. You may need to use Python to create a plugin if:

- The game stores graphics and keymap settings in the same file.

## Preparation

You'll need to:

- [Install Git](https://git-scm.com/downloads) to download your plugin and upload your changes. (You can edit files directly on GitHub, but this would make testing more difficult.)
- Create a GitHub account, if you don't already have one.

## Getting started

This section describes common steps which will need to be followed before one of the walkthroughs below.

- [Click here](https://github.com/SwitcherForGames/codeless-plugin-template/generate) to create a repository based on the [Codeless Plugin Template](https://github.com/SwitcherForGames/codeless-plugin-template).
- Choose a name (preferably lower-case, with words separate using hyphens, e.g. `doom-plugin`).
- Set the description to "Switcher plugin for X", where `X` is the name of the game. `X` should be formatted in a way that it is similar to the name of the folder where the installed; for example, "Kingdom Come Deliverance" is a suitable name to use for `X`, because it's similar to the game folder `KingdomComeDeliverance`.
- Click "Create repository from template".
- You'll be redirected to your new plugin repository.
- Click the green "Clone or download" button in the top-right.
- Copy the link using the button on the right-hand side of the link starting with `https://github.com/USERNAME`.
- In File Explorer, navigate to a directory where you will save the plugin. 
- In File Explorer, ensure nothing is selected, then hold `Shift` while right-clicking on an empty area. 
- Click "Open Powershell window here".
- In the Powershell window, type `git clone ` (with the trailing space) then right-click to paste the copied link.
- Press `Enter`.
- Your plugin repository will be downloaded.
- In the Powershell window, run `git config --global user.name "YOUR_NAME"`. (**Privacy warning**: this will be visible to anyone who downloads your repository and uses `git log`.)
- In the Powershell window, run `git config --global user.email "YOUR_EMAIL_ADDRESS"`. (**Privacy warning**: like your name in the step above, this will be visible to anyone. You should use your GitHub-provided anonymous email address, which you can find in your [GitHub settings](https://github.com/settings/emails). I also recommend ticking "Block command line pushes that expose my email" to prevent accidents.)

You can now open the folder containing your plugin with VS Code.

## Walkthrough: Doom Eternal

In this walkthrough, we'll create a plugin for Doom Eternal. I'm using Windows 10.

First, let's identify whether Doom Eternal can be supported via a codeless plugin: search Google for ["Doom Eternal graphics config file"](https://www.google.com/search?hl=en&q=doom%20eternal%20graphics%20config%20file).

From our research, we find that configs are saved to `C:\Users\USERNAME\Saved Games\id Software\DOOMEternal`. By poking around the folder, we can deduce that keymaps are stored in `base\DOOMEternalConfig.cfg` and a few graphics settings are stored in `base\DOOMEternalConfig.local`. 

Bad news: we can't fully support graphics settings unless we find a file where the remaining graphics settings are stored. However, we can support keymaps, which may be important to some players who have adjusted their keymaps for optimal efficiency; they will now be able to back up and restore their settings.

In the downloaded repository, open `plugin.yaml`. 

Set the value of `game` to `"Doom Eternal"`. To clarify the change:

```diff
- game: "TODO"
+ game: "Doom Eternal"
```

Set `api` to `1`.

Find the Steam ID of Doom Eternal, and set the value of `steamID`. To find the Steam ID, find the Steam page for the game in the browser and copy the number section of the URL; for Doom Eternal, the URL is [https://store.steampowered.com/app/782330/DOOM_Eternal/](https://store.steampowered.com/app/782330/DOOM_Eternal/) so the Steam ID is `782330`.

Set `uid` to a universal identifier. This should be unique, but its format is not important. I recommend the Java package name convention, which is to use `com.PERSON_ID.REPO_ID`; in this case, I'll use `com.thegreatcabbage.doometernal`.

Set `author` to your GitHub username. 

Set `packageName` to a lower-case, no-spaces name such as `doometernal`.

Set `features` to the supported features, as a list containing one or more of `graphics`, `keymaps` and `saves`. In our case, we'll just support `keymaps`:

```
features:
  - keymaps
```

Remove the lines for `graphicsConfig` and `savesFolder`, since they are not being used.

Set `keymapConfig` to the location of the keymap config file. Since it's stored in a folder within `C:\Users` which depends on the current user, we can't use a plain file path; we need to use what Switcher calls a "Path variable". 

Instead of `C:\Users\USERNAME\Saved Games\id Software\DOOMEternal\base\DOOMEternalConfig.cfg`, we'll use `C:\Users\$!USERNAME!$\Saved Games\id Software\DOOMEternal\base\DOOMEternalConfig.cfg`. The `$!` and `!$` surrounding `USERNAME` instructs Switcher to substitute the real username.

Now, find the folder where Doom Eternal is saved (e.g. `C:\Program Files (x86)\Steam\steamapps\common\DOOMEternal`) and set `GameDirectory` to the folder name: `DOOMEternal`. This is case-sensitive, so take care!

Find a file which identifies that the game is installed (not an empty folder), such as the `.exe` used to launch the game. For Doom Eternal, `DOOMEternalx64vk.exe` will suffice. (This path is relative to the game's folder, and you can use a nested directory such as `bin/win64/GAME.exe` if necessary.)

Now we can upload the plugin to GitHub. Open the plugin's folder in File Explorer, hold `shift` while right-clicking on an empty space and click `Open Powershell window here`. Then run the following commands:

```
git add -A
git commit -am "Initial version of plugin"
git push -u origin master
```

Finally, go to the GitHub page for your repository and click "Manage topics". Add "switcher-plugin" as a topic, and Switcher will be able to detect your plugin under the "Community plugins" section in the "Manage plugins" dialog. 