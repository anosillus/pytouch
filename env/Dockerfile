FROM nvidia/cuda:10.0-base-ubuntu16.04
LABEL iida kenji "k.iida@churadata.okinawa"

USER root

RUN apt-get update && apt-get install -y \
    curl \
    ca-certificates \
    sudo \
    git \
    bzip2 \
    libx11-6 \
 && rm -rf /var/lib/apt/lists/*

# Create a working directory
RUN mkdir /app
WORKDIR /app

# Create a non-root user and switch to it
RUN adduser --disabled-password --gecos '' --shell /bin/bash user \
 && chown -R user:user /app
RUN echo "user ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-user
USER user

# All users can use /home/user as their home directory
ENV HOME=/home/user
RUN chmod 777 /home/user

# Install Miniconda
RUN curl -so ~/miniconda.sh https://repo.continuum.io/miniconda/Miniconda3-4.5.11-Linux-x86_64.sh \
 && chmod +x ~/miniconda.sh \
 && ~/miniconda.sh -b -p ~/miniconda \
 && rm ~/miniconda.sh
ENV PATH=/home/user/miniconda/bin:$PATH
ENV CONDA_AUTO_UPDATE_CONDA=false

# Create a Python 3.7 environment
RUN /home/user/miniconda/bin/conda create -y --name py37 python=3.7 \
 && /home/user/miniconda/bin/conda clean -ya
ENV CONDA_DEFAULT_ENV=py37
ENV CONDA_PREFIX=/home/user/miniconda/envs/$CONDA_DEFAULT_ENV
ENV PATH=$CONDA_PREFIX/bin:$PATH
RUN /home/user/miniconda/bin/conda install conda-build \
 && /home/user/miniconda/bin/conda clean -ya

RUN mkdir -p /home/pytouch && \
mkdir -p ~/.jupyter && { \
echo "c.NotebookApp.allow_root = True"; \
echo "c.NotebookApp.ip = '0.0.0.0'"; \
echo "c.NotebookApp.token = ''"; \
echo "c.NotebookApp.open_browser = False"; \
echo "c.JupyterLabTemplates.include_default = True"; \
echo "c.NotebookApp.notebook_dir = '/home/pytouch'"; \
echo "c.JupyterLabTemplates.template_dirs = ['list', 'of', 'template', 'directories']"; \
} | tee ~/.jupyter/jupyter_notebook_config.py

ENV CONDA_DIR=/opt/conda

SHELL ["/bin/bash", "-c"]
RUN $CONDA_DIR/bin/conda config --system --prepend channels conda-forge

RUN apt-get update && apt-get install -yq --no-install-recommends \
    build-essential \
    fish \
    fonts-ipaexfont \
    fonts-noto-cjk \
    git \
    inkscape \
    libgl1-mesa-dev \
    pandoc \
    texlive-fonts-extra \
    texlive-fonts-recommended \
    texlive-generic-recommended \
    texlive-latex-base \
    texlive-latex-extra \
    unzip \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

COPY environment.yaml /tmp
RUN $CONDA_DIR/bin/conda install tini && \
    $CONDA_DIR/bin/conda env update -f /tmp/environment.yaml && \
    rm /tmp/environment.yaml && \
    conda clean --all -f -y

EXPOSE 8888
ENTRYPOINT [ "/usr/bin/tini", "--" ]
