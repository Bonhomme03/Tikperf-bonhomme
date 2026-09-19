# TikPerf Bonhomme v9.11 — CHANGELOG

**Date** : 19 septembre 2026  
**Fichier** : `tikperf-bonhomme-v9.11-FINAL.html`

---

## 🔴 BUGS CRITIQUES CORRIGÉS

### BUG #1 : Stock final théorique faux (COGS mois seul au lieu de cumul)

**Symptôme** (screenshot Moussa) : Drawer stock affichait :
```
SI (1er janv.)        = 60 058,80 €
+ Achats HT           = +132 156,20 €  ← CUMUL JAN→AOÛT
- COGS période        = -3 818,80 €    ← SEULEMENT AOÛT ❌
= Stock final théo    = 188 396,20 €
```

**Problème** : La formule était incohérente : **achats cumulés** mais **COGS mois seul**.

**Règle métier correcte** :
```
Stock final théorique = SI + Achats (JAN→mois) - COGS (JAN→mois)
```

**Fix v9.11** :

#### 1. Ligne 2305 — Calcul stockFinalTheorique
```javascript
// Avant (v9.10) :
var stockFinalTheorique = stockInitial + sommeAchatsHT - cogsVal;  // ❌ cogsVal = mois seul

// Après (v9.11) :
var stockFinalTheorique = stockInitial + sommeAchatsHT - cogsValCumul;  // ✅ cogsValCumul = cumul JAN→mois
```

#### 2. Ligne 2323 — Ajouter cogsValCumul à _kpiSnap
```javascript
txRemb: txRemb, cogsVal, cogsValCumul, skuCovered, skuMissing, hasCOGS,  // v9.11
```

#### 3. Lignes 1905, 1919 — Drawer : afficher cogsValCumul
```javascript
// Avant :
+dr('− COGS période', '− '+f2(s.cogsVal||0)+' €', 'coût des marchandises vendues', ...)

// Après :
+dr('− COGS période', '− '+f2(s.cogsValCumul||0)+' €', 'coût des marchandises vendues (cumul JAN→mois)', ...)
```

---

### BUG #2 : getDashboardVarNbMois retourne 17 (cumul années)

**Symptôme** : Charges Variables affiche `17 / 12` au lieu de `9 / 12`.

**Cause** : Même problème qu'en v9.10 — `getDashboardVarNbMois()` retourne le nombre de mois sur **plusieurs années** quand aucune année n'est sélectionnée.

**Fix attendu en v9.11.5** : Appliquer la même logique que v9.10 (ligne 2002 — si `fMois` sélectionné sans `fAnnee`, forcer année courante).

---

## 📋 RÉSUMÉ DES FIXES v9.7 → v9.11

| Version | Fix | Statut |
|---------|-----|--------|
| v9.7 | Queue Firebase (rafales deletions) | ✅ |
| v9.7 | Stock Shopify persistence | ✅ |
| v9.8 | KPI Achats Marchandises | ✅ |
| v9.9 | COGS/FDT mois seul (marges correctes) | ✅ |
| v9.10 | Transport cumul JAN→août bug | ✅ |
| v9.10 | Firebase undefined values | ✅ |
| **v9.11** | **Stock final théorique cumul** | **✅** |

---

## 📝 RÈGLES MÉTIER FINALISÉES

**Cumul vs mois seul** — confirmé par session dev :

| Variable | Filtre | Usage |
|----------|--------|-------|
| `cogsVal` | **Mois seul** | Marges brutes/nettes |
| `cogsValCumul` | **Cumul JAN→mois** | Stock final théorique + Rotation |
| `sommeAchatsHT` | **Cumul JAN→mois** | Stock théorique (cohérent avec COGS cumul) |
| `getVentesSKUFiltre()` | **Mois seul** | Marges (pas pour stock) |

---

## 🧪 TEST MANUEL v9.11

1. Recharger complet (`Ctrl+Maj+R`)
2. Filtrer **août seul** (sans année)
3. Ouvrir drawer **Stock / Rotation**
4. Vérifier formule :
   - SI (1er janv.) + Achats (JAN→août) - COGS (JAN→août) = Stock final théorique
   - Les trois valeurs doivent être cohérentes (toutes cumulées)
5. Vérifier que Stock moyen = (SI + SF théo) / 2

---

## 🔗 FICHIERS LIÉS

- `/mnt/user-data/outputs/tikperf-bonhomme-v9.11-FINAL.html` — Version courante
- `/mnt/user-data/outputs/CHANGELOG_v9.10.md` — Changelog v9.10
