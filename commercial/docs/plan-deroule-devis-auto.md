# Plan de construction — Déroulé devis automatique assisté

**v1.3 — 23/09/2026** · chantier 169 · s'appuie sur [bilan-deroule-devis-230926.md](bilan-deroule-devis-230926.md) (v1.1)

Le bilan dit **d'où on part** (dossier HORIBA de A à Z, règles, pièges). Ce plan dit **où on va** et dans quel ordre. Chaque lot livré met à jour les deux documents.

Décisions : **Olivier** (O), 23/09/2026. Claude = assistant.

---

## 1. Objectif

Transformer la journée HORIBA (Claude qui fait tout à la main dans le chat) en une **chaîne outillée** :
n8n exécute la mécanique dans Luminovo via le MCP (ouvert le 23/09), avec des **scripts figés et testés** ;
l'équipe valide aux portes ; Claude n'intervient que là où il faut du jugement.

Cible : **30 à 45 min par devis** au lieu d'une journée.

---

## 2. Décisions de cadrage (O, 23/09)

| Sujet | Décision |
|---|---|
| Écran de l'équipe | **devis-client**, pop-up déroulé (trame du chantier 174) — pas l'appli Luminovo `deroule-devis` (rien de plus n'y est construit tant que Luminovo n'a pas validé sa connexion) |
| Rythme | étapes 4 à 7 **automatiques dès le départ**, à condition de **contrôles bloquants** dans chaque script ; portes humaines seulement sur les vrais choix |
| Compteur d'autonomie | seules les **corrections de fond** comptent (mauvaise pièce, quantité, prix) ; les retouches de forme sont ignorées |
| Relecture finale | remplacée par la **fiche de cohérence** (§5) ; le **sourcing en est exclu** (c'est l'étape que l'humain regarde vraiment) |
| Envoi du devis | **toujours humain** |
| Devis PCB fournisseur | *(v1.3, O)* l'humain dépose le devis PDF **directement dans l'offre Luminovo** (Luminovo lit le PDF et remplit l'offre, rien à saisir) ; n8n **relit ce PDF** depuis Luminovo et le **contrôle se fait à la fin**, dans la fiche de cohérence (§5, §6). Une offre PCB sans devis PDF joint sort en 🟠 |
| Principe d'ergonomie | *(v1.3, O)* **quand on est dans Luminovo, on y traite un maximum de choses sans en ressortir** : pas d'aller-retour entre les écrans, source de perte de temps et pénible pour l'humain. Les contrôles se regroupent en fin de parcours plutôt que de couper le travail |
| Source des valeurs | *(v1.1)* **toute valeur de spec PCB porte sa source** (fichier, page) ; une valeur sans source identifiée sort en 🟠. Principe : aucune source n'est crue seule, pas même Claude |
| Rôle de l'IA | jugement seulement ; la mécanique passe par des scripts |

---

## 3. Architecture

```
 devis-client (pop-up « Étape X/8 », trame 174)
        │ clic / go / corriger          ▲ résultat + alertes
        ▼                               │
 n8n — 1 workflow par étape ───────────┘
        │  état du dossier ──────────▶ Supabase : table deroule_devis
        │                                (étape, statut, niveau, journal des go, versions)
        ├──▶ Scripts Luminovo figés (MCP), versionnés : creer_rfq_pcb, importer_bom,
        │     creer_scenarios, lancer_sourcing, lire_sourcing, controle_pcb…
        ├──▶ Claude API : jugement seulement
        ├──▶ Odoo (remplir la trame du devis, adresses)
        └──▶ FabStory (questions client)
```

**Trois règles de construction**
1. **Aucune improvisation** : toute écriture Luminovo passe par un script versionné, jamais par du code écrit à la volée (leçon de l'incident des repères, bilan §E8).
2. **Tout est journalisé** : qui a dit « go », à quelle étape, avec quelle version de script.
3. **Les contrôles bloquent** : une anomalie arrête l'étape, elle ne continue jamais « au mieux ».

**Trois rôles séparés**

| Mécanique (scripts, pas d'IA) | Jugement (Claude) | Décision (humain) |
|---|---|---|
| créer RfQ/PCB/scénarios, importer la BOM avec contrôles, lancer/lire le sourcing, remplir Odoo | lire la fiche fabricant PDF, classer les lignes douteuses, rédiger les questions client, contrôle PCB (§6), fiche de cohérence (§5), bouton « Aide de Claude » du pop-up | valider la spec, trancher le douteux, choisir les offres PCB, regarder le sourcing, envoyer |

Coût IA estimé : quelques centimes à quelques dizaines de centimes par devis (à mesurer).

---

## 4. Les étapes

| Étape | Automatique | Claude | Porte humaine | Reste à l'écran Luminovo |
|---|---|---|---|---|
| 1-3 | existant (devis-client) + correctifs bilan E1-E5 | — | — | — |
| 4 RfQ + PCB | `creer_rfq_pcb` | spec depuis la fiche fabricant PDF | go spec | 4 blocs PCB ; Gerber/PnP (piste `code_mode_stage_file`) |
| 5 BOM | `importer_bom` : repères = quantités, fabricant strict, boîtiers, consignés | lignes douteuses | douteux seulement | — |
| 6 Scénarios | `creer_scenarios` depuis « PP Chiffrage », nommage, FR/WW | Nouveau/Refab, CMS light | modèle, compteurs | — |
| 7 Sourcing + PCB | `lancer_sourcing` (fond), `lire_sourcing`, radar Safe-PCB | questions client ; **contrôle PCB (§6)** | sourcing, exclusions, questions ; dépôt devis PCB dans l'offre | choix des offres PCB |
| 8 Devis | lecture calcul + remplissage trame Odoo + adresses | **fiche de cohérence (§5)** | montants, envoi | calcul flexible |

**Niveaux d'autonomie par étape** : 1 pas à pas → 2 assisté (s'arrête aux portes) → 3 autonome (s'arrête sur alerte). Passage proposé après 5 devis consécutifs sans correction de fond ; **O décide**.

---

## 5. Fiche de cohérence (avant l'envoi, sans sourcing)

| Indicateur | Contrôle | Besoin |
|---|---|---|
| BOM | repères = quantités, pièces au bon fabricant, obsolètes signalés | règles du bilan §B.5 |
| PCB spec | *(v1.1)* recoupement **à trois sources** : spec client (fiche fabricant / empilage) + Gerber (nombre de couches, faces de sérigraphie) + spec Luminovo ; puis devis fournisseur (§6) | — |
| PCB prix | prix au dm² selon couches et délai vs historique | **référence historique** |
| Scénarios | modèle cohérent avec les règles (nouveau/refab, quantités échelonnées, CMS light) | règles du bilan §B.6 |
| M.O. | coût par composant, par tranche de quantité, vs historique | **référence historique** |

Tout 🟢 → devis validable. 🟠/🔴 → seul l'écart est montré, avec sa raison.

---

## 6. Contrôle PCB fournisseur

| Fournisseur | Qui renseigne la spec | Contrôle Claude |
|---|---|---|
| Safe-PCB (radar Luminovo) | Luminovo, depuis la fiche PCB | non (même source) |
| **Ellipse en ligne** (pcbprototypes.com) | **PP saisit le formulaire** | **oui** : devis PDF (joint à l'offre Luminovo) comparé à la spec PCB |
| Ellipse PCB (RFQ), PCB Electronics (RFQ) | le fournisseur, depuis les fichiers | **à décider** (voir §8) |

Paramètres contrôlés : couches, épaisseur, cuivre fini, finition, vernis/sérigraphie, dimensions, panneau, quantités, délai. *(v1.1)* + **date code**, **exigences matière** (Tg, low CTE…), test électrique.

*(v1.1)* Les exigences sans champ Luminovo (low CTE, zone de date code…) sont écrites dans la **note de fabrication** de la fiche PCB, avec leur source, par le script `creer_rfq_pcb`.

**Cas réel, 23/09 (AURA ADEC111290000, devis déjà envoyé)** : Luminovo portait 6 couches et ENIG (valeurs forcées sans source) ; la spec client PP5 et les Gerber donnent 8 couches et HAL sans plomb ; le devis Ellipse était juste mais sans date code ni low CTE. Les offres Safe radar, calculées sur la fausse spec, ont été expirées. Le prix envoyé reposait sur Ellipse, donc cohérent. Corrigé dans Luminovo le 23/09.

---

## 7. Lots de construction

| Lot | Contenu | Test d'acceptation |
|---|---|---|
| **0 Socle** | utilisateur Luminovo dédié ; appel MCP hebdomadaire ; table `deroule_devis` ; sécurité E9 ; **référence historique** (M.O./composant, PCB/dm²) ; levée des inconnues §9 | connexion MCP stable 2 semaines |
| **1 BOM** | `importer_bom` + étape 5 dans le pop-up | rejouer HORIBA : 85 repères, 31 postes, mêmes pièces |
| **2 RfQ + PCB** | `creer_rfq_pcb` + spec PDF + test dépôt de fichiers | spec HORIBA identique |
| **3 Scénarios + sourcing** | étapes 6-7 + contrôle PCB §6 | mêmes offres que le réel ; test sur devis Ellipse réels |
| **4 Devis** | étape 8 + fiche de cohérence | **HORIBA rejoué = 361,88 € et 105,00 €** |
| En parallèle | devis-client E1-E5 ; modèle « PP Chiffrage » (O) | — |

Banc d'essai permanent : le dossier HORIBA, dont on connaît le résultat au centime.

---

## 8. Questions ouvertes

1. Contrôle des devis PCB **RFQ** (Ellipse PCB, PCB Electronics) : relecture légère des paramètres qui font le prix, ou aucune ?
2. Offre Ellipse en ligne : les prix de l'offre sont-ils saisis par l'humain (Claude vérifie aussi prix saisis = PDF), ou extraits du PDF par Claude ?

## 9. Inconnues techniques (lot 0)

| Inconnue | Enjeu |
|---|---|
| Stockage et versionnage des scripts (`run_script`) | cœur de l'architecture |
| `code_mode_stage_file` pour Gerber/PnP | supprimer le dépôt manuel |
| Lecture des fichiers joints à une offre (`GET /offers/custom-part/:id/additional-files`) | contrôle PCB §6 — *(v1.1)* via le MCP le PDF arrive **corrompu** (binaire converti en texte) ; **résolu le 23/09** : n8n le télécharge **intact** en HTTP direct avec le lien signé fourni par la fiche PCB (`files[].path`, test sur PP5 AURA : 773 971 octets, identique à l'original). Le devis fournisseur reste lu à la source (§2) ; les PDF déjà dans Luminovo sont relisibles par n8n |
| Relecture par n8n d'un PDF joint à une **offre** (`GET /offers/custom-part/:id/additional-files`) | contrôle PCB en fin de parcours (§2) — la relecture des PDF de la **fiche PCB** est prouvée (v1.2), celle des pièces jointes d'offre reste à tester |
| Budget ~25 s par exécution de script | découper « lancer » / « lire plus tard » |
| Droits minimaux de l'utilisateur dédié | sécurité des écritures |

---

## Journal des révisions

| Version | Date | Objet |
|---|---|---|
| v1.3 | 23/09/2026 | Devis PCB déposé directement dans l'offre Luminovo, contrôle en fin de parcours ; principe « rester dans Luminovo » (O). Remplace le circuit « dépôt dans le pop-up » de la v1.1. |
| v1.2 | 23/09/2026 | Lecture des PDF Luminovo résolue (téléchargement direct par n8n) ; maintien hebdomadaire du MCP en place (`6OfqNbiplgHLbSLn`). |
| v1.1 | 23/09/2026 | Leçons du contrôle AURA : trois sources, valeur sans source = 🟠, date code et exigences matière, note de fabrication ; devis PCB déposé dans le pop-up, lu à la source par Claude puis déposé automatiquement dans l'offre (idée d'Olivier). |
| v1.0 | 23/09/2026 | Rédaction initiale après la séance HORIBA et l'ouverture du MCP n8n. |
