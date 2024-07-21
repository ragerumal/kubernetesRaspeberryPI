# Running K3s with Kubernetes on Raspberry Pi

This guide will help you set up K3s with Kubernetes and deploy an Nginx proxy server on a Raspberry Pi.

## Prerequisites

- Raspberry Pi (tested on Raspberry Pi 3 Model B)
- Micro SD card with Raspberry Pi OS installed
- Internet access for the Raspberry Pi
![image](https://github.com/user-attachments/assets/03d3b446-37cf-4e9c-a524-42ce7179ea0f)

## Installation Steps

1. **Download and Install Raspberry Pi OS**
   - Download the Raspberry Pi OS image from [Raspberry Pi Downloads](https://www.raspberrypi.com/software/)
   - Write the image to the Micro SD card and configure Wi-Fi settings.
![image](https://github.com/user-attachments/assets/bfc7c095-77d6-433d-bc63-41926348f6a9)

2. **Initial Setup**
   - Insert the Micro SD card into the Raspberry Pi and connect it to a monitor or SSH into it.
   - Ensure SSH is enabled and configure basic settings.

3. **Install K3s Kubernetes**
   - Check the Raspberry Pi OS version:
     ```bash
     uname -a
     ```
   - Enable cgroup parameters in `cmdline.txt`:
     ```bash
     sudo nano /boot/firmware/cmdline.txt
     ```
   - Restart the Raspberry Pi:
     ```bash
     sudo shutdown -r now
     ```
   - Enable IP forwarding in `/etc/sysctl.conf` and install `iptables`.
   - Install K3s (replace `YOUR_STATIC_IP` with your Raspberry Pi's IP):
     ```bash
     curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --disable=traefik --flannel-backend=host-gw --tls-san=YOUR_STATIC_IP --bind-address=YOUR_STATIC_IP --advertise-address=YOUR_STATIC_IP --node-ip=YOUR_STATIC_IP --cluster-init" sh -
     ```
     ![image](https://github.com/user-attachments/assets/cf12a3d0-ee13-4e19-8d41-1b1e78ea52ac)

   - Make `kubectl` accessible:
     ```bash
     sudo chmod 644 /etc/rancher/k3s/k3s.yaml
     ```

4. **Verify K3s Installation**
   - Check if Kubernetes components are running:
     ```bash
     sudo kubectl get all
     sudo kubectl get nodes
     ```

5. **Install Git**
   - Install Git (if not already installed):
     ```bash
     sudo apt-get install git
     ```


7. **Deploy Nginx with Kubernetes**
   - Apply the Nginx deployment and service YAML:
     ```yaml
     apiVersion: v1
     kind: Service
     metadata:
       name: my-nginx-svc
       labels:
         app: nginx
     spec:
       type: LoadBalancer
       ports:
       - port: 80
         selector:
           app: nginx
     ---
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: my-nginx
       labels:
         app: nginx
     spec:
       replicas: 2
       selector:
         matchLabels:
           app: nginx
       template:
         metadata:
           labels:
             app: nginx
         spec:
           containers:
           - name: nginx
             image: nginx:1.14.2
             ports:
             - containerPort: 80
     ```
   - Apply the YAML configuration:
     ```bash
     kubectl apply -f Edge-Computing-Systems-with-Kubernetes/ch10/yaml/nginx.yaml
     ```

8. **Verify Nginx Deployment**
   - Check if Nginx pods are running:
     ```bash
     kubectl get pods
     ```

   - Check the status of the Nginx service:
     ```bash
     kubectl get svc
     ```

   - Test if Nginx is accessible:
     ```bash
     curl http://LOCALIP:80
     ```

![image](https://github.com/user-attachments/assets/1e8c4354-c587-4b4d-a508-d449e2779a61)

## Additional Information

For troubleshooting, FAQs, or further customization, refer to the Kubernetes and K3s documentation.


