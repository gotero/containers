FROM nvidia/cuda:11.8.0-cudnn8-devel-ubuntu20.04 AS build

# Use bash to support string substitution.
SHELL ["/bin/bash", "-c"]

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y software-properties-common \
    && add-apt-repository -y ppa:ubuntu-toolchain-r/test \
    && apt-get update && apt-get install -y g++-11

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -y --no-install-recommends install \
    build-essential cmake git cuda-command-line-tools-11-8 wget vim neovim \
    && rm -rf /var/lib/apt/lists/*

#Install Miniconda package manager.
RUN wget -q -P /tmp https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && bash /tmp/Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda \
    && rm /tmp/Miniconda3-latest-Linux-x86_64.sh
ENV PATH="/opt/conda/bin:$PATH"
RUN conda install conda-forge::mamba

# Clone repo and install conda packages. 
RUN cd /opt && git clone --recursive https://github.com/baker-laboratory/RoseTTAFold-All-Atom \
    && cd /opt/RoseTTAFold-All-Atom \
    && mamba env create -n RFAA -f environment.yaml \
    && conda clean -ya \
    && mkdir examples/config_files \
    && mv rf2aa/config/inference/* examples/config_files

ENV PATH /opt/conda/envs/RFAA/bin:$PATH
RUN echo "source activate RFAA" >> ~/.bashrc \
    && cp ~/.bashrc /opt/conda/etc/bashrc
ENV BASH_ENV /opt/conda/etc/bashrc

RUN cd /opt/RoseTTAFold-All-Atom/rf2aa/SE3Transformer \
    && pip install --no-cache-dir -r requirements.txt \
    && python setup.py install 

COPY signalp-6.0h.fast.tar.gz /opt/RoseTTAFold-All-Atom
RUN cd /opt/RoseTTAFold-All-Atom \
    && tar xzf signalp-6.0h.fast.tar.gz \
    && cd signalp6_fast/signalp-6-package \
    && pip install --no-cache-dir -r requirements.txt \
    && python setup.py install 

RUN cd /opt/RoseTTAFold-All-Atom \
    && mkdir -p $CONDA_PREFIX/lib/python3.10/site-packages/signalp6-6.0+h-py3.10.egg/signalp/model_weights \
    && mv signalp6_fast/signalp-6-package/models/distilled_model_signalp6.pt \
    $CONDA_PREFIX/lib/python3.10/site-packages/signalp6-6.0+h-py3.10.egg/signalp/model_weights/ensemble_model_signalp6.pt 

RUN cd /opt/RoseTTAFold-All-Atom \
    && chmod +x install_dependencies.sh \
    && ./install_dependencies.sh 

RUN cd /opt/RoseTTAFold-All-Atom \
    && mkdir -p blast-2.2.26 \
    && wget https://ftp.ncbi.nlm.nih.gov/blast/executables/legacy.NOTSUPPORTED/2.2.26/blast-2.2.26-x64-linux.tar.gz \
    && tar -xf blast-2.2.26-x64-linux.tar.gz -C blast-2.2.26 \
    && cp -r blast-2.2.26/blast-2.2.26/ blast-2.2.26_bk \
    && rm -r blast-2.2.26 \
    && mv blast-2.2.26_bk/ blast-2.2.26

# delete tarballs 
RUN cd /opt/RoseTTAFold-All-Atom \
    && rm -f signalp-6.0h.fast.tar.gz blast-2.2.26-x64-linux.tar.gz csblast-2.2.3.tar.gz

WORKDIR /opt/RoseTTAFold-All-Atom
ENV PIPEDIR /opt/RoseTTAFold-All-Atom/
ENV PATH /opt/RoseTTAFold-All-Atom/:$PATH
