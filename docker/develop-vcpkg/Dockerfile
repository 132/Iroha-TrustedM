FROM ubuntu:16.04

# number of concurrent threads during build
# usage: docker build --build-arg PARALLELISM=8 -t name/name .
ARG PARALLELISM=1
ARG CMAKE_BUILD_TYPE=Release

ENV IROHA_HOME /opt/iroha
ENV IROHA_BUILD /opt/iroha/build

RUN apt-get update && \
    apt-get -y --no-install-recommends install apt-utils software-properties-common wget; \
    apt-get -y clean

# add repos
RUN set -e; \
    add-apt-repository -y ppa:git-core/ppa; \
    add-apt-repository -y ppa:ubuntu-toolchain-r/test; \
    wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add -; \
    echo 'deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-6.0 main' >> /etc/apt/sources.list; \
    echo 'deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-7 main' >> /etc/apt/sources.list; \
    apt-get update

RUN set -e; \
    apt-get -y --no-install-recommends install python-software-properties \
        automake libtool \
        # compilers (gcc-5, gcc-7, clang-6)
        build-essential clang-6.0 lldb-6.0 lld-6.0 g++-7 \
        # dev dependencies
        libssl-dev zlib1g-dev libcurl4-openssl-dev libc6-dbg golang \
        # CI dependencies
        git ssh tar gzip ca-certificates gnupg \
        # Pythons
        python-pip python3-pip python3-setuptools python-dev \
        # other
        curl file gdb gdbserver ccache clang-format \
        gcovr cppcheck doxygen rsync graphviz graphviz-dev unzip vim zip; \
    apt-get -y clean

# compiler clang-7 and libc++ only on x86_64, for debug purpose
RUN set -e; \
    if [ `uname -m` = "x86_64" ]; then \
      apt-get -y --no-install-recommends install \
        clang-7 lldb-7 lld-7 libc++-7-dev libc++abi-7-dev; \
      apt-get -y clean; \
    fi

# install cmake 3.11.4
RUN set -e; \
    curl -L -o /tmp/cmake.sh https://github.com/Kitware/CMake/releases/download/v3.11.4/cmake-3.11.4-Linux-x86_64.sh; \
    sh /tmp/cmake.sh --prefix=/usr/local --skip-license; \
    rm /tmp/cmake.sh

# install dependencies
COPY vcpkg /tmp/vcpkg-vars
RUN set -e; \
    git clone https://github.com/microsoft/vcpkg /tmp/vcpkg; \
    (cd /tmp/vcpkg ; git checkout $(cat /tmp/vcpkg-vars/VCPKG_COMMIT_SHA)); \
    for i in /tmp/vcpkg-vars/patches/*.patch; do git -C /tmp/vcpkg apply $i; done; \
    sh /tmp/vcpkg/bootstrap-vcpkg.sh; \
    /tmp/vcpkg/vcpkg install $(cat /tmp/vcpkg-vars/VCPKG_DEPS_LIST | cut -d':' -f1 | tr '\n' ' '); \
    /tmp/vcpkg/vcpkg install --head $(cat /tmp/vcpkg-vars/VCPKG_HEAD_DEPS_LIST | cut -d':' -f1 | tr '\n' ' '); \
    /tmp/vcpkg/vcpkg export $(/tmp/vcpkg/vcpkg list | cut -d':' -f1 | tr '\n' ' ') --raw --output=dependencies; \
    mv /tmp/vcpkg/dependencies /opt/dependencies; \
    chmod +x /opt/dependencies/installed/x64-linux/tools/protobuf/protoc*; \
    rm -rf /tmp/vcpkg*

# install sonar cli
ENV SONAR_CLI_VERSION=3.3.0.1492
RUN set -e; \
    mkdir -p /opt/sonar; \
    curl -L -o /tmp/sonar.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_CLI_VERSION}-linux.zip; \
    unzip -o -d /tmp/sonar-scanner /tmp/sonar.zip; \
    mv /tmp/sonar-scanner/sonar-scanner-${SONAR_CLI_VERSION}-linux /opt/sonar/scanner; \
    ln -s -f /opt/sonar/scanner/bin/sonar-scanner /usr/local/bin/sonar-scanner; \
    rm -rf /tmp/sonar*

# fetch lcov reports converter
RUN set -e; \
    curl -L -o /tmp/lcov_cobertura.py https://raw.githubusercontent.com/eriwen/lcov-to-cobertura-xml/8c55cd11f80a21e7e46f20f8c81fcde0bf11f5e5/lcov_cobertura/lcov_cobertura.py

# OpenJRE 8
RUN set -e; \
    apt-get -y install openjdk-8-jre; \
    java -version

RUN set -e; \
    add-apt-repository -y ppa:jonathonf/python-3.6; \
    apt-get update; \
    apt-get -y install python3.6-dev

# python bindings dependencies
RUN set -e; \
    pip install grpcio_tools pysha3; \
    pip3 install grpcio_tools pysha3

# install lcov
RUN set -e; \
    curl -L -o /tmp/lcov-1.13.tar.gz https://github.com/linux-test-project/lcov/releases/download/v1.13/lcov-1.13.tar.gz; cd /tmp; tar zxf lcov-1.13.tar.gz; cd lcov-1.13; make install

# non-interactive adduser
#   -m = create home dir
#   -s = set default shell
#   iroha-ci = username
#   -u = userid, default for Ubuntu is 1000
#   -U = create a group same as username
#   no password
RUN useradd -ms /bin/bash iroha-ci -u 1000 -U

WORKDIR /opt/iroha
RUN set -e; \
    chmod -R 777 /opt/iroha; \
    mkdir -p /tmp/ccache -m 777; \
    ccache --clear


USER iroha-ci
ENV CMAKE_TOOLCHAIN_FILE /opt/dependencies/scripts/buildsystems/vcpkg.cmake
CMD ["/bin/bash"]
