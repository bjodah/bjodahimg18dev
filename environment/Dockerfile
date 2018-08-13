FROM bjodah/bjodahimg18:v1.3
MAINTAINER Björn Dahlgren <bjodah@DELETEMEgmail.com>
RUN \
    cd /tmp && \
    curl -LOs "https://repo.continuum.io/miniconda/Miniconda3-4.5.4-Linux-x86_64.sh" && \
    /bin/bash Miniconda3-4.5.4-Linux-x86_64.sh -b -p /opt/miniconda3 && \
    rm Miniconda3*.sh && \
    PATH=/opt/miniconda3/bin:$PATH conda config --set always_yes yes && \
    PATH=/opt/miniconda3/bin:$PATH conda config --set changeps1 no && \
    PATH=/opt/miniconda3/bin:$PATH conda config --set show_channel_urls True && \
    PATH=/opt/miniconda3/bin:$PATH conda config --add channels conda-forge && \
    PATH=/opt/miniconda3/bin:$PATH conda update -n base conda && \
    PATH=/opt/miniconda3/bin:$PATH conda install --quiet \
        conda-build python=3.6 gmp numpy scipy matplotlib cython cmake gsl numba appdirs \
        pytest ipywidgets mpmath xz tk mpfr openssl sympy pip sqlite sundials boost suitesparse
