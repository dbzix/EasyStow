# EasyStow  - dotfiles made simple!

**EasyStow** is a set of custom `bash` commands built over the [GNU Stow](https://www.gnu.org/software/stow/manual/stow.html) to make [dotfiles](https://dotfiles.github.io/) management pleasant.
> **Note**: `GNU stow` operates on [packages](https://www.gnu.org/software/stow/manual/stow.html#Terminology) - minimal working units that represent collections of files and directories to back up.

## Why?

When you manage dotfiles with pure `GNU Stow`, you have to retain multiple command line options in your head.

By default, `GNU Stow` implies that the [stow directory](https://www.gnu.org/software/stow/manual/stow.html#Terminology) is your current directory, and your [target directory](https://www.gnu.org/software/stow/manual/stow.html#Terminology) is just its parent. If this is your case, you don't have to specify additional options to execute the `stow` command - everything is fine by default.

But if your current directory isn't where you store your dotfiles, you must provide paths to your `stow directory` and to your `target directory` (depending on your current location). This does not seem as a convenient workflow, isn't it?

**So, here is the EasyStow**.

## How?

It's just the `bash` script that defines custom commands (implemented as simple `bash` aliases and functions) for manipulating `stow` backups.\
These commands are:
- aliases for expore your storage:
    > `stow-ls-structure` - to show the structure of your dotfiles\
    > `stow-ls-packages` - to list all stowed packages
- functions to manage your storage:
    > `stow-simulate` - to run stow packaging in simulation mode (`stow` option `-n`)\
    > `stow-do` - to stow packages\
    > `stow-force` - to stow packages in forced mode (`stow` option `--adopt`)\
    > `stow-unstow` - to un-stow packages (`stow` option `-D`)\
    > `stow-restow` - to re-stow packages (`stow` option `-R`)

Using this, you can manage your backups **from any place** in the system and **without providing** any additional arguments.\
Also, thanks to **autocompletion** implemented in the script, you have full control over your dotfiles.

## Dotfiles storage structure

**EasyStow** operates on `categories` and `packages`.

What you have inside your root dotfiles directory are `categories`, with one special category - `secrets` - which has its own `sub-categories`. The purpose of `secrets` category is to back up anything that contains sensitive data like *passwords*, *auth keys*, etc.

What's inside `categories` and `sub-categories` is treated as `packages.`

Example:
```
$HOME/backup/dotfiles (default location)
             ├── configs
             ├── shells
             ├── tools
             └── secrets
                 ├── encryption
                 └── remote
```
Here, you have `category` directory `shells` where you plan to store `packages` that represent miscellaneous shell-related config files.\
Then you might have `sub-directoriess` - `shells/bash` and `shells/zsh` - each storing a set of config files for respective shell.\
Here `bash` and `zsh` are `packages.`

## Installation

Install *GNU Stow*:
```bash
$ apt install stow
```
Clone this repository:
```bash
cd ~
git clone https://github.com/dbzix/EasyStow
```
Add the following to your shell configuration (e.g. `.bashrc`):
```bash
export DOTFILES_ROOT_DIR=$HOME/path/to/your/dotfiles
if [ -f $HOME/EasyStow/easystow ]; then
    . $HOME/EasyStow/easystow
fi
```
Done!\
Now you have full control over your *dotfiles*!

## Usage
 
The usage is:
```bash
stow-command [secrets] <category> <package> [<package> <package> ...] [-v]
```
for manipulating ***specific packages*** in selected *category*;\
or:
```bash
stow-command [secrets] <category> [-v]
```
for manipulating ***all packages*** in selected *category*.

Here, `stow-command` is one of: `stow-simulate`, `stow-do`, `stow-force`, `stow-unstow`, and `stow-restow`.

<details>
    <summary>The <tt>-v</tt> option is for additional verbosity.</summary>

  Compare this:
  ```
  $ stow-do homeroot git
  Processing packages in 'homeroot' category: git
  $
  ```

  and this:
  ```
  $ stow-do homeroot git -v
  Processing packages in 'homeroot' category: git
  stow dir is /home/username/backup/dotfiles/homeroot
  stow dir path relative to target /home/username is backup/dotfiles/homeroot
  Planning stow of package git...
  --- Skipping .gitignore as it already points to backup/dotfiles/homeroot/git/.gitignore
  --- Skipping .gitconfig as it already points to backup/dotfiles/homeroot/git/.gitconfig
  Planning stow of package git... done
  Processing tasks...
  $
  ```

  The `-v` option may help you understand what's happening in some cases.\
  You may add more `-v` flags. They will be passed to `stow` command, adding even more verbosity.
</details>

## Examples

<details>
  <summary>1. Simulate what `stow` will do for you.</summary>

  ```
  stow-simulate secrets homeroot ssh
  ```

  > This way you ensure what exactly will be done on your system after applying the `stow` command.\
  > Here you ran the `stow` simulation on your *secret* `ssh` package which is stowed under the `homeroot` category.
</details>

<details>
  <summary>2. Store <i>new piece</i> of configuration <b>/</b> Apply stowed configuration on <i>new machine</i>.</summary>

  ```
  stow-do homeroot bash
  ```

  > In this example you stow your `bash` configuration package inside the `homeroot` category under your *storage root*.
</details>

<details>
  <summary>3. Update stowed content.</summary>

  Some programs tend to replace symlink(s) with plain file(s).\
  In this case, when you apply the `stow-do` command, `stow` rejects it.
  If that is the case, you may force `stow` to **rewrite** your stowed configuration with new content and then re-create symlinks by executing:
  
  ```
  stow-force homeroot goldendict
  ```

  > You should use this command **with caution** because old stowed content will be **replaced** with the new one.\
  > You are safe here if you use `git` to track the changes you make in your backups.
</details>

<details>
  <summary>4. Un-stow (<i>remove symlinks</i> to stowed package).</summary>

  ```
  stow-unstow homeroot kazam
  ```

  > This might be helpful when you *uninstall* some software, and you don't need unnecessary symlinks in your system.
</details>

<details>
  <summary>5. Re-stow (<i>remove</i> and then <i>re-create</i> symlinks).</summary>

  ```
  stow-restow dotfiles user-dirs
  ```
  
  >  The *same effect* as when calling `stow-unstow` and then `stow-do`.
</details>

---

Happy **EasyStow**-ing your data! :tada:

## Do you like EasyStow?

Feel free to support my work:

| Cryptocurrency | Address |
| --- | --- |
| Bitcoin (BTC) | bc1qwf90w004z04v39emd3jj8q4ev4rdna739ecqj5 |
| Ethereum (ETH)| 0xED726ADA8d6A4f908de77f689D918039b03a698C |
| Ripple (XRP) |rH8CFA1QVaijiMBaL9FgbpTzu2rYsu3FgB |
| TON / USDT on TON | UQCVsW7ygTvQWmf8xRwMST7AdfDzNxwrw0CYkThEfhA5Xsk6 |
