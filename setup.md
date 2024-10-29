---
title: Setup
---

FIXME: Setup instructions live in this document. Please specify the tools and
the data sets the Learner needs to have installed.

## Data Sets

<!--
FIXME: place any data you want learners to use in `episodes/data` and then use
       a relative link ( [data zip file](data/lesson-data.zip) ) to provide a
       link to it, replacing the example.com link.
-->
Download the [data zip file](https://example.com/FIXME) and unzip it to your Desktop

## Software Setup

### Clone the GitHub repository

The instructions below will walk you through the process of installing the software required for these lessons using the environment configuration files and build scripts in the [kaust-generative-ai/local-deployment-llama-cpp](https://github.com/kaust-generative-ai/local-deployment-llama-cpp) repository on GitHub.

```bash
git clone https://github.com/kaust-generative-ai/local-deployment-llama-cpp.git
cd local-deployment-llama-cpp/
```

### Install Miniforge, Conda, Mamba

If you haven't already done so, install [Miniforge](https://github.com/conda-forge/miniforge). Miniforge provides minimal installers for [Conda](https://conda.io/) and [Mamba](https://github.com/mamba-org/mamba) specific to [conda-forge](https://conda-forge.org/), with the following features pre-configured:

   * Packages in the base environment are obtained from the `conda-forge` channel.
   * The `conda-forge` channel is set as the default (and only) channel.

Conda/mamba will be the primary package managers used to install the required Python dependencies. For convenience, a script is included that will download and install Miniforge, Conda, and Mamba. You can run the script using the following command.

```bash
./bin/install-miniforge.sh
```

### Create the Conda environment

Create the Conda environment in a sub-directory `./env`of your project directory by running the following shell script.

```bash
./bin/create-conda-env.sh
```

:::::::::::::::: spoiler

### Support for Metal GPU acceleration (Mac ARM)

In order to support Metal GPU acceleration you need to install a few extra dependecies. These dependencies are added to the  `environment-metal-gpu.yml` and `requirements-metal-gpu.txt` files. Create the Conda environment in a sub-directory `./env`of your project directory by running the following shell script.

```bash
./bin/create-conda-env.sh environment-metal-gpu.yml
```

::::::::::::::::::::::::


:::::::::::::::: spoiler

### Support for NVIDIA GPU acceleration

In order to support Metal GPU acceleration you need to install a few extra dependecies. These dependencies are added to the `environment-nvidia-gpu.yml` and `requirements-nvidia-gpu.txt` files. Create the Conda environment in a sub-directory `./env`of your project directory by running the following shell script.

```bash
./bin/create-conda-env.sh environment-nvidia-gpu.yml
```

::::::::::::::::::::::::

## Install LLaMA C++ 

For convenience there is an installer script which can be used to download pre-compiled [LLaMA C++][LLaMA C++](https://github.com/ggerganov/llama.cpp) binaries for various OS and CPU architectures and install the binaries into the `bin/` directory of the Conda environment. You can find the latest [release](https://github.com/ggerganov/llama.cpp/releases) for LLaMA C++ on GitHub and pass the link to the zip archive for you specific release to the script as a command line argument.

:::::::::::::::: spoiler

### Mac ARM
```bash
DOWNLOAD_URL=https://github.com/ggerganov/llama.cpp/releases/download/
TAG=b3868
RELEASE_ARCHIVE=lllama-b3868-bin-macos-arm64.zip
./bin/install-llama-cpp.sh "$DOWNLOAD_URL"/"$TAG"/"$RELEASE_ARCHIVE"
```

::::::::::::::::::::::::

:::::::::::::::: spoiler

### Mac Intel

```bash
DOWNLOAD_URL=https://github.com/ggerganov/llama.cpp/releases/download/
TAG=b3868
RELEASE_ARCHIVE=llama-b3868-bin-macos-x64.zip
./bin/install-llama-cpp.sh "$DOWNLOAD_URL"/"$TAG"/"$RELEASE_ARCHIVE"
```

::::::::::::::::::::::::

:::::::::::::::: spoiler

### Ubuntu

```bash
DOWNLOAD_URL=https://github.com/ggerganov/llama.cpp/releases/download/
TAG=b3868
RELEASE_ARCHIVE=llama-b3868-bin-ubuntu-x64.zip
./bin/install-llama-cpp.sh "$DOWNLOAD_URL"/"$TAG"/"$RELEASE_ARCHIVE"
```

::::::::::::::::::::::::

:::::::::::::::: spoiler

### Windows

```bash
DOWNLOAD_URL=https://github.com/ggerganov/llama.cpp/releases/download/
TAG=b3868
RELEASE_ARCHIVE=llama-b3868-bin-win-avx512-x64.zip
./bin/install-llama-cpp.sh "$DOWNLOAD_URL"/"$TAG"/"$RELEASE_ARCHIVE"
```

::::::::::::::::::::::::

## Building LLaMA C++ from source

If there isn't an official release available for your target operating system, then you will need to build LLaMA C++ from source. Don't worry, we have created build scripts to make this process as painless as possible. After creating the Conda environment you can build LLaMA C++ by running the following command.

```bash
conda run --prefix ./env --live-stream ./bin/build-llama-cpp.sh
```

This command does the following.

1. Properly configures the Conda environment using the `conda run` command prior to running the `build-llama-cpp.sh` script.
2. Clones [LLaMA C++](https://github.com/ggerganov/llama.cpp into `./src/llama-cpp`.
4. Builds LLaMA C++ with support for CPU acceleration using OpenBlas in `./build/llama-cpp`.
5. Installs the binaries into the `bin/` directory of the Conda environment.
6. Removes the `./src/llama-cpp` directory as it is no longer needed.
7. Removes the `./build/llama-cpp` directory as it is no longer needed.

:::::::::::::::: callout

#### Install XCode and Command Line Tools

If you are using Mac OS, then you will need to install [XCode](https://developer.apple.com/xcode/) and then run the following command to install XCode Command Line Tools in order to build from source.

```bash
xcode-select --install
```

::::::::::::::::::::::::

:::::::::::::::: spoiler

### Support for Metal GPU acceleration (Mac ARM)

After creating the Conda environment you can build LLaMA C++ by running the following command.

```bash
conda run --prefix ./env --live-stream ./bin/build-llama-cpp-metal-gpu.sh
```

::::::::::::::::::::::::

:::::::::::::::: spoiler

### Support for NVIDIA GPU acceleration

After creating the Conda environment you can build LLaMA C++ by with support for GPU acceleration 
by running the following command.

```bash
conda run --prefix ./env --live-stream ./bin/build-llama-cpp-nvidia-gpu.sh
```

For a detailed discussion of additional NVIDIA GPU compilation options that might improve performance 
on particular GPU architectures see the [LLaMA C++ build documentation](https://github.com/ggerganov/llama.cpp/blob/master/docs/build.md#cuda).

::::::::::::::::::::::::

### Activating the Conda environment

Once the new environment has been created you can activate the environment with the following command.

```bash
conda activate ./env
```
