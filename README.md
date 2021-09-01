# Dotfiles management through git and stow

- [Dotfiles management through git and stow](#dotfiles-management-through-git-and-stow)
  - [Installation](#installation)
  - [Update](#update)
  - [Profiles](#profiles)
  - [How it works](#how-it-works)
- [Making it your home](#making-it-your-home)

This repository contains dotfiles for managing an `Ubuntu 21.04` home folder. Additionally it includes an assortment of scripts to streamline and secure the process. The Structure is composed as follows:

```
⤷ .setup*
    ⤷ [config_package]*
        ⧉ [config]*      # Local configuration files
⤷ bootstrap
    ⧉ [dotfiles]         # Dotfiles Needed for setup
⤷ helpers
    ⧉ [scripts]          # Scripts to be included in dotfile management scripts
⤷ dotfiles
   ⤷ [package_name]
       ⧉ [dotfiles]      # Dotfiles to be stowed in home directory
   ⧉ [scripts]           # Scripts for managing dotfiles 
⤷ secure
    ⤷ [package_name]
        ⧉ [dotfiles.gpg] # Encrypted secure dotfiles
        ⧉ [dotfiles]*    # Decrypted secure files stowed in dotfiles/
   ⧉ [scripts]           # Scripts for managing secure dotfiles 
⤷ profiles
    ⤷ template           # Template files stowed in each profile
        ⤷ dotfiles       # Same structure as root with dotfiles and
        ⤷ secure         # secure dotfiles
        ⧉ [scripts]      # Scripts for managing single profiles 
    ⤷ [profile_name]     # Each profile has the tenplate stowed into it
        ⤷ dotfiles       # Stowed from template
        ⤷ secure         # Stowed from template
        ⤷ [migration]    # Profile specific migrations
   ⧉ [scripts]           # Scripts for managing secure dotfiles 
⤷ migrations
   ⧉ [scripts]           # Possible migration scripts between versions

* personal files not tracked by git
```

## Installation

For intial installation run from your home directory:

```
git clone git@github.com:1nVitr0/.dotfiles.git
.dotfiles/install
```

The install script will interactively set up all the required configuration, decrypt the secure dotfiles and stow everything inside your home directory. Make sure you have access to all the repositories of the profiles you are using.

## Update

To update, it should be enough to run the global update script. It will pull changes automatically:

```
.dotfiles/update
```

It is recommended not to touch the encrypted dotfiles before a pull, as changes in encrypted files are very hard to track using git. The update script will automatically ask for manual intervention on merge conflicts.

After adding changes, the upstream can be updated using the normal git commit / push workflow.

## Profiles

During installation you will be prompted for profiles. Profiles are user-, machine- or organisation specific dotfile packages. You can select any number of profiles and all will be applied in order, overriding the global config as well as any profiles before it.

You will also be asked to set a default profile, which is the last profile applied independent of the order of the other profiles. You are always able to switch the main profile used using:

```
.dotfiles/switch
```

To create a new profile, you must first create an empty repository for your profile. It can be private or public, but you must have access from your command line. Then run the create script and follow the instructions:

```
./dotfiles/profiles/create [PROFILE_NAME]
```

## How it works

This repository attempts to manage dotfiles through a combination of [stow](https://www.gnu.org/software/stow/) and [git](https://git-scm.com/). The root repository works the same way profiles do:

Dotfiles are grouped into packages that get symlinked to your home directory using `stow`. Dotfiles in the secure directory are encrypted using [gpg](https://gnupg.org/) and the encrypted versions are symlinked from `secure/[PACKAGE]` to the `dotfiles/[PACKAGE]`, which in turn is symlinked to your `home` directory.

Creating profiles uses the exact same structure (in fact the exact same scripts, they are symlinked as well). Each profile is applied in the order they are given on top of the root dotfiles. The currently selecting profile is applied last and overrides all dotfiles from the previously loaded profiles.

# Making it your home

To use this repository as a dotfile management system, you will most likely have to fork the existing version. feel free to delete or edit the `.gitmodules` file if you are not using any of the containing profiles.

You can then add your own profiles and keep them updated in your own repositories. You should only edit dotfiles in your profile directories to maintain peak updatability with the original version.

Updating to the upstream version can be done through the default git worklfow:

```
git merge upstream/main
```