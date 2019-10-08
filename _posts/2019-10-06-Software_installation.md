---
layout: post
title: Software installation
date: 2019-10-06 
tag: Software
---

Software installation is always the tough thing that impedes the progress of bioinformatics analysis. During my PhD studies, it almost occupied 1/5 of my time to make the software run properly on different servers. In this section, I mainly detail in how to install bioinformatics tools.

### Install software

There are hundreds of thousands of software that can help bioinformatics analyses. However, sometimes we cannot find the software to be used on a server, which means we need to install it.

#### Use Linuxbrew/Homebrew

Software installation is not a trivial task as most software needs dependencies. Due to different architectures of servers, we always fail in software installation using the source code. Linuxbrew/homebrew as an alternative makes software installation easier.

Browsing: [Linuxbrew](http://linuxbrew.sh) (for Linux systems) or [Homebrew](https://brew.sh) (for MacOS systems)

Install Linuxbrew/homebrew:


```bash
$ git clone https://github.com/Linuxbrew/brew.git ~/.linuxbrew (## you may change the installation path)
```

Set linuxbrew to "$PATH" in ``.bashrc``


```bash
PATH="$HOME/.linuxbrew/bin:$PATH"
export MANPATH="$(brew --prefix)/share/man:$MANPATH"
export INFOPATH="$(brew --prefix)/share/info:$INFOPATH"
```

Useful command lines in brew: 


```bash
$ brew search <software>
$ brew install/uninstall <software>
$ brew link/unlink <software>
$ brew cleanup (## clean the cache)
```

#### Use Bioconda

Bioconda provides a lot of software for bioinformatics analyses. Based on anaconda/miniconda, users can install the required tools using:


```bash
$ conda install/create -n <env name> <name of the tool>  ## such as conda install/create -n python27 python=2.7
$ source activate <env name>
$ conda install -c bioconda <name of the package>
```

Prior to installing the required package, users may browse 'https://bioconda.github.io/recipes.html' to check if the package is available on Bioconda (Note: this page is down and cannot be used).

Set your "conda" using:


```bash
$ conda config --add channels defaults
$ conda config --add channels conda-forge
$ conda config --add channels bioconda
```

* For Anaconda, users may download it from 'https://www.anaconda.com/download/'. 
* For Miniconda, users may download it from 'https://conda.io/miniconda.html'

**Note**: there are two different anaconda/miniconda versions

One is for ``python2`` and the other one is for ``python3``.

Currently, conda is waay slow in package installation. We may using [mamba](https://quantstack.net/mamba.html) to speed it up. Or using the latest conda version.

#### Use docker
 
Docker is another tool to help install tools (needs root permission). The guide for installation on different systems is at [here](https://docs.docker.com/install/). You may check and install it. 

**Note**: give a group permission to users


```bash
$ sudo usermod -aG docker $USER
```

Otherwise, other people may be not able to run docker on the system.


<br>
Copyright@[Andy's blog](http://yuxuanyuan.github.io) » [Software installation](http://yuxuanyuan.github.io/2019/10/Software_installation/)  


