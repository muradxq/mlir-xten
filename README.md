<!--- (c) Copyright 2021 Xilinx, Inc. All Rights reserved.--->
<!--- (c) Copyright 2022 - 2024 Advanced Micro Devices, Inc. All Rights reserved.--->


# Extensions to Torch-MLIR

![](https://mlir.llvm.org//mlir-logo.png)

This repository contains Xilinx extensions to the torch-mlir ATen dialect to enable expressing the input and output of partial results. Using these extensions, a prototype streaming dataflow exploration tool scans the design space for compute/memory/computation balanced partitioning of CNNs on the AIE array of Xilinx' Versal devices.

[Full Documentation](https://xilinx.github.io/mlir-xten/)


## How to Build

This section explains how to build mlir-xten and provide python bindings for xten_nn dialect.

- ##### Clone and Setup the mlir-xten Repo
    ```
    git clone https://github.com/Xilinx/mlir-xten.git
    cd mlir-xten
    git submodule update --init --recursive
    ```
- ##### Create venv and Install Requirements
    ```
    conda create --name mlirdev python=3.10
    conda activate mlirdev

    # mlir-xten is an extension of MLIR and works under the same environment
    pip install --upgrade pip
    pip install -r ./llvm-project/mlir/python/requirements.txt
    
    # Install conda packages
    conda install conda-forge::ninja
    conda install conda-forge::sccache
    conda install -c conda-forge pybind11
    conda install libgcc -c conda-forge
    ```
- ##### Setup Env Variables
    ``` 
    setenv PATH /tools/batonroot/rodin/devkits/lnx64/gcc-10.2.0/bin:${PATH} 
    setenv LD_LIBRARY_PATH /tools/batonroot/rodin/devkits/lnx64/gcc-10.2.0/lib64:${LD_LIBRARY_PATH}
    setenv CC /tools/batonroot/rodin/devkits/lnx64/gcc-10.2.0/bin/gcc
    setenv CXX /tools/batonroot/rodin/devkits/lnx64/gcc-10.2.0/bin/g++
    ```
- ##### Build LLVM and MLIR
    ```
    cd llvm-project
    cmake -GNinja \
        -Bbuild \
        ./llvm \
        -DCMAKE_BUILD_TYPE=Release \
        -DLLVM_ENABLE_PROJECTS="llvm;mlir" \
        -DLLVM_TARGETS_TO_BUILD=host \
        -DCMAKE_INSTALL_PREFIX=../install \
        -DLLVM_BUILD_EXAMPLES=ON \
        -DLLVM_ENABLE_ASSERTIONS=ON \
        -DLLVM_BUILD_TOOLS=ON \
        -DLLVM_BUILD_UTILS=ON \
        -DLLVM_BUILD_RUNTIMES=ON \
        -DCMAKE_C_COMPILER_LAUNCHER=sccache \
        -DCMAKE_CXX_COMPILER_LAUNCHER=sccache \
        -DMLIR_ENABLE_BINDINGS_PYTHON=ON  | & tee cmake.log
    
    cmake --build build | & tee build.log
    cmake --build build --target install | & tee install.log
    ```
- ##### Build mlir-xten
  ```
  setenv BUILD_DIR "~/workspace/vaiml2aieIR/mlir-xten/llvm-project/build" 

  cd mlir-xten
  mkdir build && cd build
  
  cmake -G Ninja .. \
  -DMLIR_XTEN_ENABLE_TORCH=OFF \
  -DMLIR_DIR=${BUILD_DIR}/lib/cmake/mlir 

  cmake --build .
  ```
- ##### Run Testcases
   ```
   setenv PYTHONPATH ~/workspace/vaiml2aieIR/mlir-xten/build/python_packages/xten:${PYTHONPATH}
   cd ~/workspace/vaiml2aieIR/mlir-xten/test/mlir-xten
   python ./test1.py
   ```
   This test case reads the MLIR module and counts the number of InCoreChains in xten_nn.SubgraphOp.

## References:
- https://mlir.llvm.org/docs/Bindings/Python/
- [https://github.com/Xilinx/llvm-project/mlir/examples/standalone](https://github.com/Xilinx/llvm-project/tree/479c8d676f1fff3c839478beaeb1e17565a36275/mlir/examples/standalone) 
    
    
 
-----
<p align="center">Copyright&copy; 2019-2021 Xilinx</p>
