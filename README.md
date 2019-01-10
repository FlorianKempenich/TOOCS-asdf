[![Build Status](https://travis-ci.org/TOOCS/asdf.svg?branch=master)](https://travis-ci.org/TOOCS/asdf) [![Ansible Role](https://img.shields.io/ansible/role/36195.svg)](https://galaxy.ansible.com/TOOCS/asdf)


# TOOCS / Ansible Role: `TOOCS.asdf`

* Installs `asdf`
* For `zsh` users, sets up `~/.zlogin` for a ready-to-use experience
* Uses `asdf` to install the languages of your choice (limited support, see [Install languages](#install-languages))

> ### TOOCS?
> TOOCS - The Opinionated One-Click Setups are a set of tools / ansible roles designed to setup a system in one click. They are a simple, reliable, way to setup a given tool. You can use them as is, or, inspecting their code, as a tutorial to follow step by step.
>
> They are, as their name suggests, opinionated: while they guarantee to setup the given tool in one click, they do **not** guarantee consistency in _how_ they achieve it, new releases might introduce breaking changes.  
> Read the code and make sure you understand what's happening!

## Table of Contents
* [Requirements](#requirements)
* [Post Install - Shell Configuration](#post-install---shell-configuration)
  * [Zsh users](#zsh-users)
  * [Non Zsh users](#non-zsh-users)
* [Install Languages](#install-languages)
  * [Supported Languages](#supported-languages)
* [Role Variables](#role-variables)
* [Example Playbook](#example-playbook)

## Requirements
This role is only working on MacOSX & Ubuntu/Debian.

## Post install - Shell configuration
### Zsh users
When using this TOOCS on a user whose shell is `zsh`, the TOOCS will automatically configure the `~/.zlogin` file. No further action is required.

If you are not using a framework, such as `oh-my-zsh`, or if on starting your shell you get an error message like 'command not found: compinit', then add this line before the ones added by the TOOCS in your `~/.zlogin` file:
```
autoload -Uz compinit && compinit
```

### Non Zsh users
For non-`zsh` users, some manual setup is required. Depending on your OS and shell, run the following:

* Bash on Ubuntu (and other Linux distros)  
  ```
  echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.bashrc
  echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.bashrc
  ```
* Bash on macOS  
  ```
  echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.bash_profile
  echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.bash_profile
  ```

* Fish
  ```
  echo 'source ~/.asdf/asdf.fish' >> ~/.config/fish/config.fish
  mkdir -p ~/.config/fish/completions; and cp ~/.asdf/completions/asdf.fish ~/.config/fish/completions
  ```

Restart your shell so that PATH changes take effect. (Opening a new terminal tab will usually do it.)

Also if you're having issues with it not detecting the shims you've installed it's most-likely due to the sourcing of above asdf.bash or asdf.fish not being at the bottom of your ~/.bash_profile, ~/.zshrc, or ~/.config/fish/config.fish. It needs to be sourced after you've set your $PATH.


Source: [official 'asdf' setup](https://github.com/asdf-vm/asdf#setup)


## Install Languages
After installing `asdf`, this TOOCS can automatically install different versions of the languages of your choice. It will take care of installing the prerequisite dependencies for each language, and install the different versions, usually prefering to compile from source rather than downloading the pre-compiled binaries.

The variable `languages_to_install` is a list containing all the languages to install, along with the specific versions.
* ### To install a language, **add an entry to the list in `languages_to_install`**
* ### The entry **must have the form**:
  ```yaml
  - LANG:
      - x.x.x
      - y.y.y
  ```
  Where `LANG` is the language name, and `x.x.x / y.y.y` are version numbers.
* ### The list of `languages_to_install` is **ordered**:
  ```yaml
  languages_to_install:
    - erlang:
        - 21.1.14
        - 20.3.8.9

    - elixir:
        - 1.7.4
  ```
  In this example, both versions of `erlang` will be installed **before** `elixir` installed. This is convenient since `elixir` requires `erlang` to be installed.

* ### For each language, the list of _versions to install_ is also **ordered**.
  * The **first** version from the list will be set as global
  * For languages supporting multiple simultaneous `global`, all versions from the list, in order, will be set as global (ex: python)
  ```yaml
  languages_to_install:
    - erlang:
        - 21.1.14
        - 20.3.8.9

    - python: # Support for multiple `global`
        - 3.7.0
        - 2.6.6
        - 2.7.15
  ```
  In this example, after running the TOOCS, the shell will be configured as follow:
    * The `erl` command (`erlang`) will point to version `21.1.14`
    * The `python` command will point to version `3.7.0`
    * `python` supporting multiple `global`, the following commands will also be made available:
      * `python3.7` / `pip3.7`
      * `python2.7` / `pip2.7`
      * `python2.6` / `pip2.6`
      * `python3` / `pip3` => Latest `python3` version: `python3.7` / `pip3.7`
      * `python2` / `pip2` => Latest `python2` version: `python2.7` / `pip2.7`

  <!-- Versions `x.x.x` and `y.y.y` of `LANG` will be installed. -->

### Supported Languages
* **Python**
  * Support for multiple `global` versions
* **Erlang**
  * Compiled with documentation but no `jinterface`
* **Elixir**
  * **Requires `erlang`**
  * Explicitely compiled from source
  * /!\ Make sure the current version of `erlang` is supported by the version of `elixir` to install /!\
    * Ex: `elixir v1.6.6` is not compatible with `erlang 21`! To compile `elixir v1.6.6`, `erlang 20` must be used.


## Role Variables
* ### `asdf_version`
  * `asdf` version to install
  * **Default:** `v0.6.2`
* ### `skip_shell_setup`
  * If `True` will only install `asdf` without setting up the shell
  * **Default:** `False`
* ### `languages_to_install`
  * List of languages, and versions, to install using `asdf`. See [Install Languages](#install-languages)
  * **Default:** `[]`


## Example Playbook
```yaml
- hosts: sandbox
  tasks:
    - include_role:
        name: TOOCS.asdf

# OR

- hosts: sandbox
  tasks:
    - include_role:
        name: TOOCS.asdf
      vars:
        asdf_version: v0.6.1
        skip_shell_setup: True

# OR

- hosts: sandbox
  tasks:
    - include_role:
        name: TOOCS.asdf
      vars:
        languages_to_install:
          - python:
              - 3.7.0
              - 2.7.15
              - 2.6.6

          - erlang:
              - 20.3.8.9
              - 21.1.4

          - elixir:
              - 1.7.4
              - 1.6.6
```

## License
MIT

## Author Information
Follow me on Twitter: [@ThisIsFlorianK](https://twitter.com/ThisIsFlorianK)  
Find out more about my work: [Florian Kempenich - Personal Website](https://floriankempenich.com)

