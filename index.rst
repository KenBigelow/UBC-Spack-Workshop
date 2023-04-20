.. _ubc-workshop-:

Resources
---------
* **UBC myVPN**
* **UBC ARC RONIN Login:** 
* **Additional Information:**
* **RONIN Application Form:** 
* **RONIN Blog:** 
* **RONIN Link:**
* **RONIN/Spack Workshop:**
* **UBC ARC Support:**


=========================================
RONIN Auto Scale Cluster / Spack Workshop
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

A big thanks to the Spack team for a great Spack tutorial for references. 

Full citation: Todd Gamblin, Gregory Becker, Massimiliano Culpo, Tamara Dahlgren, Adam J. 
Stewart, and Harmen Stoppels. Managing HPC Software Complexity with Spack. 
Supercomputing 2022 (SC’22). Dallas, TX, November 13, 2022.

https://spack-tutorial.readthedocs.io/en/latest/

----------------
Installing Spack
----------------

Spack works out of the box. Simply clone Spack to get going. We will
clone Spack and immediately check out the most recent release, v0.20.

But first...
Lets make sure have the needed Ubuntu packages for the VM. 

.. code-block:: console

  $ sudo apt update
  $ sudo apt upgrade
  $ sudo apt install build-essential ca-certificates coreutils curl environment-modules gfortran git gpg lsb-release python3 python3-distutils python3-venv unzip zip
  $ sudo apt install awscli
  
If you have already installed the above packages the output will vary. 

Now let's install spack.
  
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

  $ . spack/share/spack/setup-env.sh

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

  $ spack list
  $ spack list 'py-*'
  $ spack list 'py-python*'
  $ spack list '*lib'
  $ spack list 'mpi'
  
The ``spack versions`` command list available versions of a package.

.. code-block:: console

  $ spack versions --help
  $ spack versions tcl
  
The ``spack find`` command shows installed packages / version / compiler used.

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

first lets get some info the htop package.

.. code-block:: console

  $ spack info htop
 
In one command you get the description,homepage,versions,variant flags, dependencies and more.

Lets spec out version 3.2.0, disable hwloc and enable debug

.. code-block:: console

  $ spack spec -I htop@3.2.0
  $ spack spec -I htop@3.2.0 ~hwloc 
  $ spack spec -I htop@3.2.0 ~hwloc +debug


Lets go ahead and install htop now. 

.. code-block:: console

  $ spack install htop@3.2.0 ~hwloc +debug
  
To uninstall a spack package. 

.. code-block:: console

  $ spack uninstall libtool@2.4.7

Notice how it fails due to dependencies with packages. 

.. code-block:: console

  ==> Will not uninstall libtool@2.4.7%gcc@7.5.0/mvje3k2
  The following packages depend on it:
    -- linux-ubuntu18.04-haswell / gcc@7.5.0 ------------------------
    ha6adqe htop@3.2.0
  ==> Error: There are still dependents.
    use `spack uninstall --dependents` to remove dependents too

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

-----------------
Spack Build Caches 
-----------------

The use of a ``binary cache`` can result in software installs up to 20x faster 
for common Spack package installs. This tutorial will explain through the process 
of setting up a source mirror with a binary cache mirrors. Binary caches allow one 
to install pre-compiled binaries to your spack installation path.

Using the binary cache

.. code-block:: console

  $ spack mirror add binary_mirror https://binaries.spack.io/develop
  $ spack buildcache keys --install --trust
  
  ==> Fetching https://binaries.spack.io/develop/build_cache/_pgp/2C8DD3224EF3573A42BD221FA8E0CA3C1C2ADA2F.pub
  gpg: key A8E0CA3C1C2ADA2F: 7 signatures not checked due to missing keys
  gpg: key A8E0CA3C1C2ADA2F: public key "Spack Project Official Binaries <maintainers@spack.io>" imported
  gpg: Total number processed: 1
  gpg:               imported: 1
  gpg: no ultimately trusted keys found
  gpg: inserting ownertrust of 6
  
  $ spack mirror list

Now lets take a look inside the buidcache 

.. code-block:: console

  $ spack buildcache list --allarch

This is a very new addition to Spack. The options are limited
and so filtering to specific arch is not yet functional. 

Build caches are hit and miss depending on spack versions and installed packaged. 
For example lammps is not listed in the buildcache mirror list. So most of the install
will still take some time.

Some example commands to try. 

.. code-block:: console

  $ spack spec -I intel-mpi
  $ spack install --cache-only intel-mpi

.. code-block:: console

  $ ==> Installing intel-mpi-2019.10.317-3d3xzc5ibrsjtqvgsv7ewvhdf5uw3ffj
    ==> intel-mpi exists in binary cache but with different hash
    ==> Error: No binary for intel-mpi-2019.10.317-3d3xzc5ibrsjtqvgsv7ewvhdf5uw3ffj found when cache-only specified
    ==> Error: Failed to install intel-mpi due to SystemExit: 1
  
Now lets try to install a package that is listed.

.. code-block:: console

  $ spack buildcache list --allarch | grep intel
  $ spack spec -I intel-tbb
  $ spack install --cache-only intel-tbb

.. code-block:: console

  $ ==> Installing intel-tbb-2020.3-rbexoowaqll5pqen452ef2wqho6jlz36
  ==> Fetching https://binaries.spack.io/develop/build_cache/linux-ubuntu18.04-x86_64-gcc-7.5.0-intel-tbb-2020.3
  rbexoowaqll5pqen452ef2wqho6jlz36.spec.json.sig
  gpg: Signature made Thu Sep  8 19:58:45 2022 UTC
  gpg:                using RSA key D2C7EB3F2B05FA86590D293C04001B2E3DB0C723
  gpg: Good signature from "Spack Project Official Binaries <maintainers@spack.io>" [ultimate]
  ==> Fetching https://binaries.spack.io/develop/build_cache/linux-ubuntu18.04-x86_64/gcc-7.5.0/intel-tbb-2020.3/linux-ubuntu18.04-x86_64-gcc-7.5.0-intel
  tbb-2020.3-rbexoowaqll5pqen452ef2wqho6jlz36.spack
  ==> Extracting intel-tbb-2020.3-rbexoowaqll5pqen452ef2wqho6jlz36 from binary cache
  ==> intel-tbb: Successfully installed intel-tbb-2020.3-rbexoowaqll5pqen452ef2wqho6jlz36
  Search: 0.00s.  Fetch: 1.11s.  Install: 0.53s.  Total: 1.64s
  [+] /home/ubuntu/spack/opt/spack/linux-ubuntu18.04-x86_64/gcc-7.5.0/intel-tbb-2020.3-rbexoowaqll5pqen452ef2wqho6jlz36
  
To remove the binary cache from your spack environment. 

.. code-block:: console

  $ spack mirror list
  $ spack mirror remove binary_mirror
  $ spack clean
  $ spack clean -b

-----------------
Spack Compilers
-----------------

Spack can install and manage a list of available compilers on the system, detected 
automatically from the user’s ``PATH`` variable. The ``spack compilers`` command 
is an alias for the command ``spack compiler list``.

.. code-block:: console

  $ spack compilers
  
.. code-block:: console

  ==> Available compilers
  -- gcc ubuntu18.04-x86_64 ---------------------------------------
  gcc@7.5.0
  
Let's install a new compiler 

.. code-block:: console

  $ spack install --cache-only gcc@8.4.0
  
.. code-block:: console

  ==> gcc: Successfully installed gcc-8.4.0-kf55dvoi3iuagjkvomjti2lemura7b42
    Stage: 8.83s.  Autoreconf: 0.00s.  Configure: 2.33s.  Build: 1h 26m 41.56s.  Install: 32.20s.  Total: 1h 27m 25.21s
  [+] /home/ubuntu/spack/opt/spack/linux-ubuntu18.04-skylake_avx512/gcc-7.5.0/gcc-8.4.0-kf55dvoi3iuagjkvomjti2lemura7b42

Now let's add the new compiler to our list of available compilers. Using the 
``spack compiler add`` command. This will allow future packages to build 
with gcc@8.4.0 if selected.

.. code-block:: console

  $ spack find -p gcc
  $ spack compiler add $(spack location -i gcc@8.4.0)
  $ spack compilers

.. code-block:: console

  -- linux-ubuntu18.04-skylake_avx512 / gcc@7.5.0 -----------------
  gcc@8.4.0  /home/ubuntu/spack/opt/spack/linux-ubuntu18.04-skylake_avx512/gcc-7.5.0/gcc-8.4.0-kf55dvoi3iuagjkvomjti2lemura7b42
  ==> 1 installed package
  
  ==> Added 1 new compiler to /home/ubuntu/.spack/linux/compilers.yaml
    gcc@8.4.0
  ==> Compilers are defined in the following files:
    /home/ubuntu/.spack/linux/compilers.yaml
    
  ==> Available compiler
  -- gcc ubuntu18.04-x86_64 ---------------------------------------
  gcc@8.4.0  gcc@7.5.0  
  
Let's use the new version of gcc/8.4.0 and install a few packages. 

.. code-block:: console

  $ spack load gcc@8.4.0
  $ spack find --loaded
  $ spack spec -I bzip2
  $ spack spec -I bzip2%gcc@8.4.0
  $ spack install bzip2%gcc@8.4.0
  $ spack find

The end result should result in packages both installed using ``gcc@7.5.0`` 
and ``gcc@8.4.0``.

Installing gcc/8.4.0 did take 1h 27m total as you can see above. I did not use a build
cache. Let's use a build cache and see how long it takes. 

.. code-block:: console

  $ spack unload gcc@8.4.0
  $ spack buildcache list --allarch | grep gcc
  $ spack install --cache-only gcc@8.4.0
  $ spack find
  
.. code-block:: console

  ==> gcc: Successfully installed gcc-8.4.0-tf5qxoqsrla6jzuno5wdcwsn6saeiy2f
  Search: 0.00s.  Fetch: 12.08s.  Install: 11.64s.  Total: 23.72s
  [+] /home/ubuntu/spack/opt/spack/linux-ubuntu18.04-x86_64/gcc-7.5.0/gcc-8.4.0-tf5qxoqsrla6jzuno5wdcwsn6saeiy2f
  
  -- linux-ubuntu18.04-skylake_avx512 / gcc@7.5.0 -----------------
  -- linux-ubuntu18.04-skylake_avx512 / gcc@8.4.0 -----------------
  -- linux-ubuntu18.04-x86_64 / gcc@7.5.0 -------------------------
  
Notice the difference with the installed packaged / compiler version vs non cache.  

-----------------
Building Apptainer Containers
-----------------

.. code-block:: console

  $ cd apptainer
  $ . spack/share/spack/setup-env.sh 
  $ nano spack.yaml
  
.. code-block:: console
  
  spack:
   specs:
    - dcm2niix
   container:
    format: singularity
  
.. code-block:: console

  $ spack load apptainer
  $ spack containerize > spack-user-dcm2niix.def
  $ apptainer build spack-user-dcm2niix.sif spack-user-dcm2niix.def
  
-----------------
Using Apptainer Containers
-----------------

.. code-block:: console

  $ apptainer exec spack-user-dcm2niix.sif dcm2niix -h

-----------------
RONIN Autoscale Cluster
-----------------

First let us configure the object storage on our cluster to grab the singularity images we created. 

.. code-block:: console

  $ sudo apt update
  $ sudo apt install build-essential ca-certificates coreutils curl environment-modules gfortran git gpg lsb-release python3 python3-distutils python3-venv unzip zip
  $ sudo apt install awscli

.. code-block:: console
  
  $ more bucket-keys
  $ aws configure

Below is the example output for the information needed to connect to the S3 Bucket. 

.. code-block:: console

  AWS Access Key ID 
  AWS Secret Access Key
  Default region name ca-central-1
  Default output format JSON
  
.. code-block:: console

  $ aws s3 ls s3://ubc-hpc.cloud
  
Now let's copy everything we will need for our cluster. 

.. code-block:: console

  $ cd /apps
  $ aws s3 cp s3://ubc-hpc.cloud .
  $ tar -zxf apps-fds-bins.tgz
  $ cd /shared
  $ aws s3 cp s3://ubc-hpc.cloud/fds-smv-shared.tgz .
  $ tar -zxf fds-smv-shared.tgz
  $ cd fds-smv/
  $ nano fds-smv.sbatch
  
-----------------
Using Slurm
-----------------

Most used Slurm commands

.. code-block:: console

  $ sbatch
  $ squeue
  $ scancel

Example Slurm Script

.. code-block:: console

  #!/bin/bash
  #SBATCH --job-name=fds-smv-job
  #SBATCH --nodes=2
  ##SBATCH --ntasks=2
  ##SBATCH --cpus-per-task=1
  ##SBATCH --ntasks-per-node=2
  #SBATCH --output=%x_%j.out

  source /apps/FDS/bin/FDS6VARS.sh
  source /apps/FDS/bin/SMV6VARS.sh

  module load intelmpi

  export OMP_NUM_THREADS=1
  export I_MPI_PIN_DOMAIN=omp

  cd /shared/fds-smv/results

  srun -N 2 -n 2 --ntasks-per-node 2 fds /shared/fds-smv/Fires/fire_whirl_pool.fds

Now let's run the job. 

.. code-block:: console

  $ sbatch fds-smv.sbatch 
  $ squeue
  
If everything is working you should see the following. 

.. code-block:: console

  $ squeue
    JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
    2   compute fds-smv-   ubuntu PD       0:00      2 (Nodes required for job are DOWN, DRAINED or reserved for jobs in higher priority partitions)
    
Once the job gets underway you should see the following. 

.. code-block:: console

  $ squeue
    JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
    2   compute fds-smv-   ubuntu  R       6:07      2 ip-10-255-2-[21,41]

-----------------
Visualize Results
-----------------

Now that our job is finished lets have a look! 

Using RONIN LINK open the Linux Desktop, then a xterm shell and run the following. 

.. code-block:: console

  $ source /apps/FDS/bin/FDS6VARS.sh
  $ source /apps/FDS/bin/SMV6VARS.sh
  $ smokeview /shared/fds-smv/results/fire_whirl_pool.smv


