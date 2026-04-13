# 🛡️ Mon Cahier de Route — DFIR · Threat Hunting · SOC
> "Je ne m'éparpille pas. Tout ce que je fais répond à une question précise."

---

## 🎯 Ce que je veux devenir

**Analyste DFIR / Threat Hunter / SOC Senior**

Un professionnel capable de :
- Répondre à un incident cyber du début à la fin
- Chasser des menaces avant qu'elles causent des dégâts
- Détecter, analyser, contenir, et documenter une attaque
- Travailler sur Windows ET Linux avec les mêmes réflexes

---

## 🧭 Ma règle d'or — Ne jamais m'éparpiller

Tout ce que je fais doit répondre à une de ces trois questions :

```
1. Qu'est-ce qui s'est passé ?   → DFIR
2. Qui le fait en ce moment ?    → SOC
3. Vont-ils revenir ?            → Threat Hunting
```

Si une activité ne répond pas à une de ces questions → je la mets de côté.

---

## 🗺️ La carte mentale — Tout se connecte

```
        DFIR + THREAT HUNTING + SOC
                     │
           ┌─────────┴─────────┐
           │                   │
        LINUX               WINDOWS
        SKILLS               SKILLS
           │                   │
    cat /proc              EZ Tools
    ps aux                 Volatility
    netstat                Event Logs
    lsof                   Prefetch/MFT
    strace                 Registry
           │                   │
           └─────────┬─────────┘
                     │
                   SIEM
                  (Wazuh)
                     │
          Corrélation des logs
          Détection des alertes
             Threat Hunting
```

**Rien n'est perdu. Tout se connecte.**

---

## 🔍 Ce que chaque outil m'apporte

### Commandes Linux (`cat /proc`, `ps`, `netstat`, `lsof`...)
**C'est du Live Forensics.**  
Ce que fait un analyste DFIR sur une machine compromise *avant* de faire une image mémoire.  
C'est la Section 1 de FOR508. Je pratique déjà sans le savoir.

```bash
cat /proc/<PID>/maps     # fichiers mappés en mémoire → même logique que windows.dlllist
ls -la /proc/<PID>/fd    # fichiers ouverts par un processus
cat /proc/<PID>/cmdline  # commande qui a lancé le processus
cat /proc/<PID>/net/tcp  # connexions réseau du processus
```

**Répond à** : "Qu'est-ce qui se passe sur cette machine en ce moment ?"

---

### Wazuh (mon SOC en miniature)
**C'est mon centre opérationnel de sécurité personnel.**  
Il collecte les logs, génère des alertes, et me permet de chasser des menaces.  
C'est exactement le travail d'un analyste SOC Tier 2.

Utilisation avancée :
- Générer de vraies attaques sur mes VMs (Atomic Red Team)
- Les détecter dans Wazuh
- Écrire des règles de détection custom
- Pratiquer le triage d'alertes

**Répond à** : "Qui attaque en ce moment et comment ?"

---

### Volatility3 (forensics mémoire)
**C'est le DFIR post-incident.**  
J'arrive après l'incident et je reconstruis ce qui s'est passé à partir d'une image RAM.

```bash
vol -f image.raw windows.pslist    # processus actifs au moment du crash
vol -f image.raw windows.cmdline   # commandes exécutées
vol -f image.raw windows.malfind   # injections mémoire suspectes
vol -f image.raw windows.netstat   # connexions réseau actives
```

**Répond à** : "Qu'est-ce qui s'est passé sur cette machine ?"

---

### Eric Zimmerman Tools (artefacts Windows)
**Ce sont les traces laissées sur le disque.**  
Même si l'attaquant a essayé d'effacer ses traces, ces artefacts restent.

| Outil | Artefact | Question |
|---|---|---|
| PECmd | Prefetch | Quels programmes ont tourné ? |
| MFTECmd | $MFT | Quels fichiers ont existé ? |
| EvtxECmd | Event Logs | Qui s'est connecté ? Quand ? |
| RECmd | Registry | Quelle persistance a été installée ? |
| LECmd | LNK files | Quels fichiers ont été ouverts ? |

**Répond à** : "Quelles traces l'attaquant a-t-il laissées ?"

---

### Plaso / log2timeline (super-timelines)
**C'est la reconstruction chronologique complète.**  
Je combine tous les artefacts en une seule timeline pour voir l'attaque minute par minute.

```bash
log2timeline --storage-file timeline.plaso image.raw
psort -o l2tcsv -w output.csv timeline.plaso
# Visualiser dans TimelineExplorer (Windows)
```

**Répond à** : "Dans quel ordre les événements se sont-ils produits ?"

---

## 📅 Mon rythme de travail

```
LUNDI / MERCREDI / VENDREDI
    MemLabs + CyberDefenders
    Forensics pur → Volatility, EZ Tools, Plaso
    Focus : "Qu'est-ce qui s'est passé ?"

MARDI / JEUDI
    Wazuh + logs Linux
    Investigation terminal /proc
    Focus : "Qui le fait maintenant ?"

WEEKEND
    Lire DFIR.report → vraies investigations publiées
    Un article SANS ISC → isc.sans.edu
    Connecter ce que j'ai appris dans la semaine
```

---

## 🏗️ Mon environnement de travail

### Machine hôte
- MacBook Apple Silicon (ARM64)
- Hyperviseur : UTM

### VM Ubuntu — Outils DFIR
```
✅ Volatility3        → forensics mémoire
✅ Autopsy/Sleuthkit  → forensics disque
✅ Plaso/log2timeline → super-timelines
✅ Wireshark/tshark   → forensics réseau
✅ bulk-extractor     → extraction données
✅ foremost           → récupération fichiers
✅ yara               → détection malwares
✅ MemLabs            → challenges pratiques
✅ Wazuh              → SIEM/EDR (SOC)
```

### VM Windows 11 — EZ Tools
```
✅ PECmd              → Prefetch
✅ MFTECmd            → Master File Table
✅ EvtxECmd           → Logs Windows
✅ RECmd              → Registry
✅ LECmd              → LNK files
✅ AmcacheParser      → Historique exécution
✅ AppCompatCacheParser → Shimcache
✅ TimelineExplorer   → Visualisation (GUI)
✅ RegistryExplorer   → Registry (GUI)
✅ MFTExplorer        → MFT (GUI)
✅ ShellBagsExplorer  → Navigation dossiers
```

---

## 📚 Ressources hebdomadaires

| Ressource | URL | Fréquence |
|---|---|---|
| DFIR.report | dfir.report | 1 rapport/semaine |
| SANS ISC | isc.sans.edu | 1 article/semaine |
| CyberDefenders | cyberdefenders.org | 1 lab/semaine |
| MemLabs | ~/MemLabs | Au rythme des labs |

---

## 🎯 Progression des labs

### MemLabs (forensics mémoire)
- [ ] Lab 1 — Beginner's Luck (Windows 7, 3 flags)
- [ ] Lab 2
- [ ] Lab 3
- [ ] Lab 4
- [ ] Lab 5
- [ ] Lab 6

### CyberDefenders
- [ ] BlueSky
- [ ] Tomcat Takeover
- [ ] Reveal

---

## 💡 Ce que je retiens

> **Wazuh** → je détecte en temps réel (SOC)  
> **Volatility** → j'analyse après coup (DFIR)  
> **`/proc`** → j'investigue une machine live (Linux forensics)  
> **EZ Tools** → je lis les traces Windows (DFIR)  
> **Plaso** → je reconstruis la chronologie (DFIR)

**Ce n'est pas de l'éparpillement. C'est le même métier vu sous différents angles.**

---

*Cahier de route créé le 13 Avril 2026*  
*Objectif : DFIR · Threat Hunting · SOC Senior*
