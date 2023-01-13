# UBC-Spack-Workshop

This tutorial will guide you through the process of installing
software using Spack. We will first cover the ``spack install`` command,
focusing on the power of the spec syntax and the flexibility it gives
to users. We will also cover the ``spack find`` command for viewing
installed packages and the ``spack uninstall`` command for uninstalling
them. Finally, we will touch on how Spack manages compilers,
especially as it relates to using Spack-built compilers within Spack.
We will include full output from all of the commands demonstrated,
although we will frequently call attention to only small portions of
that output (or merely to the fact that it succeeded). The provided
output is all from an AWS instance running Ubuntu 18.04.

Installing Spack
Spack works out of the box. Simply clone Spack to get going. We will clone Spack and immediately check out the most recent release, v0.19.

$ git clone -c feature.manyFiles=true https://github.com/spack/spack.git ~/spack
Cloning into '/home/spack1/spack'...
remote: Enumerating objects: 403295, done.K
remote: Counting objects: 100% (235/235), done.K
remote: Compressing objects: 100% (147/147), done.K
remote:nTotale4032959(delta993),4reused,1817(deltaB60),0pack-reused 403060K
Receiving objects: 100% (403295/403295), 203.42 MiB | 39.28 MiB/s, done.
Resolving deltas: 100% (162372/162372), done.
$ cd ~/spack
$ git checkout releases/v0.19
Branch 'releases/v0.19' set up to track remote branch 'releases/v0.19' from 'origin'.
Switched to a new branch 'releases/v0.19'
Next, add Spack to your path. Spack has some nice command-line integration tools, so instead of simply appending to your PATH variable, source the Spack setup script.
