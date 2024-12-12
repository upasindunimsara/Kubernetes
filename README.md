# Kubernetes


Install Kubernets

/// Install and Configure Containerd \\

sudo apt install containerd
containerd config default | sudo tee /etc/containerd/config.toml
nano /etc/containerd/config.toml
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options] Search for the line and change the value "SystemdCgroup = false" to "SystemdCgroup = true"
