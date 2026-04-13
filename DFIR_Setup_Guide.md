# 🛡️ DFIR Lab Setup Guide
> Préparation de l'environnement pour la certification SANS FOR508 / GIAC GCFA  
> Machine hôte : MacBook Apple Silicon (ARM64) | Hyperviseur : UTM

---

## VM Ubuntu (ARM64)

### 1. Volatility3 — Forensics mémoire
Analyse des images RAM de machines compromises.

```bash
sudo apt install python3-full pipx -y
pipx install volatility3
pipx ensurepath
source ~/.zshrc

# Vérification
vol -h
```

> ⚠️ Sur Ubuntu 24+ : ne pas utiliser `pip install` directement — environnement Python protégé. Utiliser `pipx`.

---

### 2. Autopsy / Sleuthkit — Forensics disque
Analyse d'images disque, récupération de fichiers supprimés.

```bash
sudo apt install -y sleuthkit autopsy

# Vérification
mmls          # analyse de partitions
autopsy       # interface web sur port 9999
```

---

### 3. Plaso / log2timeline — Super-timelines
Construction de timelines chronologiques complètes (FOR508 Section 4).

```bash
# Dépendances système
sudo apt install -y pkg-config build-essential python3-dev libssl-dev \
  libffi-dev libbz2-dev libsqlite3-dev zlib1g-dev

# Installation
pipx install plaso --force

# Vérification
log2timeline --version
```

> ⚠️ `plaso-tools` n'est pas disponible via apt sur ARM64. Utiliser `pipx`.

---

### 4. Wireshark / tshark — Forensics réseau
Analyse de captures réseau et trafic suspect.

```bash
sudo apt install -y wireshark tshark

# Vérification
wireshark --version
tshark --version
```

---

### 5. Outils complémentaires
```bash
sudo apt install -y \
  bulk-extractor \   # extraction de données massives
  foremost \         # récupération de fichiers supprimés
  binwalk \          # analyse de fichiers binaires
  yara \             # détection de malwares par signatures
  exiftool \         # extraction de métadonnées
  tcpdump \          # capture réseau CLI
  nmap \             # scan réseau
  git curl wget \
  jq unzip 7zip \
  megatools          # téléchargement Mega.nz en CLI
```

---

### 6. MemLabs — Challenges forensics mémoire
6 images mémoire Windows réelles pour pratiquer Volatility.

```bash
git clone https://github.com/stuxnet999/MemLabs.git ~/MemLabs

# Créer dossier de travail
mkdir -p ~/investigations/lab1
cd ~/investigations/lab1

# Télécharger l'image Lab 1
megadl 'https://mega.nz/#!6l4BhKIb!l8ATZoliB_ULlvlkESwkPiXAETJEF7p91Gf9CWuQI70'

# Décompresser
7z x MemLabs-Lab1.7z

# Vérification
ls -lh
# MemoryDump_Lab1.raw  1.1G
```

---

### Résumé VM Ubuntu

```
✅ Volatility3        → vol -h
✅ Autopsy            → autopsy
✅ Sleuthkit          → mmls
✅ Plaso              → log2timeline --version
✅ Wireshark/tshark   → wireshark --version
✅ bulk-extractor     → bulk_extractor --version
✅ foremost           → foremost -V
✅ yara               → yara --version
✅ MemLabs            → ~/MemLabs (Lab 0 → Lab 6)
```

---

## VM Windows 11

### Eric Zimmerman Tools
Outils pour analyser les artefacts forensiques Windows.  
Localisation après installation : `C:\Tools\EZTools\net9\`

#### Prérequis — Corriger le DNS si nécessaire
```powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "8.8.8.8","8.8.4.4"
```

#### Installation
```powershell
# Créer le dossier
New-Item -ItemType Directory -Path "C:\Tools\EZTools" -Force

# Télécharger le script
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/EricZimmerman/Get-ZimmermanTools/master/Get-ZimmermanTools.ps1" -OutFile "C:\Tools\Get-ZimmermanTools.ps1"

# Autoriser l'exécution
Set-ExecutionPolicy Bypass -Scope Process -Force

# Lancer l'installation
& "C:\Tools\Get-ZimmermanTools.ps1" -Dest "C:\Tools\EZTools"

# Vérification
Get-ChildItem "C:\Tools\EZTools" -Recurse -Filter "*.exe" | Select-Object Name
```

#### Outils installés

| Outil | Artefact | Ce qu'il révèle |
|---|---|---|
| AmcacheParser.exe | Amcache.hve | Historique d'exécution des programmes |
| AppCompatCacheParser.exe | Shimcache (SYSTEM) | Programmes vus par le système |
| EvtxECmd.exe | Logs .evtx | Événements Windows (connexions, exécutions) |
| MFTECmd.exe | $MFT | Tout ce qui a existé sur le disque NTFS |
| PECmd.exe | Prefetch | Programmes exécutés + timestamps |
| LECmd.exe | Fichiers LNK | Fichiers récemment ouverts |
| RECmd.exe | Registry (CLI) | Persistance, configuration, utilisateurs |
| JLECmd.exe | Jump Lists | Historique d'accès aux fichiers par app |
| WxTCmd.exe | Windows Timeline | Activité utilisateur récente |
| RBCmd.exe | Corbeille | Fichiers supprimés ($I files) |
| SrumECmd.exe | SRUM | Utilisation réseau et CPU par processus |
| SQLECmd.exe | Bases SQLite | Historiques navigateurs, artefacts modernes |
| RegistryExplorer.exe | Registry (GUI) | Exploration visuelle du registre |
| MFTExplorer.exe | MFT (GUI) | Exploration visuelle du MFT |
| TimelineExplorer.exe | Timelines CSV | Visualisation des super-timelines |
| ShellBagsExplorer.exe | ShellBags | Dossiers explorés par l'utilisateur |

---

### Résumé VM Windows 11

```
✅ AmcacheParser       → C:\Tools\EZTools\net9\
✅ AppCompatCacheParser
✅ EvtxECmd
✅ MFTECmd
✅ PECmd
✅ LECmd
✅ RECmd
✅ JLECmd
✅ WxTCmd
✅ RBCmd
✅ SrumECmd
✅ SQLECmd
✅ RegistryExplorer    (GUI)
✅ MFTExplorer         (GUI)
✅ TimelineExplorer    (GUI)
✅ ShellBagsExplorer   (GUI)
```

---

## Commandes Volatility3 essentielles

```bash
# Identifier l'OS
vol -f image.raw windows.info

# Lister les processus
vol -f image.raw windows.pslist
vol -f image.raw windows.pstree

# Processus cachés
vol -f image.raw windows.psscan

# Commandes exécutées
vol -f image.raw windows.cmdline

# Connexions réseau
vol -f image.raw windows.netstat

# DLLs chargées
vol -f image.raw windows.dlllist

# Fichiers ouverts
vol -f image.raw windows.filescan

# Détection d'injection mémoire
vol -f image.raw windows.malfind

# Dump d'un processus (PID)
vol -f image.raw windows.memdump --pid <PID> --dump-dir ./output/
```

---

## Ressources pratiques

| Plateforme | URL | Focus |
|---|---|---|
| MemLabs | github.com/stuxnet999/MemLabs | Forensics mémoire |
| CyberDefenders | cyberdefenders.org | DFIR complet |
| BlueTeamLabs Online | blueteamslabs.online | SOC + DFIR |
| TryHackMe | tryhackme.com | SOC Level 1 path |
| DFIR.report | dfir.report | Vraies investigations publiées |
| SANS ISC | isc.sans.edu | Analyses quotidiennes |

---

*Setup réalisé sur MacBook Apple Silicon / UTM / Ubuntu ARM64 / Windows 11*
