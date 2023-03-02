FROM {{ cookiecutter.docker_base_image }}

# Set the working directory
WORKDIR /app

{% if cookiecutter.vendor_base == "debian" %}
# Make sure installation of tzdata is non-interactive
ENV DEBIAN_FRONTEND="noninteractive"

# Install System python
RUN apt-get update -y && \
    apt-get install --no-install-recommends -y \
        python{{ cookiecutter.python_version|py_tag }}-dev \
        python{{ cookiecutter.python_version|py_tag }}-venv \
        python{{ cookiecutter.python_version|py_tag }}-pip
{% elif cookiecutter.vendor_base == "redhat" %}
# Install System python
RUN dnf install -y \
    python{{ cookiecutter.python_version|py_tag }}-devel \
    python{{ cookiecutter.python_version|py_tag }}-pip
{% elif cookiecutter.vendor_base == "archlinux" %}
# Install System python
RUN pacman -Syu --noconfirm \
    python{{ cookiecutter.python_version|py_tag }}

# Ensure pip is available
RUN python3 -m ensurepip
{% endif %}
# Ensure Docker user UID:GID matches host user UID:GID (beeware/briefcase#403)
# Use --non-unique to avoid problems when the UID:GID of the host user
# collides with entries provided by the Docker container.
ARG HOST_UID
ARG HOST_GID
RUN groupadd --non-unique --gid $HOST_GID briefcase && \
    useradd --non-unique --uid $HOST_UID --gid $HOST_GID brutus --home /home/brutus && \
    mkdir -p /home/brutus && chown brutus:briefcase /home/brutus

# As root, install system packages required by app
ARG SYSTEM_REQUIRES
{% if cookiecutter.vendor_base == "debian" %}
RUN apt-get install --no-install-recommends -y build-essential ${SYSTEM_REQUIRES}
{% elif cookiecutter.vendor_base == "redhat" %}
RUN dnf install -y rpm-build ${SYSTEM_REQUIRES}
{% elif cookiecutter.vendor_base == "archlinux" %}
RUN pacman -Syu  --noconfirm base-devel ${SYSTEM_REQUIRES}
{% endif %}

# Use the brutus user for operations in the container
USER brutus

# ========== START USER PROVIDED CONTENT ==========
{{ cookiecutter.dockerfile_extra_content }}
