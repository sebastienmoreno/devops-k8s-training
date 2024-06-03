# Intervention 2/3
*Mercredi soir - durée ~1h*

<table><tr><td>

- Kubernetes dans le privé
- L’écosystème Kubernetes
- Facilité la MEP
- Stateless / Stateful
- Persistence
- Ecosystème élargie

</td><td width="50%">

![Alt text](docs/slides/static/kubernetes.png)

</td></tr></table>



<center>  <h1>KUBERNETES</h1> </center>



## Position Kubernetes

![Alt text](docs/slides/static/kube-okd.png)



## Kubernetes dans le privé

Adoption de Kubernetes dans les entreprises du privé: 
- Présence de + en + forte
- **Engouement des organisations**: devient un standard du marché, orienté *containers*, orienté *microservices*, polyvalence de l'architecture, HA, integration dans les Cloud publics

<center><img src="docs/slides/static/kube-market.png" width="65%"></center>



![Alt text](docs/slides/static/xaas.png)



## Concepts et ressources

Les concepts de cluster facilite l'adoption:
- Ressources API: Deployment, Service, Pod...
- Stateless/Stateful: implications pour les mise en production
- Couplage lâche entre les ressources: infrastructure flexible

![Alt text](docs/slides/static/kube-resources.png)



## Kubernetes load-balancing

![Alt text](docs/slides/static/kube-services.png)



## Mais...

- Installation difficile (notamment pour edge, CI, test)
- Paramétrage complexe, 1ere expérience d’utilisation difficile
- Consommation ressources système importante
- Difficile à utiliser sur des environnements légers (IoT, Edge, ARM)
- Difficulté d'apprentissage par les devs
- Monitoring complexe

<img src="docs/slides/static/complexe.gif" width="50%" style="margin:40px 40px 40px 40px"/>



## Solutions

<table><tr><td width="70%">

*Communauté très active:*

- Intégrations dans les offres cloud: EKS, AKS, GKE...
- Outillage: Kubespray, Terraform, Kops, Rancher...
- Utilisation d'un PaaS qui encapsule Kubernetes: OKD, Rancher
- Distribution alternative: K3S...
- Distribution pour faciliter le dev: K0S, K3D / K3S, microk8s ...

</td><td>

[k3s:<br><img src="docs/slides/static/k3s.png" width="70%" /><br>https://youtu.be/9Jbxi_lz5tM](https://youtu.be/9Jbxi_lz5tM)

</td></tr></table>



## Cas d'utilisations

**Déploiement:**
- Déploiement de microservices: plateforme idéale pour ça (stateless, stateful)
- Agnostiques aux runtimes: idéal pour solutions riches (eg.: processing)
- Adaptable: deployment, jobs, cron, stateful/stateless
- Synchrones: Service Frontend / Backend API
- Asynchrones: Messages, MQTT, Streams
- Plateforme multi-solutions, multi-équipe *(rex)*
- Workflow *(rex)*

**Infrastructure:**
- Scaling
- Resilience
- Facilité de mise à jour des services



## Exemple d'architecture

<img src="docs/slides/static/kube-archi-exemple.png" width="90%"/>



<center>  <h1>Ecosysteme</h1> </center>



## Ecosystème

**L'écosystème autour de Kubernetes est tellement riche qu'il s'agit plus d'un ensemble d'écosystèmes.**

*Distributions:* K8S, K3S, Rancher, Openshift...

*Ingress / API Gateway:* Traefik, NGINX, HAProxy, Kong, Istio, LinkerD, Consul...

*Persistence:* StorageClass: GlusterFS, Minio

*FaaS:* OpenFaaS, Fission, Knative, Kyma...

*Asynchrone / Workflow:* Keda, Kubeflow, Kyma

*Deploiement:* Helm, Kustomize, FluxCD (GitOps)

*Observabilité:* Prometheus / Grafana, Sysdig...

*Sécurité:* Cert-manager, Istio...



[![Alt text](docs/slides/static/cncf-landscape.png)](https://landscape.cncf.io/)




## Service mesh : Istio

<table><tr><td>

- Traffic management
- Observability
- Security capabilities

</td><td>

<img src="docs/slides/static/istio.png" width="60%"/>

</td></tr></table>



## Mettre en prod... dans le calme!

Les services-mesh avec Kubernetes permettent une mise en prod "en douceur".

Cas d’usages:
- Test A/B 
- Blue-Green
- Canary



## AB Tests

<img src="docs/slides/static/ab-testing.png" width="90%"/>

> [source: Isak Kabir](https://towardsdatascience.com/how-to-conduct-a-b-testing-3076074a8458)



## Blue Green

<img src="docs/slides/static/blue-green.png" width="70%"/>

> [source: Flexagon](https://flexagon.com/blog/k8s-deployment-strategies-bluegreen-to-a-b/)



## Canary

<img src="docs/slides/static/canary.png" width="70%"/>

> [source: Jason Skowronski](https://dev.to/mostlyjason/intro-to-deployment-strategies-blue-green-canary-and-more-3a3)



## Persistance

<table><tr><td>

L'API PersistentVolume gère la façon dont le stockage est fourni et comment il est consommé :
- **PersistentVolume** (PV) : pièce de stockage provisionnée dans le cluster.
- **PersistentVolumeClaim** (PVC) : une demande de stockage.

**StorageClass** décrit les "classes" de stockage dynamiquement disponibles.

Les types de **PersistentVolume** sont implémentés sous forme de plugins : GlusterFS, NFS, AWSElasticBlockStore, AzureFile, GCEPersistentDisk, Flocker...

</td><td width="60%">

<img src="docs/slides/static/persistence-volume.png"/>

</td></tr></table>



## Persistent Volume Claim

D'un point de vu utilisateur, on déclare un **PVC** qui demande une capacité de stockage.
Le **PersistentVolume** résultant est monté en tant que volume dans le **Pod**.

<table><tr><td>

*<center>PersistentVolumeClaim</center>*

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: data
spec:
  resources:
    requests:
      storage: 100Mi
  storageClassName: myclaim
```

</td><td width="60%">

*<center>Pod</center>*

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypvc
  volumes:
    - name: mypvc
      persistentVolumeClaim:
        claimName: myclaim
```

</td></tr></table>



## StorageClass

| Volume Plugin        | Internal Provisioner |  Config Example  |
|----------------------|:--------------------:|:----------------:|
| AWSElasticBlockStore |           ✓          |      AWS EBS     |
| AzureFile            |           ✓          |    Azure File    |
| AzureDisk            |           ✓          |    Azure Disk    |
| CephFS               |           -          |         -        |
| Cinder               |           ✓          | OpenStack Cinder |
| FC                   |           -          |         -        |
| FlexVolume           |           -          |         -        |
| GCEPersistentDisk    |           ✓          |      GCE PD      |
| Glusterfs            |           ✓          |     Glusterfs    |
| iSCSI                |           -          |         -        |
| NFS                  |           -          |        NFS       |
| RBD                  |           ✓          |     Ceph RBD     |
| VsphereVolume        |           ✓          |      vSphere     |
| PortworxVolume       |           ✓          |  Portworx Volume |
| Local                |           -          |       Local      |



<center>  <h1 style="color:red">demo?</h1> </center>



<center>  <h1>Ecosystème élargie</h1> </center>



## Web IU, TUI et GUI

<table><tr><td>

**Web UI:**
- Kubernetes dashboard
- Octant
- ...

**Ligne de commande:**
- kubectl
- k9s
- ...

**GUI:**
- Lens
- Kubevious
- Aptakub
- ...

</td><td>

![Alt text](docs/slides/static/lens.png)

</td></tr></table>

> [Détails sur le blog de zwindler](https://blog.zwindler.fr/2022/02/21/webui-tui-gui-pour-kubernetes-episode-01/)



## Ecosystème élargie : OpenFaas

![Alt text](docs/slides/static/openfaas.png)

> [Source OpenFaas](https://docs.openfaas.com/architecture/invocations/)



## Exemple OpenFaaS

<img src="docs/slides/static/openfaas-conceptual-operator.png" width="80%"/>



## Ecosystème élargie : Fission

<img src="docs/slides/static/fission-architecture.png" width="80%"/>

> [Source Platform9](https://platform9.com/blog/introducing-fission-platform-run-functions-anywhere/)



## Demo Fission

**BDX I/O 2019 - Une fonction Quarkus déployée dans un Kubernetes K3S avec Fission…**

[![Alt text](docs/slides/static/demo-fission.png)](https://www.youtube.com/watch?v=DZSGaGEhGFE)



## Ecosystème élargie : KubeFlow

Le projet Kubeflow a pour objectif de rendre les déploiements de machine learning (ML) workflows sur Kubernetes simples, portables et évolutifs.

<img src="docs/slides/static/kubeflow-architecture.png" width="80%"/>

> [Source NVidia](https://betterprogramming.pub/kubeflow-pipelines-with-gpus-1af6a74ec2a)



## Ecosystème élargie : Keda


<table><tr><td width="40%">

**Keda** est construit pour être capable d'activer un déploiement Kubernetes en fonction d'événements provenant de diverses sources d'événements (RabbitMQ, Kafka, Pulsar...).
Il peut aussi scaler le nombre de pods en fonction de la charge.


> [Source Keda](https://keda.sh)

</td><td>

<img src="docs/slides/static/keda-architecture.png"/>


</td></tr></table>


# Questions ?

![](docs/slides/static/the-end.gif)