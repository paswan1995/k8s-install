sudo apt-get update
curl -fsSL https://get.docker.com -o get-docker.sh
sh sh get-docker.sh
# Run these commands as root
###Install GO###
wget https://storage.googleapis.com/golang/getgo/installer_linux
chmod +x ./installer_linux
./installer_linux
source ~/.bash_profile

git clone https://github.com/Mirantis/cri-dockerd.git
cd cri-dockerd
mkdir bin
go build -o bin/cri-dockerd
mkdir -p /usr/local/bin
install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
cp -a packaging/systemd/* /etc/systemd/system
sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
systemctl daemon-reload
systemctl enable cri-docker.service
systemctl enable --now cri-docker.socket
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
 curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
 echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
 sudo apt-get update
 sudo apt-get install -y kubelet kubeadm kubectl
 sudo apt-mark hold kubelet kubeadm kubectl
 ## node section is completed here, now stop ## 
 ## now we have to do in master section extra steps ##
 kubeadm init --pod-network-cidr=10.244.0.0/16
 kubeadm init --pod-network-cidr "10.244.0.0/16" --cri-socket "unix:///var/run/cri-dockerd.sock"
----------------------------------------------------------------------------------------------------------------
# after this steps u will get token so copy that token n paste on nodes.....-----
# now paste this token on each nodes-------
 kubeadm join 173.41.26.20:7443 --token ridrvm.jh9687n888sa3a7w \
 --cri-socket "unix:///var/run/cri-dockerd.sock" \
        --discovery-token-ca-cert-hash sha256:f228a307c760dcc513f38cb691d14b31905242ce9d3488f593a096b7e8933a99
----------------------------------------------------------------------------------------------------------------
