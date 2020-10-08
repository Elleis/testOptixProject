# About
This is a test repo for Cal Poly's CSC 473 Advanced Rendering course. The base of this code is from Igno Wald's OptiX 7 Course presented at SigGraph 2019/2020, specifically example 11.

# Dependencies
- Cuda 11+ (https://developer.nvidia.com/cuda-downloads)
- OptiX 7+ (https://developer.nvidia.com/designworks/optix/download)
- CMake 3.17+ (https://cmake.org/download/)
- Crytek Sponza model (https://casual-effects.com/data/)

# Build Instructions
First, make sure the following environment variables are set:
- CUDA_PATH
- OptiX_INSTALL_DIR (should contain "include" and "SDK" directories)
- OptiX_INCLUDE (should be OptiX_INSTALL_DIR/SDK/CUDA)

Now, you should be able to build by:
1. Making a "build" directory in the top level of this repository.
2. Moving into the build directory.
3. Executing the command "cmake ..".