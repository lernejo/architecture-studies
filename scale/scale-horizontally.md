# Dimensionnement horizontal

Le dimensionnement horizontal consiste à augmenter le nombre de conteneurs / vms / serveurs pour augmenter de manière
linéaire le nombre d'appels que le système peut traiter.

A l'inverse, le dimensionnement vertical, consiste à augmenter les capacités du-de-la conteneur / vm / serveur (CPU, RAM, FS) pour
augmenter le nombre d'appels que le système peut traiter.
 
Le dimensionnement vertical est à éviter dans la mesure du possible car le coût de matériel plus puissant est exponentiel au regard des performances apportées.



Considérant une application _classique_ comme celle-ci :  

### Diagramme de Contexte
![Context Diagram](https://kroki.io/c4plantuml/svg/eNptUkFqwzAQvOcVWx9KCqW99AGBtCTQS4gNPQbZ2tjCshR2VyH9fSXLsUvpbcTM7Mwu2rAokjDY1YNxjQ0aA1nYvp223gne5OWSuNUBib1bC6qmQ3qGosqoeLpTLNHrJFJlRpFald8sOKx7T9iLj9xnRkWEO1IaGSYf4A2pMYy8-E4fN1m3RrpQJ72RfaiLjICRrqZB6DynpGx4r0eLHuXetxZBk7liMbaK0TFNCYI_Q5vTldOgUWlrXE4-ol22XHpvCZPxXjIqk3DeeREesTWxCoFx6W297yFcpri_tnm3Q6it4W6a3-Qqo3ae_Etr7SKEM_lhmvufNs89I-paNT3DKxjmMFeZLePNjvES8AhfZGQ6VbrcvqoOZdRv0On4GX4ArDi-Mg== "Context Diagram")

### Diagramme de Container  
![Container Diagram v0](https://kroki.io/c4plantuml/svg/eNp1Ut1K60AQvs9TzOmFVNCq4AOoVSx4U5pyzs0B2WSnydDtbpmdVYv47s4mbVTUXA37zfc35CqKYUkbV_whX7tkMbGD6eXjNHgx5JEn24wWQuIQhlewZBo2G_h7XhRz5Bj8WNDULfIJjJb9NDo-QFFU2YtCZT8pVJS7KLh5vAnJW8O7cX2h-ENgXEsYHcNrAfoNjuOI_NSpH1ay2pbJN6c3Iaj2QxBHPj9fJ2nVhGojCEmJcfLf37OxCPiCXFPECKhqO7iENqQOL7N-hGesIglONOHbkPHuRcYNSZsqVb8nmaVq1E-QY1GNKhNzq55wW3UU262H0OjpLNMTdplF84PeXbOFFTQ5VgTjLVg0VhtgzNdZoPu46NB9yph5hxZZb7ZczktlZMJw54GwwIY0EQN5CSfgQlhD2u5df6UPXeepchTbvWHdU8pydiDsbbqiC40PR_CP9Xx9v8_6X_Y_6Tv3IQ4rDpufHb4lWiHaytTrCGdAMaavbYor9FZ_3HeOVfRw "Container Diagram")

## Introduction d'un répartiteur de charge

Le _**dimensionnement horizontal**_, c'est la capacité du système à répartir la charge sur plusieurs instances (ou noeuds).  
Du point de vue des utilisateurs il n'y a qu'un seul service (https://korekto.io par exemple).  
Pour faire celà il est nécessaire d'introduire un répartiteur de charge (**load-balancer** en anglais).  
Il en existe de plusieurs types :
* hardware (Alteon, F5, etc.)
* software (Traefik, HAProxy, etc.)

Les seconds sont souvents plus simples à configurer de manière automatisée.

Attention ici, la partie qui exécute une tache toutes les 4 heures ne doit être active que sur une seule instance, sous  
peine de faire le travail en double, triple, etc. sans compter sur les potentiels états incohérents en base.

![Container Diagram v1](https://kroki.io/c4plantuml/svg/eNp1k01v2zAMhu_-FawPQwq0DQr0NKRBGzdrhg5dUAcbsGUwZIm1hSpSoI8u6bD_PspfTQ7LiSH5vnxIwTfOM-vDRiUnUnMVBAarILsqMqM9kxrtxTZWkxOBz_QXhnw2woIpydwZYKFYiSoGHnmtYyDQcXsK3CiF3EujHaTX113nWo_HEyff8ONqni0ei09fH1dF_vnHfPqzs_g1Gcf6dDxe67Xu_VKYTHgPMJ0Cc9BBJImXXh3ggZCssmwD3y6TZInWGT3yyHiN9gzSVRulp33JeVpdeyrlbUSlJN87j5tiZoIWzO5H_JLqD8biizfpKfxJgH7DxJEqqfzFMHE-Y4ppTv6UWNwurdntye-oOxs5tK8NTOe41lLTa5DORV2-tVJX5zNjiOrBeCV1TN8GXxOe5MwjBPJwF2udRysHv7F00iMl7i0TCLhDy6WjCtKkPVxBbQIJiOXvsN1850eV9HWI9PfSL0KZthFEQsmRVC7eoxXclY1ENO3GVHR0YeUrNsyeVgFagtjMM1SRwgHTAgQyQRvQanTXJ1Tvb9FcLbMYNT1wc7fVaplTd2weXqdpfsJKEokFqb2hnDEvELbdtP9Khx2XoVTS1d2w_tr5oieLM4a3uZNuyzyvgTOlBvPeu-sSLRUT8AG-W3qB9gSHKEf9ByhKvXOQumeTzoUjsBvUgr7Df1xeOM8= "Container Diagram")

## Découpage de l'applicatif

Certaines fonctions de l'application, sont executées plus au moins souvent, et utilisent plus ou moins de CPU.  
Il est souvent intéressant de découper une application monolithique en plusieurs petites applications pour pouvoir
dimensionner finement les fonctions les plus solicitées.  
Ce découpage doit respecter une unique règle pour être profitable :  
  >Les applications ainsi découpées doivent être **indépendantes** d'un point de vue métier et technique  
  >c.a.d que si une de ces applications est amenée à évoluer, elle doit pouvoir être mise à jour sans impact dans les autres applications

![Container Diagram v2](https://kroki.io/c4plantuml/svg/eNqNVGFr2zAQ_e5fcfWH4UKbsNBPIw1t3KwZHV2owwpbhpGlqy2qSEGS22Rj_30nx_aSwcby6aJ79967d-Ar55n19VpFJ1JzVQusrYL0Ik-N9kxqtINN6EYnAp_oL_TvaYI5U5K5M8BcsQJVKDzySodCoOP2FLhRCrmXRjuILy9b5EoPh2Mnv-O75Syd3-fvP90v8-zDl9nka0vxbTwM_clwuNIr3fHFMB7zzsBkAsxBayKKvPTqwB4IyUrL1vB5FEULtM7oxCPjFdoziJf7Kj7tWs7T6tpTK9tX1IqynfO4zqem1oLZXcLfUv_OWHz2Jj6FHxHQr1dMVEHtj4aJ8ylTTHPip4f59cKa7Y74jtBp4tC-NGZaRnjEYqWlpovQrAuz2cZKXZ5PjSFnd8YrqcPzde0rsig58wg18bjBSmeBzsErFk56HPyp18qNDvQK5nn1D5lbywQCUwpwi5ZLR_RIHDu4gMrU1pHGzz6l2dYnpfRVHVK4lX5eF_G-giAtOdKQC7nuB26KZkQ0cGNKOp6w8gUbQ54sAgVB-5knKIMRB0wLEMgE2cOgHT2g-n3TJv3UYpjp_Db5L5eLjNAB3F-5AT9gKcmJBam9oTdjnqHetGp_He13XNSFkq5qxbqLZfPOWdDob3wj3SbEDZzy7Mk77hYl9q6YgDfwaOmK-wgOrRzgR_81cIQ_8N5flYzTdLeMdK4-2uQKtaAPwC9ZYl3E "Container Diagram")

## Suppression des SPOF

Les points de défaillance unique (Single Point Of Failure en anglais), sont des conteneurs du système qui ne peuvent pas être répliqués.  
Cela pose 2 problèmes :
* si le conteneur ne fonctionne plus, il y a une coupure de service
* si le trafic augmente (ici le nombre d'exercices à corriger), la seule solution est _par définition_ le dimensionnement vertical.

La cause des SPOFs est souvent un état en mémoire qui ne peut être facilement répliqué.  
C'est le cas de notre conteneur [**batch**].  

En effet, si plusieurs instances devaient s'exécuter, comment pourraient-elles _simplement_ se synchroniser pour se répartir
le travail et reprendre celui des autres instances dans le cas de pannes ?

Dans notre cas, il est possible de faire plus simple en utilisant les _hooks_ de GitHub afin d'être notifié en cas de
changement, plutôt que de lancer une tache quoi qu'il arrive.

![Container Diagram v3](https://kroki.io/c4plantuml/svg/eNqNVF1vGjEQfL9fseGhIigBlfapIihAaIhSUZRDjdRSIZ-9cFaMjWwfgVb9713fF7QVUnkyu7MzszuIW-eZ9dlGRRdSc5UJzKyC0fvlyGjPpEbb3oZudCFwRV-hro-auGRKMncFuFQsQRUeHnmqw0Og4_YSuFEKuZdGO2jc3JTIhe50ek7-wA_z8WgyXX78PJ0v44ev4_63kuJ7rxP6_U5noRe64mtAr8crA_0-MAeliSjy0qsTeyAkW1u2gS_vomiG1hnd9Mh4ivYKGvPi1bisWs7T6tpTKy5e1Irig_O4WQ5NpgWzhyZ_S_1HY_HFm8Yl_IyAPrViUyXU_mSYuB4yxTQnfipMBjNr9gfi-wM9ajq0u9xMyQjPmCy01JQIzbowG2-t1OvroTHk7NF4JXUoDzKfkkXJmUfIiMe1FzoOdA5eMXGSym9gMHtw7XOy3RNdOpMovJ7Ruw8AwD1aLh3CK4mDNl6uJAoS-FVfarz3zbX0aRYucS_9JEsaxQuCrOQIqXHhtsXAXZKPiBxuzJoCFFbuMPfiyR7QMWgZsypMOmBagEAmyBmdiDJ6QnXMNU9gZDHMVHbzDObzWUzoAK6TzsFPuJbkxILU3lDNmBfItqXa2dF6x1mWKOnSUqxKLZ5UzoJGnfOddFvmeRoyAs6UqgUq_hIpCmdMUIrPNqSZn-FvO0fy7in7NguGdmT0lP9EoPvfCjX-ZGGljtvSdHUB6Vz2z_rVWH7qafjFHMBoaLWCx1arcknoW9SC_md-A_8cfgI= "Container Diagram")

## Ne pas communiquer par le stockage !

Quand nous avons découpé notre applicatif, nous avons commis une grave erreur.  
En effet, le conteneur [**web**] et le conteneur [**grader**] communiquent par la base de données.  

C'est un **anti-pattern** d'architecture, car :
* comment changer la base par une autre sans impacter les 2 conteneurs ?
* comment changer la façon de stocker la donnée sans impacter les 2 conteneurs ?

Bref, ces 2 conteneurs sont **couplés**.

Le but ici va être d'introduire un nouveau container qui va _abstraire_ le stockage:  

![Container Diagram v4](https://kroki.io/c4plantuml/svg/eNqNVF1PWkEQfb-_YuShAaKSok8NEgWtGI0lXlKTlobs3R25G5ddsrtXpE3_e2fvF9f4kfK0zJw5c-bMwKnzzPpspaI9qbnKBGZWwfh4MTbaM6nRHq5DNtoT-EBfoY6P27hgSjK3D7hQLEEVHh55qsNDoOO2A9wohdxLox20Tk5K5Fz3egMnf-OX2cV4crv4-u12toivflwMf5YUvwa9kB_2enM91xVfCwYDXgkYDoE5KEVEkZdeNeSBkGxp2Qq-H0fRFK0zuu2R8RTtPrRmxavVqVLO0-jaUyouXpSK4q3zuFqMTKYFs9s2_0z5a2Px0ZtWB_5EQJ-6Y1sllL4xTByMmGKaEz8FJmdTa563ge8FfNx2aJ9yNSUl3GMy11LTSqjYheJ4baVeHoyMIWnXxiupQ_gs8ylplJx5hIx43OFcx4HOwQYTJyn8Cc6mV-6Q-r7Ztt_oSz6JQuw7_S4DAPAZLZcOYUPNQRsvHySKV4ORD_2PjXhT0FFDkPPGsiWGCT5Q5YqBCQRUY5EJGnpjw_BkoUfq9Lde4sWzby-lT7OwpEvpJ1nSKl4QeCRHSI0Lay8KzpO8RORwY5Z0W8LKJ8z1kLyyB5iHwj4HTAsQJILU0fLIlTtUu5PLj2NMIv3OyNyV2WwaEzqA6yPMwXe4lKTEgtTeUMyYR8jWZbd3S-sZp1mipEvLZtU9xZNKWehRX-C5dGvmeRquBzhTqm5Q8ZfIYrd3hdf3Da9fwnfk_Sb7OguCnkhok7-s6FclR82S98UQTCQfiokr8lrKf8qv8Q03ldpZScWVvdK57JW3VVm-x9vwQ9mC0dDtBgO63coCQp-iFvT_-g_JXsnI "Container Diagram")

Ainsi, dans le cas où

* une partie de la donnée doit être stockée de manière différente (dans une base de donnée relationnelle par ex)  
  --> la seule modification à apporter est dans le code du conteneur [**storage API**].
* le format de spécification d'exercice évolue pour inclure plus de données
  --> [**storage API**] on propose une nouvelle API (GET / POST / PUT / DELETE) [/api/admin/specification/exercise/${name}/v2]()  
  --> [**web**] on utilise cette nouvelle API pour que les professeurs puissent écrire des spécifications plus détaillées  
  --> [**grader**] on utilise cette nouvelle API pour corriger les exercices plus finement  
  --> [**storage API**] on supprime l'ancienne API ([/api/admin/specification/exercise/${name}/v1]()) maintenant que plus aucun conteneur ne l'utilise  

Ce genre de changement peut être apporté sans coupure de service.
