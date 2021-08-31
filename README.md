# Dotfiles for Aram Becker

- [Dotfiles for Aram Becker](#dotfiles-for-aram-becker)
  - [Installation](#installation)
  - [Update](#update)
  - [Profiles](#profiles)

This repository contains dotfiles for managing the home folder of Aram Becker. Additionally it includes an assortment of scripts to streamline and secure the process. The Structure is composed as follows:

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
        ⧉ [dotfiles]     # Decrypted secure files stowed in dotfiles/package_name
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
```

## Installation

For intial installation run from your home directory:

```
git clone git@github.com:1nVitr0/.dotfiles.git
.dotfiles/install
```

The install script will interactively set up all the required configuration, decrypt the secure dotfiles and stow everything inside your home directory.

## Update

To update, it should be enough to pull changes and run the global update script:

```
cd .dotfiles
git pull
./update
```

It is recommended not to touch the encrypted dotfiles before a pull, as changes in encrypted files are very hard to track using git. The update script will automatically ask for manual intervention on merge conflicts.

After adding changes, the upstream can be updated using the normal git workflow.

## Profiles

During installation you will be prompted for profiles. Profiles are user-, machine- or organisation specific dotfile packages. You can select any number of profiles and all will be applied in order, overriding the global config as well as any profiles before it.

You will also be asked to set a default profile, which is the last profile applied independent of the order of the other profiles. You are always able to switch the main profile used using:

```
.dotfiles/switch
```