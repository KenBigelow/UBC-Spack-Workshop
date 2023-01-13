.. _ubc-workshop-:

=========================================
Ronin / Spack Workshop
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

We will include full output from all of the commands demonstrated,
although we will frequently call attention to only small portions of
that output (or merely to the fact that it succeeded). The provided
output is all from an AWS instance running Ubuntu 18.04.

.. _basics-tutorial-install:

----------------
Installing Spack
----------------

```
    $ git clone -c feature.manyFiles=true https://github.com/spack/spack.git
```


Spack works out of the box. Simply clone Spack to get going. We will
clone Spack and immediately check out the most recent release, v0.19.

.. code-block:: console

  $ git clone -c feature.manyFiles=true https://github.com/spack/spack.git

Next, add Spack to your path. Spack has some nice command-line
integration tools, so instead of simply appending to your ``PATH``
variable, source the Spack setup script.

.. code-block:: console

  $ . share/spack/setup-env.sh

You're good to go!

-----------------
What is in Spack?
-----------------
