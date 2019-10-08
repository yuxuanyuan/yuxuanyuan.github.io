---
layout: post
title: Software management
date: 2019-10-08 
tag: software
---

Software managment is not easy and always needs some attention. There are different ways to manage software. Here I mainly talk about environment `Modules`.

Environment _Module_ provides a convenient way to manage software. Usually, ``Modules`` is installed on servers.

To set your own/private ``modulepath``, you may use: 


```bash
$ export MODULEPATH=/path/to/your/modulefiles:$MODULEPATH 
or 
$ module use /path/to/your/modulefiles
```

However, sometimes you have no ``Modules`` installed on your server(s)/pc. To solve this, you may want to install ``modules package`` by yourself (here you may need a ``super-user permission``)

### Modules

For example, you can download ``Modules`` using:


```bash
$ wget http://downloads.sourceforge.net/project/modules/Modules/modules-4.2.4/modules-4.2.4.tar.gz
```

Decompress modules 


```bash
$ tar -xvzf modules-4.2.4.tar.gz
```

Go to the modules folder


```bash
$ cd modules-4.2.4
```

Install modules (you may use ``sudo`` to install) 


```bash
./configure 
make 
make install 
```

Set modulepath


```bash
$ vim /usr/local/Modules/init/modulerc (# modify "module use --append /path/to/your/modulefiles")
```

Set modules in your bashrc


```bash
case "$0" in
          -sh|sh|*/sh)  modules_shell=sh ;;
       -ksh|ksh|*/ksh)  modules_shell=ksh ;;
       -zsh|zsh|*/zsh)  modules_shell=zsh ;;
    -bash|bash|*/bash)  modules_shell=bash ;;
esac
module() { eval `/usr/bin/tclsh /usr/local/Modules/libexec/modulecmd.tcl $modules_shell $*`; }
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

source /usr/local/Modules/init/modulerc
source /usr/local/Modules/init/profile.sh
```

module template


```bash
#%Module######################################################################
#
# <software> modulefile
#
proc ModulesHelp { } {
    puts stderr "Sets up the paths you need to use <software> version xxx"
}
set sys     [uname sysname]
set version xxx
set tool    xxx
if { [is-loaded $tool] && ! [is-loaded $tool/$version] } {
    module unload $tool
}

if [ module-info mode load ] { 
  puts stderr "======================================================"
  puts stderr "you may write some suggestion to use the tool here"
  puts stderr "======================================================"
}

prepend-path PATH /path/to/your/software/bin/
```

Some important tips for setting a module file

1. For ``lib`` and/or ``include`` in an installation: you may set


```bash
prepend-path LD_LIBRARY_PATH / PERL5LIB / R_LIBS/ PYTHONPATH / C_LIBRARY_PATH /CPLUS_LIBRARY_PATH
```

2. For ``man`` (check it in share folder) in an installation: you may set


```bash
prepend-path MANPATH
```

3. For ``info`` (check it in share folder) in an installation: you may set 


```bash
prepend-path INFOPATH
```

### Lmod

Alternatively, you may use ``lmod`` to set your ``modules``. 

``lmod`` can be download at: https://sourceforge.net/projects/lmod/files/


```bash
$ wget https://sourceforge.net/projects/lmod/files/Lmod-8.1.tar.bz2
$ tar xjf Lmod-8.1.tar.bz2
```

The installation of ``lmod`` is detailed at: https://lmod.readthedocs.io/en/latest/

Before installing ``lmod``, you need to install ``lua`` (may need root permission).

On linux, you may follow the command lines below to install ``lua``:


```bash
$ wget https://sourceforge.net/projects/lmod/files/lua-5.1.4.9.tar.bz2
$ tar xjf lua-5.1.4.9.tar.bz2
$ cd lua-5.1.4.9
$ ./configure --prefix=/opt/apps/lua/5.1.4.9 (# the path can be changed if you have no root permission)
$ make; make install
$ cd /opt/apps/lua; ln -s 5.1.4.9 lua
$ mkdir /usr/local/bin; ln -s /opt/apps/lua/lua/bin/lua /usr/local/bin
```

You may need to softlink all files in ``/opt/apps/lua/lua/include`` to ``/usr/local/include/lua/5.1``

On MacOS, you may need to install ``lua`` using ``brew``

After ``lua`` installation, you may need to install ``luarocks``.

**note**: use the source code to install luarocks


```bash
$ wget https://luarocks.org/releases/luarocks-3.1.3.tar.gz
$ tar zxpf luarocks-3.1.3.tar.gz
$ cd luarocks-3.1.3
$ ./configure; sudo make; sudo make install (# if use make bootstrap, you will have a problem in installing luaposix)
```

Now you may need to modify the config file using ``sudo vim /usr/local/etc/luarocks/config-5.1.lua``


```bash
-- LuaRocks configuration

rocks_trees = {
   { name = "user", root = home .. "/.luarocks" };
   { name = "system", root = "/usr/local" };
}

lua_interpreter = "lua";
variables = {
   LUA_DIR = "/usr/local";
   LUA_BINDIR = "/usr/local/bin";
   LUA_INCDIR = "/opt/apps/lua/lua/include";
   LUA_LIBDIR = "/opt/apps/lua/lua/lib";
}
```

Then you can install ``luarocks`` using:


```bash
$ sudo luarocks install luasocket
```

You may need to give permission to users using lua, luarocks and the corresponding libraries. Check those files:


```bash
/usr/bin/lua
/usr/share/luaxxx
/usr/include/luaxxx (#you may softlink lua.h into the include folder)
/usr/local/bin/luarocks
/usr/local/share/lua
/usr/local/lib/lua
/usr/local/lib/luarocks
```

Once the installation is completed, you need to install ``luaposix`` and ``luafilesystem``;


```bash
$ sudo luarocks install luaposix
$ sudo luarocks install luafilesystem
```

And then give permission to (chmod 755)


```bash
/usr/local/share/lua/5.3/posix
/usr/local/lib/lua/5.3/posix
```

Now you can install ``lmod``


```bash
$ sudo ./configure --prefix=/opt/apps
$ sudo make install
```

Create a file like:


```bash
$ sudo vim /opt/apps/lmod/lmod/init/.modulespath
```

In the file you need give the modulespath


```bash
/home/yyuan/modulefiles
```

Give permission to 


```bash
$ cd /opt/apps/
$ sudo chmod -R 755 lmod
```

In your ``.bashrc``, add the command line:


```bash
$ source /opt/apps/lmod/lmod/init/profile
```

Now you can use ``lmod``

The format of the lmod is different from ``Modules``. Usually, it ends up with ``.lua``.  Here is an example:


```bash
help("Sets up the paths you need to use <software> version <version_info>")
local version = '<version_info>'
local tool = '<software>'
whatis( [[<description of the software>]] )
whatis( [[Compiled with gcc/xxxx]] )
conflict("xxxxx")

if (mode() == "load") then
  if (isloaded("<dependency>") ~= true) then
    load("<dependency/version>")
  end
end
if (mode() ~= "whatis") then
prepend_path("PATH","/path/to/your/tool")
end
```

The settings of ``Modules`` can be used by ``lmod``.

If you use ``linuxbrew`` to install ``luaposix``, please ensure the settings of ``luaposix`` is in your path. An example to fix the loading problem is: 


```bash
$ export LUA_PATH "/home/yyuan/Tools/luarocks-3.1.3/lua_modules/share/lua/5.3/?.lua;/home/yyuan/Tools/luarocks-3.1.3/lua_modules/share/lua/5.3/?/init.lua;;"
$ export LUA_CPATH "/home/yyuan/Tools/luarocks-3.1.3/lua_modules/lib/lua/5.3/?.so;/home/yyuan/Tools/luarocks-3.1.3/lua_modules/lib/lua/5.3/?/?.so;;" 
```

If you have no root permission, please modify the cmds above accordingly. If you have any problem during the installation, you can contact me.


<br>
Copyright@[Andy's blog](http://yuxuanyuan.github.io) » [Software management](http://yuxuanyuan.github.io/2019/10/Software_management/)  


