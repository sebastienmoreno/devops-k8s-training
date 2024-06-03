# Intervention 1/3
*Mardi - durée ~2h*

<img src="docs/slides/static/devops.png" class="floatRight" width=50% />

- Pourquoi DevOps?
- Pourquoi les micro-services?
- Le futur des micro-services
- La “MEP”
- Evolution des technos
- Time-to-market
- Maitriser son Infra
- Maîtriser les déploiements



<p class="r-fit-text">DEVOPS</p>



## Pourquoi DevOps

DevOps est une culture, un mouvement ou une pratique qui met l'accent sur la ***collaboration et la communication*** des développeurs et des autres professionnels de l'IT, tout en automatisant le processus de livraison des logiciels et de modification des infrastructures. 

Bon nombre des idées (et des personnes) impliquées dans DevOps sont issues des mouvements ***agiles***.

Au cours des derniers mois, un mouvement a commencé à prendre forme. Il s'agit d'un mouvement de personnes qui pensent qu'il est temps de changer l'industrie informatique - il est temps d'arrêter de gaspiller de l'argent, de commencer à fournir de bons logiciels et de construire des systèmes qui évoluent et durent. **Ce mouvement est appelé Devops.**

**Patrick Debois, Agile 2008 conférence -> “Agile infrastructure”**

> source: http://www.jedi.be/blog/2010/02/12/what-is-this-devops-thing-anyway/



![](docs/slides/static/debois.png)



## Avant DevOps

<table>
<tr><td>

![](docs/slides/static/juggernaut.gif)

</td><td>

- Déploiements compliqués et risqués
- Peur du changement *(rex)*
- Mise en prod périlleuses
- "Ça marche sur ma machine !"
- Silos ... beaucoup de silos! *(rex)*
- Pas de visibilité sur la qualité ou viabilité du développement

</td></tr></table>



![](docs/slides/static/conway.png)



![](docs/slides/static/devops-actions.png)



![](docs/slides/static/devops-arrive.png)



![](docs/slides/static/devops-infra-agiles.png)



## Et aussi des outils (beaucoup...) !

<img src="docs/slides/static/outils-tableau-periodique.png" width="75%"/>

> source: [devops-tools-periodic-table](https://digital.ai/devops-tools-periodic-table)



## REX - Oracle Exadata

<img src="docs/slides/static/oracle-exadata.png" class="floatLeft" width="20%" />

- Entreprise de "Web Analytics" besoin d'améliorer exploitation, innovation et Time to market.
- Pour répondre à des exigences plus fines et plus spécifique des clients.
- Aussi pour répondre à la concurrence : gros acteurs et start-up innovantes.
- Besoin de + d'agilité (+ de release), innovation, DevOps, orienté client.
 

**Achat d'un rack Oracle Exadata:**
- A coûté très cher
- Pas permis de faire les évolutions souhaitées
- Plus de compétences rates nécessaires
- Scalabilité difficile

**Au final**: 
- Retour arrière après 6 mois de mise en oeuvre.
- On pris conscience que le Cloud leur permettrait d'expérimenter sans coût important.



## Devops aujourd'hui

<table>
<tr><td>

- Prise de conscience collective: Devops pousse des pratiques essentielles pour gérer les ressources permettant les mises en prod
- Certains points de vue plus tranchés: sans DevOps = mort du business assuré
- Outils bien installés: IaC, Cloud...
- Cloud Hybrid, Multi-Cloud
- Formations et centres d'excellence
- Audit de maturité pour mesurer la performance
- Economie d'échelle
- Met en plus avant la **Sécurité**
- Changement culturels: pas toujours en place


</td><td>

<img src="docs/slides/static/google-elite-performers.png" width="90%"/>

</td></tr></table>

> source: https://services.google.com/fh/files/misc/state-of-devops-2019.pdf



<p class="r-fit-text">MICROSERVICES</p>



## Exemple Uber : monolithe

<img src="docs/slides/static/archi-uber1.png" width="50%"/>

> [Source DreamFactory](https://blog.dreamfactory.com/microservices-examples/) 



## Exemple Uber : microservice

<img src="docs/slides/static/archi-uber2.png" width="60%"/>



## Uber’s microservices architecture from 2019

<img src="docs/slides/static/archi-uber3.png" width="60%"/>




## Equipe fonctionnelle

| Equipe fonctionnelles | Données décentralisées |
|---|---|
| ![Alt text](docs/slides/static/functional-staff.png) | ![Alt text](docs/slides/static/decentralised-data.png) |

> [Source Martin Fowler](https://martinfowler.com/articles/microservices.html) 



## Exemple 

<table><tr><td>

- Back-end de votre site Web soit au **max de sa capacité** 
- Front-end et le stockage **ne soient pas sollicités**. 

=> vous pouvez faire évoluer le back-end séparément pour améliorer les performances.

=> vous pourriez également choisir de changer le service de stockage ou de modifier le front sans avoir d'impact sur les autres composants.

</td><td>

[![Alt text](docs/slides/static/simple-microservice.png)](https://excalidraw.com/#json=wMSlHJT6fQ4LfCVy7KWzy,Lxer_6owaKRNxOq8_vG6nA)

</td></tr></table>




## Comment on est arrivé aux micro-services? 

L'arrivée de la notion de microservices est complémentaire à DevOps.

L'idée première était d'avoir:
- une production plus facilement mise à jour
- réduire le time-to-market
- moins d'impacts en cas de problèmes
- d'être plus libre des technologies utilisées (Java, JS, Go...)
- une séparation des responsabilités par une plus grande modularité
- mise à l'échelle (scaling)



## Micro-services

Des services API Rest / Asynchrones ayant des domaines de responsabilité bien déterminés.

<img src="docs/slides/static/micro-services.png" width="65%"/>

> source: [microservices-orchestration-with-kubernetes](https://faun.pub/microservices-orchestration-with-kubernetes-1cbb737cfa46)



![Alt text](docs/slides/static/xaas.png)



## Avantages et inconvenients

<table><tr><td>

**Avantages**
- Amélioration de la scalability
- Amélioration de l'isolation des erreurs
- Agnostique au langage de programmation et technologie
- Déploiement plus simple
- Possibilité de réutilisation dans différents domaines d'activité
- Mise sur le marché plus rapide (time to market)
- Possibilité d'expérimenter
- Amélioration de la sécurité des données
- Flexibilité pour le outsourcing
- Optimisation des équipes
- Attractif pour les ingénieurs
  
</td><td>

*Inconvénients*
- Les coûts initiaux sont plus élevés avec les microservices
- Le contrôle des interfaces est crucial
- Un autre type de complexité
- Tests d'intégration
- SOA vs microservices

</td></td></table>

> [source Gitlab](https://about.gitlab.com/blog/2022/09/29/what-are-the-benefits-of-a-microservices-architecture/)



## Stratégie autour des micro-services

**Organisation:**
- Séparer les responsabilités techniques (Bounded Context - architecture Hexagonale)
- Pizza team

**Positif:**
- Rapidité de développement
- Couplages lâches entre les services permet évolution
- Flexibilité des technos
- Scalabilité

**Négatif:**
- Ne résoud pas tous les problèmes (au contraire)
- Complexité de mise en oeuvre: dev et déploiement
- Archi plus complexe, parfois inutilement!
- Couplages et monolithes distribué
- Migroservices : faux microservices



## Micro-services aujourd'hui

- Culture du micro-service en place pour des third-party (OAuth, Messaging, database...)
- Revenir aux archis hexagonales pour mieux gérer le passage aux micro-services
- Retour aux monolithes? En tout cas mieux gérer la modularité et la complexité!
- Beaucoup de REX permettent de mieux gérer les situations
- Utilisation de **nanoservices** dans des cas de scaling très forts *(rex)*



## Le futur des micro-services: 

- Usage plus maitrisé des micro-services (monolithes plus facile a gérer)
- Utilisation des FaaS (rex) dans des cas précis
- Répartition services synchrones / asynchrones
- Stack technique plus légère et rapide (QuarkusIO)



<center>  <h1>LA PRODUCTION / DELIVERY</h1> </center>



## Qu’est-ce que la “production”? 

**Mise en production ou MEP**

- Moment le plus tendu d'un projet: *Casser la prod*
- Dev + Infra + Environnement + Data + Sécurité
- Intégration Continue (CI : Continuous Integration) pour tester en avance des MEP
- Continuous Delivery (CD) pour gérer Build / Tests et déploiement

**Process de MEP:**

- Promotion: builder une fois et passer d'un environnement à l'autre (Intégration -> Preprod -> Prod) par validations
- Eviter les silos, mais passer par une validation manuelle



## Infrastructure

- Infras de plus en plus complexes: *cloud, cloud hybrides, multi cloud...*
- Problématique d'où sont stockées les données: *sécurité, droits, performance...*
- **Obligatoire** de maîtriser son infra par IaC: *Terraform, Ansible...*
- Sécurité: mise à jour OS, libs, etc
- Observabilité: *monitoring, tracabilité (Open Telemetry)*

**Les infrastructures locales sont moins agiles:**

- moins flexibles pour du lab/test
- nécessitent plus de maintenance
- moins rapides à mettre à jour (patchs sécurité...)
- moins disponibles



## Maitriser ses déploiements

- Gestion des déploiement par scripts ou CI: possible mais complexe et maintenance importante
- Architectures complexe: Besoin de cartographie
- GitOps: services déployés à l'image des descriptions Git
- Testabilité du déploiement

*Il est devenu essentiel dans les projets actuels d'avoir une souplesse et maitrise de ses déploiements.*



# Questions ?

![](docs/slides/static/the-end.gif)
