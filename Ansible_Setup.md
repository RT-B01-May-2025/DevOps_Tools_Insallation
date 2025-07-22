
# This script installs Ansible using pip and configures the environment.
# It also updates the system packages and ensures that the PATH is set correctly.
# It generates a default ansible.cfg file in /etc/ansible.  
# Usage: Run this script in a terminal with appropriate permissions such as sudo.

#!/bin/bash
set -e

echo "Updating system packages..."
if command -v apt &>/dev/null; then
    sudo apt update -y
    sudo apt install -y python3 python3-pip
elif command -v yum &>/dev/null; then
    sudo yum update -y
    sudo yum install -y python3 python3-pip
elif command -v dnf &>/dev/null; then
    sudo dnf update -y
    sudo dnf install -y python3 python3-pip
else
    echo "Unsupported package manager. Please install Python 3 and pip manually."
    exit 1
fi

echo "Installing Ansible using pip..."
pip3 install --user ansible

echo "Configuring PATH..."
SHELL_RC=""
if [[ "$SHELL" == */bash ]]; then
    SHELL_RC="$HOME/.bashrc"
elif [[ "$SHELL" == */zsh ]]; then
    SHELL_RC="$HOME/.zshrc"
else
    SHELL_RC="$HOME/.profile"
fi

if ! grep -q 'export PATH=$PATH:$HOME/.local/bin' "$SHELL_RC"; then
    echo 'export PATH=$PATH:$HOME/.local/bin' >> "$SHELL_RC"
    echo "PATH updated in $SHELL_RC"
fi

source "$SHELL_RC"

echo "Verifying Ansible installation..."
ansible --version || echo "Please run: source $SHELL_RC"

echo "Ansible installation via pip completed successfully!"

echo "Generate Default ansible.cfg Using Command"

sudo mkdir -p /etc/ansible
sudo ansible-config init --disabled > /etc/ansible/ansible.cfg
