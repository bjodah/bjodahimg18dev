FROM bjodah/bjodahimg18:v2.1
MAINTAINER Björn Dahlgren <bjodah@DELETEMEgmail.com>
RUN \
    cd /tmp && \
    curl -LOs "https://repo.continuum.io/miniconda/Miniconda3-4.6.14-Linux-x86_64.sh" && \
    /bin/bash Miniconda3-4.6.14-Linux-x86_64.sh -b -p /opt/miniconda3 && \
    rm Miniconda3*.sh && \
    PATH=/opt/miniconda3/bin:$PATH conda config --set always_yes yes && \
    PATH=/opt/miniconda3/bin:$PATH conda config --set changeps1 no && \
    PATH=/opt/miniconda3/bin:$PATH conda config --set show_channel_urls True && \
    PATH=/opt/miniconda3/bin:$PATH conda config --add channels conda-forge && \
    PATH=/opt/miniconda3/bin:$PATH conda install --quiet -c plotly plotly-orca psutil && \
    PATH=/opt/miniconda3/bin:$PATH conda update -n base conda && \
    PATH=/opt/miniconda3/bin:$PATH conda install --quiet \
        conda-build python=3.7 gmp numpy scipy matplotlib cython cmake gsl numba appdirs \
        pytest ipywidgets mpmath xz tk mpfr openssl sympy pip sqlite sundials boost suitesparse && \
    PATH=/opt/miniconda3/bin:$PATH conda clean --tarballs

#RUN \
#    cd /tmp && curl -LOs http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.1.168-1_amd64.deb && \
#    dpkg -i cuda-repo-ubuntu1804_10.1.168-1_amd64.deb && \
#    apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub && \
#    apt-get update && \
#    apt-get --quiet --assume-yes --no-install-recommends install cuda && \
#    rm -r /tmp/*

RUN \
    curl -Ls "https://julialang-s3.julialang.org/bin/linux/x64/1.1/julia-1.1.1-linux-x86_64.tar.gz" | tar xz -C /opt && \
    PATH=/opt/julia-1.1.1/bin:$PATH julia -e 'using Pkg; \
      Pkg.add("DifferentialEquations"); using DifferentialEquations; \
      pkg"add https://github.com/JuliaDiffEq/DiffEqTutorials.jl"; using DiffEqTutorials; \
      Pkg.add("IJulia"); using IJulia; \
      Pkg.add("PyPlot"); using PyPlot; \
      Pkg.add("Latexify"); using Latexify; \
      Pkg.add("DiffEqBiological"); using DiffEqBiological; \
      Pkg.add("ODEInterfaceDiffEq"); using ODEInterfaceDiffEq;' && \
    cd /opt && git clone https://github.com/JuliaDiffEq/DiffEqTutorials.jl && \
    PATH=/opt/julia-1.1.1/bin:$PATH julia -e 'using Pkg, DiffEqTutorials; \
      cd(joinpath(dirname(pathof(DiffEqTutorials)), "..")); \
      Pkg.pkg"activate ."; \
      Pkg.pkg"instantiate"; \
      DiffEqTutorials.weave_folder("introduction"); \
      DiffEqTutorials.weave_folder("models"); \
      DiffEqTutorials.weave_folder("ode_extras"); \
      DiffEqTutorials.weave_folder("type_handling"); \
      '

RUN \
    cd /tmp && \
    curl -LOs "https://bitbucket.org/pypy/pypy/downloads/pypy3.6-v7.1.1-linux64.tar.bz2" && \
    tar xjf pypy3.6*.tar.bz2 -C /opt && rm pypy3.6*.tar.bz2 && \
    /opt/pypy3.6-*/bin/pypy3 -m ensurepip && \
    /opt/pypy3.6-*/bin/pypy3 -m pip install --upgrade --upgrade-strategy=eager pip && \
    /opt/pypy3.6-*/bin/pypy3 -m pip install numpy pytest sympy && \
    rm -rf /tmp/* /var/tmp/* ~/.cache/pip

# RUN \
#     PATH=/opt/julia-1.1.1/bin:$PATH python3 -m pip install diffeqpy numba && \
#     PATH=/opt/julia-1.1.1/bin:$PATH python3 -c 'import diffeqpy; diffeqpy.install()' && \
#     PATH=/opt/julia-1.1.1/bin:$PATH python3 -m pytest --pyargs diffeqpy && \
#     rm -rf /tmp/* /var/tmp/* ~/.cache/pip
