FROM bjodah/bjodahimg18:v4.0
MAINTAINER Björn Dahlgren <bjodah@DELETEMEgmail.com>

RUN \
    export CC=clang-8 CXX=clang++-8 && \
    export LLVM_ORG_VER=8.0.1 && \
    curl -Ls https://github.com/llvm/llvm-project/archive/llvmorg-${LLVM_ORG_VER}.tar.gz | tar xz -C /tmp && \
    mkdir /tmp/build_libcxx && cd /tmp/build_libcxx && cmake -DCMAKE_BUILD_TYPE=Release -DLLVM_USE_SANITIZER=MemoryWithOrigins \
      -DLLVM_CONFIG_PATH=/usr/bin/llvm-config-8 \
      -DCMAKE_INSTALL_PREFIX=/opt/libcxx8_msan /tmp/llvm-project-llvmorg-${LLVM_ORG_VER}/libcxx && make install && cd - && \
    mkdir /tmp/build_libcxxabi && cd /tmp/build_libcxxabi && cmake -DCMAKE_BUILD_TYPE=Release -DLLVM_USE_SANITIZER=MemoryWithOrigins \
      -DLIBCXXABI_LIBCXX_INCLUDES=/opt/libcxx8_msan/include/c++/v1 -DLLVM_CONFIG_PATH=/usr/bin/llvm-config-8 -DLIBCXXABI_LIBCXX_PATH=/tmp/llvm-project-llvmorg-${LLVM_ORG_VER}/libcxx \
      -DCMAKE_INSTALL_PREFIX=/opt/libcxx8_msan /tmp/llvm-project-llvmorg-${LLVM_ORG_VER}/libcxxabi && make install && cd - && \
    rm -r /tmp/build_* /tmp/llvm-project*

RUN \
    cd /tmp && \
    curl -LOs "https://bitbucket.org/pypy/pypy/downloads/pypy3.6-v7.3.0-linux64.tar.bz2" && \
    tar xjf pypy3.6*.tar.bz2 -C /opt && rm pypy3.6*.tar.bz2 && \
    /opt/pypy3.6-*/bin/pypy3 -m ensurepip && \
    /opt/pypy3.6-*/bin/pypy3 -m pip install --upgrade --upgrade-strategy=eager pip && \
    /opt/pypy3.6-*/bin/pypy3 -m pip install numpy pytest sympy && \
    rm -rf /tmp/* /var/tmp/* ~/.cache/pip

RUN \
    cd /tmp && \
    curl -LOs https://github.com/jgm/pandoc/releases/download/2.9.2/pandoc-2.9.2-1-amd64.deb && \
    dpkg -i pandoc-2.9.2-1-amd64.deb && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY scripts/get_sundials.sh /opt/
RUN \
    CC=clang-8 CFLAGS="-fsanitize=memory -fsanitize-memory-track-origins=2 -fno-omit-frame-pointer -fno-optimize-sibling-calls -O1" /opt/get_sundials.sh 5.1.0 /opt/sundials-5.1.0-msan -DLAPACK_ENABLE=OFF -DKLU_ENABLE=OFF -DSUNDIALS_INDEX_SIZE=32 && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY scripts/get_symengine.sh /opt/
RUN export CPLUS_INCLUDE_PATH=/opt/boost_1_72_p/include && cd /tmp && \
    curl -Ls "https://vorboss.dl.sourceforge.net/project/swig/swig/swig-4.0.1/swig-4.0.1.tar.gz" | tar xz && cd swig-* && ./configure && make && make install && \
    CC=gcc-8   CXX=g++-8 CXXFLAGS="-D_GLIBCXX_DEBUG -D_GLIBCXX_DEBUG_PEDANTIC -fsized-deallocation" /opt/get_symengine.sh fbd501264a86b7452ec35bb0370fbabc6d83b5c6 /opt/symengine-fbd5012-dbg Debug   -DWITH_BFD=OFF  -DINTEGER_CLASS=boostmp && \
    CC=clang-8 CXX=clang++-8                                                                        /opt/get_symengine.sh fbd501264a86b7452ec35bb0370fbabc6d83b5c6 /opt/symengine-fbd5012-rel Release -DWITH_BFD=OFF && \
    CC=clang-8 CXX=clang++-8                                                                        /opt/get_symengine.sh fbd501264a86b7452ec35bb0370fbabc6d83b5c6 /opt/symengine-fbd5012-tcm Release -DWITH_BFD=OFF -DWITH_TCMALLOC=ON

RUN \
    curl -LOs https://github.com/python/cpython/archive/v3.8.2.tar.gz && \
    tar xzf v3.8.2.tar.gz && \
    cd cpython-3.8.2 && \
    CC=gcc-8 CFLAGS="-O2" ./configure --prefix=/opt/cpython-3.8 --enable-loadable-sqlite-extensions --enable-shared --with-ensurepip=yes LDFLAGS=-Wl,-rpath=/opt/cpython-3.8/lib && \
    make -j 4 && \
    LD_LIBRARY_PATH=. ./python -c "import sqlite3, uuid, lzma, bz2" && make install && \
    cd .. && rm -r cpython-3.8.2 && /opt/cpython-3.8/bin/python3 -m pip install --upgrade --upgrade-strategy=eager pip && \
    /opt/cpython-3.8/bin/python3 -m pip install setuptools wheel appdirs pybind11 numpy cython scipy matplotlib pycparser pytest-pep8 pytest-cov pytest-flakes pytest-flake8 pytest-xdist pytest-docstyle && \
    rm v3.8.2.tar.gz

 #tar xzf v3.8.2.tar.gz && \
 #cd cpython-3.8.2 && \
 #CC=clang-8 CFLAGS="-Og" ./configure --prefix=/opt/cpython-3.8m-msan --enable-loadable-sqlite-extensions --with-memory-sanitizer --enable-shared --with-ensurepip=yes LDFLAGS=-Wl,-rpath=/opt/cpython-3.8-msan/lib && \
    


# RUN \
#     PATH=/opt/julia-1.2.0/bin:$PATH python3 -m pip install diffeqpy numba && \
#     PATH=/opt/julia-1.2.0/bin:$PATH python3 -c 'import diffeqpy; diffeqpy.install()' && \
#     PATH=/opt/julia-1.2.0/bin:$PATH python3 -m pytest --pyargs diffeqpy && \
#     rm -rf /tmp/* /var/tmp/* ~/.cache/pip

# RUN \
#     apt-get update && \
#     apt-get --quiet --assume-yes --no-install-recommends install inkscape ninja-build && \
#     apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


# RUN \
#     curl -Ls "https://julialang-s3.julialang.org/bin/linux/x64/1.2/julia-1.2.0-linux-x86_64.tar.gz" | tar xz -C /opt && \
#     PATH=/opt/julia-1.2.0/bin:$PATH julia -e 'using Pkg; \
#       Pkg.add("DifferentialEquations"); using DifferentialEquations; \
#       pkg"add https://github.com/JuliaDiffEq/DiffEqTutorials.jl"; using DiffEqTutorials; \
#       Pkg.add("IJulia"); using IJulia; \
#       Pkg.add("PyPlot"); using PyPlot; \
#       Pkg.add("Latexify"); using Latexify; \
#       Pkg.add("DiffEqBiological"); using DiffEqBiological; \
#       Pkg.add("ODEInterfaceDiffEq"); using ODEInterfaceDiffEq;' && \
#     cd /opt && git clone https://github.com/JuliaDiffEq/DiffEqTutorials.jl && \
#     PATH=/opt/julia-1.2.0/bin:$PATH julia -e 'using Pkg, DiffEqTutorials; \
#       cd(joinpath(dirname(pathof(DiffEqTutorials)), "..")); \
#       Pkg.pkg"activate ."; \
#       Pkg.pkg"instantiate"; \
#       DiffEqTutorials.weave_folder("introduction"); \
#       DiffEqTutorials.weave_folder("models"); \
#       DiffEqTutorials.weave_folder("ode_extras"); \
#       DiffEqTutorials.weave_folder("type_handling"); \
#       '

    # ~/.julia/conda/3/bin/pip install jupyter_contrib_nbextensions && \
    # PATH=/opt/julia-1.2.0/bin:$PATH julia -e 'using Pkg; \
    #   Pkg.add("WebIO"); using WebIO; \
    #   Pkg.add("ORCA"); using ORCA; \
    #   Pkg.add("PloylyJS"); using PlotlyJS; \

#RUN \
#    cd /tmp && curl -LOs http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.1.168-1_amd64.deb && \
#    dpkg -i cuda-repo-ubuntu1804_10.1.168-1_amd64.deb && \
#    apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub && \
#    apt-get update && \
#    apt-get --quiet --assume-yes --no-install-recommends install cuda && \
#    rm -r /tmp/*

    # curl -LOs "https://repo.continuum.io/miniconda/Miniconda3-4.7.12.1-Linux-x86_64.sh" && \
    # /bin/bash Miniconda3-4.7.12.1-Linux-x86_64.sh -b -p /opt/miniconda3 && \
    # rm Miniconda3*.sh && \
    # PATH=/opt/miniconda3/bin:$PATH conda config --set always_yes yes && \
    # PATH=/opt/miniconda3/bin:$PATH conda config --set changeps1 no && \
    # PATH=/opt/miniconda3/bin:$PATH conda config --set show_channel_urls True && \
    # PATH=/opt/miniconda3/bin:$PATH conda config --add channels conda-forge && \
    # PATH=/opt/miniconda3/bin:$PATH conda install --quiet -c plotly plotly-orca psutil && \
    # PATH=/opt/miniconda3/bin:$PATH conda update -n base conda && \
    # PATH=/opt/miniconda3/bin:$PATH conda install --quiet \
    #     conda-build python=3.7 gmp numpy scipy matplotlib cython cmake gsl numba appdirs \
    #     pytest ipywidgets mpmath xz tk mpfr openssl sympy pip sqlite sundials boost suitesparse && \
    # PATH=/opt/miniconda3/bin:$PATH conda clean --tarballs


# RUN curl -Ls https://github.com/catchorg/Catch2/releases/download/v2.9.2/catch.hpp -o /opt/catch2-2.9.2/include/catch.hpp --create-dirs && \
#     curl -Ls https://github.com/catchorg/Clara/releases/download/v1.1.5/clara.hpp -o /opt/clara-1.1.5/include/clara.hpp --create-dirs && \
#     curl -Ls https://github.com/nlohmann/json/releases/download/v3.7.0/json.hpp -o /opt/nlohmann_json-3.7.0/include/json.hpp --create-dirs && \
#     cd /tmp && curl -Ls https://github.com/USCiLab/cereal/archive/v1.2.2.tar.gz | tar xz -C /opt/ && \
#     cd /tmp && curl -Ls https://github.com/msgpack/msgpack-c/releases/download/cpp-3.2.0/msgpack-3.2.0.tar.gz | tar xz && cd msgpack-* && mkdir build && cd build/ && \
#     cmake -DCMAKE_INSTALL_PREFIX:PATH="/opt/msgpack-3.2.0" .. && make install && \
#     cd /tmp && curl -Ls https://github.com/abseil/abseil-cpp/archive/20190808.tar.gz | tar xz && cd abseil-cpp-20190808 && mkdir build && cd build/ && \
#     cmake -DCMAKE_INSTALL_PREFIX:PATH="/opt/abseil-cpp-20190808" .. && make install && \
#     cd /tmp && curl -Ls http://bitbucket.org/eigen/eigen/get/3.3.7.tar.bz2 | tar xj && cd eigen-* && mkdir build && cd build/ && \
#     cmake -DCMAKE_INSTALL_PREFIX:PATH="/opt/eigen-3.3.7" .. && make install && \
# curl -Ls https://github.com/danmar/cppcheck/archive/1.90.tar.gz | tar xz -C /tmp/build-cppcheck