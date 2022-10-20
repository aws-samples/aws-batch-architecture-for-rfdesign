# Original Copyright 2021 DeepMind Technologies Limited
# Modifications Copyright 2022 Amazon.com, Inc. or its affiliates. All Rights Reserved.
# SPDX-License-Identifier: Apache-2.0

ARG CUDA=11.1.1
FROM nvidia/cuda:${CUDA}-cudnn8-runtime-ubuntu18.04
# FROM directive resets ARGS, so we specify again (the value is retained if
# previously set).
ARG CUDA

COPY env_files /env_files


# Use bash to support string substitution.
SHELL ["/bin/bash", "-c"]

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
    build-essential \
    cmake \
    cuda-command-line-tools-$(cut -f1,2 -d- <<< ${CUDA//./-}) \
    git \
    hmmer \
    kalign \
    tzdata \
    wget \
    awscli \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

#clone the RF Design repo
RUN cd / && git clone https://github.com/RosettaCommons/RFDesign.git


# Install Miniconda package manager.
RUN wget -q -P /tmp \
    https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \ 
  && bash /tmp/Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda \
  && rm /tmp/Miniconda3-latest-Linux-x86_64.sh

# Install conda packages.
ENV PATH="/opt/conda/bin:$PATH"

RUN conda env update --name base --file /env_files/SE3-nvidia_base_env.yml


#download the weights
RUN cd /RFDesign/hallucination/weights/rf_Nov05 \
     && wget http://files.ipd.uw.edu/pub/rfdesign/weights/BFF_last.pt \
     && mkdir -p /RFDesign/inpainting/weights/ \
     && cd /RFDesign/inpainting/weights/ \
     && wget http://files.ipd.uw.edu/pub/rfdesign/weights/BFF_mix_epoch25.pt
     
     

WORKDIR /root

#RUN echo 2
ENTRYPOINT ["bash", "-c"] 
