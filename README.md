# Add Example SageMaker Image To Amazon ECR

Following [SageMaker Developer Guide](https://docs.aws.amazon.com/sagemaker/latest/dg/studio-byoi-sdk-add-container-image.html) and using the [example R image](https://github.com/aws-samples/sagemaker-studio-custom-image-samples/tree/main/examples/r-image).

```console
$ aws --region us-west-2 ecr create-repository \
    --repository-name smstudio-custom \
    --image-scanning-configuration scanOnPush=true
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:us-west-2:<acct-id>:repository/smstudio-custom",
        "registryId": "<acct-id>",
        "repositoryName": "smstudio-custom",
        "repositoryUri": "<acct-id>.dkr.ecr.us-west-2.amazonaws.com/smstudio-custom",
        "createdAt": 1610922135.0,
        "imageTagMutability": "MUTABLE",
        "imageScanningConfiguration": {
            "scanOnPush": true
        },
        "encryptionConfiguration": {
            "encryptionType": "AES256"
        }
    }
}
$ aws --region us-west-2 ecr get-login-password | \
    docker login --username AWS --password-stdin <acct-id>.dkr.ecr.us-west-2.amazonaws.com/smstudio-custom
WARNING! Your password will be stored unencrypted in /home/kit/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
$ docker build . -t smstudio-r -t <acct-id>.dkr.ecr.us-west-2.amazonaws.com/smstudio-custom:r
Sending build context to Docker daemon  8.192kB
Step 1/15 : FROM python:3.6
3.6: Pulling from library/python
b9a857cbf04d: Pull complete
d557ee20540b: Pull complete
3b9ca4f00c2e: Pull complete
667fd949ed93: Pull complete
4ad46e8a18e5: Pull complete
ddf9fd7ad7b9: Pull complete
d3e288ff2e11: Pull complete
1c6250e11f6c: Pull complete
0df4e3936ca9: Pull complete
Digest: sha256:da022140db3b40d07c81815158092ff8ccfd967926b533a7c0b573eeeb5be120
Status: Downloaded newer image for python:3.6
 ---> 6b0219e0ed75
Step 2/15 : ARG NB_USER="sagemaker-user"
 ---> Running in 5f6d9a18e877
Removing intermediate container 5f6d9a18e877
 ---> 67736c360a12
Step 3/15 : ARG NB_UID="1000"
 ---> Running in 8083c1f8df78
Removing intermediate container 8083c1f8df78
 ---> 12017f2dab27
Step 4/15 : ARG NB_GID="100"
 ---> Running in 9578788aadfd
Removing intermediate container 9578788aadfd
 ---> 0f57988bffb9
Step 5/15 : RUN     apt-get update &&     apt-get install -y sudo &&     useradd -m -s /bin/bash -N -u $NB_UID $NB_USER &&     chmod g+w /etc/passwd &&     echo "${NB_USER}    ALL=(ALL)    NOPASSWD:    ALL" >> /etc/sudoers &&     rm -rf /var/lib/apt/lists/*
 ---> Running in 86662a2a3c3d
Get:1 http://security.debian.org/debian-security buster/updates InRelease [65.4 kB]
Get:2 http://deb.debian.org/debian buster InRelease [121 kB]
Get:3 http://deb.debian.org/debian buster-updates InRelease [51.9 kB]
Get:4 http://security.debian.org/debian-security buster/updates/main amd64 Packages [261 kB]
Get:5 http://deb.debian.org/debian buster/main amd64 Packages [7907 kB]
Get:6 http://deb.debian.org/debian buster-updates/main amd64 Packages [7860 B]
Fetched 8415 kB in 2s (3864 kB/s)
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
The following NEW packages will be installed:
  sudo
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 1245 kB of archives.
After this operation, 3878 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian buster/main amd64 sudo amd64 1.8.27-1+deb10u2 [1245 kB]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 1245 kB in 0s (4112 kB/s)
Selecting previously unselected package sudo.
(Reading database ... 24600 files and directories currently installed.)
Preparing to unpack .../sudo_1.8.27-1+deb10u2_amd64.deb ...
Unpacking sudo (1.8.27-1+deb10u2) ...
Setting up sudo (1.8.27-1+deb10u2) ...
invoke-rc.d: could not determine current runlevel
invoke-rc.d: policy-rc.d denied execution of start.
Removing intermediate container 86662a2a3c3d
 ---> bfbad18a3e8d
Step 6/15 : USER $NB_UID
 ---> Running in 067ddd329b1e
Removing intermediate container 067ddd329b1e
 ---> 4c9f5fcfeb23
Step 7/15 : ENV SHELL=/bin/bash     NB_USER=$NB_USER     NB_UID=$NB_UID     NB_GID=$NB_GID     HOME=/home/$NB_USER     MINICONDA_VERSION=4.6.14     CONDA_VERSION=4.6.14     MINICONDA_MD5=718259965f234088d785cad1fbd7de03     CONDA_DIR=/opt/conda     PATH=$CONDA_DIR/bin:${PATH}
 ---> Running in 9b6e12924b07
Removing intermediate container 9b6e12924b07
 ---> bdb339001d63
Step 8/15 : USER root
 ---> Running in a4f35a118d48
Removing intermediate container a4f35a118d48
 ---> 63c317759d76
Step 9/15 : RUN apt-get update &&     apt-get install -y --no-install-recommends     fonts-dejavu     unixodbc     unixodbc-dev     r-cran-rodbc     gfortran     gcc &&     rm -rf /var/lib/apt/lists/* &&     mkdir -p $CONDA_DIR &&     chown -R $NB_USER:$NB_GID $CONDA_DIR &&     ln -s /bin/tar /bin/gtar
 ---> Running in ce37769f5862
Get:1 http://security.debian.org/debian-security buster/updates InRelease [65.4 kB]
Get:2 http://deb.debian.org/debian buster InRelease [121 kB]
Get:3 http://deb.debian.org/debian buster-updates InRelease [51.9 kB]
Get:4 http://security.debian.org/debian-security buster/updates/main amd64 Packages [261 kB]
Get:5 http://deb.debian.org/debian buster/main amd64 Packages [7907 kB]
Get:6 http://deb.debian.org/debian buster-updates/main amd64 Packages [7860 B]
Fetched 8415 kB in 2s (4711 kB/s)
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
gcc is already the newest version (4:8.3.0-1).
The following additional packages will be installed:
  fonts-dejavu-extra gfortran-8 libblas3 libgfortran-8-dev libgfortran5
  liblapack3 libodbc1 libpaper-utils libpaper1 odbcinst odbcinst1debian2
  r-base-core xdg-utils zip
Suggested packages:
  gfortran-multilib gfortran-doc gfortran-8-multilib gfortran-8-doc
  libgfortran5-dbg libcoarrays-dev libmyodbc odbc-postgresql tdsodbc
  unixodbc-bin ess r-doc-info | r-doc-pdf r-mathlib r-base-html
Recommended packages:
  r-recommended r-base-dev r-doc-html libfile-mimeinfo-perl libnet-dbus-perl
  libx11-protocol-perl x11-utils x11-xserver-utils
The following NEW packages will be installed:
  fonts-dejavu fonts-dejavu-extra gfortran gfortran-8 libblas3
  libgfortran-8-dev libgfortran5 liblapack3 libodbc1 libpaper-utils libpaper1
  odbcinst odbcinst1debian2 r-base-core r-cran-rodbc unixodbc unixodbc-dev
  xdg-utils zip
0 upgraded, 19 newly installed, 0 to remove and 0 not upgraded.
Need to get 40.7 MB of archives.
After this operation, 92.8 MB of additional disk space will be used.
Get:1 http://deb.debian.org/debian buster/main amd64 fonts-dejavu-extra all 2.37-1 [1982 kB]
Get:2 http://deb.debian.org/debian buster/main amd64 fonts-dejavu all 2.37-1 [32.3 kB]
Get:3 http://deb.debian.org/debian buster/main amd64 libgfortran5 amd64 8.3.0-6 [581 kB]
Get:4 http://deb.debian.org/debian buster/main amd64 libgfortran-8-dev amd64 8.3.0-6 [616 kB]
Get:5 http://deb.debian.org/debian buster/main amd64 gfortran-8 amd64 8.3.0-6 [9375 kB]
Get:6 http://deb.debian.org/debian buster/main amd64 gfortran amd64 4:8.3.0-1 [1432 B]
Get:7 http://deb.debian.org/debian buster/main amd64 libblas3 amd64 3.8.0-2 [148 kB]
Get:8 http://deb.debian.org/debian buster/main amd64 liblapack3 amd64 3.8.0-2 [2110 kB]
Get:9 http://deb.debian.org/debian buster/main amd64 libodbc1 amd64 2.3.6-0.1 [223 kB]
Get:10 http://deb.debian.org/debian buster/main amd64 libpaper1 amd64 1.1.28 [21.3 kB]
Get:11 http://deb.debian.org/debian buster/main amd64 libpaper-utils amd64 1.1.28 [18.0 kB]
Get:12 http://deb.debian.org/debian buster/main amd64 odbcinst1debian2 amd64 2.3.6-0.1 [78.1 kB]
Get:13 http://deb.debian.org/debian buster/main amd64 odbcinst amd64 2.3.6-0.1 [48.4 kB]
Get:14 http://deb.debian.org/debian buster/main amd64 zip amd64 3.0-11+b1 [234 kB]
Get:15 http://deb.debian.org/debian buster/main amd64 xdg-utils all 1.1.3-1+deb10u1 [73.7 kB]
Get:16 http://deb.debian.org/debian buster/main amd64 r-base-core amd64 3.5.2-1 [23.9 MB]
Get:17 http://deb.debian.org/debian buster/main amd64 r-cran-rodbc amd64 1.3-15-1+b2 [848 kB]
Get:18 http://deb.debian.org/debian buster/main amd64 unixodbc amd64 2.3.6-0.1 [60.9 kB]
Get:19 http://deb.debian.org/debian buster/main amd64 unixodbc-dev amd64 2.3.6-0.1 [261 kB]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 40.7 MB in 3s (12.7 MB/s)
Selecting previously unselected package fonts-dejavu-extra.
(Reading database ... 24721 files and directories currently installed.)
Preparing to unpack .../00-fonts-dejavu-extra_2.37-1_all.deb ...
Unpacking fonts-dejavu-extra (2.37-1) ...
Selecting previously unselected package fonts-dejavu.
Preparing to unpack .../01-fonts-dejavu_2.37-1_all.deb ...
Unpacking fonts-dejavu (2.37-1) ...
Selecting previously unselected package libgfortran5:amd64.
Preparing to unpack .../02-libgfortran5_8.3.0-6_amd64.deb ...
Unpacking libgfortran5:amd64 (8.3.0-6) ...
Selecting previously unselected package libgfortran-8-dev:amd64.
Preparing to unpack .../03-libgfortran-8-dev_8.3.0-6_amd64.deb ...
Unpacking libgfortran-8-dev:amd64 (8.3.0-6) ...
Selecting previously unselected package gfortran-8.
Preparing to unpack .../04-gfortran-8_8.3.0-6_amd64.deb ...
Unpacking gfortran-8 (8.3.0-6) ...
Selecting previously unselected package gfortran.
Preparing to unpack .../05-gfortran_4%3a8.3.0-1_amd64.deb ...
Unpacking gfortran (4:8.3.0-1) ...
Selecting previously unselected package libblas3:amd64.
Preparing to unpack .../06-libblas3_3.8.0-2_amd64.deb ...
Unpacking libblas3:amd64 (3.8.0-2) ...
Selecting previously unselected package liblapack3:amd64.
Preparing to unpack .../07-liblapack3_3.8.0-2_amd64.deb ...
Unpacking liblapack3:amd64 (3.8.0-2) ...
Selecting previously unselected package libodbc1:amd64.
Preparing to unpack .../08-libodbc1_2.3.6-0.1_amd64.deb ...
Unpacking libodbc1:amd64 (2.3.6-0.1) ...
Selecting previously unselected package libpaper1:amd64.
Preparing to unpack .../09-libpaper1_1.1.28_amd64.deb ...
Unpacking libpaper1:amd64 (1.1.28) ...
Selecting previously unselected package libpaper-utils.
Preparing to unpack .../10-libpaper-utils_1.1.28_amd64.deb ...
Unpacking libpaper-utils (1.1.28) ...
Selecting previously unselected package odbcinst1debian2:amd64.
Preparing to unpack .../11-odbcinst1debian2_2.3.6-0.1_amd64.deb ...
Unpacking odbcinst1debian2:amd64 (2.3.6-0.1) ...
Selecting previously unselected package odbcinst.
Preparing to unpack .../12-odbcinst_2.3.6-0.1_amd64.deb ...
Unpacking odbcinst (2.3.6-0.1) ...
Selecting previously unselected package zip.
Preparing to unpack .../13-zip_3.0-11+b1_amd64.deb ...
Unpacking zip (3.0-11+b1) ...
Selecting previously unselected package xdg-utils.
Preparing to unpack .../14-xdg-utils_1.1.3-1+deb10u1_all.deb ...
Unpacking xdg-utils (1.1.3-1+deb10u1) ...
Selecting previously unselected package r-base-core.
Preparing to unpack .../15-r-base-core_3.5.2-1_amd64.deb ...
Unpacking r-base-core (3.5.2-1) ...
Selecting previously unselected package r-cran-rodbc.
Preparing to unpack .../16-r-cran-rodbc_1.3-15-1+b2_amd64.deb ...
Unpacking r-cran-rodbc (1.3-15-1+b2) ...
Selecting previously unselected package unixodbc.
Preparing to unpack .../17-unixodbc_2.3.6-0.1_amd64.deb ...
Unpacking unixodbc (2.3.6-0.1) ...
Selecting previously unselected package unixodbc-dev:amd64.
Preparing to unpack .../18-unixodbc-dev_2.3.6-0.1_amd64.deb ...
Unpacking unixodbc-dev:amd64 (2.3.6-0.1) ...
Setting up libpaper1:amd64 (1.1.28) ...
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline

Creating config file /etc/papersize with new version
Setting up libpaper-utils (1.1.28) ...
Setting up zip (3.0-11+b1) ...
Setting up libodbc1:amd64 (2.3.6-0.1) ...
Setting up libgfortran5:amd64 (8.3.0-6) ...
Setting up fonts-dejavu-extra (2.37-1) ...
Setting up xdg-utils (1.1.3-1+deb10u1) ...
Setting up libgfortran-8-dev:amd64 (8.3.0-6) ...
Setting up fonts-dejavu (2.37-1) ...
Setting up gfortran-8 (8.3.0-6) ...
Setting up gfortran (4:8.3.0-1) ...
update-alternatives: using /usr/bin/gfortran to provide /usr/bin/f95 (f95) in auto mode
update-alternatives: using /usr/bin/gfortran to provide /usr/bin/f77 (f77) in auto mode
Setting up libblas3:amd64 (3.8.0-2) ...
update-alternatives: using /usr/lib/x86_64-linux-gnu/blas/libblas.so.3 to provide /usr/lib/x86_64-linux-gnu/libblas.so.3 (libblas.so.3-x86_64-linux-gnu) in auto mode
Setting up liblapack3:amd64 (3.8.0-2) ...
update-alternatives: using /usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3 to provide /usr/lib/x86_64-linux-gnu/liblapack.so.3 (liblapack.so.3-x86_64-linux-gnu) in auto mode
Setting up r-base-core (3.5.2-1) ...
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline

Creating config file /etc/R/Renviron with new version
Setting up r-cran-rodbc (1.3-15-1+b2) ...
Setting up odbcinst (2.3.6-0.1) ...
Setting up odbcinst1debian2:amd64 (2.3.6-0.1) ...
Setting up unixodbc-dev:amd64 (2.3.6-0.1) ...
Setting up unixodbc (2.3.6-0.1) ...
Processing triggers for libc-bin (2.28-10) ...
Processing triggers for fontconfig (2.13.1-2) ...
Processing triggers for mime-support (3.62) ...
Processing triggers for hicolor-icon-theme (0.17-2) ...
Removing intermediate container ce37769f5862
 ---> 007604e4242c
Step 10/15 : USER $NB_UID
 ---> Running in 9e32a669d7ee
Removing intermediate container 9e32a669d7ee
 ---> b50fe4712d59
Step 11/15 : ENV PATH=$CONDA_DIR/bin:${PATH}
 ---> Running in 98031594b09b
Removing intermediate container 98031594b09b
 ---> 811a59340530
Step 12/15 : RUN cd /tmp &&     curl --silent --show-error --output miniconda-installer.sh https://repo.anaconda.com/miniconda/Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh &&     echo "${MINICONDA_MD5} *miniconda-installer.sh" | md5sum -c - &&     /bin/bash miniconda-installer.sh -f -b -p $CONDA_DIR &&     rm miniconda-installer.sh &&     conda config --system --prepend channels conda-forge &&     conda config --system --set auto_update_conda false &&     conda config --system --set show_channel_urls true &&     conda install --quiet --yes conda="${CONDA_VERSION%.*}.*" &&     conda update --all --quiet --yes &&     conda clean --all -f -y &&     rm -rf /home/$NB_USER/.cache/yarn
 ---> Running in 6bb3929b95f3
miniconda-installer.sh: OK
PREFIX=/opt/conda
reinstalling: python-3.7.3-h0371630_0 ...
using -f (force) option
Python 3.7.3
reinstalling: ca-certificates-2019.1.23-0 ...
using -f (force) option
reinstalling: libgcc-ng-8.2.0-hdf63c60_1 ...
using -f (force) option
reinstalling: libstdcxx-ng-8.2.0-hdf63c60_1 ...
using -f (force) option
reinstalling: libffi-3.2.1-hd88cf55_4 ...
using -f (force) option
reinstalling: ncurses-6.1-he6710b0_1 ...
using -f (force) option
reinstalling: openssl-1.1.1b-h7b6447c_1 ...
using -f (force) option
reinstalling: xz-5.2.4-h14c3975_4 ...
using -f (force) option
reinstalling: yaml-0.1.7-had09818_2 ...
using -f (force) option
reinstalling: zlib-1.2.11-h7b6447c_3 ...
using -f (force) option
reinstalling: libedit-3.1.20181209-hc058e9b_0 ...
using -f (force) option
reinstalling: readline-7.0-h7b6447c_5 ...
using -f (force) option
reinstalling: tk-8.6.8-hbc83047_0 ...
using -f (force) option
reinstalling: sqlite-3.27.2-h7b6447c_0 ...
using -f (force) option
reinstalling: asn1crypto-0.24.0-py37_0 ...
using -f (force) option
reinstalling: certifi-2019.3.9-py37_0 ...
using -f (force) option
reinstalling: chardet-3.0.4-py37_1 ...
using -f (force) option
reinstalling: idna-2.8-py37_0 ...
using -f (force) option
reinstalling: pycosat-0.6.3-py37h14c3975_0 ...
using -f (force) option
reinstalling: pycparser-2.19-py37_0 ...
using -f (force) option
reinstalling: pysocks-1.6.8-py37_0 ...
using -f (force) option
reinstalling: ruamel_yaml-0.15.46-py37h14c3975_0 ...
using -f (force) option
reinstalling: six-1.12.0-py37_0 ...
using -f (force) option
reinstalling: cffi-1.12.2-py37h2e261b9_1 ...
using -f (force) option
reinstalling: setuptools-41.0.0-py37_0 ...
using -f (force) option
reinstalling: cryptography-2.6.1-py37h1ba5d50_0 ...
using -f (force) option
reinstalling: wheel-0.33.1-py37_0 ...
using -f (force) option
reinstalling: pip-19.0.3-py37_0 ...
using -f (force) option
reinstalling: pyopenssl-19.0.0-py37_0 ...
using -f (force) option
reinstalling: urllib3-1.24.1-py37_0 ...
using -f (force) option
reinstalling: requests-2.21.0-py37_0 ...
using -f (force) option
reinstalling: conda-4.6.14-py37_0 ...
using -f (force) option
using -f (force) option
installation finished.
Collecting package metadata: ...working... done
Solving environment: ...working... done

## Package Plan ##

  environment location: /opt/conda

  added / updated specs:
    - conda=4.6


The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    _libgcc_mutex-0.1          |      conda_forge           3 KB  conda-forge
    _openmp_mutex-4.5          |            1_gnu          22 KB  conda-forge
    ca-certificates-2020.12.5  |       ha878542_0         137 KB  conda-forge
    certifi-2020.12.5          |   py37h89c1867_1         143 KB  conda-forge
    conda-4.6.14               |           py37_0         2.1 MB  conda-forge
    libgcc-ng-9.3.0            |      h2828fa1_18         7.8 MB  conda-forge
    libgomp-9.3.0              |      h2828fa1_18         376 KB  conda-forge
    openssl-1.1.1i             |       h7f98852_0         2.1 MB  conda-forge
    python_abi-3.7             |          1_cp37m           4 KB  conda-forge
    ------------------------------------------------------------
                                           Total:        12.7 MB

The following NEW packages will be INSTALLED:

  _libgcc_mutex      conda-forge/linux-64::_libgcc_mutex-0.1-conda_forge
  _openmp_mutex      conda-forge/linux-64::_openmp_mutex-4.5-1_gnu
  libgomp            conda-forge/linux-64::libgomp-9.3.0-h2828fa1_18
  python_abi         conda-forge/linux-64::python_abi-3.7-1_cp37m

The following packages will be UPDATED:

  ca-certificates    pkgs/main::ca-certificates-2019.1.23-0 --> conda-forge::ca-certificates-2020.12.5-ha878542_0
  certifi                pkgs/main::certifi-2019.3.9-py37_0 --> conda-forge::certifi-2020.12.5-py37h89c1867_1
  libgcc-ng           pkgs/main::libgcc-ng-8.2.0-hdf63c60_1 --> conda-forge::libgcc-ng-9.3.0-h2828fa1_18
  openssl              pkgs/main::openssl-1.1.1b-h7b6447c_1 --> conda-forge::openssl-1.1.1i-h7f98852_0

The following packages will be SUPERSEDED by a higher-priority channel:

  conda                                           pkgs/main --> conda-forge


Preparing transaction: ...working... done
Verifying transaction: ...working... done
Executing transaction: ...working... done
Collecting package metadata: ...working... done
Solving environment: ...working... done

## Package Plan ##

  environment location: /opt/conda


The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    asn1crypto-1.4.0           |     pyh9f0ad1d_0          78 KB  conda-forge
    brotlipy-0.7.0             |py37h5e8e339_1001         341 KB  conda-forge
    cffi-1.14.4                |   py37hc58025e_1         225 KB  conda-forge
    chardet-4.0.0              |   py37h89c1867_1         204 KB  conda-forge
    conda-4.9.2                |   py37h89c1867_0         3.0 MB  conda-forge
    conda-package-handling-1.7.2|   py37hb5d75c8_0         915 KB  conda-forge
    cryptography-3.3.1         |   py37h7f0c10b_1         626 KB  conda-forge
    idna-2.10                  |     pyh9f0ad1d_0          52 KB  conda-forge
    ld_impl_linux-64-2.35.1    |       hea4e1c9_1         617 KB  conda-forge
    libedit-3.1.20191231       |       he28a2e2_2         121 KB  conda-forge
    libffi-3.3                 |       h58526e2_2          51 KB  conda-forge
    libstdcxx-ng-9.3.0         |      h6de172a_18         4.0 MB  conda-forge
    ncurses-6.2                |       h58526e2_4         985 KB  conda-forge
    pip-20.3.3                 |     pyhd8ed1ab_0         1.1 MB  conda-forge
    pycosat-0.6.3              |py37h5e8e339_1006         107 KB  conda-forge
    pycparser-2.20             |     pyh9f0ad1d_2          94 KB  conda-forge
    pyopenssl-20.0.1           |     pyhd8ed1ab_0          48 KB  conda-forge
    pysocks-1.7.1              |   py37h89c1867_3          27 KB  conda-forge
    python-3.7.9               |hffdb5ce_0_cpython        57.3 MB  conda-forge
    readline-8.0               |       he28a2e2_2         281 KB  conda-forge
    requests-2.25.1            |     pyhd3deb0d_0          51 KB  conda-forge
    ruamel_yaml-0.15.80        |py37h5e8e339_1003         262 KB  conda-forge
    setuptools-49.6.0          |   py37h89c1867_3         947 KB  conda-forge
    six-1.15.0                 |     pyh9f0ad1d_0          14 KB  conda-forge
    sqlite-3.34.0              |       h74cdb3f_0         1.4 MB  conda-forge
    tk-8.6.10                  |       h21135ba_1         3.2 MB  conda-forge
    tqdm-4.56.0                |     pyhd8ed1ab_0          76 KB  conda-forge
    urllib3-1.26.2             |     pyhd8ed1ab_0          98 KB  conda-forge
    wheel-0.36.2               |     pyhd3deb0d_0          31 KB  conda-forge
    xz-5.2.5                   |       h516909a_1         343 KB  conda-forge
    yaml-0.2.5                 |       h516909a_0          82 KB  conda-forge
    zlib-1.2.11                |    h516909a_1010         106 KB  conda-forge
    ------------------------------------------------------------
                                           Total:        76.8 MB

The following NEW packages will be INSTALLED:

  brotlipy           conda-forge/linux-64::brotlipy-0.7.0-py37h5e8e339_1001
  conda-package-han~ conda-forge/linux-64::conda-package-handling-1.7.2-py37hb5d75c8_0
  ld_impl_linux-64   conda-forge/linux-64::ld_impl_linux-64-2.35.1-hea4e1c9_1
  tqdm               conda-forge/noarch::tqdm-4.56.0-pyhd8ed1ab_0

The following packages will be UPDATED:

  asn1crypto         pkgs/main/linux-64::asn1crypto-0.24.0~ --> conda-forge/noarch::asn1crypto-1.4.0-pyh9f0ad1d_0
  cffi                pkgs/main::cffi-1.12.2-py37h2e261b9_1 --> conda-forge::cffi-1.14.4-py37hc58025e_1
  chardet                   pkgs/main::chardet-3.0.4-py37_1 --> conda-forge::chardet-4.0.0-py37h89c1867_1
  conda                                       4.6.14-py37_0 --> 4.9.2-py37h89c1867_0
  cryptography       pkgs/main::cryptography-2.6.1-py37h1b~ --> conda-forge::cryptography-3.3.1-py37h7f0c10b_1
  idna                  pkgs/main/linux-64::idna-2.8-py37_0 --> conda-forge/noarch::idna-2.10-pyh9f0ad1d_0
  libedit            pkgs/main::libedit-3.1.20181209-hc058~ --> conda-forge::libedit-3.1.20191231-he28a2e2_2
  libffi                 pkgs/main::libffi-3.2.1-hd88cf55_4 --> conda-forge::libffi-3.3-h58526e2_2
  libstdcxx-ng       pkgs/main::libstdcxx-ng-8.2.0-hdf63c6~ --> conda-forge::libstdcxx-ng-9.3.0-h6de172a_18
  ncurses                 pkgs/main::ncurses-6.1-he6710b0_1 --> conda-forge::ncurses-6.2-h58526e2_4
  pip                 pkgs/main/linux-64::pip-19.0.3-py37_0 --> conda-forge/noarch::pip-20.3.3-pyhd8ed1ab_0
  pycosat            pkgs/main::pycosat-0.6.3-py37h14c3975~ --> conda-forge::pycosat-0.6.3-py37h5e8e339_1006
  pycparser          pkgs/main/linux-64::pycparser-2.19-py~ --> conda-forge/noarch::pycparser-2.20-pyh9f0ad1d_2
  pyopenssl          pkgs/main/linux-64::pyopenssl-19.0.0-~ --> conda-forge/noarch::pyopenssl-20.0.1-pyhd8ed1ab_0
  pysocks                   pkgs/main::pysocks-1.6.8-py37_0 --> conda-forge::pysocks-1.7.1-py37h89c1867_3
  python                 pkgs/main::python-3.7.3-h0371630_0 --> conda-forge::python-3.7.9-hffdb5ce_0_cpython
  readline               pkgs/main::readline-7.0-h7b6447c_5 --> conda-forge::readline-8.0-he28a2e2_2
  requests           pkgs/main/linux-64::requests-2.21.0-p~ --> conda-forge/noarch::requests-2.25.1-pyhd3deb0d_0
  ruamel_yaml        pkgs/main::ruamel_yaml-0.15.46-py37h1~ --> conda-forge::ruamel_yaml-0.15.80-py37h5e8e339_1003
  setuptools            pkgs/main::setuptools-41.0.0-py37_0 --> conda-forge::setuptools-49.6.0-py37h89c1867_3
  six                 pkgs/main/linux-64::six-1.12.0-py37_0 --> conda-forge/noarch::six-1.15.0-pyh9f0ad1d_0
  sqlite                pkgs/main::sqlite-3.27.2-h7b6447c_0 --> conda-forge::sqlite-3.34.0-h74cdb3f_0
  tk                         pkgs/main::tk-8.6.8-hbc83047_0 --> conda-forge::tk-8.6.10-h21135ba_1
  urllib3            pkgs/main/linux-64::urllib3-1.24.1-py~ --> conda-forge/noarch::urllib3-1.26.2-pyhd8ed1ab_0
  wheel              pkgs/main/linux-64::wheel-0.33.1-py37~ --> conda-forge/noarch::wheel-0.36.2-pyhd3deb0d_0
  xz                         pkgs/main::xz-5.2.4-h14c3975_4 --> conda-forge::xz-5.2.5-h516909a_1
  yaml                     pkgs/main::yaml-0.1.7-had09818_2 --> conda-forge::yaml-0.2.5-h516909a_0
  zlib                    pkgs/main::zlib-1.2.11-h7b6447c_3 --> conda-forge::zlib-1.2.11-h516909a_1010


Preparing transaction: ...working... done
Verifying transaction: ...working... done
Executing transaction: ...working... done
Remove all contents from the following package caches?
  - /opt/conda/pkgs
Removing intermediate container 6bb3929b95f3
 ---> 8c22bf42bbec
Step 13/15 : RUN conda install --quiet --yes     'r-base=4.0.0'     'r-caret=6.*'     'r-crayon=1.3*'     'r-devtools=2.3*'     'r-forecast=8.12*'     'r-hexbin=1.28*'     'r-htmltools=0.4*'     'r-htmlwidgets=1.5*'     'r-irkernel=1.1*'     'r-rmarkdown=2.2*'     'r-rodbc=1.3*'     'r-rsqlite=2.2*'     'r-shiny=1.4*'     'r-tidyverse=1.3*'     'unixodbc=2.3.*'     'r-tidymodels=0.1*'     'r-reticulate=1.*'     &&     pip install --quiet --no-cache-dir     'boto3>1.0<2.0'     'sagemaker>2.0<3.0' &&     conda clean --all -f -y
 ---> Running in 07945a289f86
Collecting package metadata (current_repodata.json): ...working... done
Solving environment: ...working... failed with initial frozen solve. Retrying with flexible solve.
Collecting package metadata (repodata.json): ...working... done
Solving environment: ...working... done

## Package Plan ##

  environment location: /opt/conda

  added / updated specs:
    - r-base=4.0.0
    - r-caret=6
    - r-crayon=1.3
    - r-devtools=2.3
    - r-forecast=8.12
    - r-hexbin=1.28
    - r-htmltools=0.4
    - r-htmlwidgets=1.5
    - r-irkernel=1.1
    - r-reticulate=1
    - r-rmarkdown=2.2
    - r-rodbc=1.3
    - r-rsqlite=2.2
    - r-shiny=1.4
    - r-tidymodels=0.1
    - r-tidyverse=1.3
    - unixodbc=2.3


The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    _r-mutex-1.0.1             |      anacondar_1           3 KB  conda-forge
    binutils_impl_linux-64-2.35.1|       h193b22a_1         9.3 MB  conda-forge
    binutils_linux-64-2.35     |      hc3fd857_29          22 KB  conda-forge
    bwidget-1.9.14             |       ha770c72_0         119 KB  conda-forge
    bzip2-1.0.8                |       h7f98852_4         484 KB  conda-forge
    c-ares-1.17.1              |       h36c2ea0_0         111 KB  conda-forge
    cairo-1.16.0               |    hcf35c78_1003         1.5 MB  conda-forge
    conda-4.6.14               |           py37_0         2.1 MB  conda-forge
    curl-7.71.1                |       he644dc0_8         139 KB  conda-forge
    fontconfig-2.13.1          |    hba837de_1004         344 KB  conda-forge
    freetype-2.10.4            |       h0708190_1         890 KB  conda-forge
    fribidi-1.0.10             |       h36c2ea0_0         112 KB  conda-forge
    gcc_impl_linux-64-7.5.0    |      habd7529_18        38.3 MB  conda-forge
    gcc_linux-64-7.5.0         |      he2a3fca_29          23 KB  conda-forge
    gettext-0.19.8.1           |    h0b5b191_1005         3.6 MB  conda-forge
    gfortran_impl_linux-64-7.5.0|      h56cb351_18         9.0 MB  conda-forge
    gfortran_linux-64-7.5.0    |      ha081f1e_29          22 KB  conda-forge
    glib-2.66.4                |       hcd2ae1e_1         494 KB  conda-forge
    graphite2-1.3.13           |    h58526e2_1001         102 KB  conda-forge
    gsl-2.6                    |       he838d99_2         3.2 MB  conda-forge
    gxx_impl_linux-64-7.5.0    |      hd0bb8aa_18         9.5 MB  conda-forge
    gxx_linux-64-7.5.0         |      h547f3ba_29          22 KB  conda-forge
    harfbuzz-2.4.0             |       h9f30f68_3         1.5 MB  conda-forge
    icu-64.2                   |       he1b5a44_1        12.6 MB  conda-forge
    jpeg-9d                    |       h36c2ea0_0         264 KB  conda-forge
    kernel-headers_linux-64-2.6.32|      h77966d4_13         707 KB  conda-forge
    krb5-1.17.2                |       h926e7f8_0         1.4 MB  conda-forge
    libblas-3.9.0              |       7_openblas          11 KB  conda-forge
    libcblas-3.9.0             |       7_openblas          11 KB  conda-forge
    libcurl-7.71.1             |       hcdd3856_8         312 KB  conda-forge
    libev-4.33                 |       h516909a_1         104 KB  conda-forge
    libgcc-devel_linux-64-7.5.0|      hda03d7c_18         3.6 MB  conda-forge
    libgfortran-ng-7.5.0       |      h14aa051_18          22 KB  conda-forge
    libgfortran4-7.5.0         |      h14aa051_18         1.3 MB  conda-forge
    libglib-2.66.4             |       h164308a_1         3.0 MB  conda-forge
    libiconv-1.16              |       h516909a_0         1.4 MB  conda-forge
    liblapack-3.9.0            |       7_openblas          11 KB  conda-forge
    libnghttp2-1.41.0          |       h8cfc5f6_2         774 KB  conda-forge
    libopenblas-0.3.12         |pthreads_hb3c22a3_1         8.2 MB  conda-forge
    libpng-1.6.37              |       h21135ba_2         306 KB  conda-forge
    libsodium-1.0.18           |       h36c2ea0_1         366 KB  conda-forge
    libssh2-1.9.0              |       hab1572f_5         225 KB  conda-forge
    libstdcxx-devel_linux-64-7.5.0|      hb016644_18         9.3 MB  conda-forge
    libtiff-4.2.0              |       hdc55705_0         633 KB  conda-forge
    libuuid-2.32.1             |    h7f98852_1000          28 KB  conda-forge
    libwebp-base-1.1.0         |       h36c2ea0_3         864 KB  conda-forge
    libxcb-1.13                |    h7f98852_1003         395 KB  conda-forge
    libxml2-2.9.10             |       hee79883_0         1.3 MB  conda-forge
    lz4-c-1.9.3                |       h9c3ff4c_0         179 KB  conda-forge
    make-4.3                   |       hd18ef5c_1         507 KB  conda-forge
    pandoc-2.11.3.2            |       h7f98852_0        17.8 MB  conda-forge
    pango-1.42.4               |       h7062337_4         521 KB  conda-forge
    pcre-8.44                  |       he1b5a44_0         261 KB  conda-forge
    pcre2-10.34                |       h2f06484_0         664 KB  conda-forge
    pixman-0.38.0              |    h516909a_1003         594 KB  conda-forge
    pthread-stubs-0.4          |    h36c2ea0_1001           5 KB  conda-forge
    r-askpass-1.1              |    r40hcdcec82_2          28 KB  conda-forge
    r-assertthat-0.2.1         |    r40h6115d3f_2          70 KB  conda-forge
    r-backports-1.2.1          |    r40hcfec24a_0          99 KB  conda-forge
    r-base-4.0.0               |       hdca8982_3        23.6 MB  conda-forge
    r-base64enc-0.1_3          | r40hcdcec82_1004          43 KB  conda-forge
    r-bit-4.0.4                |    r40hcdcec82_0         618 KB  conda-forge
    r-bit64-4.0.5              |    r40hcdcec82_0         510 KB  conda-forge
    r-blob-1.2.1               |    r40h6115d3f_1          63 KB  conda-forge
    r-brew-1.0_6               | r40h6115d3f_1003          93 KB  conda-forge
    r-brio-1.1.0               |    r40h9e2df91_1          37 KB  conda-forge
    r-broom-0.7.3              |    r40hc72bb7e_0         1.7 MB  conda-forge
    r-callr-3.5.1              |    r40h142f84f_0         388 KB  conda-forge
    r-caret-6.0_86             |    r40hcdcec82_2         6.0 MB  conda-forge
    r-cellranger-1.1.0         | r40h6115d3f_1003         108 KB  conda-forge
    r-class-7.3_17             |    r40hcfec24a_1         107 KB  conda-forge
    r-cli-2.2.0                |    r40hc72bb7e_0         443 KB  conda-forge
    r-clipr-0.7.1              |    r40h142f84f_0          65 KB  conda-forge
    r-codetools-0.2_18         |    r40hc72bb7e_0         106 KB  conda-forge
    r-colorspace-2.0_0         |    r40h9e2df91_0         2.5 MB  conda-forge
    r-commonmark-1.7           | r40hcdcec82_1002         152 KB  conda-forge
    r-covr-3.5.1               |    r40h0357c0b_0         302 KB  conda-forge
    r-cpp11-0.2.5              |    r40hc72bb7e_0         153 KB  conda-forge
    r-crayon-1.3.4             | r40h6115d3f_1003         747 KB  conda-forge
    r-crosstalk-1.1.1          |    r40hc72bb7e_0         664 KB  conda-forge
    r-curl-4.3                 |    r40hcdcec82_1         698 KB  conda-forge
    r-data.table-1.13.6        |    r40hcfec24a_0         2.0 MB  conda-forge
    r-dbi-1.1.1                |    r40hc72bb7e_0         685 KB  conda-forge
    r-dbplyr-2.0.0             |    r40hc72bb7e_0         749 KB  conda-forge
    r-desc-1.2.0               | r40h6115d3f_1003         291 KB  conda-forge
    r-devtools-2.3.2           |    r40h6115d3f_0         343 KB  conda-forge
    r-dials-0.0.9              |    r40h6115d3f_0         747 KB  conda-forge
    r-dicedesign-1.8_1         |    r40hcdcec82_1         337 KB  conda-forge
    r-diffobj-0.3.3            |    r40hcfec24a_0         1.0 MB  conda-forge
    r-digest-0.6.27            |    r40h1b71b39_0         213 KB  conda-forge
    r-dplyr-1.0.3              |    r40h03ef668_0         1.1 MB  conda-forge
    r-dt-0.17                  |    r40hc72bb7e_0         1.5 MB  conda-forge
    r-ellipsis-0.3.1           |    r40hcdcec82_0          49 KB  conda-forge
    r-evaluate-0.14            |    r40h6115d3f_2          81 KB  conda-forge
    r-fansi-0.4.2              |    r40hcfec24a_0         199 KB  conda-forge
    r-farver-2.0.3             |    r40h0357c0b_1         1.4 MB  conda-forge
    r-fastmap-1.0.1            |    r40h0357c0b_1          55 KB  conda-forge
    r-forcats-0.5.0            |    r40h6115d3f_1         375 KB  conda-forge
    r-foreach-1.5.1            |    r40h142f84f_0         132 KB  conda-forge
    r-forecast-8.12            |    r40hc8faad4_1         1.7 MB  conda-forge
    r-fracdiff-1.5_1           |    r40hc57f18f_1         116 KB  conda-forge
    r-fs-1.5.0                 |    r40h0357c0b_0         515 KB  conda-forge
    r-furrr-0.2.1              |    r40h142f84f_0        1003 KB  conda-forge
    r-future-1.21.0            |    r40hc72bb7e_0         582 KB  conda-forge
    r-generics-0.1.0           |    r40hc72bb7e_0          83 KB  conda-forge
    r-ggplot2-3.3.3            |    r40hc72bb7e_0         3.9 MB  conda-forge
    r-gh-1.2.0                 |    r40hc72bb7e_0         133 KB  conda-forge
    r-git2r-0.28.0             |    r40hf628c3e_0         894 KB  conda-forge
    r-gitcreds-0.1.1           |    r40hc72bb7e_0          92 KB  conda-forge
    r-globals-0.14.0           |    r40hc72bb7e_0         108 KB  conda-forge
    r-glue-1.4.2               |    r40hcfec24a_0         141 KB  conda-forge
    r-gower-0.2.2              |    r40hcdcec82_0         176 KB  conda-forge
    r-gpfit-1.0_8              |    r40h6115d3f_1          88 KB  conda-forge
    r-gtable-0.3.0             |    r40h6115d3f_3         423 KB  conda-forge
    r-hardhat-0.1.5            |    r40hc72bb7e_0         784 KB  conda-forge
    r-haven-2.3.1              |    r40hde08347_0         373 KB  conda-forge
    r-hexbin-1.28.1            |    r40h31ca83e_2         1.1 MB  conda-forge
    r-highr-0.8                |    r40h6115d3f_2          58 KB  conda-forge
    r-hms-1.0.0                |    r40hc72bb7e_0         105 KB  conda-forge
    r-htmltools-0.4.0          |    r40h0357c0b_1         218 KB  conda-forge
    r-htmlwidgets-1.5.3        |    r40hc72bb7e_0         272 KB  conda-forge
    r-httpuv-1.5.5             |    r40h03ef668_0         910 KB  conda-forge
    r-httr-1.4.2               |    r40h6115d3f_0         493 KB  conda-forge
    r-infer-0.5.4              |    r40hc72bb7e_0         1.8 MB  conda-forge
    r-ini-0.3.1                | r40h6115d3f_1003          30 KB  conda-forge
    r-ipred-0.9_9              |    r40hcdcec82_2         393 KB  conda-forge
    r-irdisplay-0.7            |         r40_1002          35 KB  conda-forge
    r-irkernel-1.1.1           |    r40h6115d3f_0         234 KB  conda-forge
    r-isoband-0.2.3            |    r40h03ef668_0         2.1 MB  conda-forge
    r-iterators-1.0.13         |    r40h142f84f_0         338 KB  conda-forge
    r-jsonlite-1.7.2           |    r40hcfec24a_0         462 KB  conda-forge
    r-kernsmooth-2.23_18       |    r40h7679c2e_0         104 KB  conda-forge
    r-knitr-1.30               |    r40h6115d3f_0         1.3 MB  conda-forge
    r-labeling-0.4.2           |    r40h142f84f_0          67 KB  conda-forge
    r-later-1.1.0.1            |    r40h0357c0b_0         156 KB  conda-forge
    r-lattice-0.20_41          |    r40hcfec24a_3         1.1 MB  conda-forge
    r-lava-1.6.8.1             |    r40hc72bb7e_0         2.4 MB  conda-forge
    r-lazyeval-0.2.2           |    r40hcdcec82_2         164 KB  conda-forge
    r-lhs-1.1.1                |    r40h1b71b39_0         410 KB  conda-forge
    r-lifecycle-0.2.0          |    r40h6115d3f_1         112 KB  conda-forge
    r-listenv-0.8.0            |    r40h6115d3f_1         118 KB  conda-forge
    r-lmtest-0.9_38            |    r40h31ca83e_1         409 KB  conda-forge
    r-lubridate-1.7.9.2        |    r40he524a50_0         1.1 MB  conda-forge
    r-magrittr-2.0.1           |    r40hcfec24a_1         211 KB  conda-forge
    r-markdown-1.1             |    r40hcfec24a_1         142 KB  conda-forge
    r-mass-7.3_53              |    r40hcfec24a_0         1.1 MB  conda-forge
    r-matrix-1.3_2             |    r40he454529_0         3.9 MB  conda-forge
    r-memoise-1.1.0            | r40h6115d3f_1004          42 KB  conda-forge
    r-mgcv-1.8_33              |    r40h7fa42b6_0         2.9 MB  conda-forge
    r-mime-0.9                 |    r40hcfec24a_1          51 KB  conda-forge
    r-modeldata-0.1.0          |    r40h142f84f_0         4.8 MB  conda-forge
    r-modelmetrics-1.2.2.2     |    r40h0357c0b_1         161 KB  conda-forge
    r-modelr-0.1.8             |    r40h6115d3f_0         218 KB  conda-forge
    r-munsell-0.5.0            | r40h6115d3f_1003         246 KB  conda-forge
    r-nlme-3.1_150             |    r40h31ca83e_0         2.3 MB  conda-forge
    r-nnet-7.3_14              |    r40hcfec24a_1         132 KB  conda-forge
    r-numderiv-2016.8_1.1      |    r40h6115d3f_3         125 KB  conda-forge
    r-openssl-1.4.3            |    r40he5c4762_0         1.2 MB  conda-forge
    r-parallelly-1.23.0        |    r40hc72bb7e_0         228 KB  conda-forge
    r-parsnip-0.1.4            |    r40h142f84f_0         746 KB  conda-forge
    r-pbdzmq-0.3_4             |    r40h534d7c8_0         527 KB  conda-forge
    r-pillar-1.4.7             |    r40hc72bb7e_0         194 KB  conda-forge
    r-pkgbuild-1.2.0           |    r40hc72bb7e_0         157 KB  conda-forge
    r-pkgconfig-2.0.3          |    r40h6115d3f_1          24 KB  conda-forge
    r-pkgload-1.1.0            |    r40h0357c0b_0         167 KB  conda-forge
    r-plogr-0.2.0              | r40h6115d3f_1003          19 KB  conda-forge
    r-plyr-1.8.6               |    r40h0357c0b_1         830 KB  conda-forge
    r-praise-1.0.0             | r40h6115d3f_1004          23 KB  conda-forge
    r-prettyunits-1.1.1        |    r40h6115d3f_1          40 KB  conda-forge
    r-proc-1.17.0.1            |    r40h03ef668_0         847 KB  conda-forge
    r-processx-3.4.5           |    r40hcfec24a_0         296 KB  conda-forge
    r-prodlim-2019.11.13       |    r40h0357c0b_1         411 KB  conda-forge
    r-progress-1.2.2           |    r40h6115d3f_2          90 KB  conda-forge
    r-promises-1.1.1           |    r40h0357c0b_0         1.2 MB  conda-forge
    r-ps-1.5.0                 |    r40hcfec24a_0         290 KB  conda-forge
    r-purrr-0.3.4              |    r40hcdcec82_1         410 KB  conda-forge
    r-quadprog-1.5_8           |    r40hed91ed1_3          46 KB  conda-forge
    r-quantmod-0.4.18          |    r40hc72bb7e_0         1.0 MB  conda-forge
    r-r6-2.5.0                 |    r40hc72bb7e_0          89 KB  conda-forge
    r-rappdirs-0.3.1           | r40hcdcec82_1004         150 KB  conda-forge
    r-rcmdcheck-1.3.3          |    r40h6115d3f_3         139 KB  conda-forge
    r-rcolorbrewer-1.1_2       | r40h6115d3f_1003          59 KB  conda-forge
    r-rcpp-1.0.6               |    r40h03ef668_0         2.0 MB  conda-forge
    r-rcpparmadillo-0.10.1.2.2 |    r40h306847c_0         1.2 MB  conda-forge
    r-readr-1.4.0              |    r40h1b71b39_0         861 KB  conda-forge
    r-readxl-1.3.1             |    r40hde08347_4         859 KB  conda-forge
    r-recipes-0.1.15           |    r40hc72bb7e_0         1.1 MB  conda-forge
    r-rematch-1.0.1            | r40h6115d3f_1003          19 KB  conda-forge
    r-rematch2-2.1.2           |    r40h6115d3f_1          52 KB  conda-forge
    r-remotes-2.2.0            |    r40h6115d3f_0         392 KB  conda-forge
    r-repr-1.1.0               |    r40h6115d3f_1         143 KB  conda-forge
    r-reprex-0.3.0             |    r40h6115d3f_2         429 KB  conda-forge
    r-reshape2-1.4.4           |    r40h0357c0b_1         135 KB  conda-forge
    r-reticulate-1.18          |    r40h1b71b39_0         1.1 MB  conda-forge
    r-rex-1.2.0                |    r40h6115d3f_1         131 KB  conda-forge
    r-rlang-0.4.10             |    r40hcfec24a_0         1.1 MB  conda-forge
    r-rmarkdown-2.2            |    r40h6115d3f_0         3.0 MB  conda-forge
    r-rodbc-1.3_16             |    r40hcdcec82_1         862 KB  conda-forge
    r-roxygen2-7.1.1           |    r40h0357c0b_0         657 KB  conda-forge
    r-rpart-4.1_15             |    r40hcfec24a_2         730 KB  conda-forge
    r-rprojroot-2.0.2          |    r40hc72bb7e_0         110 KB  conda-forge
    r-rsample-0.0.8            |    r40h6115d3f_0         407 KB  conda-forge
    r-rsqlite-2.2.2            |    r40h03ef668_0         1.1 MB  conda-forge
    r-rstudioapi-0.13          |    r40hc72bb7e_0         281 KB  conda-forge
    r-rversions-2.0.2          |    r40h6115d3f_0          72 KB  conda-forge
    r-rvest-0.3.6              |    r40h6115d3f_0         1.5 MB  conda-forge
    r-scales-1.1.1             |    r40h6115d3f_0         556 KB  conda-forge
    r-selectr-0.4_2            |    r40h6115d3f_1         449 KB  conda-forge
    r-sessioninfo-1.1.1        | r40h6115d3f_1002          57 KB  conda-forge
    r-shiny-1.4.0.2            |    r40h6115d3f_1         4.3 MB  conda-forge
    r-slider-0.1.5             |    r40hcdcec82_0         249 KB  conda-forge
    r-sourcetools-0.1.7        | r40he1b5a44_1002          52 KB  conda-forge
    r-squarem-2021.1           |    r40hc72bb7e_0         194 KB  conda-forge
    r-stringi-1.4.6            |    r40h0e574ca_2         778 KB  conda-forge
    r-stringr-1.4.0            |    r40h6115d3f_2         209 KB  conda-forge
    r-survival-3.2_7           |    r40hcdcec82_0         7.6 MB  conda-forge
    r-sys-3.4                  |    r40hcdcec82_0          48 KB  conda-forge
    r-testthat-3.0.1           |    r40h03ef668_0         1.6 MB  conda-forge
    r-tibble-3.0.5             |    r40hcfec24a_0         669 KB  conda-forge
    r-tidymodels-0.1.2         |    r40hc72bb7e_0          72 KB  conda-forge
    r-tidyr-1.1.2              |    r40h0357c0b_0         806 KB  conda-forge
    r-tidyselect-1.1.0         |    r40h6115d3f_0         204 KB  conda-forge
    r-tidyverse-1.3.0          |    r40h6115d3f_2         452 KB  conda-forge
    r-timedate-3043.102        | r40h6115d3f_1002         1.6 MB  conda-forge
    r-tinytex-0.28             |    r40hc72bb7e_0         120 KB  conda-forge
    r-tseries-0.10_47          |    r40hed91ed1_5         390 KB  conda-forge
    r-ttr-0.24.2               |    r40hcdcec82_0         523 KB  conda-forge
    r-tune-0.1.2               |    r40hc72bb7e_0         2.0 MB  conda-forge
    r-urca-1.3_0               | r40h31ca83e_1006         1.1 MB  conda-forge
    r-usethis-1.6.3            |    r40h6115d3f_0         581 KB  conda-forge
    r-utf8-1.1.4               | r40hcdcec82_1003         161 KB  conda-forge
    r-uuid-0.1_4               |    r40hcdcec82_1          22 KB  conda-forge
    r-vctrs-0.3.6              |    r40hcfec24a_0         1.1 MB  conda-forge
    r-viridislite-0.3.0        | r40h6115d3f_1003          64 KB  conda-forge
    r-waldo-0.2.3              |    r40hc72bb7e_0          78 KB  conda-forge
    r-warp-0.2.0               |    r40h0eb13af_0          73 KB  conda-forge
    r-whisker-0.4              |    r40h6115d3f_1          80 KB  conda-forge
    r-withr-2.4.0              |    r40hc72bb7e_0         217 KB  conda-forge
    r-workflows-0.2.1          |    r40h142f84f_0         178 KB  conda-forge
    r-xfun-0.20                |    r40hcfec24a_0         299 KB  conda-forge
    r-xml2-1.3.2               |    r40h0357c0b_1         247 KB  conda-forge
    r-xopen-1.0.0              | r40h6115d3f_1003          28 KB  conda-forge
    r-xtable-1.8_4             |    r40h6115d3f_3         697 KB  conda-forge
    r-xts-0.12.1               |    r40hcdcec82_0         906 KB  conda-forge
    r-yaml-2.2.1               |    r40hcfec24a_1         119 KB  conda-forge
    r-yardstick-0.0.7          |    r40h0357c0b_0         784 KB  conda-forge
    r-zeallot-0.1.0            | r40h6115d3f_1002          62 KB  conda-forge
    r-zoo-1.8_8                |    r40hcdcec82_0         1.1 MB  conda-forge
    sed-4.8                    |       he412f7d_0         264 KB  conda-forge
    sysroot_linux-64-2.12      |      h77966d4_13        30.2 MB  conda-forge
    tktable-2.10               |       hb7b940f_3          89 KB  conda-forge
    unixodbc-2.3.9             |       h0e019cf_0         293 KB  conda-forge
    xorg-kbproto-1.0.7         |    h7f98852_1002          27 KB  conda-forge
    xorg-libice-1.0.10         |       h516909a_0          57 KB  conda-forge
    xorg-libsm-1.2.3           |    h84519dc_1000          25 KB  conda-forge
    xorg-libx11-1.6.12         |       h516909a_0         917 KB  conda-forge
    xorg-libxau-1.0.9          |       h7f98852_0          13 KB  conda-forge
    xorg-libxdmcp-1.1.3        |       h7f98852_0          19 KB  conda-forge
    xorg-libxext-1.3.4         |       h516909a_0          51 KB  conda-forge
    xorg-libxrender-0.9.10     |    h516909a_1002          31 KB  conda-forge
    xorg-renderproto-0.11.1    |    h14c3975_1002           8 KB  conda-forge
    xorg-xextproto-7.3.0       |    h7f98852_1002          28 KB  conda-forge
    xorg-xproto-7.0.31         |    h7f98852_1007          73 KB  conda-forge
    zeromq-4.3.3               |       h58526e2_3         288 KB  conda-forge
    zstd-1.4.8                 |       ha95c52a_1         702 KB  conda-forge
    ------------------------------------------------------------
                                           Total:       332.2 MB

The following NEW packages will be INSTALLED:

  _r-mutex           conda-forge/noarch::_r-mutex-1.0.1-anacondar_1
  binutils_impl_lin~ conda-forge/linux-64::binutils_impl_linux-64-2.35.1-h193b22a_1
  binutils_linux-64  conda-forge/linux-64::binutils_linux-64-2.35-hc3fd857_29
  bwidget            conda-forge/linux-64::bwidget-1.9.14-ha770c72_0
  bzip2              conda-forge/linux-64::bzip2-1.0.8-h7f98852_4
  c-ares             conda-forge/linux-64::c-ares-1.17.1-h36c2ea0_0
  cairo              conda-forge/linux-64::cairo-1.16.0-hcf35c78_1003
  curl               conda-forge/linux-64::curl-7.71.1-he644dc0_8
  fontconfig         conda-forge/linux-64::fontconfig-2.13.1-hba837de_1004
  freetype           conda-forge/linux-64::freetype-2.10.4-h0708190_1
  fribidi            conda-forge/linux-64::fribidi-1.0.10-h36c2ea0_0
  gcc_impl_linux-64  conda-forge/linux-64::gcc_impl_linux-64-7.5.0-habd7529_18
  gcc_linux-64       conda-forge/linux-64::gcc_linux-64-7.5.0-he2a3fca_29
  gettext            conda-forge/linux-64::gettext-0.19.8.1-h0b5b191_1005
  gfortran_impl_lin~ conda-forge/linux-64::gfortran_impl_linux-64-7.5.0-h56cb351_18
  gfortran_linux-64  conda-forge/linux-64::gfortran_linux-64-7.5.0-ha081f1e_29
  glib               conda-forge/linux-64::glib-2.66.4-hcd2ae1e_1
  graphite2          conda-forge/linux-64::graphite2-1.3.13-h58526e2_1001
  gsl                conda-forge/linux-64::gsl-2.6-he838d99_2
  gxx_impl_linux-64  conda-forge/linux-64::gxx_impl_linux-64-7.5.0-hd0bb8aa_18
  gxx_linux-64       conda-forge/linux-64::gxx_linux-64-7.5.0-h547f3ba_29
  harfbuzz           conda-forge/linux-64::harfbuzz-2.4.0-h9f30f68_3
  icu                conda-forge/linux-64::icu-64.2-he1b5a44_1
  jpeg               conda-forge/linux-64::jpeg-9d-h36c2ea0_0
  kernel-headers_li~ conda-forge/noarch::kernel-headers_linux-64-2.6.32-h77966d4_13
  krb5               conda-forge/linux-64::krb5-1.17.2-h926e7f8_0
  libblas            conda-forge/linux-64::libblas-3.9.0-7_openblas
  libcblas           conda-forge/linux-64::libcblas-3.9.0-7_openblas
  libcurl            conda-forge/linux-64::libcurl-7.71.1-hcdd3856_8
  libev              conda-forge/linux-64::libev-4.33-h516909a_1
  libgcc-devel_linu~ conda-forge/linux-64::libgcc-devel_linux-64-7.5.0-hda03d7c_18
  libgfortran-ng     conda-forge/linux-64::libgfortran-ng-7.5.0-h14aa051_18
  libgfortran4       conda-forge/linux-64::libgfortran4-7.5.0-h14aa051_18
  libglib            conda-forge/linux-64::libglib-2.66.4-h164308a_1
  libiconv           conda-forge/linux-64::libiconv-1.16-h516909a_0
  liblapack          conda-forge/linux-64::liblapack-3.9.0-7_openblas
  libnghttp2         conda-forge/linux-64::libnghttp2-1.41.0-h8cfc5f6_2
  libopenblas        conda-forge/linux-64::libopenblas-0.3.12-pthreads_hb3c22a3_1
  libpng             conda-forge/linux-64::libpng-1.6.37-h21135ba_2
  libsodium          conda-forge/linux-64::libsodium-1.0.18-h36c2ea0_1
  libssh2            conda-forge/linux-64::libssh2-1.9.0-hab1572f_5
  libstdcxx-devel_l~ conda-forge/linux-64::libstdcxx-devel_linux-64-7.5.0-hb016644_18
  libtiff            conda-forge/linux-64::libtiff-4.2.0-hdc55705_0
  libuuid            conda-forge/linux-64::libuuid-2.32.1-h7f98852_1000
  libwebp-base       conda-forge/linux-64::libwebp-base-1.1.0-h36c2ea0_3
  libxcb             conda-forge/linux-64::libxcb-1.13-h7f98852_1003
  libxml2            conda-forge/linux-64::libxml2-2.9.10-hee79883_0
  lz4-c              conda-forge/linux-64::lz4-c-1.9.3-h9c3ff4c_0
  make               conda-forge/linux-64::make-4.3-hd18ef5c_1
  pandoc             conda-forge/linux-64::pandoc-2.11.3.2-h7f98852_0
  pango              conda-forge/linux-64::pango-1.42.4-h7062337_4
  pcre               conda-forge/linux-64::pcre-8.44-he1b5a44_0
  pcre2              conda-forge/linux-64::pcre2-10.34-h2f06484_0
  pixman             conda-forge/linux-64::pixman-0.38.0-h516909a_1003
  pthread-stubs      conda-forge/linux-64::pthread-stubs-0.4-h36c2ea0_1001
  r-askpass          conda-forge/linux-64::r-askpass-1.1-r40hcdcec82_2
  r-assertthat       conda-forge/noarch::r-assertthat-0.2.1-r40h6115d3f_2
  r-backports        conda-forge/linux-64::r-backports-1.2.1-r40hcfec24a_0
  r-base             conda-forge/linux-64::r-base-4.0.0-hdca8982_3
  r-base64enc        conda-forge/linux-64::r-base64enc-0.1_3-r40hcdcec82_1004
  r-bit              conda-forge/linux-64::r-bit-4.0.4-r40hcdcec82_0
  r-bit64            conda-forge/linux-64::r-bit64-4.0.5-r40hcdcec82_0
  r-blob             conda-forge/noarch::r-blob-1.2.1-r40h6115d3f_1
  r-brew             conda-forge/noarch::r-brew-1.0_6-r40h6115d3f_1003
  r-brio             conda-forge/linux-64::r-brio-1.1.0-r40h9e2df91_1
  r-broom            conda-forge/noarch::r-broom-0.7.3-r40hc72bb7e_0
  r-callr            conda-forge/noarch::r-callr-3.5.1-r40h142f84f_0
  r-caret            conda-forge/linux-64::r-caret-6.0_86-r40hcdcec82_2
  r-cellranger       conda-forge/noarch::r-cellranger-1.1.0-r40h6115d3f_1003
  r-class            conda-forge/linux-64::r-class-7.3_17-r40hcfec24a_1
  r-cli              conda-forge/noarch::r-cli-2.2.0-r40hc72bb7e_0
  r-clipr            conda-forge/noarch::r-clipr-0.7.1-r40h142f84f_0
  r-codetools        conda-forge/noarch::r-codetools-0.2_18-r40hc72bb7e_0
  r-colorspace       conda-forge/linux-64::r-colorspace-2.0_0-r40h9e2df91_0
  r-commonmark       conda-forge/linux-64::r-commonmark-1.7-r40hcdcec82_1002
  r-covr             conda-forge/linux-64::r-covr-3.5.1-r40h0357c0b_0
  r-cpp11            conda-forge/noarch::r-cpp11-0.2.5-r40hc72bb7e_0
  r-crayon           conda-forge/noarch::r-crayon-1.3.4-r40h6115d3f_1003
  r-crosstalk        conda-forge/noarch::r-crosstalk-1.1.1-r40hc72bb7e_0
  r-curl             conda-forge/linux-64::r-curl-4.3-r40hcdcec82_1
  r-data.table       conda-forge/linux-64::r-data.table-1.13.6-r40hcfec24a_0
  r-dbi              conda-forge/noarch::r-dbi-1.1.1-r40hc72bb7e_0
  r-dbplyr           conda-forge/noarch::r-dbplyr-2.0.0-r40hc72bb7e_0
  r-desc             conda-forge/noarch::r-desc-1.2.0-r40h6115d3f_1003
  r-devtools         conda-forge/noarch::r-devtools-2.3.2-r40h6115d3f_0
  r-dials            conda-forge/noarch::r-dials-0.0.9-r40h6115d3f_0
  r-dicedesign       conda-forge/linux-64::r-dicedesign-1.8_1-r40hcdcec82_1
  r-diffobj          conda-forge/linux-64::r-diffobj-0.3.3-r40hcfec24a_0
  r-digest           conda-forge/linux-64::r-digest-0.6.27-r40h1b71b39_0
  r-dplyr            conda-forge/linux-64::r-dplyr-1.0.3-r40h03ef668_0
  r-dt               conda-forge/noarch::r-dt-0.17-r40hc72bb7e_0
  r-ellipsis         conda-forge/linux-64::r-ellipsis-0.3.1-r40hcdcec82_0
  r-evaluate         conda-forge/noarch::r-evaluate-0.14-r40h6115d3f_2
  r-fansi            conda-forge/linux-64::r-fansi-0.4.2-r40hcfec24a_0
  r-farver           conda-forge/linux-64::r-farver-2.0.3-r40h0357c0b_1
  r-fastmap          conda-forge/linux-64::r-fastmap-1.0.1-r40h0357c0b_1
  r-forcats          conda-forge/noarch::r-forcats-0.5.0-r40h6115d3f_1
  r-foreach          conda-forge/noarch::r-foreach-1.5.1-r40h142f84f_0
  r-forecast         conda-forge/linux-64::r-forecast-8.12-r40hc8faad4_1
  r-fracdiff         conda-forge/linux-64::r-fracdiff-1.5_1-r40hc57f18f_1
  r-fs               conda-forge/linux-64::r-fs-1.5.0-r40h0357c0b_0
  r-furrr            conda-forge/noarch::r-furrr-0.2.1-r40h142f84f_0
  r-future           conda-forge/noarch::r-future-1.21.0-r40hc72bb7e_0
  r-generics         conda-forge/noarch::r-generics-0.1.0-r40hc72bb7e_0
  r-ggplot2          conda-forge/noarch::r-ggplot2-3.3.3-r40hc72bb7e_0
  r-gh               conda-forge/noarch::r-gh-1.2.0-r40hc72bb7e_0
  r-git2r            conda-forge/linux-64::r-git2r-0.28.0-r40hf628c3e_0
  r-gitcreds         conda-forge/noarch::r-gitcreds-0.1.1-r40hc72bb7e_0
  r-globals          conda-forge/noarch::r-globals-0.14.0-r40hc72bb7e_0
  r-glue             conda-forge/linux-64::r-glue-1.4.2-r40hcfec24a_0
  r-gower            conda-forge/linux-64::r-gower-0.2.2-r40hcdcec82_0
  r-gpfit            conda-forge/noarch::r-gpfit-1.0_8-r40h6115d3f_1
  r-gtable           conda-forge/noarch::r-gtable-0.3.0-r40h6115d3f_3
  r-hardhat          conda-forge/noarch::r-hardhat-0.1.5-r40hc72bb7e_0
  r-haven            conda-forge/linux-64::r-haven-2.3.1-r40hde08347_0
  r-hexbin           conda-forge/linux-64::r-hexbin-1.28.1-r40h31ca83e_2
  r-highr            conda-forge/noarch::r-highr-0.8-r40h6115d3f_2
  r-hms              conda-forge/noarch::r-hms-1.0.0-r40hc72bb7e_0
  r-htmltools        conda-forge/linux-64::r-htmltools-0.4.0-r40h0357c0b_1
  r-htmlwidgets      conda-forge/noarch::r-htmlwidgets-1.5.3-r40hc72bb7e_0
  r-httpuv           conda-forge/linux-64::r-httpuv-1.5.5-r40h03ef668_0
  r-httr             conda-forge/noarch::r-httr-1.4.2-r40h6115d3f_0
  r-infer            conda-forge/noarch::r-infer-0.5.4-r40hc72bb7e_0
  r-ini              conda-forge/noarch::r-ini-0.3.1-r40h6115d3f_1003
  r-ipred            conda-forge/linux-64::r-ipred-0.9_9-r40hcdcec82_2
  r-irdisplay        conda-forge/noarch::r-irdisplay-0.7-r40_1002
  r-irkernel         conda-forge/noarch::r-irkernel-1.1.1-r40h6115d3f_0
  r-isoband          conda-forge/linux-64::r-isoband-0.2.3-r40h03ef668_0
  r-iterators        conda-forge/noarch::r-iterators-1.0.13-r40h142f84f_0
  r-jsonlite         conda-forge/linux-64::r-jsonlite-1.7.2-r40hcfec24a_0
  r-kernsmooth       conda-forge/linux-64::r-kernsmooth-2.23_18-r40h7679c2e_0
  r-knitr            conda-forge/noarch::r-knitr-1.30-r40h6115d3f_0
  r-labeling         conda-forge/noarch::r-labeling-0.4.2-r40h142f84f_0
  r-later            conda-forge/linux-64::r-later-1.1.0.1-r40h0357c0b_0
  r-lattice          conda-forge/linux-64::r-lattice-0.20_41-r40hcfec24a_3
  r-lava             conda-forge/noarch::r-lava-1.6.8.1-r40hc72bb7e_0
  r-lazyeval         conda-forge/linux-64::r-lazyeval-0.2.2-r40hcdcec82_2
  r-lhs              conda-forge/linux-64::r-lhs-1.1.1-r40h1b71b39_0
  r-lifecycle        conda-forge/noarch::r-lifecycle-0.2.0-r40h6115d3f_1
  r-listenv          conda-forge/noarch::r-listenv-0.8.0-r40h6115d3f_1
  r-lmtest           conda-forge/linux-64::r-lmtest-0.9_38-r40h31ca83e_1
  r-lubridate        conda-forge/linux-64::r-lubridate-1.7.9.2-r40he524a50_0
  r-magrittr         conda-forge/linux-64::r-magrittr-2.0.1-r40hcfec24a_1
  r-markdown         conda-forge/linux-64::r-markdown-1.1-r40hcfec24a_1
  r-mass             conda-forge/linux-64::r-mass-7.3_53-r40hcfec24a_0
  r-matrix           conda-forge/linux-64::r-matrix-1.3_2-r40he454529_0
  r-memoise          conda-forge/noarch::r-memoise-1.1.0-r40h6115d3f_1004
  r-mgcv             conda-forge/linux-64::r-mgcv-1.8_33-r40h7fa42b6_0
  r-mime             conda-forge/linux-64::r-mime-0.9-r40hcfec24a_1
  r-modeldata        conda-forge/noarch::r-modeldata-0.1.0-r40h142f84f_0
  r-modelmetrics     conda-forge/linux-64::r-modelmetrics-1.2.2.2-r40h0357c0b_1
  r-modelr           conda-forge/noarch::r-modelr-0.1.8-r40h6115d3f_0
  r-munsell          conda-forge/noarch::r-munsell-0.5.0-r40h6115d3f_1003
  r-nlme             conda-forge/linux-64::r-nlme-3.1_150-r40h31ca83e_0
  r-nnet             conda-forge/linux-64::r-nnet-7.3_14-r40hcfec24a_1
  r-numderiv         conda-forge/noarch::r-numderiv-2016.8_1.1-r40h6115d3f_3
  r-openssl          conda-forge/linux-64::r-openssl-1.4.3-r40he5c4762_0
  r-parallelly       conda-forge/noarch::r-parallelly-1.23.0-r40hc72bb7e_0
  r-parsnip          conda-forge/noarch::r-parsnip-0.1.4-r40h142f84f_0
  r-pbdzmq           conda-forge/linux-64::r-pbdzmq-0.3_4-r40h534d7c8_0
  r-pillar           conda-forge/noarch::r-pillar-1.4.7-r40hc72bb7e_0
  r-pkgbuild         conda-forge/noarch::r-pkgbuild-1.2.0-r40hc72bb7e_0
  r-pkgconfig        conda-forge/noarch::r-pkgconfig-2.0.3-r40h6115d3f_1
  r-pkgload          conda-forge/linux-64::r-pkgload-1.1.0-r40h0357c0b_0
  r-plogr            conda-forge/noarch::r-plogr-0.2.0-r40h6115d3f_1003
  r-plyr             conda-forge/linux-64::r-plyr-1.8.6-r40h0357c0b_1
  r-praise           conda-forge/noarch::r-praise-1.0.0-r40h6115d3f_1004
  r-prettyunits      conda-forge/noarch::r-prettyunits-1.1.1-r40h6115d3f_1
  r-proc             conda-forge/linux-64::r-proc-1.17.0.1-r40h03ef668_0
  r-processx         conda-forge/linux-64::r-processx-3.4.5-r40hcfec24a_0
  r-prodlim          conda-forge/linux-64::r-prodlim-2019.11.13-r40h0357c0b_1
  r-progress         conda-forge/noarch::r-progress-1.2.2-r40h6115d3f_2
  r-promises         conda-forge/linux-64::r-promises-1.1.1-r40h0357c0b_0
  r-ps               conda-forge/linux-64::r-ps-1.5.0-r40hcfec24a_0
  r-purrr            conda-forge/linux-64::r-purrr-0.3.4-r40hcdcec82_1
  r-quadprog         conda-forge/linux-64::r-quadprog-1.5_8-r40hed91ed1_3
  r-quantmod         conda-forge/noarch::r-quantmod-0.4.18-r40hc72bb7e_0
  r-r6               conda-forge/noarch::r-r6-2.5.0-r40hc72bb7e_0
  r-rappdirs         conda-forge/linux-64::r-rappdirs-0.3.1-r40hcdcec82_1004
  r-rcmdcheck        conda-forge/noarch::r-rcmdcheck-1.3.3-r40h6115d3f_3
  r-rcolorbrewer     conda-forge/noarch::r-rcolorbrewer-1.1_2-r40h6115d3f_1003
  r-rcpp             conda-forge/linux-64::r-rcpp-1.0.6-r40h03ef668_0
  r-rcpparmadillo    conda-forge/linux-64::r-rcpparmadillo-0.10.1.2.2-r40h306847c_0
  r-readr            conda-forge/linux-64::r-readr-1.4.0-r40h1b71b39_0
  r-readxl           conda-forge/linux-64::r-readxl-1.3.1-r40hde08347_4
  r-recipes          conda-forge/noarch::r-recipes-0.1.15-r40hc72bb7e_0
  r-rematch          conda-forge/noarch::r-rematch-1.0.1-r40h6115d3f_1003
  r-rematch2         conda-forge/noarch::r-rematch2-2.1.2-r40h6115d3f_1
  r-remotes          conda-forge/noarch::r-remotes-2.2.0-r40h6115d3f_0
  r-repr             conda-forge/noarch::r-repr-1.1.0-r40h6115d3f_1
  r-reprex           conda-forge/noarch::r-reprex-0.3.0-r40h6115d3f_2
  r-reshape2         conda-forge/linux-64::r-reshape2-1.4.4-r40h0357c0b_1
  r-reticulate       conda-forge/linux-64::r-reticulate-1.18-r40h1b71b39_0
  r-rex              conda-forge/noarch::r-rex-1.2.0-r40h6115d3f_1
  r-rlang            conda-forge/linux-64::r-rlang-0.4.10-r40hcfec24a_0
  r-rmarkdown        conda-forge/noarch::r-rmarkdown-2.2-r40h6115d3f_0
  r-rodbc            conda-forge/linux-64::r-rodbc-1.3_16-r40hcdcec82_1
  r-roxygen2         conda-forge/linux-64::r-roxygen2-7.1.1-r40h0357c0b_0
  r-rpart            conda-forge/linux-64::r-rpart-4.1_15-r40hcfec24a_2
  r-rprojroot        conda-forge/noarch::r-rprojroot-2.0.2-r40hc72bb7e_0
  r-rsample          conda-forge/noarch::r-rsample-0.0.8-r40h6115d3f_0
  r-rsqlite          conda-forge/linux-64::r-rsqlite-2.2.2-r40h03ef668_0
  r-rstudioapi       conda-forge/noarch::r-rstudioapi-0.13-r40hc72bb7e_0
  r-rversions        conda-forge/noarch::r-rversions-2.0.2-r40h6115d3f_0
  r-rvest            conda-forge/noarch::r-rvest-0.3.6-r40h6115d3f_0
  r-scales           conda-forge/noarch::r-scales-1.1.1-r40h6115d3f_0
  r-selectr          conda-forge/noarch::r-selectr-0.4_2-r40h6115d3f_1
  r-sessioninfo      conda-forge/noarch::r-sessioninfo-1.1.1-r40h6115d3f_1002
  r-shiny            conda-forge/noarch::r-shiny-1.4.0.2-r40h6115d3f_1
  r-slider           conda-forge/linux-64::r-slider-0.1.5-r40hcdcec82_0
  r-sourcetools      conda-forge/linux-64::r-sourcetools-0.1.7-r40he1b5a44_1002
  r-squarem          conda-forge/noarch::r-squarem-2021.1-r40hc72bb7e_0
  r-stringi          conda-forge/linux-64::r-stringi-1.4.6-r40h0e574ca_2
  r-stringr          conda-forge/noarch::r-stringr-1.4.0-r40h6115d3f_2
  r-survival         conda-forge/linux-64::r-survival-3.2_7-r40hcdcec82_0
  r-sys              conda-forge/linux-64::r-sys-3.4-r40hcdcec82_0
  r-testthat         conda-forge/linux-64::r-testthat-3.0.1-r40h03ef668_0
  r-tibble           conda-forge/linux-64::r-tibble-3.0.5-r40hcfec24a_0
  r-tidymodels       conda-forge/noarch::r-tidymodels-0.1.2-r40hc72bb7e_0
  r-tidyr            conda-forge/linux-64::r-tidyr-1.1.2-r40h0357c0b_0
  r-tidyselect       conda-forge/linux-64::r-tidyselect-1.1.0-r40h6115d3f_0
  r-tidyverse        conda-forge/noarch::r-tidyverse-1.3.0-r40h6115d3f_2
  r-timedate         conda-forge/noarch::r-timedate-3043.102-r40h6115d3f_1002
  r-tinytex          conda-forge/noarch::r-tinytex-0.28-r40hc72bb7e_0
  r-tseries          conda-forge/linux-64::r-tseries-0.10_47-r40hed91ed1_5
  r-ttr              conda-forge/linux-64::r-ttr-0.24.2-r40hcdcec82_0
  r-tune             conda-forge/noarch::r-tune-0.1.2-r40hc72bb7e_0
  r-urca             conda-forge/linux-64::r-urca-1.3_0-r40h31ca83e_1006
  r-usethis          conda-forge/noarch::r-usethis-1.6.3-r40h6115d3f_0
  r-utf8             conda-forge/linux-64::r-utf8-1.1.4-r40hcdcec82_1003
  r-uuid             conda-forge/linux-64::r-uuid-0.1_4-r40hcdcec82_1
  r-vctrs            conda-forge/linux-64::r-vctrs-0.3.6-r40hcfec24a_0
  r-viridislite      conda-forge/noarch::r-viridislite-0.3.0-r40h6115d3f_1003
  r-waldo            conda-forge/noarch::r-waldo-0.2.3-r40hc72bb7e_0
  r-warp             conda-forge/linux-64::r-warp-0.2.0-r40h0eb13af_0
  r-whisker          conda-forge/noarch::r-whisker-0.4-r40h6115d3f_1
  r-withr            conda-forge/noarch::r-withr-2.4.0-r40hc72bb7e_0
  r-workflows        conda-forge/noarch::r-workflows-0.2.1-r40h142f84f_0
  r-xfun             conda-forge/linux-64::r-xfun-0.20-r40hcfec24a_0
  r-xml2             conda-forge/linux-64::r-xml2-1.3.2-r40h0357c0b_1
  r-xopen            conda-forge/noarch::r-xopen-1.0.0-r40h6115d3f_1003
  r-xtable           conda-forge/noarch::r-xtable-1.8_4-r40h6115d3f_3
  r-xts              conda-forge/linux-64::r-xts-0.12.1-r40hcdcec82_0
  r-yaml             conda-forge/linux-64::r-yaml-2.2.1-r40hcfec24a_1
  r-yardstick        conda-forge/linux-64::r-yardstick-0.0.7-r40h0357c0b_0
  r-zeallot          conda-forge/noarch::r-zeallot-0.1.0-r40h6115d3f_1002
  r-zoo              conda-forge/linux-64::r-zoo-1.8_8-r40hcdcec82_0
  sed                conda-forge/linux-64::sed-4.8-he412f7d_0
  sysroot_linux-64   conda-forge/noarch::sysroot_linux-64-2.12-h77966d4_13
  tktable            conda-forge/linux-64::tktable-2.10-hb7b940f_3
  unixodbc           conda-forge/linux-64::unixodbc-2.3.9-h0e019cf_0
  xorg-kbproto       conda-forge/linux-64::xorg-kbproto-1.0.7-h7f98852_1002
  xorg-libice        conda-forge/linux-64::xorg-libice-1.0.10-h516909a_0
  xorg-libsm         conda-forge/linux-64::xorg-libsm-1.2.3-h84519dc_1000
  xorg-libx11        conda-forge/linux-64::xorg-libx11-1.6.12-h516909a_0
  xorg-libxau        conda-forge/linux-64::xorg-libxau-1.0.9-h7f98852_0
  xorg-libxdmcp      conda-forge/linux-64::xorg-libxdmcp-1.1.3-h7f98852_0
  xorg-libxext       conda-forge/linux-64::xorg-libxext-1.3.4-h516909a_0
  xorg-libxrender    conda-forge/linux-64::xorg-libxrender-0.9.10-h516909a_1002
  xorg-renderproto   conda-forge/linux-64::xorg-renderproto-0.11.1-h14c3975_1002
  xorg-xextproto     conda-forge/linux-64::xorg-xextproto-7.3.0-h7f98852_1002
  xorg-xproto        conda-forge/linux-64::xorg-xproto-7.0.31-h7f98852_1007
  zeromq             conda-forge/linux-64::zeromq-4.3.3-h58526e2_3
  zstd               conda-forge/linux-64::zstd-1.4.8-ha95c52a_1

The following packages will be DOWNGRADED:

  conda                                4.9.2-py37h89c1867_0 --> 4.6.14-py37_0


Preparing transaction: ...working... done
Verifying transaction: ...working... done
Executing transaction: ...working... done
Remove all contents from the following package caches?
  - /opt/conda/pkgs
Removing intermediate container 07945a289f86
 ---> d2ec305324c0
Step 14/15 : WORKDIR $HOME
 ---> Running in 0a32d12ac13f
Removing intermediate container 0a32d12ac13f
 ---> 9e3ff09239c6
Step 15/15 : USER $NB_UID
 ---> Running in e9e8cb334766
Removing intermediate container e9e8cb334766
 ---> 214d10bd4c3a
Successfully built 214d10bd4c3a
Successfully tagged smstudio-r:latest
Successfully tagged <acct-id>.dkr.ecr.us-west-2.amazonaws.com/smstudio-custom:r
$ docker push <acct-id>.dkr.ecr.us-west-2.amazonaws.com/smstudio-custom:r
The push refers to repository [<acct-id>.dkr.ecr.us-west-2.amazonaws.com/smstudio-custom]
6e4f32055e27: Pushed
bba88b8e2beb: Pushed
25794ecf33cd: Pushed
3a3e4feb409c: Pushed
0180b4a734cc: Pushed
c1544b13dc7d: Pushed
94d2eef34804: Pushed
b6f245391ec4: Pushed
cd702377e4e5: Pushed
aa7af8a465c6: Pushed
ef9a7b8862f4: Pushed
a1f2f42922b1: Pushed
4762552ad7d8: Pushed
r: digest: sha256:4f609d19482c62da3f856a835052969e457b6aeebb55f2c45b1dec94fbac931c size: 3066
```
