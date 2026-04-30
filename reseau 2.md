# 🌐 Réseau 2 – Windows Server & Active Directory

---

## ⚙️ Étape 1 : Configuration du réseau

Configurer l’adresse IP du serveur :

* Adresse IP statique
* Masque de sous-réseau
* Passerelle
* DNS

<img src="https://github.com/user-attachments/assets/89898664-d0ec-447f-b174-fcdd02194622" width="50%">
<img src="https://github.com/user-attachments/assets/7a797e09-70fe-4928-ac14-04c9130c15a0" width="50%">
<img src="https://github.com/user-attachments/assets/38fc9d40-85a5-49d5-977e-a08be738d9d6" width="50%">
<img src="https://github.com/user-attachments/assets/c1fad82f-b918-4cc5-87fc-84b65c757b87" width="50%">
<img src="https://github.com/user-attachments/assets/ce6e1e33-be84-454c-a161-35b2ef761bcd" width="50%">

---

## 🖥️ Étape 2 : Installation d’Active Directory

Installation du rôle **AD DS**

<img src="https://github.com/user-attachments/assets/13f75479-0309-4888-af59-e0e49ee89e7d" width="50%">
<img src="https://github.com/user-attachments/assets/14d7f0df-d17b-4829-86de-b174c1206c13" width="50%">
<img src="https://github.com/user-attachments/assets/61c26094-7435-48a4-9d43-4dfdd1e7d0c8" width="50%">
<img src="https://github.com/user-attachments/assets/86313d02-7b34-435f-873b-1693e231821f" width="50%">
<img src="https://github.com/user-attachments/assets/3286541d-e4bc-4525-8b19-02e0f165b107" width="50%">
<img src="https://github.com/user-attachments/assets/bfeafa31-54a0-4370-8559-f05fe589e630" width="50%">

---

## 👤 Création des utilisateurs

Créer un utilisateur dans AD

<img src="https://github.com/user-attachments/assets/0137dd07-141d-41c4-83c2-526efafec9c2" width="50%">

---

## 🗂️ Création d’une OU

Permet d’organiser les utilisateurs

<img src="https://github.com/user-attachments/assets/9c27f105-f9a0-4ebe-b2b0-81e7c09de3dd" width="50%">

---

## 👥 Création d’un groupe

* Global → utilisateurs
* Local → permissions

<img src="https://github.com/user-attachments/assets/2df25789-9b6b-4ba8-8722-61924c844350" width="50%">

---

## 📁 Dossiers utilisateurs (Home Folder)

Créer un dossier principal :

```text
C:\perso
```

Créer un dossier par utilisateur :

```text
C:\perso\user1
```

Partager le dossier :

```text
\\Serveur\perso
```

<img src="https://github.com/user-attachments/assets/a8c242bc-52f7-4e2a-a2bc-cf1b439e595f" width="50%">

---

## 🔐 Permissions NTFS

Objectif :

* Chaque utilisateur accède seulement à son dossier

Étapes :

* Désactiver l’héritage
* Supprimer les permissions
* Ajouter :

  * utilisateur → contrôle total
  * Administrators
  * SYSTEM

<img src="https://github.com/user-attachments/assets/c0f8bb2f-b9fb-4132-86d3-ab66f9647468" width="50%">
<img src="https://github.com/user-attachments/assets/d01e5c6d-d8f8-4504-ad93-9353fba192d6" width="50%">

---

## ⚙️ Script PowerShell (création des home folders + liaison AD)

```powershell
Import-Module ActiveDirectory

$SharePath = "C:\perso"
$ShareName = "perso"
$Domain = "formatif"
$ServerName = $env:COMPUTERNAME

# Créer C:\perso
if (-not (Test-Path $SharePath)) {
    New-Item -Path $SharePath -ItemType Directory
}

# Créer le partage \\$serveur\perso
if (-not (Get-SmbShare -Name $ShareName -ErrorAction SilentlyContinue)) {
    New-SmbShare -Name $ShareName -Path $SharePath -FullAccess "Administrators","Domain Admins" -ChangeAccess "Authenticated Users"
}

# Boucle utilisateurs
for ($i = 1; $i -le 10; $i++) {

    $username = "user$i"
    $UserFolder = "$SharePath\$username"
    $HomeDirectory = "\\$ServerName\$ShareName\$username"

    # Créer dossier
    if (-not (Test-Path $UserFolder)) {
        New-Item -Path $UserFolder -ItemType Directory
    }

    # Permissions
    icacls $UserFolder /inheritance:r
    icacls $UserFolder /grant "${Domain}\${username}:(OI)(CI)F"
    icacls $UserFolder /grant "Administrators:(OI)(CI)F"
    icacls $UserFolder /grant "SYSTEM:(OI)(CI)F"

    # Assigner dans AD
    Set-ADUser -Identity $username -HomeDrive "H:" -HomeDirectory $HomeDirectory
}
```

---

## 🖥️ Connexion à distance (RDP)

Permet de se connecter au serveur à distance

<img src="https://github.com/user-attachments/assets/752fc1ed-b6e8-4eb5-baf3-0fbe2ee9cd88" width="50%">

---

## 💾 Sauvegarde Windows Server

Installation de l’outil de sauvegarde

<img src="https://github.com/user-attachments/assets/c7aad947-7d06-4842-9a4e-6074cd485da6" width="50%">
<img src="https://github.com/user-attachments/assets/31a0de40-5a09-4a1f-bcfb-0730d6bfc097" width="50%">

---

## 🧠 Résumé

* Configuration réseau = base
* Active Directory = gestion utilisateurs
* OU = organisation
* Groupes = gestion accès
* Dossiers perso = stockage
* Permissions = sécurité
* RDP = accès à distance
* Sauvegarde = protection
