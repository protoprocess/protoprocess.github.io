# Bilan « Déroulé devis assisté par Claude » — HORIBA 1300131501D de A à Z

**v1.0 — 23/09/2026** · chantier 169 (déroulé devis) · chantier 174 (hub d'accueil) · fil « Attente retour Luminovo n8n »

Document de référence rédigé à la fin de la séance du 23/09/2026, à partir du transcript complet, du chantier 169 (Supabase) et du fil Luminovo/n8n. Chaque point porte sa date et sa source. Il sert d'ouverture à la conversation suivante : « chantier 169, on construit le déroulé auto assisté — lire le bilan v1.0 ».

Auteurs des décisions : **Olivier** (O), **Ariel** (A). Claude = assistant.

---

## A. Ce qui a été fait — dossier HORIBA de A à Z

### A.1 Chronologie de la journée (23/09/2026)

| Heure | Étape | Fait par | Résultat |
|---|---|---|---|
| matin | Virage tactique : le déroulé devis passe par Claude (connecteur MCP Luminovo de claude.ai), sans attendre Luminovo | O | Règles du déroulé fixées (§B) |
| matin | Boulette n°1 : analyse devis-client rattachée à la mauvaise carte (352 = One Connect) → analyse supprimée, on repart de zéro | O + Claude | Défaut devis-client noté (§E) |
| matin | Nouvelle analyse, création carte `CLI277-1300131501` « Control Agilis », indice D | A | Carte FabStory + Drive créés par le bouton habituel |
| matin | Étape 4 : RfQ `1300131501D Control Agilis` + assemblage + fiche PCB `1300131502D ControlAgilis` créés par Claude ; devis Odoo `SO2609-515` créé par Ariel dans devis-client | Claude / A | rfqId `c4302095-f25c-4dad-bf1b-f1841405c62f` |
| matin | Gerber déposés sur la fiche PCB (main) ; PnP `.rep` refusé → converti en CSV par Claude, déposé par Ariel | A | 90 composants, tous face TOP |
| matin | Étape 5 BOM : 31 lignes + PCB ; **incident repères inventés L03-L33** (corrigé dans un autre fil) ; 5 boîtiers renseignés sur les fiches pièces | Claude | 85 repères exacts, 32 postes |
| midi | Étape 6 : 4 scénarios créés (sourcing Qté 3 / Qté 50 ; fab NOUVELLE FAB STD ×3, REFAB STD ×50) | Claude | Compteurs manuels saisis par Ariel |
| midi | Sourcing lancé (**load-api-offers explicite**), lecture, corrections fabricants (KEMET), modèle sourcing, Mouser | Claude / O | §B.5, §C |
| 13h-14h | PCB : spécification complétée, 4 blocs validés à l'écran (A), radar Safe-PCB refusé puis accepté (structure interne 0,20 mm), devis manuel Ellipse saisi | Claude / A | Offres Safe DEP827466 + Ellipse |
| 14h-15h | Ariel choisit les offres PCB à la main (Safe ×3, Ellipse ×50) ; L1 exclu du sourcing ; questions client FabStory créées (C15, L1) | A / Claude | fiche_de_vie 368, 369 |
| 15h-16h | FSV360FP retiré de D1 ; calculs fab lus ; calcul flexible « FAB Q3 23/09 » créé par Ariel ; prix confirmés au centime | A / Claude | 361,88 € ×3 ; 105,00 € ×50 ; NRE 213,20 € |
| 16h20 | Devis Odoo SO2609-515 rempli par Claude (sonde n8n, nœud d'écriture ajouté puis retiré), contre-lu | Claude | Total HT 6 593,34 € |
| 16h30 | Adresses livraison / facturation complétées à la main par Olivier (oubli de Claude → règle §B.7) | O | Devis prêt à l'envoi |

### A.2 Artefacts et identifiants

| Objet | Valeur |
|---|---|
| Carte FabStory | produit 424 · `CLI277-1300131501` · libellé « Control Agilis » · indice D · désignation client « 1300131501D controlAgilis Board » |
| Devis Odoo | `SO2609-515` (id 1179), validité 22/11/2026, partenaire 89 HORIBA FRANCE SAS |
| RfQ Luminovo | `c4302095-f25c-4dad-bf1b-f1841405c62f` · assemblage `a23166af-…` · PCB `f5708882-…` · custom part PCB `f0b9fc22-…` |
| Scénarios sourcing | Qté 3 `01a0cd9c-d6e4-…` · Qté 50 `01a0cd9c-ded5-…` |
| Scénarios fab | Qté 3 NOUVELLE FAB STD `acc979ca-…` · Qté 50 REFAB STD `9f3cda8c-…` |
| Calcul flexible | « FAB Q3 23/09 » `01a0ce96-…`, modèle `019d0732-…` |
| Offres PCB | Safe-PCB DEP827466 (radar) ; Ellipse-tronic 2 offres manuelles (valables 23/10) |
| Questions client | fiche_de_vie 368 (C15 obsolète), 369 (L1 indisponible), étape DEVIS, visibles client |
| PnP converti | `PnP 1300131502D ControlAgilis.csv` (90 lignes) |
| Lignes Odoo | 8400 NRE · 8401 ×3 · 8402 note · 8403 ×50 · 8404 note · 8405 section · 8406 IPC · 8412 C15/L1 · 8413 F4 |

### A.3 Chiffres du dossier

| | Qté 3 | Qté 50 |
|---|---|---|
| Matière (composants + PCB, L1 exclu) | 422,87 € → 140,96 €/carte | 2 694,52 € → 53,89 €/carte |
| Fabrication (coût atelier) | 81,97 €/carte + NRE 116,95 € | 15,18 €/carte |
| **Prix de vente** (modèle client standard) | **361,88 €** | **105,00 €** (+0,89 logistique Ellipse = 105,89 sur le devis) |
| NRE facturé | 213,20 € | — |
| Délai | PCB 8 j + fab 14 j ≈ 4 semaines | idem |

**En suspens sur ce dossier** : réponse HORIBA sur C15 et L1 (équivalents), F4 (fusible dans la pose, absent de la BOM), C16 (55 j, à régler à la commande), relecture et envoi du devis depuis Odoo (geste humain).

---

## B. Règles métier fixées, par étape du déroulé

Numérotation des étapes : 1 dossier reçu · 2 analyse devis-client · 3 carte + devis Odoo · 4 RfQ + PCB · 5 BOM + pièces · 6 scénarios · 7 sourcing + PCB · 8 calcul + devis Odoo + envoi.

### B.0 Cadre général (O, 23/09)
- **Valideurs** des écritures Luminovo/Odoo : Maura, Dorian, Ariel ou Olivier. Claude fait leur travail en assistant ; ils valident. Un « go » avant chaque écriture.
- **Dossier de test** = un vrai dossier à chiffrer. Ariel déroule, Olivier en support, tant que le projet « Équipe » n'existe pas.
- Discours : jamais « je demande à Olivier » ; « je regarde et j'en discute avec l'équipe » (O, 22/09, déroulé commande — s'applique aussi au devis).
- Trame écran (O, 21-22/09, chantier 174) : **le pop-up est l'écran de travail**, une étape à la fois dans l'ordre, étapes déjà faites = « déjà fait : vérifie et valide », panneau droit = conseils / lu sur le dossier / aide / demander de l'aide à Claude, Quitter (reprendre plus tard) et Abandonner (raison obligatoire). Rejeté : un pop-up qui commente l'appli et la cache.

### B.2-3 Analyse et carte
- HORIBA : référence carte = famille sans indice (`1300131501`), indice à part (`D`), libellé court sans « Board » ; **réf PCB nu ≠ réf carte** (1300131502D) — la fiche PCB porte la réf du PCB.
- Rattachement d'une analyse : ne jamais proposer une carte « approchante » du même client ; la carte proposée doit avoir exactement la réf détectée, sinon proposer la création (O, 23/09).
- Absence de Gerber ≠ « PCB fournis par le client » ; c'est « fichiers manquants » (Claude, validé O).
- Ordre : Claude crée le RfQ → donne le lien → Ariel crée le devis Odoo avec ce lien (quantités du client).

### B.4 RfQ et PCB
- Nommage RfQ : convention actuelle (`1300131501D Control Agilis`).
- Gerber et PnP : dépôt **manuel** (pas d'API de dépôt de fichier accessible à Claude). Tout PnP non CSV/xlsx est converti par Claude (le `.rep` eCADSTAR n'est pas accepté).
- Spécification PCB : source de vérité = **fiche fabricant du client** (PDF), recopiée par Claude ; Luminovo ne stocke que les écarts avec sa lecture Gerber (le reste se fond dans la mesure). Les 4 blocs (fichiers, perçage, stackup, spécification) se **valident à l'écran seulement**.
- La « classe 4 » d'une fiche HORIBA = classe de finesse (0,20 mm), pas la classe IPC.
- Plans internes exportés « en traits » (eCADSTAR) : les outils mesurent 0,05 mm d'isolement, c'est un artefact → surcharger la structure interne à 0,20 mm dans la spec ; **ne rien demander à HORIBA, c'est le fabricant PCB qui se débrouille** (O).
- Panneau : 3 cartes = 1 panneau de 3 (bords techniques 10 mm) ; 51 cartes = 17 panneaux de 3 (choix Ariel, validé).
- UL et RoHS (BOM client) : stratifié UL dans la spec, logos en sérigraphie.
- Portail Safe-PCB : Reference = clé carte avec indice + nom (`CLI277-1300131501D Control Agilis`), Circuit name = réf PCB nu + nom (`1300131502D ControlAgilis`). Portail Ellipse : Référence = clé carte avec indice + nom, Version = indice ; « 17/35 µm » = 35 µm fini.
- Chiffrage PCB : **radar Luminovo d'abord** (Safe-PCB connecté ; offres arrivent dans la fiche PCB), devis manuel chez Ellipse en comparaison, saisi comme offre custom-part par Claude. **Le choix de l'offre PCB reste à la main** (A/O).

### B.5 BOM et pièces
- **Règle BOM (O, 23/09, après l'incident)** : ne jamais affirmer une propriété de la BOM (pas de repères, pas de MPN…) d'après le résultat d'analyse — toujours revérifier les données brutes (exécution n8n nœud Entree, ou fichier) ; si repères absents ou repères ≠ quantité → **arrêt et signalement, jamais de repères inventés**.
- **Fabricant strict (O)** : la fiche retenue est exactement celle du fabricant écrit dans la BOM (alias Luminovo et fautes de frappe « YAEGO » tolérés) ; jamais une fiche de regroupement (« KEMET / YAGEO »), jamais une maison-mère (Yageo pour KEMET) ; si aucune fiche du bon fabricant → **ligne en erreur**, signalée, jamais une approximation. Si la BOM se trompe elle-même (RC0603FR-074R99L marqué KEMET), le signaler.
- Pas de validation ligne par ligne : Claude applique d'office ce qui est sûr, ne soumet que le douteux (O).
- Pièces obsolètes : approuvées si le client l'a écrit ; une pièce **définitivement** obsolète (plus d'achat possible, ex. FSV360FP dernier achat 01/2022) est retirée de la ligne, après vérification.
- Boîtiers manquants : Claude les renseigne sur la **fiche pièce** (partagée, une fois pour toutes), avec source, sur go.
- Lignes consignées Luminovo = fournitures client, à lire au devis (transfert chantier 177).
- IPN codifiés sans MPN (ex. R1R-0603) : ligne créée sur l'IPN.

### B.6 Scénarios
- Nommage : fab « Qté X - JJMMAA nom du modèle », sourcing « Qté X - JJMMAA ».
- Sourcing : FR/WW selon le pays du client (pré-choix Claude) ; **un nouveau scénario part toujours du modèle « PP Chiffrage », jamais d'un ancien scénario**.
- Fabrication : Refab vs Nouveau (refab = reprendre l'existant) ; 100 % Trad / CMS light à demander.
- **CMS LIGHT** = 1 à ~10 CMS simples, sans boîtier fin (BGA, QFN…) ; en cas de doute, calculer les deux et garder le plus juste.
- **Quantités échelonnées sur un nouveau produit** : petite qté en NOUVELLE FAB STD, grande qté en REFAB STD ; **aucune mention « sous réserve »** sur le devis (explication commerciale si besoin).
- Compteurs manuels (CMS à coller, modif méthodes) : saisis par le valideur ; Claude propose (0 ici) et vérifie.
- **Préférence de délai** : Olivier **refuse** de passer les scénarios / le modèle en « meilleur prix avant date » — reste « le plus rapide », la sélection des offres se fait à la main quand ça compte.

### B.7 Sourcing et devis
- Lancer la recherche de prix (`load-api-offers`) **dès que la BOM est validée** (plusieurs minutes, tâche de fond) ; par l'API elle ne se lance pas seule ; après chargement, « relancer la sélection des offres » à l'écran si les offres PCB n'apparaissent pas.
- Fournisseurs « bidon » (TISIT, ICS…) = agrégateurs (Octopart, TrustedParts, Sourcengine) dans les sources du modèle → à retirer dans « Sources de données » (O regarde le modèle lui-même).
- Une fiche fournisseur par site de stock (DigiKey US / inconnu, Farnell ×3, Mouser ×2) : même statut aux doublons.
- Offres anciennes (ex. Arrow 296 jours d'ancienneté) : exclusion des offres hors validité activée sur les scénarios.
- Ligne sans solution (L1, MOQ 500 → excédent) : **exclure du sourcing** (choix A) plutôt qu'une offre estimée ; question client posée.
- Composants non disponibles à signaler au client **seulement** s'ils ne sont pas remplaçables à l'identique (pas un condensateur ni une résistance : C16 laissé) ; obsolètes non remplaçables (MAX3051) : information.
- Questions client au stade devis : entrées FabStory **sur la carte** (étape DEVIS, visibles client) — la page client est rattachée à la carte, pas à la commande, donc visibles via le lien du mail Odoo ; textes reproduits en commentaire du devis.
- **Calcul flexible** : créé à l'écran par le valideur (modèle client standard : matière +40 %, fabrication ×1,823 structure + facteur difficulté + 10 %, NRE ×1,823 sans marge) ; l'API ne lit pas les résultats (sauf calcul figé) → Claude recalcule et **fait confirmer les montants affichés**.
- **Devis Odoo** : la trame devis-client existe déjà (NRE / ligne qté 1 / note matière-M.O. / ligne qté 2 / note / section Commentaires / IPC) → **remplir les lignes, pas en créer** ; NRE en tête ; frais logistiques PCB fondus dans le prix unitaire ; « à titre indicatif » pour la grande quantité en note ; mentions C15/L1/F4 en notes.
- **Adresses (O, 16h30)** : adresse de livraison = le contact demandeur chez le client ; adresse de facturation = le contact « Comptabilité fournisseur » de la société. À faire à chaque devis.
- « Traité » = devis **envoyé** (pas créé), constaté chaque matin par le workflow « PP — Devis : état Odoo du matin » (chantier 174). L'envoi reste un geste humain.

---

## C. Leçons techniques et pièges

### Luminovo (API via MCP `code_mode_exec`)
- `POST /sourcing-scenarios/:id/load-api-offers` : à appeler explicitement ; 1-3 min.
- `POST /sourcing/calculation/read-selection` : lecture des sélections (statut, offre, prix, dispo) ; le PCB y est la ligne sans `part_options`.
- `PATCH /design-items/bulk` : `part_specification` **à la racine** (pas dans « update ») + `add_part_events: []` ; un mauvais format vide silencieusement les options.
- Recherche pièces `POST /parts/off-the-shelf/search/mpn/many` : plusieurs fiches par MPN (fabricant, groupe, « Other », distributeur) → choisir sur `manufacturer.name` exact.
- Fiche PCB : `PUT /ems/pcb/v2/pcbs/:pcb/specifications/:spec` accepte `baseMaterial 'fr-4'`, `surfaceFinish 'enig'`, `tgValue {Celsius}`, `ulLayerStack`, `ipc600Class 'ipc2'`, `eTest`, `minInnerLayerStructure {Millimeter}` ; seules les valeurs ≠ analyse sont conservées ; l'approbation des blocs n'a pas d'API (GET seulement).
- Radar PCB : `POST /assemblies/:id/pcb/:pcbId/offer-state` (statuts InvalidSpecification / OffersAvailable…) ; offres dans `GET /offers/custom-part?part=`, `pricing_mode Panel`, `price_breaks` par délai.
- Offre manuelle : `POST /offers/custom-part` (unit_of_measurement, price_breaks, one_time_costs, restricted_for RfQ).
- Calcul flexible : `GET /flexible-calculation/plans/rfq/:rfqId` donne la structure, **pas les résultats** ; `GET /calculations/calculation-assembly-costs` = 404 tant que le calcul n'est pas créé à l'écran.
- Modèle sourcing : `GET /sourcing-templates` ; préférences fournisseurs `Approved/Preferred/Excluded` ; « préféré » ≠ « approuvé » côté API mais un seul statut à l'écran ; ne jamais copier les préférences d'un ancien scénario.
- Extraction de spec PDF : `GET /pcb-pdf-specification-extraction` existe, **aucun déclencheur trouvé** (ni écran ni API) → à voir avec Marco.
- Pas de dépôt de fichier (Gerber, PnP) possible depuis Claude.
- Budget d'exécution `code_mode_exec` ≈ 25 s : ne pas attendre plus de ~20 s dans un script.

### n8n / Odoo
- Sonde `HTRsv9n4T4hc4gJY` (« PP — Odoo : exploration en lecture seule ») : JSON-RPC avec `$vars.ODOO_BASE / ODOO_LOGIN / ODOO_CLE_API` (pas de clé en clair) ; écriture = ajouter un nœud HTTP `execute_kw sale.order write` avec `order_line: [[1,id,{…}],[0,0,{…}]]`, exécuter, **retirer le nœud**, contre-lire.
- Devis créé par devis-client = 7 lignes pré-tramées (ids séquentiels), à mettre à jour.
- `sale.order` : `partner_shipping_id`, `partner_invoice_id` = contacts enfants de la société (res.partner parent_id).

### FabStory / Supabase
- `fiche_de_vie` : question client = `type 'question_client'`, `etat 'en_attente'`, `visible_client true`, `etape_code 'DEVIS'` (libellé « DEVIS / CHIFFRAGE »), `so_id null` accepté, `so_texte` = n° devis ; jetons client par **produit** (`fabstory_jetons`) et par partenaire.
- Convention Safe-PCB / Ellipse (§B.4).

---

## D. Carte des gestes — qui fait quoi

| Geste | Claude seul | Humain valide (« go ») | Écran seulement |
|---|---|---|---|
| Lire analyse, devis, BOM brute, exécutions n8n | ✔ | | |
| Créer RfQ, assemblage, fiche PCB, écrire la spec | | ✔ | |
| Déposer Gerber / PnP / PDF | | | ✔ (Ariel) |
| Convertir un PnP | ✔ | | |
| Valider les 4 blocs PCB | | | ✔ |
| Extraction PDF spec Luminovo | | | ✔ (méthode à retrouver) |
| Importer la BOM, associer les pièces (fabricant strict) | | ✔ (sauf lignes sûres) | |
| Renseigner les boîtiers (fiche pièce) | | ✔ | |
| Créer scénarios (depuis modèle) | | ✔ | |
| Saisir compteurs manuels | | ✔ (valideur) | |
| Lancer la recherche de prix, lire, alerter | ✔ | | |
| Corriger le modèle sourcing (sources, statuts) | | | ✔ (Olivier) |
| Radar PCB, lire les offres | ✔ | | |
| Saisir une offre PCB manuelle | | ✔ | |
| Choisir l'offre PCB / le délai | | | ✔ (à la main) |
| Exclure une ligne du sourcing | | ✔ / écran | |
| Créer les questions client FabStory | | ✔ | |
| Créer le calcul flexible | | | ✔ |
| Lire les prix de vente | ✗ (recalcul + confirmation) | ✔ | |
| Remplir le devis Odoo (lignes, notes, adresses) | | ✔ | |
| Envoyer le devis | | | ✔ |

---

## E. Défauts et manques constatés (à corriger)

| # | Où | Constat | Source |
|---|---|---|---|
| E1 | devis-client | Rattachement proposé = première carte du client ; doit être la carte dont la réf = réf détectée, sinon création | O, 23/09 |
| E2 | devis-client | Sans Gerber → « PCB fournis par le client » (faux : fichiers manquants) | 23/09 |
| E3 | devis-client | « Piste 50 µm » lue sur les plans internes : vraie mesure mais artefact d'export → à qualifier « plans en traits » plutôt que « hors standard » | 23/09 |
| E4 | devis-client | L'analyse ne conserve pas les fichiers → PnP non rechargeable par Claude ; PnP `.rep` non converti | 23/09 |
| E5 | devis-client / trame devis | Adresses livraison / facturation non préremplies ; notes « x€ / délai x jrs » à remplir | O, 16h30 |
| E6 | Luminovo modèle « PP Chiffrage » | Agrégateurs dans les sources (fournisseurs bidon) ; liste des approuvés hétéroclite ; Mouser corrigé par O | O, 23/09 |
| E7 | Luminovo | Extraction PDF de spec introuvable à l'écran | 23/09 |
| E8 | Claude | Incident repères inventés ; sélection fabricant laxiste ; PATCH bulk mal formé ; adresses oubliées ; préférence de délai proposée à tort → règles §B | 23/09 |
| E9 | Sécurité | Webhook devis-analyses `2WOsElx0jJbY2OuY` ouvert sans clé ; clé API Odoo en clair dans `06tqfU9B8HglMT6r` | 21/09 |
| E10 | Chantier 125 | Modèles de calcul flexible à enrichir (postures commerciales) | O |
| E11 | Chantier 174 | Déroulé commande v2.0 déployé (recette), **non testé** (pas de commande reçue) ; « Demander de l'aide à Claude » = message d'attente ; écarts bon/devis non calculés | 22/09 |

---

## F. Dépendances Luminovo (fil « Attente retour Luminovo n8n »)

| Élément | État au 23/09 | Débloque |
|---|---|---|
| Webhook n8n « Déroulé devis — ping » | **v0.2**, exige `X-PP-Secret`, test 403 / 200 passé, workflow de test archivé | — |
| Appli `deroule-devis` | **v0.6** (release 7), connexion n8n *auth static*, plus de connexion Anthropic ; en attente de revue | ① l'équipe lance le déroulé depuis l'appli Luminovo |
| Secret | credential n8n « Luminovo deroule-devis — Header Auth » + coffre Luminovo (statut active) ; jamais dans le code, mails, Supabase | — |
| MCP pour n8n | enregistrement dynamique si callback whitelisté ; **vraie URL n8n Cloud = `https://oauth.n8n.cloud/oauth2/callback`** (relais partagé — risque à signaler à Marco) ; mail corrigé | ② n8n agit seul dans Luminovo |
| API publique à jeton | proposée par Marco ; fourchette connue 4-5 k€/an ; prix à demander | flux automatisés sans chat |
| Utilisateur Luminovo dédié | à créer par Olivier (droits minimaux) | ①② |
| Relance de Marco | non (O lui fait confiance) | — |

**Aucune de ces attentes ne bloque le déroulé piloté par Claude** ; l'exécutant (Claude → n8n/appli) changera quand ① ou ② sera ouvert, le cadrage reste le même.

---

## G. Cible « déroulé auto avec assistance Claude »

Ce que le dossier HORIBA a monté, étape par étape, avec l'acteur et l'entrée/sortie. Base de la conception de la version suivante.

| Étape | Entrée | Claude | Humain | Sortie |
|---|---|---|---|---|
| 1-3 (existant) | mail / zip client | — | analyse devis-client, carte, devis Odoo | analyse rattachée à la bonne carte, SO créé |
| 4 RfQ + PCB | analyse + fiche fabricant PDF | crée RfQ/assemblage/PCB, écrit la spec depuis la fiche fabricant, signale les écarts Gerber/spec | go ; dépose Gerber/PnP/PDF ; valide 4 blocs | fiche PCB validée, radar possible |
| 5 BOM | BOM brute (nœud Entree) | importe, associe (fabricant strict), boîtiers, lit les consignés, signale douteux/obsolètes/F4 | go sur le douteux | BOM 100 % repères, pièces approuvées |
| 6 Scénarios | pays client, quantités, nouveau/refab | pré-choisit FR/WW, propose modèles, crée depuis le modèle, propose compteurs | choisit modèle, saisit compteurs | 2×N scénarios liés |
| 7 Sourcing + PCB | scénarios | lance les offres (fond), radar PCB, lit, classe (prix, délai, non-dispo), propose alternatives / questions client | corrige modèle, choisit offres PCB, exclut/valide, go questions client | matière chiffrée, questions posées |
| 8 Calcul + devis | calcul flexible | recalcule, fait confirmer, remplit lignes/notes/adresses Odoo, contre-lit | crée le calcul, confirme les montants, relit, envoie | devis envoyé (« traité » le lendemain) |

Points d'entrée : tuile « Créer un devis » du hub (protoprocess.github.io/recette) → devis-client (1-3) → « chiffre <dossier> » à Claude (projet Équipe à créer) pour 4-8.

Durée observée : une journée avec incidents et apprentissage ; cible réaliste 30-45 min par devis une fois les règles codées.

---

## H. Backlog priorisé (proposition, à arbitrer par Olivier)

1. **Créer le projet « Équipe »** avec ces règles consignées, pour qu'Ariel/Maura déroulent seuls.
2. **devis-client** : E1, E2, E5 (adresses préremplies dans la trame, notes), E4 (conserver les fichiers, convertir le PnP), E3 (qualification « plans en traits »).
3. **Modèle sourcing « PP Chiffrage »** : sources sans agrégateurs, liste des approuvés à jour, doublons de sites (Olivier).
4. **Passe BOM codifiée** (règle fabricant strict, confiance haute/douteuse) — en script réutilisable côté Claude, puis n8n quand le MCP s'ouvre.
5. **Questions client au devis** : générer les entrées FabStory + notes Odoo depuis la lecture du sourcing (non-dispo non remplaçables, obsolètes, écarts pose/BOM).
6. **Extraction PDF Luminovo** : demander la méthode à Marco.
7. Sécurité E9.
8. Chantier 125 (modèles de calcul) ; test du déroulé commande v2.0 à la prochaine commande.
9. Prix de l'API publique Luminovo (décision au vu du volume).

---

## Journal des révisions

| Version | Date | Objet |
|---|---|---|
| v1.0 | 23/09/2026 | Rédaction initiale à la fin de la séance HORIBA (chantiers 169/174, fil Luminovo/n8n). |
