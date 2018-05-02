# RAHT (Region-adaptive Hierarchical Transform)
This code implements an improved version of the RAHT-rlgr coder and decoder as described by Queiroz and Chou [1].

[1] R. L. de Queiroz and P. A. Chou, “Compression of 3d point clouds using a region-adaptive hierarchical transform,” IEEE Transactions on Image Processing, vol. 25, no. 8, pp. 3947–3956, August 2016.

<a href="http://divp.org">Digital Image and Video Processing Group</a><br>
<a href="http://unb.br">University of Brasília</a><br>
<a href="http://cic.unb.br">Department of Computer Science</a><br>
<a href="http://www.ene.unb.br/">Department of Electrical Engineering</a><br>

After compilation, two executables are generated:
  * **RAHT_cod:**    Region Adaptive Hierarchical Transform coder (RAHT-rlgr)
  * **RAHT_dec:**    Region Adaptive Hierarchical Transform decoder (RAHT-rlgr)

Their usage, how to compile the executable and testing is described below

# Usage
### RAHT_cod (Region Adaptive Hierarchical Transform coder)

**Usage**    | **Description**
------------ | -------------
```RAHT_cod()```                 | prints help information
```CT = RAHT_cod(V, C, depth)``` | computes the transformed coefficients. The coefficients are not quantized and not coded to any file
```CT = RAHT_cod(V, C, depth, filename, Qstep)``` | transforms the coefficients and write the coded data to the file given by "filename" with the given quantization step. The output is optional and is not quantized. Only the coefficients writen to file are quantized

**INPUTS**     | **Description**
-------------- | -------------
```V```        | (*Nx3 double matrix*) vertices of each voxel in the order (X, Y, Z). The values should be non negative integers
```C```        | (*Nx3 double matrix*) colors associated to each voxel. This input is transparente to RGB, YUV, YCbCr or any other color space to be used as long as it is a triplet. It is also transparent to the color range: colors can be in the range 0--1, 0--255, 0--65535 or any other range
```depth```    | (*1x1 double value*) the depth of the octree to be used for the transform. It needs to be an integer value grater than 0
```filename``` | (*string of chars*) file name
```Qstep```    | (*1x1 double value*) quantization step. It is dependent of the color range
**OUTPUT**   | 
```CT``` | (*Nx3 double matrix*) transformed colors coefficients

### RAHT_dec (Region Adaptive Hierarchical Transform decoder)

**Usage**    | **Description**
------------ | -------------
```RAHT_dec()```                 | prints help information
```C = RAHT_dec(V, CT, depth)``` | performs the inverse transform from the given coefficients
```C = RAHT_dec(V, filename)```  | decodes the data from the given file

**INPUTS**   | **Description**
------------ | -------------
```V```     | (*Nx3 double matrix*) vertices of each voxel in the order (X, Y, Z). The values should be non negative integers
```CT```    | (*Nx3 double matrix*) transformed color coefficients
```depth``` | (*1x1 double value*) the depth of the octree to be used for the transform. It
**OUTPUT** | 
```C```     | (*Nx3*) colors associated to each voxel

# Compilation
There are two ways to compile the code, depending on your MATLAB version

## Newer versions
```matlab
mex( ...
    'd_main.cpp', ...
    'haar3D.cpp', ...
    './file/file.cpp', ...
    '-I./file', ...
    '-I.', ...
    '-output', ...
    'RAHT_cod', ...
    'COMPFLAGS="$COMPFLAGS /EHsc"')

mex( ...
    'i_main.cpp', ...
    'haar3D.cpp', ...
    './file/file.cpp', ...
    '-I./file', ...
    '-I.', ...
    '-output', ...
    'RAHT_dec', ...
    'COMPFLAGS="$COMPFLAGS /EHsc"')
```

## Older versions
```matlab
mex( ...
    'd_main.cpp', ...
    'haar3D.cpp', ...
    './file/file.cpp', ...
    '-I./file', ...
    '-I.', ...
    '-o', ...
    'RAHT_cod', ...
    'COMPFLAGS="$COMPFLAGS /EHsc"')

mex( ...
    'i_main.cpp', ...
    'haar3D.cpp', ...
    './file/file.cpp', ...
    '-I./file', ...
    '-I.', ...
    '-o', ...
    'RAHT_dec', ...
    'COMPFLAGS="$COMPFLAGS /EHsc"')
```
    
# FOR TESTING, ON MATLAB EXECUTE:
## Test 1: CODER AND DECODER
This test will compute the transformed coefficients that will be writen to file 'test.bin', decode the colors matrix from file and display the resulting Root Mean Squared Error. The RMSE is higher in this case due to quantization

```matlab
load test.mat
RAHT_cod(V, C, 9, 'test.raht', 10);
C2 = RAHT_dec(V, 'test.raht');
fprintf('RMSE: %g\n', sqrt(mean((C2(:)-C(:)).^2)))
```
Expected result:
>> RMSE: 2.88745

## Test 2: ONLY APPLY HAAR3D TRANSFORM
This test will only compute the transformed coefficients and its inverse and display the resulting Root Mean Squared Error

```matlab
load test.mat
CT = RAHT_cod(V, C, 9);
C2 = RAHT_dec(V, CT, 9);
fprintf('RMSE: %g\n', sqrt(mean((C2(:)-C(:)).^2)))
```
Expected result:
>> RMSE: 7.09696e-14
