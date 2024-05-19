# EasyStow  - dotfiles made simple!

**EasyStow** is a set of custom `bash` commands built over the [GNU Stow](https://www.gnu.org/software/stow/manual/stow.html) to make [dotfiles](https://en.wikipedia.org/wiki/Hidden_file_and_hidden_directory) management pleasant.
> **Note**: `GNU stow` operates on [packages](https://www.gnu.org/software/stow/manual/stow.html#Terminology) - minimal working units that represent collections of files and directories to back up.

## Why?

Some time ago, I started using *GNU Stow* to manage my *[dotfiles](https://dotfiles.github.io/)*. After I've played with it for some time, I realized that the workflow it proposes is just not too comfortable. Why is that?

By default, *GNU Stow* implies that the [stow directory](https://www.gnu.org/software/stow/manual/stow.html#Terminology) is your current directory, and your [target directory](https://www.gnu.org/software/stow/manual/stow.html#Terminology) is just its parent. In this case, you don't have to specify additional options to execute the `stow` command - everything is fine by default.

But if your storage structure differs a bit, then things become complicated. You have to specify paths to your *stow directory* and to your *target directory* (depending on your current location). And you need to have a mental map of your backup storage to specify all this stuff properly.

**So, at some moment things became too complicated for me**.\
I just wanted to manage my *stow* packages without specifying tons of additional information - by running simple commands that expect only package names to process. Also, it would be nice to have some autocompletions. You know, just in case.

And I decided to write a set of custom tools over *GNU Stow* to make my life easier.

## How?

I wrote the `bash` script that defines custom commands (implemented as simple `bash` aliases and functions) for manipulating *stow* backups.\
These commands are:
- aliases:
    > `stow-ls-structure` - to show the structure of backup storage\
    > `stow-ls-packages` - to list all stowed packages
- functions:
    > `stow-simulate` - to run stow packaging in simulation mode (`stow` option `-n`)\
    > `stow-do` - to stow packages\
    > `stow-force` - to stow packages in forced mode (`stow` option `--adopt`)\
    > `stow-unstow` - to un-stow packages (`stow` option `-D`)\
    > `stow-restow` - to re-stow packages (`stow` option `-R`)

From now on, I can manage my backups **from any place** in my system and **without providing** tons of additional arguments.\
Also, thanks to **autocompletion** implemented in the script, every custom *stow*-function helps me manage my backups conveniently.

## Backup storage structure

**EasyStow** is assumed to be working with a certain structure of backup storage.\
By default, it is:
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
Here, the `$HOME/backup/dotfiles` directory is the root of backup storage.\
In `secrets` are stored configuration backups that include sensitive data (passwords, auth keys, GPG keys, etc.)

Other than that, all configuration *packages* are split into **categories** (kind of labels).\
The script defines three default *categories*:
 - `homeroot` - for storing configurations from the `$HOME` directory
 - `dotconfig` - for storing configurations from the `$HOME/.config` directory
 - `other` - for storing configurations from other places (e.g. `$HOME/.local/`)

## Installation

Install *GNU Stow*:
```bash
$ apt install stow
```
Load script into your current *shell*:
```bash
source .bashrc_easystow
```
Done!\
Now you can use custom *stow*-commands to manipulate your *dotfiles* **easily**!

<details>
  <summary>Want to persist these commands for later use?</summary>
  
  1. Place the `.bashrc_easystow` file into your `$HOME` directory.
  2. Add the following lines at the end of your `.bashrc` file:
      ```
      # dotfiles (EasyStow)
      if [ -f $HOME/.bashrc_easystow ]; then
          . $HOME/.bashrc_easystow
      fi
      ```
</details>

## Usage
 
The usage is:
```bash
stow-command [secrets] <category> <package> [<package> <package> ...] [-v]
```
where `stow-command` is one of: `stow-simulate`, `stow-do`, `stow-force`, `stow-unstow`, and `stow-restow`.

<details>
    <summary>The <tt>-v</tt> option is for additional verbosity.</summary>

  Compare this:
  ```bash
  $ stow-do homeroot git
  $
  ```

  and this:
  ```bash
  $ stow-do homeroot git -v
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
  <summary>1. Simulate what <i>stow</i> will do for you.</summary>

  ```bash
  stow-simulate secrets homeroot ssh
  ```

  > This way you ensure what exactly will be done on your system after applying the `stow` command.\
  > Here you ran the `stow` simulation on your *secret* `ssh` package which is stowed under the `homeroot` category.
</details>

<details>
  <summary>2. Store <i>new piece</i> of configuration <b>/</b> Apply stowed configuration on <i>new machine</i>.</summary>

  ```bash
  stow-do homeroot bash
  ```

  > In this example you stow your `bash` configuration package inside the `homeroot` category under  your *storage root*.
</details>

<details>
  <summary>3. Update stowed content.</summary>

  Some programs tend to replace symlink(s) with plain file(s).\
  In this case, when you apply the `stow-do` command, `stow` rejects it.
  If that is the case, you may force `stow` to **rewrite** your stowed configuration with new content and then re-create symlinks by executing:
  
  ```bash
  stow-force homeroot goldendict
  ```

  > You should use this command **with caution** because old stowed content will be **replaced** with the new one.\
  > You are safe here if you use `git` to track the changes you make in your backups.
</details>

<details>
  <summary>4. Un-stow (<i>remove symlinks</i> to stowed package).</summary>

  ```bash
  stow-unstow homeroot kazam
  ```

  > This might be helpful when you *uninstall* some software, and you don't need unnecessary symlinks in your system.
</details>

<details>
  <summary>5. Re-stow (<i>remove</i> and then <i>re-create</i> symlinks).</summary>

  ```bash
  stow-restow dotfiles user-dirs
  ```
  
  >  The *same effect* as when calling `stow-unstow` and then `stow-do`.
</details>

## Customization

Storage configuration is defined in a set of **\_\_DF\_\*** variables that you can customize.\
You may easily adapt it for your storage and your workflow.

Happy **EasyStow**-ing your data! :tada:

## Do you like EasyStow?

Feel free to support my work:

| Cryptocurrency | Address |
| --- | --- |
| Bitcoin (BTC) | bc1qwf90w004z04v39emd3jj8q4ev4rdna739ecqj5 |
| Ethereum (ETH)| 0xED726ADA8d6A4f908de77f689D918039b03a698C |
| Ripple (XRP) |rH8CFA1QVaijiMBaL9FgbpTzu2rYsu3FgB |
| TON / USDT on TON | UQCVsW7ygTvQWmf8xRwMST7AdfDzNxwrw0CYkThEfhA5Xsk6 |
