# How to install Root Cern with Pythia 6 and Pythia 8

- This process is based on Linux

## Install Root Cern

### Prerequisites:
- To run root without error

~~~
foo@bar:~$ sudo apt-get install build-essential git subversion dpkg-dev make g++ gcc binutils libx11-dev libxpm-dev libxft-dev libxext-dev
~~~

- Optional(recommend) prerequisite

~~~
foo@bar:~$ sudo apt-get install gfortran libssl-dev libpcre3-dev libglu1-mesa-dev libglew-dev libftgl-dev libmysqlclient-dev libfftw3-dev libcfitsio3-dev graphviz-dev libavahi-compat-libdnssd-dev libldap2-dev python-dev libxml2-dev libkrb5-dev libgsl0-dev libqt4-dev
~~~

- Install font server and fonts for Root

~~~
foo@bar:~$ sudo apt-get install xfs xfstt
foo@bar:~$ sudo apt-get install t1-xfree86-nonfree ttf-xfree86-nonfree ttf-xfree86-nonfree-syriac xfonts-75dpi xfonts-100dpi service xfs start
~~~

### Get source from github

##### Since make is deprecated will use cmake to install root

- Get Root source as in <https://root.cern.ch/get-root-sources>

~~~
git clone http://root.cern.ch/git/root.git
mv root root-sources
mkdir root-build
mkdir root-install
cd root-sources
git tag -l
~~~

- Choose your preferred version. As an example, v6-13-02:

~~~
git checkout -b v6-13-02 v6-13-02
~~~

- Now build Root
 - N is for the number of cpu core
     - grep -c ^processor /proc/cpuinfo can see the number of cpu

~~~
cd ../root-build
cmake -DCMAKE_INSTALL_PREFIX=$HOME/root/root-install ../root-source
cmake --build . --target install -- -j N
~~~

- Setup environment

~~~
echo "source $PWD/bin/thisroot.sh" >> $HOME/.bashrc
source $HOME/.bashrc
~~~

