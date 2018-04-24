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

## Install Pythia 6 with 8 

- Go to root directory

~~~
cd $ROOTSYS
~~~

- Get Pythia 6 source

~~~
wget https://root.cern.ch/download/pythia6.tar.gz
tar zxvf pythia6.tar.gz
rm -rf pythia6.tar.gz
wget http://www.hepforge.org/archive/pythia6/pythia-6.4.28.f.gz
gzip -d pythia-6.4.28.f.gz
mv pythia-6.4.28.f pythia6/pythia6428.f
rm -rf pythia6/pythia6416.f
mv pythia6 pythia6428
cd pythia6428
./makePythia6.linuxx8664
cd ..
~~~

- Get Pythia 8 source
  - N is for the number of cpu core
     - grep -c ^processor /proc/cpuinfo can see the number of cpu 

~~~
wget http://home.thep.lu.se/~torbjorn/pythia8/pythia8186.tgz 2
tar zxvf pythia8186.tgz
rm -rf pythia8186.tgz
cd pythia8186
./configure --enable-shared --enable-64bit
make -j N
cd ..
~~~

- Setup environment

~~~
echo "export PYTHIA6=$PWD/pythia6416" >> $HOME/bashrc
echo "export PYTHIA8=$PWD/pythia8186" >> $HOME/.bashrc
echo "export PYTHIA8DATA=$PYTHIA8/xmldoc" >> $HOME/.bashrc
sudo ldconfig
source $HOME/.bashrc
~~~

- Recompile Root
  - N is for the number of cpu core
     - grep -c ^processor /proc/cpuinfo can see the number of cpu

~~~
cd $ROOTSYS
cmake -DCMAKE_INSTALL_PREFIX=$HOME/root/root-install -DPYTHIA8_DIR=$PYTHIA8 -DPYTHIA8_INCLUDE_DIR=$PYTHIA8/include -DPYTHIA8_LIBRARY=$PYTHIA8/lib/libpythia8.so -DPYTHIA6_LIBRARY=$PYTHIA6/libPythia6.so -DGSL_DIR=/usr/local -Dbuiltin_xrootd=ON -Droofit=ON -Dpythia8=ON -Dpythia6=ON ../root-sources
cmake --build . --target install -- -j N
~~~

- Build the Pythia Dictionary

~~~
cd $PYTHIA8
mkdir PythiaDict && cd PythiaDict
~~~

   - Download the following files
   ~~~
   wget http://www.graverini.net/elena/content/repo/pythiaROOT.h
   wget http://www.graverini.net/elena/content/repo/pythiaLinkdef.h
   ~~~
   
   - Build the dictionary
   ~~~
   rootcint -f pythiaDict.cxx -c -I$PYTHIA8/include pythiaROOT.h pythiaLinkdef.h
   g++ -I$PYTHIA8/include `root-config --glibs` `root-config --cflags` -shared -fPIC -o pythiaDict.so pythiaDict.cxx
   ~~~
   
   - Open the folder where you work
   ~~~
   cd path/to/my/work/folder
   gedit rootlogon.C
   ~~~
   
   - Enter
   ~~~
   {
        gSystem->Load("$PYTHIA8/lib/libpythia8");
        gSystem->Load("libEG");
        gSystem->Load("libEGPythia8");
        gSystem->Load("$PYTHIA8/PythiaDict/pythiaDict.so");
   }
   ~~~
    
    
##### Reference
- http://www.graverini.net/elena/computing/physics-software/install-root/
- http://www.graverini.net/elena/computing/physics-software/install-pythia/
- https://root-forum.cern.ch/t/root-with-pythia6-and-pythia8/19211/4
- https://root.cern.ch/building-root