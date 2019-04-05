FROM bjodah/bjodahimg18:v1.5
MAINTAINER Björn Dahlgren <bjodah@DELETEMEgmail.com>
RUN \
    cd /tmp && \
    curl -LOs "https://repo.continuum.io/miniconda/Miniconda3-4.5.12-Linux-x86_64.sh" && \
    /bin/bash Miniconda3-4.5.12-Linux-x86_64.sh -b -p /opt/miniconda3 && \
    rm Miniconda3*.sh && \
    PATH=/opt/miniconda3/bin:$PATH conda config --set always_yes yes && \
    PATH=/opt/miniconda3/bin:$PATH conda config --set changeps1 no && \
    PATH=/opt/miniconda3/bin:$PATH conda config --set show_channel_urls True && \
    PATH=/opt/miniconda3/bin:$PATH conda config --add channels conda-forge/label/cf201901 && \
    PATH=/opt/miniconda3/bin:$PATH conda update -n base conda && \
    PATH=/opt/miniconda3/bin:$PATH conda install --quiet \
        conda-build python=3.6 gmp numpy scipy matplotlib cython cmake gsl numba appdirs \
        pytest ipywidgets mpmath xz tk mpfr openssl sympy pip sqlite sundials boost suitesparse && \
    PATH=/opt/miniconda3/bin:$PATH conda clean --tarballs

RUN \
    curl -LOs "https://julialang-s3.julialang.org/bin/linux/x64/1.1/julia-1.1.0-linux-x86_64.tar.gz" && \
    tar xzf julia-*.tar.gz -C /opt && rm julia*.tar.gz && \
    PATH=/opt/julia-1.1/bin:$PATH julia -c 'using Pkg; Pkg.add("DifferentialEquations"); using DifferentialEquations; pkg"add https://github.com/JuliaDiffEq/DiffEqTutorials.jl"; using DiffEqTutorials'

RUN \
    cd /tmp && \
    curl -LOs "https://bitbucket.org/pypy/pypy/downloads/pypy3.6-v7.1.0-linux64.tar.bz2" && \
    tar xjf pypy3.6*.tar.bz2 -C /opt && rm pypy3.6*.tar.bz2 && \
    /opt/pypy3.6-*/bin/pypy3.6 -m ensurepip && \
    /opt/pypy3.6-*/bin/pypy3.6 -m pip install numpy pytest sympy