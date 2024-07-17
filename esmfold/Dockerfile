FROM nvidia/cuda:11.8.0-cudnn8-devel-ubuntu20.04

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y cuda-command-line-tools-12-1 curl git wget build-essential g++ python3.8 python3-pip 

RUN pip3 install networkx
RUN pip3 install scipy
RUN pip3 install omegaconf
RUN pip3 install ml_collections
RUN pip3 install dm-tree
RUN pip3 install Bio
RUN pip3 install biotite
RUN pip3 install matplotlib
RUN pip3 install einops
RUN pip3 install torch==2.0.1 torchvision==0.15.2 torchaudio==2.0.2 --index-url https://download.pytorch.org/whl/cu118
RUN pip3 install pytorch-lightning==1.8.4 
RUN pip3 install git+https://github.com/facebookresearch/esm.git 
RUN pip3 install 'dllogger @ git+https://github.com/NVIDIA/dllogger.git'
RUN pip3 install 'openfold @ git+https://github.com/aqlaboratory/openfold.git@4b41059694619831a7db195b7e0988fc4ff3a307'

