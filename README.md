# Fiscus RDC - Système de Déclaration & Paiement Fiscal

**Fiscus RDC** est une application Python moderne conçue pour automatiser et simplifier la déclaration et le règlement des taxes et impôts en République Démocratique du Congo (RDC). Elle propose une interface graphique (IHM) sombre et épurée (Dark Mode premium) bâtie avec **Tkinter**, connectée à une base de données locale **SQLite**.

L'application démontre l'utilisation de concepts avancés de programmation orientée objet (POO) en Python, de design patterns (Singleton, Factory) et de polymorphisme par Duck Typing.

---

## 🚀 Fonctionnalités Clés

1. **Gestion des Contribuables (Authentification & Inscription) :**
   - Connexion via le Numéro d'Identification Fiscale (NIF). Le format du NIF congolais est validé strictement (9 caractères alphanumériques).
   - Inscription d'un nouveau contribuable avec attribution automatique d'un NIF unique (ex: `A1234567B`) et dotation d'un portefeuille fiscal virtuel fictif.
   
2. **Calculateur de Taxes Dynamique (Polymorphisme) :**
   Le calcul des impôts s'adapte automatiquement selon le profil du contribuable :
   - **Personne Physique (Salarié, Profession Libérale) :** Calcule l'**IPR** (Impôt Professionnel sur les Rémunérations) selon un barème progressif congolais simplifié (tranches de 3% à 30%), avec un plafonnement légal à 30% du revenu total.
   - **Petite Entreprise (PME) :** Soumise au régime synthétique libératoire simplifié de **1%** sur le chiffre d'affaires.
   - **Grande Entreprise :** Soumise à la **TVA (16%)** et à l'**IBP** (Impôt sur les Bénéfices et Profits - **30%** du bénéfice estimé à 25% du chiffre d'affaires).

3. **Soumission & Historique des Déclarations :**
   - Enregistrement permanent des déclarations en base de données.
   - Tableau de bord récapitulatif (historique) des déclarations soumises avec leur statut actuel (`En attente` ou `Payé`).

4. **Passerelle de Paiement et Reçu Fiscal (Duck Typing) :**
   - Intégration de deux processeurs de paiement autonomes :
     - **Mobile Money** (Orange Money, M-Pesa, Airtel Money)
     - **Virement Bancaire** (Rawbank, EquityBCDC, TMB)
   - Génération instantanée d'un reçu fiscal officiel numérisé avec une référence de transaction unique (ex: `TX-MOB-ORA-XXXXXXXX` ou `TX-BANK-RAW-XXXXXXXXXX`).
   - Débit réel sur le solde virtuel du contribuable avec gestion des erreurs de solde insuffisant.

---

## 📁 Architecture du Code

Le projet est structuré de manière modulaire :

* 📑 **[main.py](file:///c:/Users/tegra/OneDrive/Desktop/ElimHealth/Chris/Genie-log/main.py)** : Le point d'entrée de l'application. Initialise la base de données et démarre la boucle principale Tkinter (`mainloop`).
* 📑 **[models.py](file:///c:/Users/tegra/OneDrive/Desktop/ElimHealth/Chris/Genie-log/models.py)** : Contient le cœur métier de l'application (modèles de données).
  - Définition de la hiérarchie de classes héritant de la classe abstraite `Contribuable` : `PersonnePhysique`, `PersonneMorale`, `GrandeEntreprise`, `PetiteEntreprise`.
  - Implémentation des processeurs de paiement `PaiementMobileMoney` et `PaiementVirementBancaire` pour illustrer le **Duck Typing**.
* 📑 **[factory.py](file:///c:/Users/tegra/OneDrive/Desktop/ElimHealth/Chris/Genie-log/factory.py)** : Implémente le pattern **Factory** (`ContribuableFactory`) pour instancier dynamiquement le bon objet métier (`Contribuable`) selon le type stocké en base de données.
* 📑 **[database.py](file:///c:/Users/tegra/OneDrive/Desktop/ElimHealth/Chris/Genie-log/database.py)** : Gère la persistance locale via **SQLite**. Utilise le pattern **Singleton** (`DatabaseManager`) pour assurer une seule connexion active dans toute l'application.
* 📑 **[exceptions.py](file:///c:/Users/tegra/OneDrive/Desktop/ElimHealth/Chris/Genie-log/exceptions.py)** : Définit des exceptions personnalisées propres au domaine fiscal (`NumeroImpotInvalideError`, `SoldeInsuffisantError`, `ContribuableExistantError`) héritant d'une classe de base `TaxeErreur`.
* 📑 **[gui.py](file:///c:/Users/tegra/OneDrive/Desktop/ElimHealth/Chris/Genie-log/gui.py)** : Gère toute l'interface utilisateur. Comprend le style moderne et les transitions entre les 3 vues principales :
  1. *Vue de connexion / inscription.*
  2. *Vue de formulaire et calcul de déclaration.*
  3. *Vue d'historique, paiement et reçu.*

---

## 🛠️ Installation et Lancement

### Prérequis
L'application utilise uniquement la bibliothèque standard de Python 3. Elle ne requiert l'installation d'**aucune dépendance externe** !

### Exécution
1. Ouvrez un terminal dans le répertoire du projet.
2. Lancez l'application avec la commande suivante :
   ```bash
   python main.py
   ```

---

## 💡 Guide d'Utilisation pas à pas

### 1. Authentification ou Enregistrement
- **Connexion :** Saisissez un NIF valide à 9 caractères existant dans la base de données (par exemple, vous pouvez en trouver dans la table `contribuables` de `fiscus_rdc.db`).
- **Inscription :** Si vous n'avez pas de compte, entrez un nom/raison sociale, sélectionnez votre profil fiscal et cliquez sur **Créer un Compte Fiscal**. Votre NIF généré s'affichera à l'écran. Notez-le pour vos futures connexions !

### 2. Déclarer ses Revenus
- Une fois connecté, vous arrivez sur l'écran de déclaration périodique.
- Entrez votre **Revenu Mensuel** (si personne physique) ou votre **Chiffre d'Affaires** (si entreprise).
- Cliquez sur **Calculer les taxes** pour prévisualiser la déclaration calculée selon les lois de la RDC.
- Cliquez sur **Soumettre la déclaration** pour l'enregistrer dans la base de données. Son statut initial sera `En attente`.

### 3. Payer la Taxe et Obtenir le Reçu
- Cliquez sur **Voir Historique & Paiement** pour accéder au tableau de bord.
- Votre **Portefeuille Fiscal** affiche votre solde virtuel restant (chaque contribuable commence avec un solde virtuel généreux, par ex. 5 000 000 ou 8 000 000 CDF).
- Dans l'**Historique des Déclarations**, cliquez sur la ligne correspondant à votre déclaration `En attente`.
- Choisissez votre moyen de règlement : *Mobile Money* ou *Virement Bancaire*.
- Cliquez sur **Procéder au Paiement**.
- **Résultat :** Le solde de votre portefeuille est débité du montant de l'impôt. Le statut passe à `Payé` et un reçu officiel de la DGI s'affiche avec la référence de la transaction bancaire ou mobile générée.
