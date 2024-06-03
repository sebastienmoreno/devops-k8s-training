# Intervention 3/3

*Jeudi le matin - durée 1h30*

<table><tr><td width="50%">

- Deploiement
- Integration: 
  - Ingress
  - API
  - Messages
- Observabilité
- Sécurité

</td><td>

<img src="docs/slides/static/deployment.png" width="90%"/>

</td></tr></table>



<center>  <h1>DEPLOYMENT</h1> </center>



<img src="docs/slides/static/deploy-manifest.jpg" width="90%"/>



<img src="docs/slides/static/deploy-service.png" width="90%"/>



## Deploiement: manifests Kubernetes

<table><tr><td>

*<center>Deployment</center>*
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: nginx-deployment
 labels:
   app: nginx
spec:
 replicas: 3
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
       image: nginx:1.7.9
       ports:
       - containerPort: 80
```

</td><td>

*<center>Service</center>*
```yaml
kind: Service
apiVersion: v1
metadata:
 name: my-service
spec:
 selector:
   app: MyApp
 ports:
 - protocol: TCP
   port: 80
   targetPort: 9376
```

</td></tr><tr><td colspan="2">

*<center>Application des manifests:</center>*
```sh
kubectl apply -f deployment.yaml -f service.yaml
```

</td></tr></table>



## Deploiement: Kustomize
### Composing

<table><tr><td>

*deployment.yaml*
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
```

</td><td>

*service.yaml*
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
```

</td><td>

*kustomization.yaml*
```yaml
resources:
- deployment.yaml
- service.yaml
```

</td></tr><td colspan="3">

*Application Kustomize:*
```sh
kubectl apply -k src/manifests
```

</td></tr></table>



## Deploiement: Kustomize
### Configmap Generator

<table><tr><td>

*kustomization.yaml*
```
configMapGenerator:
- name: example-configmap-3
  literals:
  - FOO=Bar
generatorOptions:
  disableNameSuffixHash: true
  labels:
    type: generated
  annotations:
    note: generated
```

</td><td>

*résultat:*
```yaml
apiVersion: v1
data:
  FOO: Bar
kind: ConfigMap
metadata:
  annotations:
    note: generated
  labels:
    type: generated
  name: example-configmap-3
```

</td></tr></table>



## Deploiement: Kustomize
### Patch

<table><tr><td>

*deployment.yaml*
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
```

</td><td>

*kustomization.yaml*
```yaml
resources:
- deployment.yaml
images:
- name: nginx
  newName: my.image.registry/nginx
  newTag: 1.4.0
```

</td><td>

*résultat:*
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      run: my-nginx
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - image: my.image.registry/nginx:1.4.0
        name: my-nginx
        ports:
        - containerPort: 80
```

</td></tr></table>



## Deploiement: Helm

<table><tr><td>

*Helm* est un outil de gestion des paquets Kubernetes appelés *charts*.

- Créer de nouveaux charts à partir de zéro
- Packager des charts dans des archives (tgz)
- Interagir avec les dépôts de charts où les charts sont stockées.
- Installer et désinstaller des charts dans un cluster Kubernetes existant
- Gérer le cycle de *release* des charts qui ont été installés avec Helm
- Lancer des *smoke tests* sur les releases de charts installés


</td><td width="20%">

![Alt text](docs/slides/static/helm.png)

</td></tr></table>



![Alt text](docs/slides/static/helm-struct.png)



<center><h1 style="color:red;">DEMO</h1><img src="docs/slides/static/working.gif" width="30%"/></center>



## Opérateurs


- Contrôleur Kubernetes spécifique à une application
- Permet de créer, configurer et gérer des instances d'applications complexes par l'API Kubernetes
- Utilise des ressources personnalisées (CRD) pour gérer des applications et leurs composants



## Opérateurs

![Alt text](docs/slides/static/kubernetes-operators.jpg)

> Source: https://blog.codecentric.de/kubernetes-operators-helm



## Exemple d'opérateur: Elastic-Stack

Une fois l'opérateur déployé: pod + CRD, on utilise des instances de ressources

<table><tr><td width="30%">

*<center>Elasticsearch:</center>*
```yaml
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: elasticsearch-sample
spec:
  version: 8.5.0
  nodeSets:
    - name: default
      config:
        node.roles:
          - master
          - data
        node.attr.attr_name: attr_value
        node.store.allow_mmap: false
      podTemplate:
        metadata:
          labels:
            foo: bar
        spec:
          containers:
            - name: elasticsearch
              resources:
                requests:
                  memory: 4Gi
                  cpu: 1
                limits:
                  memory: 4Gi
                  cpu: 2
      count: 3

```

</td><td width="30%">

*<center>Beat:</center>*
```yaml
apiVersion: beat.k8s.elastic.co/v1beta1
kind: Beat
metadata:
  name: heartbeat-sample
spec:
  type: heartbeat
  version: 8.5.0
  elasticsearchRef:
    name: elasticsearch-sample
  config:
    heartbeat.monitors:
      - type: tcp
        schedule: '@every 5s'
        hosts:
          - 'elasticsearch-sample-es-http.default.svc:9200'
  deployment:
    replicas: 1
    podTemplate:
      spec:
        securityContext:
          runAsUser: 0
```

</td><td width="30%">

*<center>Kibana:</center>*
```yaml
apiVersion: kibana.k8s.elastic.co/v1
kind: Kibana
metadata:
  name: kibana-sample
spec:
  version: 8.5.0
  count: 1
  elasticsearchRef:
    name: elasticsearch-sample
  podTemplate:
    metadata:
      labels:
        foo: bar
    spec:
      containers:
        - name: kibana
          resources:
            requests:
              memory: 1Gi
              cpu: 0.5
            limits:
              memory: 2Gi
              cpu: 2
```

</td></tr></table>

> Source: https://operatorhub.io/operator/elastic-cloud-eck



## Integration continue

L'intégration continue est portée par les "pipelines" scriptés ou déclaratifs sur le code source.

![Alt text](docs/slides/static/continuous-integration.png)



![Alt text](docs/slides/static/continuous.png)



## Déploiment: GitOps

- Continuous Delivery / Continuous Deployment
- Pas de scripts complexes portés par l'intégration continue
- Basé sur les outils de déploiement classiques et sur Git

> *Un référentiel Git qui contient toujours des descriptions déclaratives de l'infrastructure actuellement souhaitée dans l'environnement de production et un processus automatisé pour que l'environnement de production corresponde à l'état décrit dans le référentiel.*

> Kelsey Hightower



<img src="docs/slides/static/gitops.png" width="80%"/>

> Source https://techblost.com/what-is-gitops/



## Ce qu'apporte GitOps

- Déployer plus rapidement et plus souvent
- Récupération facile et rapide des erreurs
- Gestion simplifiée des secrets
- Déploiements auto-documentés
- Le partage des connaissances en équipe



## GitOps: les outils

- *ArgoCD* : Un opérateur GitOps pour Kubernetes avec une interface web
- *FluxCD* : L'opérateur GitOps pour Kubernetes par les créateurs de GitOps Weaveworks
- *Fleet* : Fleet est un opérateur GitOps à l'échelle. Conçu par Rancher pour gérer jusqu'à un million de clusters.
- *Gitkube* : Un outil pour construire et déployer des images docker sur Kubernetes en utilisant git push.
- *JenkinsX* : Continuous Delivery sur Kubernetes avec GitOps intégré.
- *Terragrunt* : Un wrapper pour Terraform pour garder les configurations DRY et gérer l'état à distance.
- *WKSctl* : Un outil de gestion de la configuration des clusters Kubernetes basé sur les principes de GitOps.
- *werf* : Un outil CLI pour construire des images et les déployer sur Kubernetes via une approche "push".
- *Gimlet* : Gimlet est un outil en ligne de commande et un tableau de bord qui regroupe un ensemble de conventions et de flux de travail correspondants pour gérer efficacement une plateforme de développement Gitops.



## Exemple GitOps: FluxCD

<table><tr><td>

*<center>GitRepository</center>*
```yaml
apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: GitRepository
metadata:
  name: podinfo
  namespace: flux-system
spec:
  interval: 30s
  ref:
    branch: master
  url: https://github.com/stefanprodan/podinfo
```

</td><td>

*<center>Kustomize</center>*
```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: podinfo
  namespace: test
spec:
  interval: 5m0s
  path: ./kustomize
  prune: true
  sourceRef:
    kind: GitRepository
    name: podinfo
  targetNamespace: default
```

</td><td>

*<center>Ou Helm</center>*
```yaml
apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: podinfo-helm
  namespace: test
spec:
  interval: 5m
  chart:
    # Peut être aussi un repo Helm
    spec:
      chart: ./helm/charts/podinfo
      sourceRef:
        kind: GitRepository
        name: podinfo
      interval: 1m
  values:
    # Values a remplacer
```

</td></tr></table>

> Et aussi: Notifications, Webhook, Vault, intégration cloud



![Alt text](docs/slides/static/flux-helm-operator-registry.png)



<center><h1>Integration</h1></center>



## Integration: Ingress, API, Messages

**Intégration:**
- Assemblage des services entre eux
- Gestion du couplage et des versions
- API synchrone
- Message asynchrone



## API

Définir son API avec OpenAPI Specification & Swagger Tools

![Alt text](docs/slides/static/swagger.png)



## Ingress

![Alt text](docs/slides/static/ingress.png)



## Ingress Controller

- AKS Application Gateway Ingress Controller
- Ambassador API Gateway is an Envoy-based ingress controller.
- Apache APISIX ingress controller is an Apache APISIX-based ingress controller.
- Avi Kubernetes Operator provides L4-L7 load-balancing using VMware NSX Advanced Load Balancer.
- The Citrix ingress controller works with Citrix Application Delivery Controller.
- Gloo is an open-source ingress controller based on Envoy, which offers API gateway functionality.
- *HAProxy Ingress* is an ingress controller for HAProxy. *(rex)*
- The *HAProxy Ingress Controller* for Kubernetes is also an ingress controller for HAProxy.
- *Istio Ingress* is an Istio based ingress controller. *(rex)*
- The *Kong Ingress Controller* for Kubernetes is an ingress controller driving Kong Gateway.
- Kusk Gateway is an OpenAPI-driven ingress controller based on Envoy.
- The *NGINX Ingress Controller* for Kubernetes works with the NGINX webserver (as a proxy).
- The Pomerium Ingress Controller is based on Pomerium, which offers context-aware access policy.
- The *Traefik Kubernetes Ingress provider* is an ingress controller for the Traefik proxy.
- Tyk Operator extends Ingress with Custom Resources to bring API Management capabilities to Ingress. Tyk Operator works with the Open Source Tyk Gateway & Tyk Cloud control plane.
- Voyager is an ingress controller for HAProxy.
- ...



## Choisir son Ingress Controller selon ses besoins

<center><img src="docs/slides/static/kubernetes-ingress-comparison.png" width="60%"/></center>

> Source: https://blog.palark.com/comparing-ingress-controllers-for-kubernetes/



## Ingress config

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: simple-fanout-example
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /foo
        pathType: Prefix
        backend:
          service:
            name: service1
            port:
              number: 4200
      - path: /bar
        pathType: Prefix
        backend:
          service:
            name: service2
            port:
              number: 8080
```



## Istio

![Alt text](docs/slides/static/istio-alb-breakdown.png)

> [Source](https://excalidraw.com/#json=NpR3zD8kImiNUB3Q8yUYY,76WeweiwvcGt1QVVBlMTSw)



![Alt text](docs/slides/static/istio-security.png)



## Message Queue

<table><tr><td>

![Alt text](docs/slides/static/mq.png)

</td><td>

*Avantages:*
- Backup
- Messages asynchrones
- Résilience
- Scalability

*Inconvénients:*
- Système plus complexe
- Format de message a mettre en place
- Surveillance essentielle du bus de messages
- Difficile de traiter en synchrone

</td></tr></table>

> Source: https://itzone.com.vn/en/article/microservice-why-use-message-queue/



## Asynch-API

Similaire à OpenAPI pour les messages asynchrones

![Alt text](docs/slides/static/asynchapi.png)



## Bonnes pratiques

- Eviter un couplage trop fort des API de services
  - Sinon risque de monolithe distribué...
- Est ce le bon découpage de services? D'API?
  - Ateliers d'impact mapping
- Versioning : *Semantic Versionning* (eg.: X.Y.Z, 1.2.3...)
- Microservices: couplage minimal, messaging...



## A éviter: le monolithe distribué

![Alt text](docs/slides/static/example-distributed-monolith.png)

> Source: https://www.techtarget.com/searchapparchitecture/tip/The-distributed-monolith-What-it-is-and-how-to-escape-it



<center><h1>Observabilité</h1></center>



## Observabilité

*Avec 10 micro-services ça va...*

*... mais avec 10000?*

**Monitoring est primordial à la mise en place d'architectures distribuées.**

- Souvent négligé sur des projets rapides
- Pourtant de nombreuses solutions existent sur le marché:

<table><tr><td width="70%">

**Logs:**
- Elastic Stack
- Graylog
- Loki
- Splunk
- ...

**Metrics and alerting:**
- Prometheus / Grafana
- InfluxDB with Kapacitor
- Sensu
- ...

</td><td width="70%">

**Global Monitoring:**
- NewRelic
- DataDog
- Sysdig
- ...

**Tracing (Open-Telemetry):**
- Jaeger
- Zipkin

</td></tr></table>



## Logs: exemple Elastic Stack

<img src="docs/slides/static/elastic-infra.png" width="90%"/>



## Composants Elastic Stack

![Alt text](docs/slides/static/elastic-advanced.png)



## Elastic : Kibana

![Alt text](docs/slides/static/kibana.png)



## Metrics: exemple Prometheus

<img src="docs/slides/static/prometheus-arch.png" width="85%"/>



## Metrics: Grafana

<img src="docs/slides/static/grafana.png" width="85%"/>



## Tracing

- Une trace est un chemin de données/exécution à travers le système, et peut être considérée comme un graphe acyclique.
- Elle permet l'analyse des appels, traitements, requêtes... *(rex)*
- Les systèmes de tracing permettent aussi de suivre un *correlation-id* sur l'ensemble des traces.

![Alt text](docs/slides/static/spans-traces.png)



## Tracing: exemple Jaeger

<img src="docs/slides/static/jaeger-arch.png" width="85%"/>



## Jaeger UI

![Alt text](docs/slides/static/jaeger-ui.png)



<center><h1>Sécurité</h1></center>



## Sécurité externe

Sécurité externe:
- firewall
- cert-managers



![Alt text](docs/slides/static/cert-manager.png)



## Sécurité interne

A l'intérieur du cluster des barrières de sécurité doivent être mise en place pour éviter un détournement des services.

- Sécurité du cluster: RootLess
- Sécurité des containers: RootLess, SecurityContext
- Vault pour les secrets: pas de mots de passe en clair mais des secrets dans un Vault
- service-mesh: pour autoriser les services à s'appeler entre eux et à l'extérieure
- service-account Kubernetes: pour controler l'appel des API Kubernetes




## Sécurité interne: Docker

<table><tr><td>

**Bonnes pratiques:**
- Utiliser user non *root*, limiter les privilèges
- Mot de passes transmis au run (par variable d'environnement)
- Image avec *minimum* runtime
- Mise à jour *régulière* de l'image
- Analyse *automatique* des failles sécurité, base d'image de confiance
- Build sans surplus (multi-stage)

</td><td>

```dockerfile
FROM alpine:3.12
# Create user and set ownership and permissions as required
RUN adduser -D myuser && chown -R myuser /myapp-data
# ... copy application files
USER myuser
ENTRYPOINT ["/myapp"]
```

</td></tr></table>

> Exemples: https://sysdig.com/blog/dockerfile-best-practices/



## Securité interne: Kubernetes

Mettre en place *SecurityContext* Kubernetes

<table><tr><td>

- Privilèges pour chaque containers
- Permissions sur volumes
- Seccomp profile ([Docker](https://docs.docker.com/engine/security/seccomp/) et [Kubernetes](https://kubernetes.io/docs/tutorials/security/seccomp/) )
- SELinux labels

</td><td>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  volumes:
  - name: sec-ctx-vol
    emptyDir: {}
  containers:
  - name: sec-ctx-demo
    image: busybox:1.28
    command: [ "sh", "-c", "sleep 1h" ]
    volumeMounts:
    - name: sec-ctx-vol
      mountPath: /data/demo
    securityContext:
      allowPrivilegeEscalation: false
```

</td></tr></table>

> Voir https://kubernetes.io/docs/tasks/configure-pod-container/security-context/



## Sécurité interne: Service Mesh

- Chiffre le traffic avec [Mutual TLS](https://istio.io/latest/docs/ops/best-practices/security/#mutual-tls)
- Filtrer le trafic avc [Authorization policies](https://istio.io/latest/docs/ops/best-practices/security/#authorization-policies)
- Verification [TLS](https://istio.io/latest/docs/ops/best-practices/security/#configure-tls-verification-in-destination-rule-when-using-tls-origination)
- ...

> Exemples Istio: https://istio.io/latest/docs/ops/best-practices/security/



![Alt text](docs/slides/static/istio-security.png)



# Questions ?

![](docs/slides/static/the-end.gif)
