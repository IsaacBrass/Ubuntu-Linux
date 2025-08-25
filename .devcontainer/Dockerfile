# Use Ubuntu 24.04 (Noble Numbat) as the base image
FROM ubuntu:24.04

# Avoid prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Update package lists and install base dependencies
RUN apt-get update && apt-get install -y \
    software-properties-common \
    build-essential \
    gdb \
    cmake \
    git \
    wget \
    gnupg \
    ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# --- Install g++ 15 ---
# Add the PPA for the latest GCC toolchain
RUN add-apt-repository ppa:ubuntu-toolchain-r/test -y

# --- Install clang++ 20 ---
# Add the LLVM GPG key and repository
RUN wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add -
RUN add-apt-repository "deb http://apt.llvm.org/noble/ llvm-toolchain-noble-20 main" -y

# --- Install Compilers and Clean Up ---
# Update package lists again and install the specific compiler versions
RUN apt-get update && apt-get install -y \
    g++-15 \
    clang-20 \
    lld-20 && \
    rm -rf /var/lib/apt/lists/*

# Set g++-15 and clang++-20 as the default compilers for the session if needed
RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-15 100 && \
    update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-15 100 && \
    update-alternatives --install /usr/bin/clang clang /usr/bin/clang-20 100 && \
    update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-20 100

# --- User and Workspace Setup ---
# Create a non-root user 'vscode' with sudo access
RUN useradd -m -s /bin/bash -G sudo vscode && \
    echo "vscode ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/vscode

# Switch to the non-root user
USER vscode
WORKDIR /home/vscode

# --- Set Up Compiler Aliases with Custom Switches ---
# Append the aliases to the user's bash profile so they are always available.
#
# ❗️ VITAL: Replace the placeholder switches below with your actual list.
#
RUN echo '' >> /home/vscode/.bashrc && \
    echo '# --- Custom Compiler Aliases ---' >> /home/vscode/.bashrc && \
    echo "alias g++-custom='g++-15 -Wall -Wextra -Wpedantic -O3 -std=c++2b'" >> /home/vscode/.bashrc && \
    echo "alias clang++-custom='clang++-20 -Wall -Wextra -Wpedantic -O3 -std=c++2b -stdlib=libc++'" >> /home/vscode/.bashrc && \
    echo "alias gcc-custom='gcc-15 -Wall -Wextra -Wpedantic -O3 -std=c2x'" >> /home/vscode/.bashrc

# Set a default command to keep the container running
CMD ["/bin/bash"]
