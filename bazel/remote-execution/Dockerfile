# Copyright 2022-2024 The NativeLink Authors. All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# This file is a slim image for building Redpanda remotely with Nativelink.
FROM debian:bookworm-slim

# Install all build dependencies required for Redpanda and Seastar, matching install_deps.sh
RUN apt-get update && apt-get install -y --no-install-recommends \
    zstd \
    libstdc++6 \
    autoconf \
    automake \
    autopoint \
    bison \
    git \
    libtool \
    make \
    pkgconf \
    ragel \
    sudo \
 && rm -rf /var/lib/apt/lists/*

# Copy compressed toolchain tarball in
COPY llvm-18-ubuntu-22.04-x86_64.tar.zst /opt/

# Decompress and extract toolchain
RUN cd /opt \
 && zstd -d llvm-18-ubuntu-22.04-x86_64.tar.zst \
 && mkdir /opt/install \
 && tar xf llvm-18-ubuntu-22.04-x86_64.tar -C /opt/install \
 && rm llvm-18-ubuntu-22.04-x86_64.tar*

# Ensure /opt/bin exists and symlink llvm-ar there
RUN mkdir -p /opt/bin && ln -sf /opt/install/bin/llvm-ar /opt/bin/llvm-ar

# Simple compiler wrapper because its absence caused issues
RUN echo '#!/bin/bash\nexec clang "$@"' > /opt/bin/cc_wrapper.sh && chmod +x /opt/bin/cc_wrapper.sh

# Set toolchain bin first in PATH
ENV PATH="/opt/bin:/opt/install/bin:$PATH"

WORKDIR /work

CMD [ "bash" ]