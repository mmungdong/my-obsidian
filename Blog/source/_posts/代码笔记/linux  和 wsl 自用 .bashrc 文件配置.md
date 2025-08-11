---
title: linux  和 wsl 自用 .bashrc 文件配置
categories:
  - 代码笔记
tags:
  - wsl
  - linux
archive: false
hide: false
date: 2025-08-09 20:22:44
---
```bash
# .bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
    . /etc/bashrc
fi

# Alias definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_aliases, instead of adding them here directly.
# See /usr/share/doc/bash-doc/examples in the bash-doc package.
if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

# Enable color support of ls and also add handy aliases
if [ -x /usr/bin/dircolors ]; then
    test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'
    alias ll='ls --color=auto -l'
    alias la='ls --color=auto -A'
    alias l='ls --color=auto -lA'
    alias dir='dir --color=auto'
    alias vdir='vdir --color=auto'

    alias grep='grep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias egrep='egrep --color=auto'
fi

# Colored GCC warnings and errors
export GCC_COLORS='error=01;31:warning=01;35:note=01;36:caret=01;32:locus=01:quote=01'

# Some aliases to prevent mistakes
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Create a default workspace directory to keep all work files in one place
if [ ! -d $HOME/workspace ]; then
    mkdir -p $HOME/workspace
fi

# User-specific environment settings
# Basic environment
# Set system language to en_US.UTF-8 to avoid Chinese character display issues in the terminal
export LANG="en_US.UTF-8"
# The default PS1 setting displays the full path, to prevent it from becoming too long,
# it now shows "username@dev last_directory_name"
export PS1='[\u@dev \W]\$ '
# Set the workspace directory
export WORKSPACE="$HOME/workspace"
# Add $HOME/bin directory to the PATH variable
export PATH=$HOME/bin:$PATH
# Set the default editor to vim
export EDITOR=vim

# When logging into the system, default to the Workspace directory
# cd $WORKSPACE

# User-specific aliases, configures and functions

# Go envs
# Go version setting
export GOVERSION=go1.22.2
# Go installation directory
export GO_INSTALL_DIR=$HOME/go
# GOROOT setting
export GOROOT=$GO_INSTALL_DIR/$GOVERSION/go
# GOPATH setting
export GOPATH=$WORKSPACE/golang #
# Add the binaries from both the Go language and those installed via go install to the PATH
export PATH=$GOROOT/bin:$GOPATH/bin:$PATH
# Enable Go modules feature
export GO111MODULE="on"
# Proxy server setting for installing Go modules
export GOPROXY=https://goproxy.cn,direct
export GOPRIVATE=
# Turn off checking the hash value of Go dependency packages
export GOSUMDB=off
# Alias and environments for onex quick access
export GOSRC="$WORKSPACE/golang/src"
# OneX project root directory, used in many places.
export ONEX_ROOT="$GOSRC/github.com/superproj/onex"
# Allows you to run latest compiled onex components like executing
# Linux commands, for example: onexctl.
export PATH=${ONEX_ROOT}/_output/platforms/linux/amd64:${ONEX_ROOT}/scripts:$PATH
export OVERSION=v1.0.0
# a very convenient alias used to enter superproj root directory.
alias sp="cd $GOSRC/github.com/superproj"
# a very convenient alias used to enter onex root directory.
alias o="cd $GOSRC/github.com/superproj/onex"

export GOSUMDB=sum.golang.org

# �VSCode  open setting
if [ -n "$WT_SESSION" ]; then
    cd "$(wslpath "$(powershell.exe -Command 'Get-Location')")"
fi
```