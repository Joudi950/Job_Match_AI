# 🎓 JobMatch AI — Student Job Finder

> Trouve ton job étudiant idéal en 30 secondes grâce à l'intelligence artificielle.

![JobMatch AI](https://img.shields.io/badge/Powered%20by-Groq%20AI-6c63ff?style=for-the-badge)
![n8n](https://img.shields.io/badge/Backend-n8n-ff6b9d?style=for-the-badge)
![Vercel](https://img.shields.io/badge/Frontend-Vercel-000000?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-00d4aa?style=for-the-badge)

---

## ✨ Présentation

**JobMatch AI** est une application web complète qui aide les étudiants à trouver des jobs part-time et remote compatibles avec leur emploi du temps. L'IA analyse le profil de l'étudiant et filtre des centaines d'offres en temps réel pour ne proposer que les plus pertinentes.

### 🎯 Fonctionnalités principales

- **Matching intelligent** — L'IA compare ton emploi du temps, tes compétences et ton budget avec chaque offre
- **Double source d'offres** — Adzuna (jobs locaux) + Remotive (jobs remote) interrogés en temps réel
- **Score de compatibilité** — Chaque offre reçoit un score de 0 à 100% avec une explication
- **AI Career Advisor** — Un coach carrière IA répond à toutes tes questions après la recherche
- **Mode démo** — Fonctionne même sans backend connecté, pour tester immédiatement
- **100% responsive** — Optimisé mobile et desktop

---

##  Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     UTILISATEUR                         │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              FRONTEND — Vercel                          │
│              index.html (HTML/CSS/JS)                   │
│   • Formulaire 4 étapes                                 │
│   • Affichage des résultats                             │
│   • Chat AI Career Advisor                              │
└────────────────────────┬────────────────────────────────┘
                         │ POST /webhook/student-job-finder
                         │ POST /webhook/career-advisor
                         ▼
┌─────────────────────────────────────────────────────────┐
│              BACKEND — n8n sur Render                   │
│                                                         │
│   Webhook → Validate → Adzuna API ──┐                  │
│                        Remotive API ─┤→ Merge          │
│                                      ↓                  │
│                               Groq AI (llama-3.3-70b)  │
│                                      ↓                  │
│                              JSON Response              │
└─────────────────────────────────────────────────────────┘
                         │
            ┌────────────┴────────────┐
            ▼                         ▼
┌───────────────────┐     ┌───────────────────┐
│   Adzuna Jobs API │     │  Remotive Jobs API │
│   (jobs locaux)   │     │  (jobs remote)     │
└───────────────────┘     └───────────────────┘
```

---

## 🛠 Stack technique

| Couche | Technologie | Rôle |
|--------|-------------|------|
| Frontend | HTML5 / CSS3 / Vanilla JS | Interface utilisateur |
| Hébergement frontend | Vercel | Déploiement continu depuis GitHub |
| Backend workflow | n8n | Orchestration des APIs |
| Hébergement backend | Render | Serveur n8n (Docker) |
| IA principale | Groq llama-3.3-70b | Filtrage et analyse des offres |
| IA career advisor | Groq llama-3.3-70b | Coach carrière conversationnel |
| Jobs locaux | Adzuna API | Offres part-time France/Tunisie |
| Jobs remote | Remotive API | Offres 100% remote internationales |

---

## 📁 Structure du projet

```
jobmatch-ai/
│
├── index.html                      # Frontend complet (single file)
├── JobMatch_AI_n8n_workflow.json   # Workflow n8n à importer
├── GUIDE_DEPLOIEMENT.md            # Guide de déploiement détaillé
└── README.md                       # Ce fichier
```

---

## 🚀 Déploiement rapide

### Prérequis
- Compte GitHub
- Compte Vercel (gratuit)
- Compte Render (gratuit)

### 1. Frontend sur Vercel (5 min)

```bash
# 1. Crée un repo GitHub et upload index.html
# 2. Connecte le repo à Vercel
# 3. Deploy — c'est tout !
```

Ton site sera disponible sur : `https://jobmatch-ai.vercel.app`

### 2. Backend n8n sur Render (10 min)

**Image Docker :** `n8nio/n8n`

**Variables d'environnement requises :**
```env
N8N_PORT               = 10000
N8N_HOST               = 0.0.0.0
WEBHOOK_URL            = https://ton-backend.onrender.com
N8N_BASIC_AUTH_ACTIVE  = true
N8N_BASIC_AUTH_USER    = admin
N8N_BASIC_AUTH_PASSWORD = MotDePasseSecret
```

### 3. Importer le workflow n8n (3 min)

1. Ouvre ton instance n8n
2. **Import from file** → sélectionne `JobMatch_AI_n8n_workflow.json`
3. **Save** → **Activate**

### 4. Connecter frontend ↔ backend (1 min)

Dans `index.html`, modifie la ligne suivante :

```javascript
// Remplace par ton URL Render réel :
const BACKEND_URL = 'https://ton-backend.onrender.com/webhook/student-job-finder';
```

> 📖 Guide complet étape par étape dans `GUIDE_DEPLOIEMENT.md`

---

##  API Reference

### POST `/webhook/student-job-finder`

Recherche les offres d'emploi adaptées au profil étudiant.

**Request body :**
```json
{
  "study_field": "Informatique",
  "location": "Tunis, Tunisie",
  "schedule": "Lun 8h-12h | Mer libre | Ven 14h-18h",
  "skills": "Python, Excel, Communication",
  "availability_hours_per_week": 20,
  "preferred_job_type": "remote",
  "monthly_budget_needed": 600,
  "language": "fr"
}
```

**Response :**
```json
{
  "success": true,
  "ai_model": "Groq llama-3.3-70b-versatile",
  "results": {
    "total_jobs_found": 8,
    "summary": "8 offres compatibles trouvées...",
    "job_offers": [
      {
        "rank": 1,
        "title": "Développeur Web Junior",
        "company": "TechStartup",
        "location": "Tunis",
        "job_type": "Hybride",
        "working_hours": { "schedule": "Flexible", "hours_per_week": "20h" },
        "compensation": { "amount": "800 TND/mois" },
        "apply": { "url": "https://...", "source_platform": "Adzuna" },
        "compatibility": { "score": 92, "reason": "Correspond à ton domaine et emploi du temps" },
        "required_skills": ["HTML", "CSS", "JavaScript"],
        "personalized_tip": "Mentionne tes projets GitHub"
      }
    ],
    "additional_platforms": [...]
  }
}
```

---

### POST `/webhook/career-advisor`

Chat avec le coach carrière IA.

**Request body :**
```json
{
  "message": "Comment améliorer mon CV ?",
  "profile": {
    "study_field": "Informatique",
    "location": "Tunis",
    "skills": "Python, Excel"
  },
  "jobs_found": 8
}
```

**Response :**
```json
{
  "success": true,
  "reply": "Voici mes conseils pour ton CV...",
  "timestamp": "2025-03-14T10:00:00.000Z"
}
```

---

## 🎨 Design System

Le projet utilise un système de design cohérent basé sur des variables CSS :

```css
--bg:       #0a0a0f   /* Fond principal */
--surface:  #12121a   /* Cartes */
--accent:   #6c63ff   /* Violet principal */
--accent2:  #ff6b9d   /* Rose secondaire */
--accent3:  #00d4aa   /* Vert succès */
--text:     #f0f0f5   /* Texte principal */
```

**Typographie :** Syne (titres, bold) + DM Sans (corps, interface)

---

## 🔧 Configuration avancée

### Changer les clés API

Dans le workflow n8n, node **"Adzuna Job Search API"** :
```
app_id  → Récupère sur https://developer.adzuna.com
app_key → Récupère sur https://developer.adzuna.com
```

Dans les nodes Groq :
```
Authorization: Bearer gsk_VOTRE_CLE_GROQ
```
Récupère ta clé gratuite sur https://console.groq.com

### Activer CORS (si erreur navigateur)

Ajoute dans Render :
```env
N8N_CORS_ORIGIN = *
```

---

##  Roadmap — Prochaines fonctionnalités

- [ ] **Authentification** — Comptes utilisateurs avec Supabase
- [ ] **Sauvegarde des recherches** — Historique des offres consultées
- [ ] **Alertes email** — Notifications hebdomadaires des nouvelles offres (SendGrid)
- [ ] **Dashboard utilisateur** — Suivi des candidatures
- [ ] **Freemium** — 3 recherches/jour gratuit, illimité en premium
- [ ] **API Tunisie** — Intégration TanitJobs et OptioCarrière directement
- [ ] **Extension mobile** — PWA installable

---

##  Contribution

Les contributions sont les bienvenues !

1. Fork le projet
2. Crée ta branche (`git checkout -b feature/ma-fonctionnalite`)
3. Commit tes changements (`git commit -m 'Ajout de ma fonctionnalité'`)
4. Push (`git push origin feature/ma-fonctionnalite`)
5. Ouvre une Pull Request

---

##  Licence

Ce projet est sous licence **MIT**. Tu es libre de l'utiliser, le modifier et le distribuer.

---

##  Auteur

Construit avec ❤️ pour les étudiants · Propulsé par **Groq AI**, **n8n**, **Adzuna** et **Remotive**

---

*JobMatch AI — Parce que trouver un job étudiant ne devrait pas être un job à plein temps.*
