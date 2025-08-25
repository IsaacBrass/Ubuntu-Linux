# Use Ubuntu 24.04 (Noble Numbat) as the base image
FROM ubuntu:24.04

# Avoid prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# A single RUN command to set up all dependencies to optimize layer caching.
RUN apt-get update && apt-get install -y \
    software-properties-common \
    build-essential \
    gdb \
    cmake \
    git \
    wget \
    gnupg \
    ca-certificates && \
    \
    # --- Add Repositories ---
    # Add the PPA for the latest GCC toolchain and the LLVM repository for Clang
    add-apt-repository ppa:ubuntu-toolchain-r/test -y && \
    wget -qO- https://apt.llvm.org/llvm-snapshot.gpg.key | gpg --dearmor -o /etc/apt/trusted.gpg.d/llvm-archive-keyring.gpg && \
    echo "deb http://apt.llvm.org/noble/ llvm-toolchain-noble-20 main" > /etc/apt/sources.list.d/llvm.list && \
    \
    # --- Install Compilers ---
    # Update package lists again and install the specific compiler versions
    apt-get update && \
    apt-get install -y \
    g++-15 \
    gcc-15 \
    clang-20 \
    lld-20 && \
    \
    # --- Set Compiler Alternatives ---
    # Set g++-15 and clang++-20 as the default compilers
    update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-15 100 && \
    update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-15 100 && \
    update-alternatives --install /usr/bin/clang clang /usr/bin/clang-20 100 && \
    update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-20 100 && \
    \
    # --- Clean Up ---
    # Remove package lists to reduce image size
    rm -rf /var/lib/apt/lists/*

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
