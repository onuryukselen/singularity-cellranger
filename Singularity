BootStrap: docker
From: ubuntu:16.04

%labels

    AUTHOR Onur Yukselen <onur.yukselen@umassmed.edu>
    Version v1.0

%environment
    PATH=$PATH:/bin:/sbin:/usr/bin/bcl2fastq2-v2.17.1.14/bin:/usr/bin/cellranger-3.0.2:/usr/bin/cellranger-atac-1.2.0
    export PATH
    export LC_ALL=C

%post
    apt-get update 
    apt-get -y upgrade
    apt-get dist-upgrade
    apt-get -y install  unzip libsqlite3-dev libbz2-dev libssl-dev python python-dev \
    python-pip git libxml2-dev software-properties-common wget tree vim sed xvfb xauth xfonts-base \
    subversion g++ gcc gfortran libcurl4-openssl-dev curl zlib1g-dev build-essential libffi-dev  python-lzo 
 
    pip install --upgrade pip==9.0.3
    pip install pysam==0.15.2
    pip install numpy scipy biopython
    pip install --upgrade pip    
    pip install multiqc

    #################
    ## BCL2FASTQ v2.17.1.14
    #################
    add-apt-repository universe
    apt-get update
    apt-get -y install zip unzip zlibc libc6 libboost-all-dev cmake
    
    export TMP=/tmp/singularity/programs
    export SOURCE=${TMP}/bcl2fastq
    export BUILD=${TMP}/bcl2fastq2-v2.17.1.14-build
    export INSTALL_DIR=/usr/bin/bcl2fastq2-v2.17.1.14
    ## git clone https://github.com/onuryukselen/singularity /tmp/singularity
    mkdir -p /tmp/singularity/programs
    cd ${TMP}
    wget https://galaxyweb.umassmed.edu/pub/software/bcl2fastq2-v2.17.1.14.tar.zip
    ## wget ftp://webdata2:webdata2@ussd-ftp.illumina.com/downloads/Software/bcl2fastq/bcl2fastq2-v2.17.1.14.tar.zip
    unzip bcl2fastq2-v2.17.1.14.tar.zip
    tar -xvzf bcl2fastq2-v2.17.1.14.tar.gz
    mkdir ${BUILD}
    cd ${BUILD}
    sed -i 's@HINTS ENV C_INCLUDE_PATH ENV CPATH ENV CPLUS_INCLUDE_PATH@HINTS ENV C_INCLUDE_PATH ENV CPATH ENV CPLUS_INCLUDE_PATH /usr/include/x86_64-linux-gnu/@g' ${SOURCE}/src/cmake/macros.cmake
    sed -i 's@boost::property_tree::xml_writer_make_settings@boost::property_tree::xml_writer_make_settings<ptree::key_type>@g' ${SOURCE}/src/cxx/lib/io/Xml.cpp
    ${SOURCE}/src/configure --prefix=${INSTALL_DIR}
    make
    make install
	

    #################
    ## R ##
    #################
    NPROCS=`awk '/^processor/ {s+=1}; END{print s}' /proc/cpuinfo`
    cd /tmp 
    wget http://security.ubuntu.com/ubuntu/pool/main/i/icu/libicu52_52.1-3ubuntu0.8_amd64.deb
    dpkg -i libicu52_52.1-3ubuntu0.8_amd64.deb
    wget https://cran.rstudio.com/src/base/R-3/R-3.5.1.tar.gz
    tar xvf R-3.5.1.tar.gz
    cd /tmp/R-3.5.1
    apt-get update
    apt-get install -y libblas3 libblas-dev liblapack-dev liblapack3 ghostscript  libicu52 \
    libgmp10 libgmp-dev fort77 aptitude libpcre3-dev liblzma-dev libmariadb-client-lgpl-dev pandoc
    aptitude install -y xorg-dev libreadline-dev
    apt-get install -y bioperl
    apt-get update 
  
    ./configure --enable-R-static-lib --with-blas --with-lapack --enable-R-shlib=yes 
    echo "Will use make with $NPROCS cores."
    make -j${NPROCS}
    make install
    
    R --slave -e "source('https://bioconductor.org/biocLite.R'); biocLite()"
    R --slave -e "install.packages(c('devtools', 'gplots', 'R.utils'), dependencies = TRUE, repos='https://cloud.r-project.org', Ncpus=${NPROCS})"
    R --slave -e "install.packages(c('Seurat', 'rmarkdown'), dependencies = TRUE, repos='https://cloud.r-project.org', Ncpus=${NPROCS})"
    R --slave -e "install.packages(c('RColorBrewer', 'Cairo'), dependencies = TRUE, repos='https://cloud.r-project.org', Ncpus=${NPROCS})"
    
    
    #X11 display fix
    Xvfb :0 -ac -screen 0 1960x2000x24 &

    #################
    ## Cell Ranger ##
    #################

    cd /usr/bin
    wget https://galaxyweb.umassmed.edu/pub/software/cellranger-3.0.2.tar.gz
    tar -xzvf cellranger-3.0.2.tar.gz

    ######################
    ## Cell Ranger-ATAC ##
    ######################

   wget https://galaxyweb.umassmed.edu/pub/software/cellranger-atac-1.2.0.tar.gz
   tar -xzvf cellranger-atac-1.2.0.tar.gz


