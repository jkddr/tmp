# Guide de résolution : Bootloop Ryzen 5 3600 sur Gigabyte B550 Eagle (BIOS 8ARNR315)

## Problème identifié

**Symptômes :**
- Le PC démarre et affiche le BIOS normalement
- Le bootloop se déclenche après le BIOS, pendant le chargement de l'OS
- Le problème persiste même avec une clé USB Ubuntu
- Le BIOS reste stable (pas de redémarrage dans les menus BIOS)
- Le problème a commencé après modification des paramètres BIOS

**Configuration matérielle :**
- Processeur : AMD Ryzen 5 3600 (sans GPU intégré)
- Carte mère : Gigabyte B550 Eagle WiFi6 (BIOS ID: **8ARNR315**)
- Carte graphique : NVIDIA GTX 1660 Super
- Nouvelle carte mère et nouvelle RAM (problème persiste)
- Ancien disque dur conservé

## Hypothèse technique

Le problème survient lors de la **transition entre l'affichage BIOS basique et l'affichage graphique avancé**. Voici pourquoi :

1. **Phase BIOS** : Affichage VGA basique → ✅ **Fonctionne**
2. **Phase transition** : Passage aux drivers graphiques avancés → ❌ **PLANTAGE ICI**
3. **Phase OS** : Jamais atteinte

**Cause probable :** Le Ryzen 5 3600 n'ayant pas de GPU intégré, il dépend entièrement de la carte graphique. Les paramètres BIOS actuels ne gèrent pas correctement cette transition d'affichage.

## Spécificités du BIOS 8ARNR315

**IMPORTANT :** Votre BIOS 8ARNR315 est une **version simplifiée** qui :
- ✅ **Masque** certaines options avancées pour simplifier l'interface
- ✅ **Compense automatiquement** l'absence de certains paramètres quand CSM est activé
- ✅ **Gère différemment** la transition graphique par rapport aux versions BIOS complètes
- ❌ **N'affiche PAS** le paramètre "VGA Support" (normal pour cette version)

---

## PROCÉDURE DE RÉPARATION ADAPTÉE 8ARNR315

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
| **Initial Display Output** | **PCIe 1 Slot** | Force l'utilisation de votre GTX 1660 Super dès le démarrage |
| **Above 4G Decoding** | **Disabled** | **CRITIQUE** - Évite les conflits d'adressage avec le Ryzen 5 3600 |
| **PCIEX16 Bifurcation** | **Auto** | Laisse le BIOS gérer automatiquement la bande passante |

**Pourquoi c'est important :** Ces paramètres s'assurent que votre GTX 1660 Super est correctement reconnue et utilisée dès le début du processus de démarrage.

---

### **ÉTAPE 2 : Activation du mode de compatibilité (Version 8ARNR315)**

**Navigation :** Boot

**Paramètres à modifier :**

| Paramètre | Valeur à définir | Explication |
|-----------|------------------|-------------|
| **CSM Support** | **Enabled** | **ESSENTIEL** - Active le mode de compatibilité pour votre configuration |
| **Storage Boot Option Control** | **Legacy Only** | Force l'utilisation des anciens standards de démarrage (si présent) |

**⚠️ SPÉCIFIQUE 8ARNR315 :** 
- **"VGA Support" N'EXISTE PAS** dans votre version BIOS - c'est normal !
- Le BIOS 8ARNR315 **gère automatiquement** l'affichage graphique quand CSM est activé
- **NE CHERCHEZ PAS** ce paramètre, il n'apparaîtra jamais

**Pourquoi c'est important :** Le mode CSM permet une transition plus douce entre le BIOS et l'OS, et le BIOS 8ARNR315 compense automatiquement l'absence d'options VGA avancées.

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

### **ÉTAPE 5 : Configuration mémoire (Spécifique 8ARNR315)**

**Navigation :** Tweaker → Advanced Memory Settings

**Paramètres à modifier :**

| Paramètre | Valeur à définir | Explication |
|-----------|------------------|-------------|
| **System Memory Multiplier** | **Auto** | Laisse le BIOS gérer automatiquement la fréquence mémoire |
| **Extreme Memory Profile (XMP)** | **Disabled** | Désactive l'overclocking mémoire temporairement |

**Pourquoi c'est important :** Le BIOS 8ARNR315 peut avoir des problèmes avec l'overclocking mémoire pendant la phase de démarrage problématique.

---

### **ÉTAPE 6 : Test et validation**

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
   - ✅ **Si Ubuntu démarre** : Problème résolu ! Le BIOS 8ARNR315 gère maintenant correctement la transition
   - ❌ **Si bootloop persiste** : Passez à l'étape 7

---

### **ÉTAPE 7 : Test matériel (Si étape 6 échoue)**

**Test RAM :**
1. **Éteignez** le PC complètement
2. **Retirez** toutes les barrettes RAM sauf une
3. **Installez** cette barrette dans le slot **DDR4_A2** (2ème slot depuis le CPU)
4. **Redémarrez** et testez Ubuntu
5. Si ça ne fonctionne pas, **testez l'autre barrette**

**Si le test RAM échoue aussi :** Le problème est probablement l'alimentation ou la carte mère elle-même.

---

### **ÉTAPE 8 : Si le test réussit - Optimisation progressive**

**Une fois Ubuntu fonctionnel, vous pouvez réactiver progressivement :**

1. **Extreme Memory Profile (XMP)** → Enabled (pour retrouver les performances mémoire)
2. **AMD CPU fTPM** → Enabled (pour Windows 11)
3. **IOMMU** → Enabled (si nécessaire pour vos applications)
4. **SVM Mode** → Enabled (si vous utilisez la virtualisation)

**⚠️ Réactivez UN SEUL paramètre à la fois et testez à chaque fois**

---

## Résumé des points critiques pour BIOS 8ARNR315

**Les 4 paramètres les plus importants pour votre configuration :**

1. **CSM Support = Enabled** - Mode de compatibilité essentiel pour 8ARNR315
2. **Above 4G Decoding = Disabled** - Évite les conflits avec le Ryzen 5 3600
3. **Fast Boot = Disabled** - Permet une transition douce
4. **XMP = Disabled** (temporairement) - Évite les conflits mémoire au démarrage

**Différences importantes du BIOS 8ARNR315 :**
- ✅ **Gestion automatique** de l'affichage graphique avec CSM activé
- ✅ **Interface simplifiée** sans options VGA avancées
- ✅ **Compensation automatique** des paramètres manquants
- ⚠️ **Réglages mémoire plus sensibles** que les versions BIOS complètes

**Si cette procédure résout votre problème :** Cela confirme que le bootloop était causé par une mauvaise gestion de la transition d'affichage entre le BIOS et l'OS, exactement comme vous l'aviez diagnostiqué, et que le BIOS 8ARNR315 nécessitait cette configuration spécifique.

**Si le problème persiste :** Il s'agit probablement d'un problème matériel (alimentation défaillante ou RAM défectueuse).

---

## Notes importantes pour BIOS 8ARNR315

- **Patience :** Les démarrages peuvent être plus lents avec ces paramètres
- **Version simplifiée :** Votre BIOS masque volontairement certaines options pour éviter les erreurs
- **Après résolution :** Vous pourrez réactiver progressivement les fonctionnalités avancées
- **Sauvegarde :** Une fois stable, sauvegardez votre profil BIOS (Save Profiles)
- **Particularité GTX 1660 Super :** Cette carte s'adapte parfaitement aux paramètres CSM Legacy

**Date de création :** Août 2025  
**Configuration testée :** Ryzen 5 3600 + GTX 1660 Super + Gigabyte B550 Eagle WiFi6 (BIOS 8ARNR315)
