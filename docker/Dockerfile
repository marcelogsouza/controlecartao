FROM ubuntu:20.04

LABEL maintainer="Marcelo Souza"

ARG DEBIAN_FRONTEND=noninteractive

# Install some basic utilities
RUN apt-get update && apt-get install -y \
    curl \
    ca-certificates \
    sudo \
    git \
    bzip2 \
    ffmpeg \
    libsm6 \
    libxext6 \
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

# Set up the Conda environment
ENV CONDA_AUTO_UPDATE_CONDA=false
ENV PATH=/home/user/miniconda/bin:$PATH

COPY environment.yml /app/environment.yml
COPY requirements.txt /app/requirements.txt

RUN curl -sLo ~/miniconda.sh https://repo.continuum.io/miniconda/Miniconda3-py39_4.10.3-Linux-x86_64.sh \
&& chmod +x ~/miniconda.sh \
&& ~/miniconda.sh -b -p ~/miniconda \
&& rm ~/miniconda.sh

RUN pip --no-cache-dir install -r requirements.txt \
&& pip install 'python-language-server[all]' \
&& rm /app/requirements.txt

RUN conda config --append channels conda-forge \
&& conda env update -n base -f /app/environment.yml \
&& conda clean -ya \
&& rm /app/environment.yml
 
CMD [ "/bin/bash" ]