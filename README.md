# cmake-demo
CMake demo project

To run it:
```
$ mkdir build
$ cd build
$ cmake -DBRAND=[branda|brandb|brandc] -DOUTPUT_DIR="./test" ..
```

When configuration completes, check out the content of the generated output file `Output.txt`.

To build it and explore which targets are executed when, run either of these commands:
```
$ cmake --build .
$ cmake --build . --target all
$ cmake --build . --target print_cmake_version
$ cmake --build . --target print_timestamp
