---
title: Compiling R 3.0.1 with MKL support
author: Flavio Barros
date: '2013-06-19'
categories:
  - English
  - R e RStudio
  - r-bloggers
tags:
  - compilation
  - MKL
  - multithreading
slug: compiling-r-3-0-1-with-mkl-support
---

Before you begin, be aware that there is others excellent posts about the issue, as:

  1. [Compiling 64-bit R 2.10.1 with MKL in Linux](http://www.r-bloggers.com/compiling-64-bit-r-2-10-1-with-mkl-in-linux/)

2. [Speeding up R with Intel's Math Kernel Library (MKL)](http://www.r-bloggers.com/speeding-up-r-with-intels-math-kernel-library-mkl/)

3. [Performance benefits of linking R to multithreaded math libraries](http://blog.revolutionanalytics.com/2010/06/performance-benefits-of-multithreaded-r.html)

4. [Using MKL-Linked R in Eclipse](http://www.r-bloggers.com/using-mkl-linked-r-in-eclipse/)

But, as recently i faced some problems to compile R 3.0.1 com MKL support on Debian Wheezy, i would like to share this update.

First the acronyms: what is MKL? MKL is an acronym for Math Kernel Library, i.e., a library that provides a series of mathematical functions, as matrix product or even a Fourier transform. But what  is special and why I would need a library of functions like that on R?

![mkl-large-product-plain](http://www.flaviobarros.net/wp-content/uploads/2013/06/mkl-large-product-plain.png)

First it is important to note that regardless of your purpose  to use the MKL, this is a library specific to [Intel](http://software.intel.com/en-us/intel-mkl) processors. If you use other architectures is likely that MKL and this post can be useless to you. BUT, specifically in relation to R, you may already have heard that it can be very [slow](http://www.r-bloggers.com/how-slow-is-r-really/). One of the origins of a slow code, especially with those new processors like the Intel Core i5 or i7, is that when you install the standard R (binaries available for download from [CRAN](http://www.r-project.org/)) you are installing a series of numerical libraries that perform mathematical operations where performance is critical. These functions can be written in C, C + + or Fortran in order to obtain superior performance. HOWEVER, the numerical libraries that comes with the R ([generic BLAS](http://en.wikipedia.org/wiki/Basic_Linear_Algebra_Subprograms), [LAPACK](http://en.wikipedia.org/wiki/LAPACK)) do not have [multithreading](http://en.wikipedia.org/wiki/Multithreading_(software)#Multithreading) capabilities. In practice this means that if you have a processor with 8 cores, using the R default you will be performing several critical operations with a [single processor](http://www.r-bloggers.com/speeding-up-r-with-intels-math-kernel-library-mkl/)!

How to solve the problem? One possible solution is to install the MKL on R. The MKL, beyond the capabilities of multithreading, also has specific optimizations for Intel processors, with performance far superior to traditional libraries. So from now on I will show you how you can download and install this library and link it to R. But first a few caveats:

1) If you are using Windows, can be more interesting for you to use [Revolution Analytics R](http://www.revolutionanalytics.com/), that already comes standard [with MKL installed](http://blog.revolutionanalytics.com/2010/06/performance-benefits-of-multithreaded-r.html).

2) If you use Linux [Red Hat](http://www.redhat.com/) and derivatives ([CentOS](http://www.centos.org/)) the Revolution R should also be the best option.

3) If you use Ubuntu you can install R from repositories and then install the packages r-revobase-revolution, revolution-r and revolution-mkl.

and at this point you may be asking yourself: if there are these options why I would compile R? Well, there is several reasons: you might want to do a custom install; use R for commercial purposes without having to pay a license for the Revolution; learn how to compile R. That said, I'll have to say that the official documentation of the R is a bit [confusing and outdated](http://cran.r-project.org/doc/manuals/R-admin.html#MKL) in relation to the installation of MKL. That's why I'm writing this post.

**1. Downloading and Installing MKL**

I will not dwell in that part, but the installation process is actually very simple: just enter the [site](https://registrationcenter.intel.com/RegCenter/NComForm.aspx?ProductID=1461) and register. Then you will receive an email with download instructions. Extract the downloaded file and you will find scripts and [RPM](http://en.wikipedia.org/wiki/RPM_Package_Manager) packages. If you are trying to install on Debian may be necessary to install support for RPM packages. Install using the install.sh script. From this moment I will assume that your installation is at / opt/intel/mkl/11.0.

**2. Compiling R**

To compile the R you will need the [source code](http://cran.r-project.org/src/base/R-3/R-3.0.1.tar.gz). Extract to the directory of your choice:

````r

<blockquote>
  tar -zxvf R-3.0.1.tar.gz
  [/sourcecode]
</blockquote>

Before starting the compilation is necessary to set two system variables: MKLROOT and LD_LIBRARY_PATH, which can be done at once with the following command:

````r

<blockquote>
  source /opt/intel/mkl/bin/mklvars.sh intel64
  [/sourcecode]
</blockquote>

note the location of the file mklvars.sh that may be in a different place depending on your installation of MKL.

Once defined system variables just run the usual Linux compilation  with . /configure && make && make install. The moment to "tell" to the compiler where the MKL is installed and allow R has access to it, is at the . /configure step. So:

````r

<blockquote>
  ./configure --enable-R-shlib --enable-threads=posix --with-lapack --with-blas="-fopenmp -m64 -I$MKLROOT/include -L$MKLROOT/lib/intel64 -lmkl_gf_lp64 -lmkl_gnu_thread -lmkl_core -lpthread -lm"
  make
  make install
  [/sourcecode]
</blockquote>

And from that moment your R already has support for multithreading libraries from Intel. To ensure that libraries are installed correctly, run this [benchmark script](http://r.research.att.com/benchmarks/R-benchmark-25.R)  before and after installation, and you can check differences.

````r
source(R-benchmark-25.R)
````

If you already have an installation of R on your computer, you may need to reinstall all the packages again. An easy way to do this is with the following command:

````r
update.packages(checkBuilt=TRUE)
````

Just as a reference, in my notebook (Core i7, 8GB RAM) a product of two matrices 5000x5000 takes around 19 seconds with MKL. Prior to the installation was more than 1 minute!
