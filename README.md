# k8s-cluster-setup

Here is a detailed step-by-step guide for installing Kubernetes on a Ubuntu-based system:

**Note:** Make sure you have SSH access to the server and have the necessary permissions to run the following commands.

1. **Update the system:**
   
   ```shell
   sudo apt update -y
   ```

2. **Install required packages:**

   ```shell
   sudo apt install -y ca-certificates curl gnupg lsb-release
   ```

3. **Setup Docker repository:**

   ```shell
   sudo mkdir -p /etc/apt/keyrings
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   sudo apt update
   sudo apt install -y containerd.io
   sudo service containerd status
   sudo containerd config default > /etc/containerd/config.toml
   ```

4. **Setup Kubernetes repository:**

   ```shell
   curl -fsSLo /etc/apt/keyrings/kubernetes.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
   echo "deb [signed-by=/etc/apt/keyrings/kubernetes.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
   sudo apt update
   apt-mark hold kubeadm kubectl kubelet
   ```

5. **Install Kubernetes components:**

   ```shell
   sudo apt install -y kubeadm kubectl kubelet
   ```

6. **Additional Kubernetes configurations:**

   ```shell
   sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://dl.k8s.io/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update -y
   sudo apt-get update -y
   apt install -y kubeadm kubectl kubelet
   ```

7. **Initialize Kubernetes with pod network CIDR:**

   ```shell
   kubeadm init --pod-network-cidr=10.244.0.0/16
   ```

8. **Configure Containerd:**

   ```shell
   service containerd status
   service containerd restart
   service containerd status
   ```

9. **Enable IP forwarding:**

   ```shell
   cat /proc/sys/net/ipv4/ip_forward
   echo 1 > /proc/sys/net/ipv4/ip_forward
   ```

10. **Configure kubectl for the current user:**

    ```shell
    mkdir -p $HOME/.kube
    cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    chown $(id -u):$(id -g) $HOME/.kube/config
    ```

11. **Apply a network plugin (Flannel is used here):**

    ```shell
    kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
    ```

12. **Verify that pods are running:**

    ```shell
    kubectl get po -A
    ```

13. **Create a test pod:**

    ```shell
    kubectl run my-first-pod --image=nginx
    ```

14. **Verify that the test pod is running:**

    ```shell
    kubectl get po -A
    ```

15. **(Optional) Taint and untaint nodes:**

    To schedule pods on control-plane nodes (not recommended for production):

    ```shell
    kubectl taint node <node-name> node-role.kubernetes.io/control-plane:NoSchedule-
    ```

    To untaint control-plane nodes:

    ```shell
    kubectl taint node <node-name> node-role.kubernetes.io/control-plane:NoSchedule-
    ```

16. **Delete the test pod:**

    ```shell
    kubectl delete po my-first-pod
    ```

17. **To check the version of kubectl:**

    ```shell
    kubectl version
    ```
Now you have a basic Kubernetes cluster up and running on your Ubuntu system. You can proceed to deploy applications and manage your cluster as needed.
