# Moteur IA Camerounais — Traduction & Reconnaissance Vocale

Projet du **Groupe 13** — Licence 2 Informatique, Université de Yaoundé I
Encadrement : Quantum Technologies SAS (QuamTechs)

Moteur d'intelligence artificielle permettant la reconnaissance vocale (ASR) et la traduction automatique du français camerounais, du Camfranglais et des langues vernaculaires, vers le français ou l'anglais (et inversement).

📄 Cahier des charges complet : voir `docs/cahier-des-charges.pdf`

---

## Équipe & répartition des rôles

| Membre | Rôle | Focus |
|---|---|---|
| KAMDEM WEGANG Marianela | Cadrage Stratégique & Assurance Qualité | Contexte, glossaire, critères WER/BLEU |
| **NONGNI TEMGOUA Channelle** — Coordination du groupe | Architecte Cloud & Backend | Microservices, gRPC, RabbitMQ |
| SIBEUFO NGUEMBU Jordanelle | Responsable Données & NLP (Data Scientist) | Fine-tuning Whisper (ASR) et NLLB (NMT) |
| Souotchueng Kom Annaelle & Pola Wachie Elisabeth Reine | Expérience Utilisateur (UI/UX) | Interfaces Next.js / Flutter |
| Souotchueng Kom Annaelle & Pola Wachie Elisabeth Reine | Étude de Terrain & Sondage | Collecte des 300 réponses, corpus initial |

---

## Architecture

```
Client (Web / Mobile)
        │
        ▼
  API Gateway (NestJS)
        │
   ┌────┴─────┬──────────────┐
   ▼          ▼              ▼
Django      Service ASR   Service NMT
(métier,    (Whisper)     (NLLB)
auth, DB)      │              │
               └──── RabbitMQ ┘
            (files d'attente audio)
```

| Composant | Technologie | Rôle |
|---|---|---|
| API Gateway | NestJS | Point d'entrée, réception asynchrone des flux audio |
| Backend métier | Django | Auth, ORM, base de données |
| Base de données | PostgreSQL | Utilisateurs, historique, métadonnées corpus |
| Reconnaissance vocale | Whisper (fine-tuné) | Voix → texte |
| Traduction | NLLB (fine-tuné) | Texte → texte |
| Communication sync. | gRPC (HTTP/2) | Échanges rapides inter-services |
| Communication async. | RabbitMQ | Traitement des audios longs en tâche de fond |
| Frontend Web | Next.js | Interface navigateur |
| Frontend Mobile | Flutter | Application Android/iOS |
| Traitement des données | Python, PyTorch, Hugging Face, Librosa, Ffmpeg, Label Studio | Nettoyage, annotation, entraînement des modèles |
| Conteneurisation | Docker / Docker-Compose | Déploiement reproductible de tous les services |

---

## Arborescence du dépôt (monorepo)

```
moteur-ia-camerounais/
├── README.md
├── docker-compose.yml
├── .env.example
├── .gitignore
│
├── docs/
│   ├── cahier-des-charges.pdf
│   ├── rapport-collectif.pdf
│   └── rapports-individuels/
│
├── backend-gateway/          # NestJS — API Gateway (Channelle)
├── backend-core/             # Django — serveur métier (Channelle)
├── ai-asr-service/           # Whisper fine-tuning (Jordanelle)
├── ai-nmt-service/           # NLLB fine-tuning (Jordanelle)
├── data/                     # Corpus & sondage (Annaelle & Pola)
│   ├── raw/
│   ├── processed/
│   ├── annotation/
│   └── scripts/
├── frontend-web/             # Next.js (Annaelle & Pola)
├── frontend-mobile/          # Flutter (Annaelle & Pola)
└── qa/                       # Assurance Qualité (Marianela)
    ├── metrics/              # scripts WER / BLEU / chrF
    └── glossaire-technique.md
```

---

## Planning : 5 sprints d'une semaine

| Sprint | Semaine | Objectif | Livrable |
|---|---|---|---|
| Sprint 1 | S1 | Cadrage + lancement de la collecte | CDC finalisé + sondage actif |
| Sprint 2 | S2 | Collecte + prétraitement audio | Corpus brut nettoyé + squelette backend |
| Sprint 3 | S3 | Annotation + fine-tuning IA (Whisper/NLLB) | Corpus annoté + modèles fine-tunés |
| Sprint 4 | S4 | Intégration backend/IA + conteneurisation | Environnement Docker fonctionnel |
| Sprint 5 | S5 | Tests, évaluation, synthèse | Application finale + rapport de synthèse |

> Le planning est une feuille de route, pas un cadre figé : en fin de semaine, un point rapide en équipe permet d'ajuster la semaine suivante si un pôle prend du retard.

---

## Organisation Git

**Branches**
- `main` — version stable, protégée (merge via Pull Request uniquement)
- `feature/backend-channelle`
- `feature/nlp-jordanelle`
- `feature/frontend-annaelle-pola`
- `feature/terrain-annaelle-pola`
- `feature/qa-marianela`

**Convention de commit**
```
[pole] type: description courte

Exemples :
[backend] feat: ajout de l'API Gateway NestJS
[nlp] fix: correction du script de fine-tuning Whisper
[terrain] docs: ajout du guide de collecte
```

**Workflow**
1. Créer une branche depuis `main` pour chaque tâche
2. Ouvrir une Pull Request avec description claire
3. Au moins une relecture par un autre membre avant merge
4. `main` doit toujours rester fonctionnel (buildable via `docker-compose up`)

---

## Démarrage local

```bash
git clone https://github.com/Channelle08/moteur-ia-camerounais.git
cd moteur-ia-camerounais
cp .env.example .env
docker-compose up --build
```

Services exposés (à ajuster selon configuration) :
- API Gateway : `http://localhost:3000`
- Backend Django : `http://localhost:8000`
- Frontend Web : `http://localhost:3001`
- RabbitMQ (management) : `http://localhost:15672`

---

## Critères de validation

| Indicateur | Objet mesuré | Seuil visé |
|---|---|---|
| WER (Word Error Rate) | Qualité de la transcription (ASR) | Défini en phase pilote |
| BLEU / chrF | Qualité de la traduction (NMT) | Défini en phase pilote |
| Nombre de répondants | Représentativité du corpus | 300 participants minimum |
| Disponibilité des services | Fiabilité de l'infrastructure | Pas d'interruption de l'auth en cas de charge IA |

Phase pilote centrée sur le **Français Camerounais Urbain**, avant extension aux autres variantes du Camfranglais et langues locales.

---

## Licence & Encadrement

Projet réalisé dans le cadre du stage/formation chez **Quantum Technologies SAS** (QuamTechs).
Contact : contact@quamtechs.com — www.quamtechs.com
