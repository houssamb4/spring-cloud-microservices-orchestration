# 🚀 TP19 – Orchestration de Microservices avec Spring Cloud  
**Eureka · Gateway · OpenFeign · Load Balancer**

Ce projet illustre une architecture microservices complète basée sur **Spring Cloud**, intégrant :  
👉 *Eureka Discovery Server*  
👉 *API Gateway (Spring Cloud Gateway)*  
👉 *OpenFeign*  
👉 *Spring Cloud LoadBalancer*  

Il s’inscrit dans le cadre du cours :  
**Architecture Microservices : Conception, Déploiement et Orchestration**

---

# 📌 Étape 0 — Contexte & Architecture

## 🎯 Objectifs
- Comprendre le rôle des composants essentiels de Spring Cloud  
- Visualiser le flux complet d’une requête dans une architecture microservices  
- Introduire l'orchestration avec Eureka, Gateway et le Load Balancer  

---

# 🏗️ Concepts Clés des Microservices

### ✔️ Services autonomes  
Chaque service gère une fonctionnalité précise et est déployé indépendamment.

### ✔️ Communication légère  
REST/HTTP (synchrone via Feign) ou messaging (asynchrone).

### ✔️ Données isolées  
Chaque microservice possède son propre stockage (H2 pour ce TP).

### ✔️ Scalabilité horizontale  
Possibilité d’exécuter plusieurs instances du même service.

---

# ☁️ Spring Cloud — Vue d’ensemble

- 🔍 **Découverte de services** (Eureka)  
- 🚪 **Routage intelligent** (Spring Cloud Gateway)  
- ⚙️ **Configuration centralisée** (Spring Cloud Config — non utilisé ici)  
- 🛡️ **Résilience** (Resilience4j/Hystrix, timeouts, retries)  
- 📈 **Observabilité** (Actuator, Sleuth/Zipkin)

---

# 🔍 Eureka — Service Discovery

### Fonctionnement :
- Les microservices **s'enregistrent automatiquement** auprès d’Eureka.
- Les clients utilisent Eureka pour **découvrir dynamiquement** les instances disponibles.
- Base sur :
  - Heartbeats
  - TTL (time-to-live)
  - Self-preservation
  - Cache côté client

---

# 🚪 Spring Cloud Gateway

### Rôle principal :
Point d’entrée unique pour tout le système.

### Fonctions clés :
- ✔️ Routage statique et dynamique (`lb://SERVICE-NAME`)
- ✔️ Filtrage / pré- et post-processing
- ✔️ Sécurité & CORS
- ✔️ Réécriture d’URL, logs, metrics

---

# ⚖️ Load Balancer – Client-Side

Spring Cloud LoadBalancer choisit **l’instance la plus appropriée** selon une stratégie (round-robin par défaut).

### Avantages :
- Aucune dépendance à un load balancer externe
- Adaptation au nombre dynamique d’instances
- Pas de point unique de défaillance

---

# 🗺️ Topologie du Projet

| Service           | Description                                         | Port |
|------------------|-----------------------------------------------------|------|
| **Eureka Server** | Registre de services                                | 8761 |
| **SERVICE-CLIENT** | Microservice CRUD Clients (H2 Database)             | 8088 |
| **SERVICE-VOITURE** | Gère les voitures + Appelle SERVICE-CLIENT via Feign | 8089 |
| **Gateway**        | Point d’entrée du système (routage dynamique)       | 8888 |

---

# 🔄 Flux d’une Requête — Étapes

1. **Enregistrement** : les microservices démarrent et annoncent leur présence à Eureka.  
2. **Requête client** → Gateway  
3. Gateway résout la route  
   - soit statique  
   - soit dynamique via `lb://SERVICE-NAME`  
4. LoadBalancer sélectionne une instance  
5. Le service traite la requête  
6. La réponse revient → Gateway → Client  

---

# 🛡️ Résilience & Observabilité

### ✔️ Timeouts & Retries  
Évitent les blocages et comportements imprévisibles.

### ✔️ Circuit Breaker (Hystrix / Resilience4j)  
Isolation en cas de défaillance d'un service.

### ✔️ Health Checks (Actuator)  
Endpoints utiles :  
`/actuator/health` – `/actuator/info` – `/actuator/metrics`

---

# 🏷️ Nommage & Adressage

- **Nom logique** : défini par `spring.application.name`
- **Routage dynamique** : `lb://SERVICE-NAME`
- **Ports du TP** :
  - Eureka → 8761  
  - Gateway → 8888  
  - Client → 8088  
  - Voiture → 8089  

---

# 🔒 Sécurité & CORS

La Gateway centralise :
- Authentification
- Autorisation
- Configuration CORS

> ⚠️ Non indispensable pour ce TP, mais essentiel en production.

---

# 🚀 Démarrage du Projet

### 📌 Ordre recommandé

```bash
# 1. Eureka Server
cd EurekaServer
mvn spring-boot:run
```

```bash
# 2. SERVICE-CLIENT
cd Client
mvn spring-boot:run
```

```bash
# 3. SERVICE-VOITURE
cd Voiture
mvn spring-boot:run
```

```bash
# 4. API Gateway
cd GateWay
mvn spring-boot:run
```

## 📸 Captures d’écran (Vérifications finales)

### 🟦 Eureka : présence de SERVICE-CLIENT et SERVICE-VOITURE
<img width="1900" height="1019" alt="eureka-services" src="https://github.com/user-attachments/assets/452e5c77-9f92-412c-8ce1-a0530c3088c8" />

---

### 🚪 Gateway — Routage Statique  
➡️ http://localhost:8888/clients

<img width="569" height="400" alt="Image" src="https://github.com/user-attachments/assets/0e111695-154d-45ce-9055-429573b38f02" />


➡️ http://localhost:8888/client/1

<img width="569" height="186" alt="Image" src="https://github.com/user-attachments/assets/0de43b06-fc4f-4c63-813f-27d16cb98110" />

➡️ http://localhost:8888/voitures

<img width="561" height="531" alt="Image" src="https://github.com/user-attachments/assets/0e9fbd1c-19af-4937-831e-901cc057095e" />

➡️ http://localhost:8888/voiture/1

<img width="578" height="316" alt="Image" src="https://github.com/user-attachments/assets/372e8bd5-9023-4251-99f7-0639520f135a" />

➡️ http://localhost:8888/voitures/client/1

<img width="624" height="414" alt="Image" src="https://github.com/user-attachments/assets/8171aa7f-a75f-457e-8b9e-a30f89df6b41" />

---
