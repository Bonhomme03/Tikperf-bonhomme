# TikPerf Bonhomme — CHANGELOG v9.7

**Date** : 18 septembre 2026  
**Fichier** : `tikperf-bonhomme-v9.7-FINAL.html`

---

## 🔧 FIXES CRITIQUES

### Fix A — File d'attente de sauvegarde Firebase (régression v9.5/v9.6)

**Problème identifié** : 
- Suppressions de doublons en rafale → les 2e, 3e, 4e... suppressions étaient ignorées silencieusement
- Raison : drapeau `_isSaving` rejetait tout save pendant qu'un autre était en vol
- Seule la 1re suppression atteignait Firebase ; les autres disparaissaient en local
- Au rechargement ou déploiement d'une nouvelle version, les doublons réapparaissaient

**Corrections appliquées** (lignes 6057, 6075, 6088, 6140, 6147) :
- Ajout d'un drapeau `_pendingSave` (vs. simple rejet)
- Quand un save est en vol et une modification arrive : `_pendingSave = true`
- À la fin du 1er save, si `_pendingSave` : relancer automatiquement un save avec l'état à jour
- Idem en cas d'erreur réseau (pas de perte de données)
- Le `beforeunload` couvre désormais aussi `_pendingSave`

**Résultat** : suppressions multiples garanties d'atteindre Firebase, même en rafale.

---

### Fix B — Persistance des quantités Shopify (régression v9.5/v9.6)

**Problème identifié** :
- Après import du catalogue XLSX, les quantités de stock s'affichaient correct dans Variables (~60 449,70 €)
- Mais au rechargement ou passage à une autre session, stock Shopify → 0 €
- Raison : le format compact envoyé à Firebase (`t/s/v/c/p/k`) oubliait la quantité (`q`)

**Corrections appliquées** (lignes 6119, 6226, 6250) :
- Ajout du champ `q` (inventory) au payload Shopify compact avant envoi à Firebase
- Lecture de `p.q||0` lors du chargement depuis Firebase (vs. forcé à 0)
- Recalcul automatique de la variable "Stock Shopify" après chargement, avec garde-fou

**Résultat** : catalogue restauré garde ses quantités ; stock Shopify stable au rechargement.

---

## 🆕 NOUVELLES FONCTIONNALITÉS

### Détection automatique de version + toast de rechargement (v9.7)

**Déploiement sans action manuelle** :
- À chaque chargement, vérifie si une ancienne version était en localStorage
- Si changement de version détecté : affiche un toast discret (bas-gauche)  
  `🔄 Nouvelle version disponible · Recharger`
- Utilisateur peut cliquer immédiatement ou ignorer (le toast s'efface après 8s)
- Une fois cliqué, rechargement automatique (200 ms pour visual feedback)

**Avantages** :
- Utilisateurs mobiles (PWA) : plus besoin de Ctrl+F5 (inaccessible sur mobile)
- Aucune interruption forcée : l'utilisateur reste maître du timing
- Disparition automatique après 8s si ignoré (ne pollue pas l'interface)
- Gradient bleu standard TikPerf, cohérent avec l'UI existante

---

## 📋 NUMÉROTATION VERSION

| Élément | Ligne | Avant | Après |
|---------|-------|-------|-------|
| Lock footer | 57 | v9.6 | v9.7 |
| Sidebar footer | 532 | v9.6 | v9.7 |
| localStorage tracking | — | — | `_tikperf_version` (nouveau) |

---

## 🧪 VALIDATION POST-DÉPLOIEMENT

**Checklist immédiate** :
- ✅ Version = v9.7 visible en bas de page (login + sidebar)
- ✅ Toast de rechargement apparaît pour les utilisateurs sur v9.6 (ne pas actualiser manuellement pour tester)
- ✅ Clic sur toast → rechargement sans erreur
- ✅ localStorage affiche `_tikperf_version: "v9.7"` dans DevTools

**Checklist fonctionnelle** (après suppression de doublons) :
- ✅ Supprimer plusieurs factures de suite (~5-10 d'affilée) → toutes disparaissent du tableau
- ✅ Recharger → aucune n'est réapparue (vs. v9.6 où les 2e+ revenaient)
- ✅ Déconnecter/reconnecter → idem
- ✅ Import catalogue XLSX → Variables "Stock Shopify" ~60 449,70 € ou proche
- ✅ Recharger → stock Shopify inchangé (vs. v9.6 où il tombait à 0 €)

---

## ⚠️ IMPACT UTILISATEURS

**Avant déploiement** : 
1. Valider fixes en local ou staging
2. Déployer `tikperf-bonhomme-v9.7-FINAL.html` sur GitHub Pages

**Après déploiement** :
1. **Toi** : actualise normalement
2. **Autres utilisateurs** : si onglet resté ouvert → toast s'affiche automatiquement
3. **Doublons revenus** : supprimer une 2e fois (cette fois persistent définitivement)

**Recommandation** : si utilisateurs ont plusieurs onglets TikPerf ouverts, fermer les autres avant de cliquer sur le toast. Un onglet sur l'ancienne version qui se synchronise en même temps peut réécrire les doublons.

---

## 📚 POINTS EN SUSPENS

- **v9.8** : import PNG/JPEG pour Transport Achats (Moussa a signalé besoin)
- **Améliorations UX** : toast progressif avec barre de chargement (countdown avant rechargement)

---

**Historique des versions** :
- **v9.6** : stock calculation fix, Shopify import fix, KPI cleanup
- **v9.5** : Firebase sync loss + beforeunload gate (régression introduite)
- **v9.4** : baseline (original)
