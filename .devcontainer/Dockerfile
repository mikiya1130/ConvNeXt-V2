FROM nvidia/cuda:11.1.1-cudnn8-devel-ubuntu20.04

ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo
ENV SHELL=/bin/bash

ARG USERNAME=${USERNAME}
ARG USER_UID=${USER_UID}
ARG USER_GID=${USER_GID}

# SHELL の行は仮想環境名を変数に置き換えられない
ARG CONDA_ENV=convnextv2

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    git \
    g++ \
    wget

RUN groupadd --gid $USER_GID $USERNAME && \
    useradd --uid $USER_UID --gid $USER_GID -m $USERNAME

USER $USERNAME
WORKDIR /home/$USERNAME

RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
    sh Miniconda3-latest-Linux-x86_64.sh -b -p /home/$USERNAME/miniconda3 && \
    rm -r Miniconda3-latest-Linux-x86_64.sh
ENV PATH /home/$USERNAME/miniconda3/bin:$PATH
RUN conda init bash
RUN conda create -n $CONDA_ENV python=3.8 -y
SHELL ["conda", "run", "-n", "convnextv2", "/bin/bash", "-c"]

RUN conda install openblas-devel -c anaconda -y

COPY ./requirements.txt /tmp/requirements.txt
RUN python -m pip install -U pip && \
    python -m pip --disable-pip-version-check --no-cache-dir install -r /tmp/requirements.txt

RUN git clone -b 22.04-dev https://github.com/NVIDIA/apex && \
    cd apex && \
    pip install -v --disable-pip-version-check --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" ./

WORKDIR /workspace

RUN echo 'alias python="python3.8"' >> ~/.bashrc
RUN echo 'alias python3="python3.8"' >> ~/.bashrc
RUN echo 'alias pip="python -m pip"' >> ~/.bashrc
RUN echo "conda activate ${CONDA_ENV}" >> ~/.bashrc
