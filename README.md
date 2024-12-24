# Install Kubernets



/// Install and Configure Containerd \\\

    sudo apt install containerd
    containerd config default | sudo tee /etc/containerd/config.toml
    nano /etc/containerd/config.toml
    [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options] Search for the line and change the value "SystemdCgroup = false" to "SystemdCgroup = true"

/// Disable swap \\\

    sudo swapoff -a
    Need to remove Swap from /etc/fstab also

/// Enable bridging \\\

    nano /etc/sysctl.conf Open this file and uncoment this #net.ipv4.ip_forward=1

/// Enable br_netfilter \\\

    nano /etc/modules-load.d/k8s.conf Open this and add this br_netfilter

Reboot the server

/// Install the Kubernets \\\

    sudo apt-get install -y apt-transport-https ca-certificates curl gpg
    curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
    sudo apt-get update
    sudo apt-get install -y kubelet kubeadm kubectl
    sudo apt-mark hold kubelet kubeadm kubectl

/// Final Configurations  \\\

    sysctl -w net.ipv4.ip_forward=1
    sysctl -p
    
    kubeadm init --control-plane-endpoint=172.16.250.216 --node-name controller --pod-network-cidr=10.244.0.0/16 (In this need to give server hostname and master server IP Before Run this make sure disable the swap)
    
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    suod chown $(id -u):$(id -g) $HOME/.kube/config

/// Install Overlay Network \\\

    kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml


kubectl get nodes
kubeadm token create --print-join-command (Get Token for Joining workers to the cluster)


#  Install Helm 
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod +x get_helm.sh
    ./get_helm.sh

    







#  Install Nginx Ingres

If your Deploy Nginx Ingress in Single node on Master you  need to run below comand

    *kubectl describe node <node-name> | grep Taints // Check your taints



    *If taint is node-role.kubernetes.io/control-plane run bellow command
        kubectl taint nodes --all node-role.kubernetes.io/control-plane-



    *If taint is node-role.kubernetes.io/master run bellow command
        kubectl taint nodes --all node-role.kubernetes.io/master-

        

    *Install Nginx-ingress using helm chart 
        helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
        helm repo update
        
        helm install nginx-ingress ingress-nginx/ingress-nginx \
        --namespace ingress-nginx \
        --create-namespace \
        --set controller.service.type=NodePort ( This will install nginx ingress using node port if you wat install it with Load Balance you need to install without node port)

        helm install nginx-ingress ingress-nginx/ingress-nginx \
        --namespace ingress-nginx \
        --create-namespace \
        --set controller.service.type=NodePort \
        --set controller.service.nodePorts.http=30080 \
        --set controller.service.nodePorts.https=30443 (Aloso you can specify node port as per your requirement)


        After Installation you can Run the deployments. 



