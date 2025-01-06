# Use Ubuntu as base image
FROM ubuntu:22.04

# Set working directory
WORKDIR /app

# Install required packages
RUN apt-get update && apt-get install -y \
    wget \
    unzip \
    ffmpeg \
    && rm -rf /var/lib/apt/lists/*

# Download N_m3u8DL-RE release
RUN wget https://github.com/nilaoda/N_m3u8DL-RE/releases/download/v0.3.0-beta/N_m3u8DL-RE_v0.3.0-beta_linux-x64_20241203.tar.gz \
    && tar zxfv N_m3u8DL-RE_v0.3.0-beta_linux-x64_20241203.tar.gz \
    && rm N_m3u8DL-RE_v0.3.0-beta_linux-x64_20241203.tar.gz \
    && chmod +x N_m3u8DL-RE

# Create a directory for downloads
RUN mkdir /downloads
#
## Set the working directory to downloads
WORKDIR /downloads

# Define entrypoint
ENTRYPOINT ["/app/N_m3u8DL-RE"]