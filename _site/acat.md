# Heterogeneous computing
In the High-Luminosity phase of the LHC (HL-LHC) the accelerator will reach an instantaneous luminosity of 7 × 10<sup>34</sup> cm<sup>-2</sup>s<sup>-1</sup> with an average pileup of 200 proton-proton collisions. This will lead to a computational challenge for the online and offline reconstruction software that have been developed. To face this complexity CMS has decided to use heterogeneous computing, meaning that different types of processors will be used instead of CPUs only.<br>
The CMS experiment has equipped the HLT with some GPUs and so heterogeneous computing has already been used in production during Run-3 for:
<ol>
  <li>pixel unpacking, local reconstruction, tracks and vertices</li>
  <li>ECAL unpacking and local reconstruction</li>
  <li>HCAL local reconstruction</li>
</ol>
This has resulted in:
<ol>
  <li>higher throughput</li>
  <li>better physics performance (algorithms redesigned when written for GPUs)</li>
<ol>
The goal for the LH-LHC will be to offload to computing accelerators at least 50% of the HLT in Run-4 and 80% in Run-5.

# Performance portability
Currently the code executed on GPUs is written in CUDA, that is specific for NVIDIA GPUs, and through a compatibility layer is able to run entirely on CPUs if no GPU is available. In this way, to offload the computational work on different non-CPU resources, different implementations of the same code are required. This approach should be avoided because it would introduce code-duplication that is not easily mainteinable. A solution is the use of performance portability libraries, that allow the programmer to write a single source code and then to execute it on different architectures.<br>
The CMS experiment has evaluated some performance portability libraries and Alpaka has been chosen for Run-3. (porting /migration verrà fatto nei prossimi mesi) despite this, studies on these libraries are still ongoing and other solutions are being explored.<br>
One possibility is to use Data Parallel C++ (DPC++), an open source compiler project developed by Intel, that is part of the oneAPI programming model. DPC++ is based on SYCL, is a cross-platform abstraction layer mainteined by Khronos Group that allows code to be written using standard ISO C++ both for the host and the device in the same source file. 

# CUDA2SYCL
SYCL/oneAPI so far has been tested with the porting from CUDA of two algorithms:
<ul>
  <li>CLUE (CLUstering of Energy): a fast parallel clustering algorithm for high granularity calorimeters</li>
  <li>pixeltrack: a heterogeneous implementation of pixel tracks and vertices reconstruction chain</li>
</ul>
Some of the difference between CUDA and SYCL that we noticed while porting the code are:
<ul>
  <li>the use of different terms (e.g. thread, block, grid become work-item, work-group, Nd range) or of the same term but with a different meaning</li>
  <li>the need to avoid hardware specific code, to enhance its portability</li>
  <li>each device has a queue (analog of a CUDA stream) that is needed to launch kernels and to do memory operations</li>
  <li>same features like pinned memory are not explicitly available in SYCL as they are managed by the SYCl runtime</li>
</ul>
To ease the process of porting the code from CUDA to SYCL, Intel made available the Data Parallel Compatibility Tool (DPCT). It was used partially at the beginning to gain confidence with the SYCL language and turned out to be helpful even though the code still needed revision after that.

# Performance
The porting of pixeltrack is still ongoing, but we are able to run successfully the code both on Intel CPUs and Intel GPUs. On the other hand CLUE has been fully ported and tested. We run the algorithm also on NVIDIA GPUs and compared its performance with native CUDA and Alpaka.
The target was a Tesla T4 and the results are really promising, showing that SYCL is able to reach the same throughput of native CUDA.

# Reference
pixeltrack e heteroclue repo+articoli
sito patatrack con guida
libro dpcpp 