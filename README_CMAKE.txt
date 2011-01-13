Building arac with cmake
-------------------------------

required packages:
        python development headers (python-devel)
        swig
        cmake
        blas libraries with c-header (libblas-dev)
        
You will not necessarily need the Google Testing Framework or the ATLAS 
libraries.


Go to your arac top directory and use the following commands to compile arac:

mkdir build
cd build
cmake .. #Note: You can use the option -DCMAKE_INSTALL_PREFIX=~/local to define 
         #an install prefix.
make install


Now you should find the following files:

install_prefix
|-- bin
|   `-- test-arac
`-- lib
    |-- libarac.so
    `-- python2.6
        `-- site-packages
            `-- arac
                |-- cppbridge.py
                |-- _cppbridge.so
                |-- __init__.py
                |-- pybrainbridge.py
                `-- utilities.py
                

If you have used an install prefix, make sure arac can be found
by the linker and the python interpreter. For the 
example -DCMAKE_INSTALL_PREFIX=~/local you would have to do this:

export $LD_LIBRARY_PATH=~/local/lib
export $PYTHONPATH=~/local/lib/python2.6/site-packages


Note: Even if there are some tweaks to use the build file on Windows machines,
it was not tested.



Contact: daniel.dube@uni-tuebingen.de
