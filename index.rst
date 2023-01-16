.. _ubc-workshop-:

=========================================
RONIN / Spack Workshop
=========================================

In this workshop we will guide you through the process of using spack
to build software stacks. First we will build our virtual machine
and install spack. Then a deep dive into spack focusing on the 
power of various specs syntax and the flexibility it gives
to users. We will cover the ``spack spec``/``spack install`` for 
installing, the ``spack find``/``spack list`` command for viewing 
installed packages and the ``spack uninstall`` for uninstalling packages. 
Next a section on how to manage compilers with Spack paying close attention 
while using Spack-built compilers within Spack. Then we will cover 
custom build scripts for managing complex software stacks as well lessons
learned using spack. Finally we will take everything we learned using spack
and the package singularity / apptainer to create a singularity container
and run this container using slurm inside of the autoscale cluster then
visualize the results using the RONIN Link application. 

We will include a few output from the commands demonstrated, to save time
we will frequently call attention to only small portions of
that output.

The provided output is all from an AWS instance running Ubuntu 18.04.

----------------
About Spack & Credits 
----------------

Spack is a package management tool designed to support multiple versions and configurations 
of software on a wide variety of platforms and environments. It was designed for large 
supercomputing centers, where many users and application teams share common installations 
of software on clusters with exotic architectures, using libraries that do not have a 
standard ABI. Spack is non-destructive: installing a new version does not break existing 
installations, so many configurations can coexist on the same system.

Most importantly, Spack is simple. It offers a simple spec syntax so that users can specify 
versions and configuration options concisely. Spack is also simple for package 
authors: package files are written in pure Python, and specs allow package authors to maintain 
a single file for many different builds of the same package.

A big thanks to the Spack team for a great Spack tutorial. 

Full citation: Todd Gamblin, Gregory Becker, Massimiliano Culpo, Tamara Dahlgren, Adam J. 
Stewart, and Harmen Stoppels. Managing HPC Software Complexity with Spack. 
Supercomputing 2022 (SC’22). Dallas, TX, November 13, 2022.

https://spack-tutorial.readthedocs.io/en/latest/

.. _basics-tutorial-install:

----------------
Installing Spack
----------------

Spack works out of the box. Simply clone Spack to get going. We will
clone Spack and immediately check out the most recent release, v0.20.

But first...
Lets make sure have the needed ubuntu packages for the VM. 

.. code-block:: console

  $ sudo apt update
  $ sudo apt install build-essential ca-certificates coreutils curl environment-modules gfortran git gpg lsb-release python3 python3-distutils python3-venv unzip zip
  $ sudo apt install awscli
  
If you have already installed the above packages the output will varry. 

Now lets install spack.
  
.. code-block:: console

  $ git clone -c feature.manyFiles=true https://github.com/spack/spack.git
  
.. code-block:: console

  Cloning into '/home/spack1/spack'...
  remote: Enumerating objects: 403295, done.K
  remote: Counting objects: 100% (235/235), done.K
  remote: Compressing objects: 100% (147/147), done.K
  remote:nTotale4032959(delta993),4reused,1817(deltaB60),0pack-reused 403060K
  Receiving objects: 100% (403295/403295), 203.42 MiB | 39.28 MiB/s, done.
  Resolving deltas: 100% (162372/162372), done.

Next, add Spack to your path. Spack has some nice command-line
integration tools, so instead of simply appending to your ``PATH``
variable, source the Spack setup script.

.. code-block:: console

  $ . share/spack/setup-env.sh

Ready to go!

-----------------
Inside Spack
-----------------

The ``spack`` command will prompt a feature rich list of common spack commands. 

.. code-block:: console

  $ spack

.. code-block:: console

  A flexible package manager that supports multiple versions,
  configurations, platforms, and compilers.
  
  These are common spack commands:
  
  query packages:
  list                  list and search available packages
  info                  get detailed information on a particular package
  find                  list and search installed packages
  
  build packages:
  install               build and install packages
  uninstall             remove installed packages
  gc                    remove specs that are now no longer needed
  spec                  show what would be installed, given a spec
  
  configuration:
  external              manage external packages in Spack configuration
  
  environments:
  env                   manage virtual environments
  view                  project packages to a compact naming scheme on the filesystem.
  
  create packages:
  create                create a new package file
  edit                  open package files in $EDITOR
  
  system:
  arch                  print architecture information about this machine
  audit                 audit configuration files, packages, etc.
  compilers             list available compilers
  
  user environment:
  load                  add package to the user environment
  module                generate/manage module files
  unload                remove package from the user environment
  
  optional arguments:
  --color {always,never,auto}
                        when to colorize output (default: auto)
  -V, --version         show version number and exit
  -h, --help            show this help message and exit
  -k, --insecure        do not check ssl certificates when downloading
  
  more help:
  spack help --all       list all commands and options
  spack help <command>   help on a specific command
  spack help --spec      help on the package specification syntax
  spack docs             open https://spack.rtfd.io/ in a browser

-----------------
Spack Common Commands
-----------------

The ``spack list`` command shows available packages to install.

.. code-block:: console

  $ spack list --help

Some example query strings for fun.

.. code-block:: console

  $ spack list 'py-*'
  $ spack list 'py-python*'
  $ spack list '*lib'
  $ spack list 'mpi'
  
The ``spack versions`` command list available versions of a package.

.. code-block:: console

  $ spack versions --help
  $ spack versions tcl
  
The ``spack find`` command shows installed packages / version / compiller used.

.. code-block:: console

  $ spack find --help
  $ spack find 
  
The ``spack spec`` command shows what would be installed, given a spec.

.. code-block:: console

  $ spack spec --help
  $ spack spec -I tcl

The ``spack install`` command will build and install packages.

.. code-block:: console

  $ spack install --help
  $ spack install tcl
  
The ``spack uninstall`` command will remove installed packages.

.. code-block:: console

  $ spack uninstall --help
  $ spack uninstall tcl
  
-----------------
Spack Install / Uninstall / Build Caches
-----------------

Lets start with a simple package install of tcl ``spack install``.

.. code-block:: console

  $ spack spec -I  tcl
  
.. code-block:: console

  $ spack spec -I  tcl
  Input spec
  --------------------------------
  -   tcl
  
  Concretized
  --------------------------------
  -   tcl@8.6.12%gcc@7.5.0 build_system=autotools arch=linux-ubuntu18.04-skylake_avx512
  [+]      ^zlib@1.2.13%gcc@7.5.0+optimize+pic+shared build_system=makefile arch=linux-ubuntu18.04-skylake_avx512

You will see the packages needed as well the package requested / version / compiler version. 

lets go ahead and install tcl.

.. code-block:: console

  $ spack install tcl

Now lets start to add custom search strings and flags to our install specifications ``spec``. 
Always use the ``spack spec -I`` command to spec out the install before you do the final install.

first lets get some info the nmap package.

.. code-block:: console

  $ spack info htop
 
In one command you get the description,homepage,versions,variant flags, dependencies and more.

Lets spec out version 3.2.0, disable hwloc and enable debug

.. code-block:: console

  $ spack spec -I htop@3.2.0
  $ spack spec -I htop@3.2.0 ~hwloc 
  $ spack spec -I htop@3.2.0 ~hwloc +debug


Lets go ahead and insall htop now. 

.. code-block:: console

  $ spack install htop@3.2.0 ~hwloc +debug

Loading up installed modules 

.. code-block:: console

  $ which htop
  /usr/bin/htop
  $ htop --version
  htop 2.1.0 - (C) 2004-2018 Hisham Muhammad
  Released under the GNU GPL.
  
  $ spack load htop
  $ which htop
  /home/ubuntu/spack/opt/spack/linux-ubuntu18.04-skylake_avx512/gcc-7.5.0/htop-3.2.0-zoznzvyv5ilhshf3at4gqnkhajzgdev7/bin/htop
  $ htop --version
  htop 3.2.0
  
  

  

