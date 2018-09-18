FROM ubuntu:16.04


ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

# jupyter のログインパスワード
ENV jupyter_token="dolphin"

# Install dependences
RUN apt-get update --fix-missing && \
  apt-get install -y \
    wget \
    bzip2 \ 
    ca-certificates \
    libglib2.0-0 \
    libxext6 \
    libsm6 \
    libxrender1 \
    git \
    mercurial \
    subversion \
    sudo \
    git \
    zsh \
    openssh-server \
    wget \
    gcc \
    g++ \
    libatlas-base-dev \
    libboost-dev \
    libboost-system-dev \
    libboost-filesystem-dev \
    curl \
    make \
    cmake --fix-missing \
    fonts-ipa* \
    # install opencv development for cabbage
    libopencv-dev

# require to cmake
# ref: https://stackoverflow.com/questions/8711109/could-not-find-module-findopencv-cmake-error-in-configuration-process
ENV OpenCV_DIR /usr/share/OpenCV

ENV TINI_VERSION v0.6.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /usr/bin/tini
RUN chmod +x /usr/bin/tini
ENTRYPOINT ["/usr/bin/tini", "--"]

# Install miniconda dir and add path
ENV CONDA_DIR /opt/conda
ENV PATH ${CONDA_DIR}/bin:${PATH}

RUN wget --quiet https://repo.continuum.io/miniconda/Miniconda3-4.1.11-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p ${CONDA_DIR} && \
    rm ~/miniconda.sh &&\
    conda install -y conda

RUN pip install -U pip && \
  pip install jupyter notebook

ADD requirements.txt requirements.txt
RUN pip install -r requirements.txt

# clean up ;)
RUN apt-get autoremove -y && apt-get clean && \
  conda clean -i -l -t -y && \
  rm -rf /usr/local/src/*

RUN mkdir ~/.jupyter
RUN echo "c.NotebookApp.open_browser = False\n\
c.NotebookApp.ip = '*'\n\
c.NotebookApp.token = '${jupyter_token}'" | tee -a ${HOME}/.jupyter/jupyter_notebook_config.py

WORKDIR /home
EXPOSE 8888

CMD [ "jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--allow-root"]
