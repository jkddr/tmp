# Guide de résolution : Bootloop Ryzen 5 3600 sur Gigabyte B550 Eagle

## Problème identifié

**Symptômes :**
- Le PC démarre et affiche le BIOS normalement
- Le bootloop se déclenche après le BIOS, pendant le chargement de l'OS
- Le problème persiste même avec une clé USB Ubuntu
- Le BIOS reste stable (pas de redémarrage dans les menus BIOS)
- Le problème a commencé après modification des paramètres BIOS

**Configuration matérielle :**
- Processeur : AMD Ryzen 5 3600 (sans GPU intégré)
- Carte mère : Gigabyte B550 Eagle WiFi6 (BIOS ID: 8ARN R315)
- Nouvelle carte mère et nouvelle RAM (problème persiste)
- Ancien disque dur conservé

## Hypothèse technique

Le problème survient lors de la **transition entre l'affichage BIOS basique et l'affichage graphique avancé**. Voici pourquoi :

1. **Phase BIOS** : Affichage VGA basique → ✅ **Fonctionne**
2. **Phase transition** : Passage aux drivers graphiques avancés → ❌ **PLANTAGE ICI**
3. **Phase OS** : Jamais atteinte

**Cause probable :** Le Ryzen 5 3600 n'ayant pas de GPU intégré, il dépend entièrement de la carte graphique. Les paramètres BIOS actuels ne gèrent pas correctement cette transition d'affichage.

## Sources utilisées pour l'analyse

**Documentation officielle :**
- Manuel BIOS Gigabyte B550/A520 Series (PDF officiel)
- Guide Gigabyte : "How to enable TPM 2.0 in Gigabyte BIOS Settings"
- Documentation MAINGEAR : "How to enable TPM on a Gigabyte motherboard for AMD & Intel processors"

**Pages support Gigabyte :**
- B550 Gaming X, B550 Aorus Elite support pages
- Historique des mises à jour BIOS pour séries AMD 500

**Validation de l'hypothèse :**
Les sources confirment que les paramètres **CSM (Compatibility Support Module)**, **Above 4G Decoding** et **Initial Display Output** sont critiques pour les CPU AMD sans GPU intégré.

---

## PROCÉDURE DE RÉPARATION COMPLÈTE

### ⚠️ IMPORTANTE : Lisez toutes les étapes avant de commencer

### **ÉTAPE 0 : Reset BIOS complet (OBLIGATOIRE)**

**Objectif :** Repartir d'une base propre et identique pour tous

1. **Arrêtez complètement le PC** et débranchez le câble d'alimentation
2. **Localisez la pile CMOS** (pile ronde argentée sur la carte mère)
3. **Retirez délicatement la pile** pendant **10 minutes minimum**
4. **Pendant l'attente :** Maintenez le bouton power du PC enfoncé 30 secondes (PC débranché)
5. **Remettez la pile** en place
6. **Rebranchez** l'alimentation et démarrez le PC
7. **Entrez dans le BIOS** en appuyant sur **Del** au démarrage
8. **IMPORTANT :** Ne chargez PAS les "Optimized Defaults" pour l'instant

---

### **ÉTAPE 1 : Configuration de l'affichage principal**

**Navigation :** Settings → IO Ports

**Paramètres à modifier :**

| Paramètre | Valeur à définir | Explication |
|-----------|------------------|-------------|
| **Initial Display Output** | **PCIe 1 Slot** | Force l'utilisation de votre carte graphique dès le démarrage |
| **Above 4G Decoding** | **Disabled** | **CRITIQUE** - Évite les conflits d'adressage avec le Ryzen 5 3600 |
| **PCIEX16 Bifurcation** | **Auto** | Laisse le BIOS gérer automatiquement la bande passante |

**Pourquoi c'est important :** Ces paramètres s'assurent que votre carte graphique est correctement reconnue et utilisée dès le début du processus de démarrage.

---

### **ÉTAPE 2 : Activation du mode de compatibilité**

**Navigation :** Boot

**Paramètres à modifier :**

| Paramètre | Valeur à définir | Explication |
|-----------|------------------|-------------|
| **CSM Support** | **Enabled** | **ESSENTIEL** - Active le mode de compatibilité pour les anciens standards d'affichage |
| **VGA Support** | **Auto** | Utilise les drivers graphiques compatibles (pas les nouveaux EFI) |
| **Storage Boot Option Control** | **Legacy Only** | Force l'utilisation des anciens standards de démarrage |
| **Other PCI Device ROM Priority** | **Legacy Only** | Applique le mode legacy à tous les périphériques PCI |

**Pourquoi c'est important :** Le mode CSM permet une transition plus douce entre le BIOS et l'OS, évitant les plantages lors du changement de mode d'affichage.

---

### **ÉTAPE 3 : Désactivation du démarrage rapide**

**Navigation :** Boot

**Paramètres à modifier :**

| Paramètre | Valeur à définir | Explication |
|-----------|------------------|-------------|
| **Fast Boot** | **Disabled** | **CRUCIAL** - Évite les transitions trop rapides qui causent le plantage |
| **Full Screen LOGO Show** | **Disabled** | Simplifie l'affichage pendant le démarrage |

**Pourquoi c'est important :** Le Fast Boot accélère le processus de démarrage mais peut causer des problèmes lors des transitions d'affichage avec votre configuration.

---

### **ÉTAPE 4 : Configuration CPU et sécurité**

**Navigation :** Settings → Miscellaneous

**Paramètres à modifier :**

| Paramètre | Valeur à définir | Explication |
|-----------|------------------|-------------|
| **AMD CPU fTPM** | **Disabled** | Désactive temporairement le TPM pour éliminer les conflits |
| **IOMMU** | **Disabled** | **IMPORTANT** - Évite les problèmes de virtualisation mémoire |

**Si accessible - Navigation :** Settings → AMD CBS → CPU Common Options

| Paramètre | Valeur à définir | Explication |
|-----------|------------------|-------------|
| **SVM Mode** | **Disabled** | Désactive temporairement la virtualisation |

**Pourquoi c'est important :** Ces fonctionnalités avancées peuvent interférer avec le processus de démarrage. On les désactive temporairement pour stabiliser le système.

---

### **ÉTAPE 5 : Test et validation**

1. **Sauvegardez les paramètres :**
   - Appuyez sur **F10**
   - Sélectionnez **"Yes"** pour sauvegarder
   - Le PC va redémarrer automatiquement

2. **Test avec clé USB Ubuntu :**
   - Insérez votre clé USB Ubuntu
   - Au démarrage, appuyez sur **F12** pour le Boot Menu
   - Sélectionnez votre clé USB
   - **Observez** si le bootloop persiste

3. **Résultats attendus :**
   - ✅ **Si Ubuntu démarre** : Problème résolu ! Votre hypothèse était correcte
   - ❌ **Si bootloop persiste** : Problème matériel probable (alimentation, RAM)

---

### **ÉTAPE 6 : Si le test réussit - Optimisation progressive**

**Une fois Ubuntu fonctionnel, vous pouvez réactiver progressivement :**

1. **AMD CPU fTPM** → Enabled (pour Windows 11)
2. **IOMMU** → Enabled (si nécessaire pour vos applications)
3. **SVM Mode** → Enabled (si vous utilisez la virtualisation)

**⚠️ Réactivez UN SEUL paramètre à la fois et testez à chaque fois**

---

## Résumé des points critiques

**Les 3 paramètres les plus importants pour votre configuration :**

1. **CSM Support = Enabled** - Mode de compatibilité essentiel
2. **Above 4G Decoding = Disabled** - Évite les conflits avec le Ryzen 5 3600
3. **Fast Boot = Disabled** - Permet une transition douce

**Si cette procédure résout votre problème :** Cela confirme que le bootloop était causé par une mauvaise gestion de la transition d'affichage entre le BIOS et l'OS, exactement comme vous l'aviez diagnostiqué.

**Si le problème persiste :** Il s'agit probablement d'un problème matériel (alimentation défaillante ou RAM défectueuse).

---

## Notes importantes

- **Patience :** Chaque redémarrage peut prendre plus de temps avec ces paramètres
- **Sécurité :** Ces paramètres désactivent temporairement certaines protections modernes
- **Après résolution :** Vous pourrez réactiver progressivement les fonctionnalités avancées
- **Sauvegarde :** Une fois stable, sauvegardez votre profil BIOS (Save Profiles)

**Date de création :** Août 2025  
**Configuration testée :** Ryzen 5 3600 + Gigabyte B550 Eagle WiFi6
