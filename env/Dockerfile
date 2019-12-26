FROM nvidia/cuda:10.0-base-ubuntu16.04
LABEL iida kenji "k.iida@churadata.okinawa"

USER root

RUN apt-get update && apt-get install -yq --no-install-recommends \
    build-essential \
    bzip2 \
    ca-certificates \
    curl \
    fish \
    fonts-ipaexfont \
    build-essential \
    fonts-noto-cjk \
    git \
    inkscape \
    libgl1-mesa-dev \
    libglib2.0-0 \
    libsm6 \
    libx11-6 \
    libxext6 \
    libxrender1 \
    mercurial \
    pandoc \
    subversion \
    sudo \
    texlive-fonts-extra \
    texlive-fonts-recommended \
    texlive-generic-recommended \
    texlive-latex-base \
    texlive-latex-extra \
    unzip \
    wget \
&& apt-get clean && rm -rf /var/lib/apt/lists/*

# All users can use /home/user as their home directory
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH

RUN wget --quiet https://repo.anaconda.com/archive/Anaconda3-2019.10-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/conda && \
    rm ~/anaconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    /opt/conda/bin/conda clean -afy

COPY environment.yaml /tmp
RUN /opt/conda/bin/conda env create -f /tmp/environment.yaml && \
    /opt/conda/bin/conda clean --all -f -y && \
    rm /tmp/environment.yaml
RUN mkdir -p ~/.jupyter && { \
echo "c.NotebookApp.allow_root = True"; \
echo "c.NotebookApp.ip = '0.0.0.0'"; \
echo "c.NotebookApp.token = ''"; \
echo "c.NotebookApp.open_browser = False"; \
echo "c.JupyterLabTemplates.include_default = True"; \
echo "c.NotebookApp.notebook_dir = '/root'"; \
echo "c.JupyterLabTemplates.template_dirs = ['list', 'of', 'template', 'directories']"; \
} | tee ~/.jupyter/jupyter_notebook_config.py

SHELL ["/bin/bash", "-c"]
RUN conda config --system --prepend channels conda-forge

EXPOSE 8888
