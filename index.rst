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

  Cloning into '/home/spack1/spack'...
  remote: Enumerating objects: 403295, done.K
  $ sudo apt update
  $sudo apt install build-essential ca-certificates coreutils curl environment-modules 
  gfortran git gpg lsb-release python3 python3-distutils python3-venv unzip zip

.. code-block:: console
    $ sudo apt update
    $sudo apt install build-essential ca-certificates coreutils curl environment-modules 
    gfortran git gpg lsb-release python3 python3-distutils python3-venv unzip zip
  
If you have already installed the above packages the output will varry. 
  
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

You're good to go!

-----------------
Inside Spack
-----------------


