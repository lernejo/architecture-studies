# Dimensionnement horizontal

Le dimensionnement horizontal consiste à augmenter le nombre de conteneurs / vms / serveurs pour augmenter de manière
linéaire le nombre d'appels que le système peut traiter.

A l'inverse, le dimensionnement vertical, consiste à augmenter les capacités du-de-la conteneur / vm / serveur (CPU, RAM, FS) pour
augmenter le nombre d'appels que le système peut traiter.
 
Le dimensionnement vertical est à éviter dans la mesure du possible car le coût de matériel plus puissant est exponentiel au regard des performances apportées.



Considérant une application _classique_ comme celle-ci :  

### Diagramme de Contexte
![Context Diagram](https://kroki.io/c4plantuml/svg/eNptUkFqwzAQvOcVWx9KCqW99AGBUBrIJcSGHoNsbWxhWQq7q5D-vpLl2KH0NmJmdmYXbVgUSRjs6sm4xgaNgSxsP05b7wRv8nZJ3OqAxN6tBVXTIb1CUWVUvNwpluh1Eqkyo0ityh8WHNa9J-zFR26fURHhFymNDJMP8IbUGEZefKfPm6xbI12ok97ILtRFRsBIV9MgdJ5T0oNBj2LvW4ugyVyxGDvF4JilBMGfoc3ZymnQqLQ1Luce0S47Lq23hMl4rxiVSThvvAiP2JpYhMC49Lbe9xAuU9xf27zZIdTWcDfNb3KVUTtPftBauwjhTH6Y5v6nzXPPiLpWTc_wDoY5zFVmy3izY7wEPMM3GZlOlS63q6pDGfUbdDp-hV839b2M "Context Diagram")

### Diagramme de Container  
![Container Diagram v0](https://kroki.io/c4plantuml/svg/eNp1Ut1K60AQvs9TjL2QCloVfAC1iAVvSlM8N4JsstNk6Xa3zMyq5XDe_cwmbayouRr2m-9vyC2LIUkbX5y4UPtkMZGH6c3rNAYxLiBNthktxIlHGF7BOtOQ2cDzVVHMkTiGsaCpW6RzGC37aXR2gFhUOYhCZT8pVJQ7Fty83scUrKHduL5W_CkSriWOzuBvAfoNjmNGeuvUDytZbUsuNBf3Mar2UxTvQn6-S9KqiauNICQl8uQlPJKxCPiBVDtGBlS1HdxAG1OHl1mf4R0rdoITTfhvyPjwIePGSZsqVX90MkvVqJ8gx3I1qgznVkcE2y3H2OjhLLk37BKLpge9uiaLK2hyKAYTLFg0VvMj59ss0H_ec2g-Jcy8Q4esN1su56UyMmG48kBYYOM0D4ELEs_Bx7iGtN27_kofms5T5R23e8O6p5Tl7EDY23RFFxofTuEP6fH6fsf6X_aP9L3_FIcVxc3PDt8SrRBtZeo1wyU45vS1TXGLwepv-x-k6fPK "Container Diagram")

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

![Container Diagram v1](https://kroki.io/c4plantuml/svg/eNp1k01v2zAMhu_-FawPgwO0DQr0NKRBGy9rhg5dUBsbsGUwFIm1hSpSoI8u2bD_Psqx3eSwnBi-5MuHFHzrPLM-bFRyJjVXQWCwCvLrKjfaM6nRXm6jmpwJfKa_MOTzDCumJHPngJVia1Qx8MgbHQOBjtsRcKMUci-NdpDe3HSVKz0eT5z8je_Leb54rD5-eSyr4tP3-fRHZ_FzMo76dDxe6ZXu_VKYTHgPMJ0Cc9BBJImXXh3hgZCstmwDX6-SZInWGZ15ZLxBew5peYjSUS85T6trT1JxiEhKir3zuKlmJmjB7D7jV6Q_GIsv3qQj-JMA_YaJmVqT_NkwcTFjimlO_pRY3C2t2e3J76Q6zxza1xamc1xpqek1qM_FvmJrpa4vZsYQ1YPxSuqYvgu-ITzJmUcI5OEuV7qIVg5-4dpJj5S4t0wg4A4tl44UpEl7uIbGBGoglr_DdvOdz2rpmxDp76VfhHV6iCASSo7U5eI9jhpEW2xMTScXVr5iS-xpEaAViMw8Qx0ZHDAtQCATxE-L0VWfUL29RHuz3GLs6XHbq5XlsqDqWDy8TVv8hLUkDgtSe0M5Y14gbLtp_20dNlyGtZKu6Yb1ty4WPVmcMbzMB-m2zPMGOFNqMO-9uypxoGIC3sE3S_c_nOAY5aT-CEWpNw7q7tmkc-EE7Ba1oK_wH-a_OCk= "Container Diagram")

## Découpage de l'applicatif

Certaines fonctions de l'application, sont executées plus au moins souvent, et utilisent plus ou moins de CPU.  
Il est souvent intéressant de découper une application monolithique en plusieurs petites applications pour pouvoir
dimensionner finement les fonctions les plus solicitées.  
Ce découpage doit respecter une unique règle pour être profitable :  
  >Les applications ainsi découpées doivent être **indépendantes** d'un point de vue métier et technique  
  >c.a.d que si une de ces applications est amenée à évoluer, elle doit pouvoir être mise à jour sans impact dans les autres applications

![Container Diagram v2](https://kroki.io/c4plantuml/svg/eNqNU2Fr2zAQ_e5fcfWH4UCbsNBPIw1tvKwZHV2owwpbhpGlqy2qSEGSu2Rj_32nxPacwcby6aJ79967d_jaeWZ9vVHRmdRc1QJrqyC9zFOjPZMa7XAbutGZwCf6C917mmDOlGTuHDBXrEAVCo-80qEQ6LgdADdKIffSaAfx1VWDXOvRaOLkd3yzmqeL-_zdx_tVnr3_PJ9-aSi-TkahPx2N1nqtW74YJhPeGphOgTloTESRl1717IGQrLRsA5_GUbRE64xOPDJeoT2HeHWs4kHbcp5W155a2bGiVpTtncdNPjO1FszuE_6a-nfG4rM38QB-REC_TjFRBbU_GCYuZkwxzYmfHhY3S2t2e-I7QaeJQ_tyMNMwwiMWay01XYRmXZjNtlbq8mJmDDm7M15JHZ5val-RRcmZR6iJxw3XOgt0Dr5h4aTH4Z96jdy4p1cwz6t_yNxaJhCYUoA7tFw6okfi2MMlVKa2jjR-dinNdz4ppa_qkMKt9Iu6iI8VBGnJkYZcyLU3IA5gY0o6nbDyBQ92PBkEioG2M09QBhsOmBYgkAkyh0E5ekD1-6KH7FOLYaZ1e0h_tVpmhA7g7sYH8AOWknxYkNobejPmGepto_bX0W7DZV0o6apGrL1XtmidBY3uwm-l24awgVOaHXnL3aDE0RUT8AoeLd3wGEHfSg8__q-BE3zPe3dTMk7T7TLSufpkk2vUgj7_X5brXR4= "Container Diagram")

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

![Container Diagram v3](https://kroki.io/c4plantuml/svg/eNqNVF1vGjEQfL9fsbmHiqAEVNqniqAApSFKRFEONVJLhYy9cFaMjWwfgVb9710fd8e1FVJ5MvsxMzuDuHWeWZ9tVHQhNVeZwMwqGL5fDI32TGq0rW3oRhcCV_QVqvqwgQumJHNXgAvFlqjCwyNPdXgIdNxeAjdKIffSaAfxzU0xOdftdtfJH_hhNhqOJ4tPnyezRXL_ddT7VkB877ZDv9duz_Vcl3gxdLu8FNDrAXNQiIgiL72qyQMh2dqyDXx5F0VTtM7ohkfGU7RXEM-Or_iybDlPp2tPreT4olaUHJzHzWJgMi2YPTT4W-o_GIsv3sSX8DMC-lSMDbWk9qNh4nrAFNOc8Kkw7k-t2R8I74_pYcOh3eViCkR4xuVcS02J0K4Lu8nWSr2-HhhDyh6MV1KHcj_zKUmUnHmEjHBca66TAOfgFZdOUvkN9Kf3rnWOtlPjJZvEUesZvrswALhHy6VDeCVy0MbLlURBBL8qp0Z731hLn2bBiTvpx9kyPr4g0EqOkBoXvK0tiHzYmDXFJ6zcYa7EkzggK-gUszpKdMC0AIFMkC4yiBJ6QnVKNfd_aDHslGLzBGazaULTYbjKOR9-wrUkHRak9oZqxrxAti3Yzq5WF06zpZIuLcjKzJJxqSxwVCl_lG7LPE9DQsCZUhVBiV9MiqMyJijDZxuyzG34W84JvFNH32ZB0I6E1vFrBJ3_ZqjmawcrdbqWtksHpHPZP-eXa7nVk_B7OYDR0GwGjc1mqZKmb1EL-pf5DfqPfVw= "Container Diagram")

## Ne pas communiquer par le stockage !

Quand nous avons découpé notre applicatif, nous avons commis une grave erreur.  
En effet, le conteneur [**web**] et le conteneur [**grader**] communiquent par la base de données.  

C'est un **anti-pattern** d'architecture, car :
* comment changer la base par une autre sans impacter les 2 conteneurs ?
* comment changer la façon de stocker la donnée sans impacter les 2 conteneurs ?

Bref, ces 2 conteneurs sont **couplés**.

Le but ici va être d'introduire un nouveau container qui va _abstraire_ le stockage:  

![Container Diagram v4](https://kroki.io/c4plantuml/svg/eNqNVNtuGjEQfd-vmPBQEZQEleSpIigJpSFKlaIsaqSWCnntCWvF2Mj2cmnVf-94b2yUi8qTmTlz5syZgQvnmfXZUkUHUnOVCcysguHZfGi0Z1KjPVmFbHQg8JG-Qh0ftnHOlGTuCHCuWIIqPDzyVIeHQMftIXCjFHIvjXbQOj8vkTPd7fad_I2fpqPh-G7-5dvddB7f_BgNfpYUv_rdkB90uzM90xVfC_p9XgkYDIA5KEVEkZdeNeSBkGxh2RK-n0XRBK0zuu2R8RTtEbSmxat1WKWcp9G1p1RcvCgVxTvncTm_MpkWzO7a_CPlb43FJ29ah_AnAvrUHdsqofRXw8TxFVNMc-KnwPhyYs12F_iewYdth3adqykp4QGTmZaaVkLFLhTHKyv14vjKGJJ2a7ySOoQvM5-SRsmZR8iIx53MdBzoHGwwcZLCH-BycuNOqO-rbXuNvuSTKMS-0e86AAC3aLl0CBtqDtp4-ShRvBiMfOi9b8Srgk4bgpw3li0wTPCOKlcMTCCgGotM0NAbG4YnCz1Sp7_1Ekdb315In2ZhSdfSj7OkVbwg8EiOkBoX1t4oEDnYmAVdlrByjbkaEld2APNYmOeAaQGCJJA2Wh15co9qf3D5aQxJot_bmHsynU5iQgdwfYI5-B4XknRYkNobihnzBNmq7PZmaT3hJEuUdGnZrLqmeFwpCz3q-_ss3Yp5nobbAc6UqhtU_CWy2Ox94fRDw-nn8D15r8m-yoKgNQlt8pcVvarktFnythiCieRdMXFFXkv5T_k1vuGmUnsrqbiyVzqXvfC2Ksv3eBd-JjswGjqdYECnU1lA6AvUgv5d_wEnw8ki "Container Diagram")

Ainsi, dans le cas où

* une partie de la donnée doit être stockée de manière différente (dans une base de donnée relationnelle par ex)  
  --> la seule modification à apporter est dans le code du conteneur [**storage API**].
* le format de spécification d'exercice évolue pour inclure plus de données
  --> [**storage API**] on propose une nouvelle API (GET / POST / PUT / DELETE) [/api/admin/specification/exercise/${name}/v2]()  
  --> [**web**] on utilise cette nouvelle API pour que les professeurs puissent écrire des spécifications plus détaillées  
  --> [**grader**] on utilise cette nouvelle API pour corriger les exercices plus finement  
  --> [**storage API**] on supprime l'ancienne API ([/api/admin/specification/exercise/${name}/v1]()) maintenant que plus aucun conteneur ne l'utilise  

Ce genre de changement peut être apporté sans coupure de service.
