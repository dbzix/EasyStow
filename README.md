# EasyStow  - dotfiles made simple!

**EasyStow** is nothing more than a set of custom `bash` aliases and functions that make work with your [dotfiles](https://en.wikipedia.org/wiki/Hidden_file_and_hidden_directory) pleasant.\
Uses [GNU Stow.](https://www.gnu.org/software/stow/manual/stow.html)
> **Note**: `GNU stow` operates on [packages](https://www.gnu.org/software/stow/manual/stow.html#Terminology) - minimal working units that represent collections of files and directories to back up.

## Installation

Install *GNU Stow*:
```bash
$ apt install stow
```
Load script into your current *shell session*:
```bash
source .bashrc_easystow
```
Done!\
Now you can use helpers defined in this script to manipulate your *[dotfiles](https://dotfiles.github.io/)* easily!

## Storage structure

**EasyStow** is assumed to be working with certain backup storage structure.\
By default it is:
```
$HOME/backup/dotfiles
             ├── dotconfig
             ├── homeroot
             ├── other
             └── secrets
                 ├── dotconfig
                 ├── homeroot
                 └── other
```
which is stored in `$HOME/backup/dotfiles` directory.

Here, `dotfiles` is a root of your stow storage.\
In `secrets` you backup configurations that include any sensitive data (passwords, auth keys, GPG keys, etc.)

Other than that, all configuration *packages* are split into **categories**.\
The script defines three *categories*:
 - `homeroot` - stores configurations that are from your `$HOME` directory
 - `dotfiles` - stores configurations that are from your `$HOME/.config` directory
 - `other` - stores all other configurations

## Implementation

Helper script defines some aliases and functions to manipulate your dotfiles backup.\
They are:
- aliases:
    > `stow-ls-structure` - to show storage structure\
    > `stow-ls-packages` - to list all stowed packages
- functions:
    > `stow-simulate` - to run stow packaging in simulation mode (`stow` option `-n`)\
    > `stow-do` - to stow packages\
    > `stow-force` - to stow packages in forced mode (`stow` option `--adopt`)\
    > `stow-unstow` - to un-stow packages (`stow` option `-D`)\
    > `stow-restow` - to re-stow packages (`stow` option `-R`)

Every custom *stow*-function **uses autocompletion** to help you manage your `stow` backup storage in a most productive way.

## Usage / Examples
 
How do you use **EasyStow**?

 1. To simulate what `stow` will do for you:
    ```
    stow-simulate secrets homeroot ssh
     ```
    >  This way you ensure what exactly will be done on your system after applying `stow` command.\
    > Here you ran simulation on your *secret* `ssh` package under `homeroot` category.

2. To stow *new piece* of configuration, or to apply stowed configuration on *new machine*:
    ```
    stow-do homeroot bash
    ```
    > In this example you stow your `bash` configuration package inside `homeroot` category in your *storage root*.

3. If you apply previous command and `stow` rejects it, this is probably because some program replaced symlink(s) with pure file(s).\
You may force `stow` to **rewrite** your stowed configuration with new content by calling:
    ```
    stow-force homeroot goldendict
    ```
    > You should use this command **with caution**, because old stowed content will be **replaced** with the new one.

4. To un-stow (*remove symlinks* to stowed package), you use:
    ```
    stow-unstow homeroot kazam
    ```
    > This might be helpful when you *uninstall* some software and you don't need unnecessary symlinks.

5. To re-stow (*remove* and then *re-create* symlinks):
     ```
     stow-restow dotfiles user-dirs
    ```
    >  The *same effect* as when calling `stow-unstow` and then `stow-do`.

## Customization
*Categories* are stored in **\_\_DF\_\*** variables defined inside `.bashrc_easystow`, as well as the *root storage path* and path to *secrets*.\
**These are configurable things!**

All you need to do for customizing is just:
- add/remove/change your categories
- update **\_\_DF\_CATEGORIES** variable with appropriate categories you've set
- change paths if needed

More information is in script itself - it is very self-describing thing.

Happy **EasyStow**-ing your data! :tada:

## Do you like EasyStow?
Please, donate:

| Cryptocurrency | Address |
| --- | --- |
| Bitcoin (BTC) | bc1qwf90w004z04v39emd3jj8q4ev4rdna739ecqj5 |
| Ethereum (ETH)| 0xED726ADA8d6A4f908de77f689D918039b03a698C |
| Ripple (XRP) |rH8CFA1QVaijiMBaL9FgbpTzu2rYsu3FgB |
| TON / USDT on TON | UQCVsW7ygTvQWmf8xRwMST7AdfDzNxwrw0CYkThEfhA5Xsk6 |
