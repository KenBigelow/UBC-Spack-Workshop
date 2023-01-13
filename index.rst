.. _ubc-workshop-:

=========================================
Ronin / Spack Workshop
=========================================

In this blaj bfsfsd workshop we will guide you through the process of using spack
to build software stacks. First we will build our virtual machine
and install spack. Then a deep dive into spack focusing on the 
power of various specs syntax and the flexibility it gives
to users. We will cover the ``spack install`` and ``spack spec`` for 
installing, the ``spack find`` command for viewing installed packages 
and the ``spack uninstall`` for uninstalling packages. Next a 
section on how to manage compilers with Spack paying close attention 
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

The ``spack list`` command shows available packages.

.. code-block:: console

  $ spack command here

The ``spack list`` command can also take a query string. Spack
automatically adds wildcards to both ends of the string, or you
can add your own wildcards. For example, we can view all available
Python packages.

.. code-block:: console

  $ spack command here

-------------------
Installing Packages
-------------------

Installing a package with Spack is very simple. To install a piece of
software, simply type ``spack install <package_name>``.

.. code-block:: console

  $ spack command here
  
Spack can install software either from source or from a binary
cache. Packages in the binary cache are signed with GPG for
security. For the tutorial we have prepared a binary cache so you
don't have to wait on slow compilation from source. To be able to
install from the binary cache, we will need to configure Spack with
the location of the binary cache and trust the GPG key that the binary
cache was signed with.

.. code-block:: console

  $ spack command here
  
You'll learn more about configuring Spack later in the tutorial, but
for now you will be able to install the rest of the packages in the
tutorial from a binary cache using the same ``spack install``
command. By default this will install the binary cached version if it
exists and fall back on installing from source if it does not.

Spack's spec syntax is the interface by which we can request specific
configurations of the package. The ``%`` sigil is used to specify
compilers.

.. code-block:: console

  $ spack command here
  
Note that this installation is located separately from the previous
one. We will discuss this in more detail later, but this is part of what
allows Spack to support arbitrarily versioned software.

You can check for particular versions before requesting them. We will
use the ``spack versions`` command to see the available versions, and then
install a different version of ``zlib``.

.. code-block:: console

  $ spack command here

