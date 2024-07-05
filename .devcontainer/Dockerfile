# For more CUDA-supported base images, refer to https://hub.docker.com/r/nvidia/cuda
# For tag list, see https://hub.docker.com/r/nvidia/cuda/tags
FROM nvidia/cuda:12.4.1-cudnn-devel-ubuntu22.04

# This value is set to avoid any prompts hanging the build process during package installation.
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    git xvfb curl \
    zlib1g g++ freeglut3-dev \
    libx11-dev libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev libfreeimage-dev \
    libgl1-mesa-glx libosmesa6 \
    build-essential g++ cmake swig ffmpeg unrar unzip zip curl wget \
    && rm -rf /var/lib/apt/lists/*

    

ENV CNDA=/conda
RUN mkdir -p $CNDA && chmod 755 $CNDA
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O $CNDA/miniconda.sh\
 && chmod +x $CNDA/miniconda.sh \
 && $CNDA/miniconda.sh -b -u -p $CNDA/miniconda \
 && rm $CNDA/miniconda.sh
ENV PATH=$CNDA/miniconda/bin:$PATH
ENV CONDA_AUTO_UPDATE_CONDA=false

# Disable ssl
RUN conda config --set ssl_verify false 

# Create a Python 3.11 environment
RUN $CNDA/miniconda/bin/conda create -y --name ml python=3.11 \
 && $CNDA/miniconda/bin/conda clean -ya
ENV CONDA_DEFAULT_ENV=ml
ENV CONDA_PREFIX=$CNDA/miniconda/envs/$CONDA_DEFAULT_ENV
ENV PATH=$CONDA_PREFIX/bin:$PATH
RUN $CNDA/miniconda/bin/conda clean -ya


# Install dependencies for the conda env
RUN pip3 install --upgrade setuptools pip


# Jax and PyTorch are not installed by default.
# # Jax CUDA version
# RUN pip3 install --upgrade "jax[cuda12]" \
#     flax \
#     optax

# # PyTorch CPU version
# RUN pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu

RUN pip3 install \
    opencv-python==4.9.0.80 \
    seaborn==0.13.2 \
    tqdm==4.66.2

RUN pip3 install numpy \
    jupyter==1.0.0 \
    scikit-learn \
    matplotlib \
    regex

# Used by OpenMP, a library for parallel programming. This variable sets the number of threads that OpenMP will use for parallel regions (if not otherwise specified in the code). Setting it to 1 restricts OpenMP to use only one thread, 
ENV OMP_NUM_THREADS=1

# Force the Python interpreter to run in unbuffered mode. Normally, Python buffers output of its programs (storing it in memory until the buffer is full), which can delay the output from appearing on the terminal or in logs. When unbuffered, the output is written directly to the terminal or file, which is particularly useful for logging in real time and for applications where immediate output is needed, such as in Docker containers where logs are monitored.
ENV PYTHONUNBUFFERED=1
ENV LANG="C.UTF-8"

# Set the directory where Numba, a just-in-time compiler for Python that uses the LLVM compiler infrastructure, caches its compiled function objects.
ENV NUMBA_CACHE_DIR=/tmp

# Disable vram pre-allocation of the XLA compiler (Accelerated Linear Algebra), which is often used in machine learning frameworks like TensorFlow and Jax.
# Please refer to: https://jax.readthedocs.io/en/latest/gpu_memory_allocation.html
ENV XLA_PYTHON_CLIENT_PREALLOCATE=false

# Setting it to 0.8 limits the fraction of GPU memory XLA should pre-allocate for its operations to 80% of the available GPU memory. 
# ENV XLA_PYTHON_CLIENT_MEM_FRACTION 0.8
