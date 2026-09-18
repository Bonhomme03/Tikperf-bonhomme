# TikPerf v9.6 | CHANGELOG

**Date** : 18 septembre 2026  
**Release** : v9.6 (depuis v9.5)  
**Type** : Version bump après stabilisation

---

## 📦 Contenu de v9.6

**Cumul des changements depuis v9.4** :

### ✅ Stock & Inventory (v9.5)
- Fix #1 : Achats HT cumulatifs JAN → selected month
- Fix #2 : COGS cumulatif JAN → selected month  
- Fix #3 : FDT cumulatif JAN → selected month
- Fix #4 : Stock Shopify auto-renseigné depuis import XLSX

### ✅ Firebase Sync (v9.5)
- Save immédiat sur suppression (Transport Achats)
- Avertissement beforeunload si save en cours

### ✅ Dashboard UI (v9.6)
- Suppression "9/12 mois réalisés" (TikTok Ads, Marge nette)
- Suppression "% achats" (Transport sur Ventes)
- Suppression "47 ligne(s)" (FDT)
- Suppression "prorata période" (Charges Variables)

---

## 🎯 KPI Dashboard — Affichage simplifié

| KPI | Avant v9.6 | Après v9.6 |
|-----|-----------|-----------|
| TikTok Ads | 9.11% du CA HT · 9/12 mois | **9.11% du CA HT** |
| Transport ventes | 10.35% du CA HT · 25.19% achats | **10.35% du CA HT** |
| FDT | 1.34% du CA HT · 47 ligne(s) | **1.34% du CA HT** |
| Charges var. | 8.80% du CA HT · prorata période | **8.80% du CA HT** |
| Marge nette | 16.99% du CA HT · 9/12 mois | **16.99% du CA HT** |

---

## 🧪 Validation v9.6

**À vérifier après déploiement** :

1. ✅ Version affichée en bas = **v9.6**
2. ✅ Dashboard KPIs = étiquettes simplifiées
3. ✅ Stock Shopify = auto-renseigné ~60 449,70 €
4. ✅ Formule stock = cumul JAN → période sélectionnée
5. ✅ Suppression facture = save immédiat (pas de perte)

---

## 📊 Formule stock (inchangée, stable depuis v9.5)

```
Stock Value = Stock Initial (1er jan) 
            + SUM(Achats HT, JAN → selected) 
            - SUM(COGS, JAN → selected)
```

**Exemple Septembre 2026** :
```
Stock initial : 60 058,80 €
+ Achats cumulatif : 132 156,20 €
- COGS cumulatif : 85 804,00 €
━━━━━━━━━━━━━━━━━━━━━━━━━━
= Stock théorique : 106 411,00 € ✅
```

---

## 🚀 Déploiement

**Fichier** : `tikperf-bonhomme-v9.6-FINAL.html`

**Replaces** : `tikperf-bonhomme-v9.5-FINAL.html` (ou quelle que soit la version live)

**Backup** : Conserver v9.5 en local pour rollback si besoin

---

## 📝 Notes

- **Commentaires de code** : Les mentions "Fix v9.5" restent dans le code (historique)
- **Affichage** : "v9.6 · 2025–2026" en bas de page (login et sidebar)
- **Stabilité** : Tous les fixes de v9.5 inclus + nettoyage UI

---

**v9.6 — Production Ready** ✅
