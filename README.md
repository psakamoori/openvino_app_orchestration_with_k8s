# openvino_app_orchestration_with_k8s
OpenVINO Person Detection container for deployment on Kubernetes clsuter 

# Pre-requisites to run/deploy this application

1. Download OpenVINO install package (for Ubuntu) from : https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit/download.html (Ex: like l_openvino_toolkit....tgz)
2. place downloaded tgz file in Dockerfile.amd64 root directory
3. Up and running Kubernetes cluster (physical or virtual)
4. Setup local docker registry (pd-deploy.yaml takes local docker registry image) 

# Steps to build and push docker image with OpenVINO person detection application to local registry:

1. Build Docker image: docker build -f Dockerfile.amd64 -t kube_sample_app .
2. Tagging: docker tag kube_sample_app:latest "master node url Ex: k8s-master.com":"port number"/kube_app   (Ex: home-ss-as-master.com:5000/kube_app)
3. Pushing docker image: docker push home-ss-as-master.com:5000/kube_app

    Ex: The push refers to repository [home-ss-as-master.com:5000/kube_app]
        977cbbf347e3: Pushed 
        e55a7af913c5: Pushed 
        ....
        ....
        ....
        latest: digest: sha256:98e78a2d6557b752492a16340c88bd54004dcdb7862988c500348f69aadd8207 size: 4099

# Steps to deploy OpenVINO app on K8s cluster:
1. Check if k8s cluster is up and status looking good
         $kubectl get nodes


         Ex: NAME                STATUS     ROLES        AGE   VERSION
             home-ss-as-master   Ready      master       86d   v1.19.3
             node01              Ready      workernode   86d   v1.19.3

2. Command to deploy app on to cluster

      $kubectl -f apply pd-deploy.yaml

Troubleshooting:

1. Make sure all the pods in all the namespaces are properly running on the clsuter

    Ex: All should be in running state
   ```
      kubectl get pods --all-namespaces
      NAMESPACE     NAME                                        READY   STATUS    RESTARTS   AGE
      kube-system   coredns-f9fd979d6-7l928                     1/1     Running   10         84d
      kube-system   coredns-f9fd979d6-dj2th                     1/1     Running   10         84d
      kube-system   etcd-home-ss-as-master                      1/1     Running   4          84d
      kube-system   kube-apiserver-home-ss-as-master            1/1     Running   81         84d
      kube-system   kube-controller-manager-home-ss-as-master   1/1     Running   16         84d
      kube-system   kube-flannel-ds-dv5xf                       1/1     Running   5          84d
      kube-system   kube-flannel-ds-rc9cl                       1/1     Running   1          84d
      kube-system   kube-proxy-dsjvr                            1/1     Running   1          84d
      kube-system   kube-proxy-f78xm                            1/1     Running   3          84d
      kube-system   kube-scheduler-home-ss-as-master            1/1     Running   17         84d
```
3. Fixing CoreDNS service Crash issue:

       1 . Add below DNS settings to sudo nano /etc/resolv.conf
             nameserver 8.8.8.8
             nameserver 8.8.4.4
       2. sudo systemctl daemon-reload
          sudo systemctl restart docker

 


