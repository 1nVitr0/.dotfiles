# Dotfiles management through git and stow

![.dotfiles manager](https://img.shields.io/badge/.dotfile-management-orange?logo=git)
![license](https://img.shields.io/github/license/1nVitr0/.dotfiles)
![latest release](https://img.shields.io/github/v/release/1nVitr0/.dotfiles?logo=semanticrelease)
![code size](https://img.shields.io/github/languages/code-size/1nVitr0/.dotfiles)


- [Dotfiles management through git and stow](#dotfiles-management-through-git-and-stow)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Update / Import](#update--import)
  - [Profiles](#profiles)
  - [Secure dotfiles](#secure-dotfiles)
  - [Config](#config)
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
    ⤷ [profile_name]     # Each profile has the template stowed into it
        ⤷ dotfiles       # Stowed from template
        ⤷ secure         # Stowed from template
        ⤷ [migration]    # Profile specific migrations
   ⧉ [scripts]           # Scripts for managing secure dotfiles 
⤷ migrations
   ⧉ [scripts]           # Possible migration scripts between versions

* personal files not tracked by git
```

## Prerequisites

The repository is designed for ubuntu-based machines, most recent versions should work. It might also work on debian systems, for other operating systems it's most likely required to update the helper scripts to fit your package manager and os-specific commands. But the general idea should work on all systems that have a home folder using dotfiles. The only required software is:

  - [Git](https://git-scm.com/)
  - [Bash](https://www.gnu.org/software/bash/)

Both are preinstalled on most systems, no other configuration is needed. During installation a few required packages will be set up automatically if not already installed:

  - [stow](https://www.gnu.org/software/stow/)
  - [gpg](https://gnupg.org/)
  - [coreutils](https://www.gnu.org/software/coreutils/)
  - [Visual Studio Code](https://code.visualstudio.com/) (this is used for the update mechanism of encrypted files using diff. Feel free to use a different editor)

## Installation

For initial installation run from your home directory:

```
git clone git@github.com:1nVitr0/.dotfiles.git
.dotfiles/install
```

The install script will interactively set up all the required configuration, decrypt the secure dotfiles and stow everything inside your home directory. Make sure you have access to all the repositories of the profiles you are using.

After installation all dotfiles that were already present in your `home` directory will be copied into the repository. After editing them to your liking you can commit and update the repository.

## Update / Import

To update, it should be enough to run the global update script. It will pull changes automatically:

```
.dotfiles/update
```

It is recommended not to touch the encrypted dotfiles before a pull, as changes in encrypted files are very hard to track using git. The update script will automatically ask for manual intervention on merge conflicts.

After adding changes, the upstream can be updated using the normal git `commit` / `push` workflow.

***

To import new dotfiles into the repository the `import` script can be used:

```shell
.dotfiles/import [FILES] [PACKAGE]
```

To import your ssh config for example, you can use:

```shell
./dotfiles/import .ssh/ ssh
```

## Profiles

During installation you will be prompted for profiles. Profiles are user-, machine- or organization specific dotfile packages. They have the same structure as the root repository but override the base dotfiles. T switch between profiles, the following command can be used: 

```
.dotfiles/switch
```

To create a new profile, you must first create an empty repository for your profile. It can be private or public, but you must have access from your command line. Then run the create script and follow the instructions:

```
./dotfiles/profiles/create [PROFILE_NAME]
```

## Secure dotfiles

The `secure` directory that resides in the root ans under all profile directories contains all the secure dotfiles. They are encrypted using a gpg key specified in the initial installation and only their encrypted version is committed into the repository. On installation or update they are decrypted, when change manually merged and then stowed into the `dotfile` directory. To import secure dotfiles use the default `import` syntax and provide an prepend the directory `secure`:

```shell
./dotfiles import secure/ssh
# Or for profiles
./dotfiles import profiles/arambecker/secure/ssh
```

## Config

Configuration which is set up during initial installation can always be reset by running `.dotfile/config clear`. Additionally, running the `config clear` command in either `dotfiles`, `migrate` or `secure` allows for resetting their configurations. `config clear [CONFIG?]` also accepts an optional second argument for the specific configuration parameter to clear, e.g. to reset the GPG key ids run:

```shell
./dotfiles/secure/config clear gpg_key_id
```

## How it works

This repository attempts to manage dotfiles through a combination of [stow](https://www.gnu.org/software/stow/) and [git](https://git-scm.com/). The root repository works the same way profiles do:

Dotfiles are grouped into packages that get symlinked to your home directory using `stow`. Dotfiles in the secure directory are encrypted using [gpg](https://gnupg.org/) and the encrypted versions are symlinked from `secure/[PACKAGE]` to the `dotfiles/[PACKAGE]`, which in turn is symlinked to your `home` directory.

Creating profiles uses the exact same structure (in fact the exact same scripts, they are symlinked as well). When a profile is switched all the dotfiles of the current profile (or the base dotfiles) are temporarily unlinked and the dotfiles from the profile are applied.

# Making it your home

To use this repository as a dotfile management system, you will most likely have to fork the existing version. feel free to delete or edit the `.gitmodules` file if you are not using any of the containing profiles.

You can then add your own profiles and keep them updated in your own repositories. You should only edit dotfiles in your profile directories to maintain peak updatability with the original version.

Updating to the upstream version can be done through the default git workflow:

```
git merge upstream/main
```