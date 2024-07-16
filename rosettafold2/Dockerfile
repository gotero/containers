ARG CUDA=11.7
FROM nvidia/cuda:${CUDA}.1-cudnn8-devel-ubuntu20.04 AS build
ARG CUDA

# Use bash to support string substitution.
SHELL ["/bin/bash", "-c"]
ENV TZ=Etc/UTC

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      build-essential cmake cuda-command-line-tools-${CUDA/./-} git \
      tzdata wget \
    && rm -rf /var/lib/apt/lists/*

# Install Miniconda package manager.
RUN wget -q -P /tmp https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && bash /tmp/Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda \
    && rm /tmp/Miniconda3-latest-Linux-x86_64.sh
ENV PATH="/opt/conda/bin:$PATH"

# Install conda packages
# https://github.com/uw-ipd/RoseTTAFold2/blob/main/RF2-linux.yml
RUN conda install conda-forge::mamba && mamba create -y -n RF2 -c pytorch -c nvidia -c defaults -c conda-forge -c bioconda \
    python=3.10 pip hhsuite \
    nvidia/label/cuda-11.7.1::cuda-toolkit 'pytorch=2.0.*=*cuda11*' 'torchvision=*=*cu11*' torchdata \
    pytorch-cuda=11.7 dglteam/label/cu117::dgl pyg::pyg requests pandas pydantic && \
    conda clean -ya
ENV PATH /opt/conda/envs/RF2/bin:$PATH
RUN echo "source activate RF2" > ~/.bashrc \
    && cp ~/.bashrc /opt/conda/etc/bashrc
ENV BASH_ENV /opt/conda/etc/bashrc
ENV LC_ALL en_US.utf-8
ENV LANG en_US.utf-8

# Clone repo and install conda packages.
RUN cd /opt && git clone --recursive https://github.com/uw-ipd/RoseTTAFold2.git && \
    cd /opt/RoseTTAFold2 && \
    chmod +x run_RF2.sh && \
    cd SE3Transformer && \
    pip install --no-cache-dir -r requirements.txt && \
    python setup.py install

WORKDIR /opt/RoseTTAFold2

ENV PIPEDIR /opt/RoseTTAFold2/
ENV PATH /opt/RoseTTAFold2/:$PATH
