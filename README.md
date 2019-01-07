[![Build Status](https://travis-ci.org/TOOCS/asdf.svg?branch=master)](https://travis-ci.org/TOOCS/asdf) [![Ansible Role](https://img.shields.io/ansible/role/36149.svg)](https://galaxy.ansible.com/FlorianKempenich/toocs_asdf)


# TOOCS / Ansible Role: `toocs_asdf`

* Installs `asdf`
* For `zsh` users, sets up `~/.zlogin` for a ready-to-use experience

> ### TOOCS?
> TOOCS - The Opinionated One-Click Setups are a set of tools / ansible roles designed to setup a system in one click. They are a simple, reliable, way to setup a given tool. You can use them as is, or, inspecting their code, as a tutorial to follow step by step.
>
> They are, as their name suggests, opinionated: while they guarantee to setup the given tool in one click, they do **not** guarantee consistency in _how_ they achieve it, new releases might introduce breaking changes.  
> Read the code and make sure you understand what's happening!

## Requirements
This role is only working on MacOSX & Ubuntu/Debian.

## Post install
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

## Role Variables
* ### `asdf_version`
  * `asdf` version to install
  * **Default:** v0.6.2

## Example Playbook
```
- hosts: sandbox
  tasks:
    - include_role:
        name: FlorianKempenich.toocs_asdf
      vars:
        python_versions:
          - 3.7.0
          - 2.7.15

# OR

- hosts: sandbox
  tasks:
    - include_role:
        name: FlorianKempenich.toocs_asdf
      vars:
        asdf_version: v0.6.1
```

## License
MIT

## Author Information
Follow me on Twitter: [@ThisIsFlorianK](https://twitter.com/ThisIsFlorianK)  
Find out more about my work: [Florian Kempenich - Personal Website](https://floriankempenich.com)

