# kubernetesRaspeberryPI
Running k3s on raspberry pi
Kubernetes on raspberry PI – Nginx proxy server installation
1.	Any latest raspberry pi you get it available – I have used Raspberry PI 2 model 3 

2.	Install the raspberry image https://www.raspberrypi.com/software/
 

3.	And install OS on the micro SD card which goes in to Adopter connected your laptop for this setup
 

4.	Press CTRL+SHIFT+X to get in to WIFI connection settings

5.	After installation is successful , you can insert the Micro SD card to Raspberry PI H/w

6.	Now connect the raspberry pi to monitor or SSH once IP is know, ( Enable SSH ) , Use mouse to navigate to raspberry PI to setup further.

7.	Now assuming raspberry pI is up and have access to internet. Let start installing K3s Kubernetes 

8.	Check for Raspberry PI OS version and flavor installed

raghu@raspberrypi:~ $ uname -a
Linux raspberrypi 6.6.31+rpt-rpi-v7 #1 SMP Raspbian 1:6.6.31-1+rpt1 (2024-05-29) armv7l GNU/Linux

9.	Enable cgroup parameters in cmdline.txt to enable container namespace , this  is mandatory for Kubernetes to run
raghu@raspberrypi:~ $ sudo nano /boot/firmware/cmdline.txt

10.	Perform restart of raspberry , to make changes take affect
Sudo shutdown -r now

11.	Enable below 
/etc/sysctl.conf
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding=1

12.	Perform restart of raspberry , to make changes take affect
Sudo shutdown -r now
13.	 Install iptables
sudo apt-get install iptables

14.	Perform restart of raspberry , to make changes take affect
Sudo shutdown -r now

15.	Install K3s note: Replace YOUR_STATIC_IP with raspberry pi IP, get it from “ifconfig” command.
MASTER_IP=YOUR_STATIC_IP
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --disable=traefik --flannel-backend=host-gw --tls-san=192.168.1.85 --bind-address=192.168.1.85 --advertise-address=192.168.1.85 --node-ip=192.168.1.85 --cluster-init" sh -s -
 

16.	raghu@raspberrypi:~ $ sudo chmod 644 /etc/rancher/k3s/k3s.yaml

17.	Check for installation is success
raghu@raspberrypi:~ $ sudo kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   89s
raghu@raspberrypi:~ $
raghu@raspberrypi:~ $ sudo kubectl get nodes
NAME          STATUS   ROLES                  AGE     VERSION
raspberrypi   Ready    control-plane,master   2m45s   v1.29.6+k3s2
raghu@raspberrypi:~ $

18.	Install GIT
raghu@raspberrypi:~ $ sudo apt-get install git
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
git is already the newest version (1:2.39.2-1.1).
0 upgraded, 0 newly installed, 0 to remove and 24 not upgraded.

19.	Perform Git clone https://github.com/PacktPublishing/Edge-Computing-Systems-with-Kubernetes.git
    
raghu@raspberrypi:~ $ git clone https://github.com/PacktPublishing/Edge-Computing-Systems-with-Kubernetes.git
Cloning into 'Edge-Computing-Systems-with-Kubernetes'...
remote: Enumerating objects: 788, done.
remote: Counting objects: 100% (96/96), done.
remote: Compressing objects: 100% (57/57), done.
remote: Total 788 (delta 54), reused 68 (delta 34), pack-reused 692
Receiving objects: 100% (788/788), 17.16 MiB | 1.98 MiB/s, done.
Resolving deltas: 100% (373/373), done.

20.	 Below NGINIX yaml 
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


21.	Check for status if pod is runing
raghu@raspberrypi:~/Edge-Computing-Systems-with-Kubernetes/ch10/yaml $ kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-86dcfdf4c6-2nsk5   1/1     Running   0          3m18s
my-nginx-86dcfdf4c6-dhhqd   1/1     Running   0          3m17s

22.	Perform below cmd
raghu@raspberrypi:~/Edge-Computing-Systems-with-Kubernetes/ch10/yaml $ kubectl get svc
NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes     ClusterIP      10.43.0.1      <none>        443/TCP        104m
my-nginx-svc   LoadBalancer   10.43.172.36   <pending>     80:30372/TCP   50m

23.	Check if nginx server is running on port 80
curl http://10.43.172.36:80
raghu@raspberrypi:~/Edge-Computing-Systems-with-Kubernetes/ch10/yaml $ curl http://10.43.172.36:80
<
 
