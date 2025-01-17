# syntax = docker/dockerfile:experimental

FROM debian:11-slim AS builder
ARG PULUMI_VERSION
RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y \
    curl \
    build-essential \
    git

# Install the Pulumi SDK, including the CLI and language runtimes.
RUN curl -fsSL https://get.pulumi.com/ | bash -s -- --version $PULUMI_VERSION


# The runtime container
FROM debian:11-slim
LABEL org.opencontainers.image.description="Pulumi CLI container for dotnet"
WORKDIR /pulumi/projects

ARG DOTNET_VERSION="3.1"

RUN apt-get update -y && \
    apt-get install -y \
    git \
    curl

# arm64 packages are not available in apt-get, per
# https://docs.microsoft.com/en-us/dotnet/core/install/linux-debian, so we need
# to install via another method:
RUN curl -fsSL https://dot.net/v1/dotnet-install.sh | bash -s -- -c ${DOTNET_VERSION}

# Uses the workdir, copies from pulumi interim container
COPY --from=builder /root/.pulumi/bin/pulumi /pulumi/bin/pulumi
COPY --from=builder /root/.pulumi/bin/*-dotnet* /pulumi/bin/

ENV PATH "/root/.dotnet:/pulumi/bin:${PATH}"
ENV DOTNET_ROOT /root/.dotnet
ENV DOTNET_SYSTEM_GLOBALIZATION_INVARIANT 1

CMD ["pulumi"]
