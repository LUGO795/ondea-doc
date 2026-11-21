# Ondea Docs Manager — v1.0.0

Application de gestion documentaire d'entreprise, 100 % locale, de la suite **Ondea Studio**. Elle reprend le design system « Obsidienne & Or » d'Ondea Staff Manager.

## Arborescence

```
Ondea-Docs-Manager/
├── index.html                        ← L'APPLICATION COMPLÈTE (fichier unique autonome, ~745 Ko)
├── guide.html                        ← GUIDE D'UTILISATION illustré (même design, hors ligne, imprimable en PDF)
├── LISEZMOI.md                       ← ce fichier
└── ondea-docs-manager-sources.zip    ← sources modulaires (pour maintenance / évolution)
    ├── build.py                      ← réassemble index.html à partir de src/
    ├── src/head.html, body.html, fonts.html (polices embarquées)
    ├── src/base.css (design system repris à l'identique), docs.css (composants documentaires)
    ├── src/js/00-core.js … 20-zoom.js  (modules JavaScript commentés en français)
    └── tests/ (tests automatisés Playwright)
```

Pour utiliser l'application, seul `index.html` est nécessaire. Placez `guide.html` **dans le même dossier** : il s'ouvre alors depuis le menu entreprise › « Guide d'utilisation ». Le fichier ZIP des sources ne sert qu'à faire évoluer le code (`python build.py` régénère `dist/index.html`).

## Lancer l'application dans Google Chrome sous Windows 10 / 11

1. Copiez `index.html` dans un dossier stable, par exemple `Documents\Ondea Docs\`. Ne le laissez pas dans `Téléchargements` : les données sont liées à l'emplacement du fichier.
2. Faites un clic droit sur `index.html` > **Ouvrir avec** > **Google Chrome** (ou Microsoft Edge).
3. Au premier lancement, saisissez le nom de votre entreprise, puis choisissez l'une de ces options :
   - **Démarrer avec des exemples (démo)** : 27 documents fictifs marqués « DÉMO », que vous pouvez supprimer en un clic.
   - **Démarrer avec une base vide**.
   - **Restaurer une sauvegarde** existante.
4. Ajoutez le favori (Ctrl + D) pour rouvrir l'application facilement.

Quelques points à connaître :

- Aucune installation, aucun serveur, aucune connexion Internet ni aucun abonnement ne sont nécessaires.
- Les données sont liées au navigateur, au profil Windows **et** à l'emplacement du fichier. Si vous déplacez `index.html` ou changez de navigateur, l'application s'ouvrira vide. Dans ce cas, restaurez votre dernière sauvegarde.
- N'ouvrez pas l'application en navigation privée : les données y seraient effacées à la fermeture.

## Se déconnecter

Le bouton **Se déconnecter** se trouve en bas de la barre latérale, à droite du nom de l'entreprise. C'est la même icône et le même emplacement que dans Ondea Staff Manager. La commande est aussi disponible dans le menu entreprise, en haut à droite.

- **Avec un code de verrouillage** (Paramètres › Confidentialité & verrouillage) : le code est exigé pour revenir, y compris après fermeture du navigateur.
- **Sans code** : la session est fermée et un écran « Se reconnecter » s'affiche, même après un rechargement de la page.

Dans les deux cas, aucune donnée n'est supprimée.

## Effectuer une sauvegarde complète

Menu **Sauvegarde & restauration** > **Créer une sauvegarde complète**. Chrome télécharge un fichier `ondea-docs-sauvegarde-complete-AAAA-MM-JJ-HHhMM.zip`. Copiez-le sur une clé USB, un disque externe ou un NAS.

Contenu de l'archive (format ZIP standard, qui s'ouvre aussi avec l'Explorateur Windows) :

| Élément | Contenu |
|---|---|
| `manifest.json` | Version du format, compteurs, liste des fichiers avec sommes CRC-32 et empreintes SHA-256 |
| `data/documents.json` | Toutes les métadonnées (tiers, dates, montants, paiements, historique…) |
| `data/folders.json`, `settings.json`, `activity.json`, `alerts.json` | Dossiers, paramètres, journal, état des alertes |
| `files/<id>/<nom>` | **Fichiers originaux**, identiques octet pour octet |
| `LISEZMOI.txt` | Description du format |

Options disponibles :

- **Chiffrement AES-256** par mot de passe. Le fichier produit est un `.odmx`. Sans le mot de passe, ce fichier est irrécupérable.
- **Sauvegarde incrémentale** : elle ne contient que les documents modifiés depuis la dernière sauvegarde.
- **Export d'une sélection** : cochez des documents dans une liste, puis cliquez sur « Exporter (ZIP) ».
- **Export des métadonnées en JSON** (sans les fichiers) et **liste des documents en CSV** (lisible dans Excel).

Un rappel s'affiche dans la cloche si aucune sauvegarde complète n'a été faite depuis 7 jours. Ce délai se règle dans les Paramètres.

## Restaurer une sauvegarde

Menu **Sauvegarde & restauration** > **Choisir une sauvegarde (.zip ou .odmx)**. Vous pouvez aussi glisser-déposer le fichier.

1. L'archive est **entièrement vérifiée** avant toute écriture : structure, sommes de contrôle, présence et intégrité de chaque fichier. En cas d'anomalie, rien n'est modifié.
2. Un **récapitulatif** s'affiche : documents ajoutés, mis à jour, doublons, conflits, erreurs.
3. Choisissez ensuite le mode de restauration :
   - **Importation complémentaire** (recommandée) : elle ajoute les documents manquants et met à jour ceux dont la version dans l'archive est plus récente. L'identification se fait par un identifiant unique et stable. Elle **ne supprime jamais** un document existant. Vous choisissez aussi ce que deviennent les doublons (ignorés ou copiés) et les conflits (garder la version locale ou celle de l'archive).
   - **Restauration complète** : elle remplace toutes les données. Elle demande de saisir `RESTAURER` et propose de télécharger d'abord une sauvegarde de sécurité. L'écriture se fait en une seule transaction : en cas d'erreur, rien n'est modifié.

Pour restaurer des sauvegardes incrémentales, restaurez d'abord la dernière sauvegarde complète, puis importez chaque incrémentale dans l'ordre, en mode complémentaire.

## Tests réalisés

Les tests ont été exécutés automatiquement avec Chromium (Playwright), fichier ouvert en `file://`, langue fr-FR.

**Résultat : 46/46 tests principaux, 14/14 tests complémentaires, 0 bouton sans action.**

- **Lancement** sans erreur JavaScript.
- **Navigation** dans les 20 rubriques, sans lien mort.
- **Thème et affichage** : bascule clair/sombre, contraste élevé et texte à 135 % puis 150 %, tous conservés après rechargement. Aucun débordement horizontal.
- **Importation** :
  - PDF : Blob intact, SHA-256 identique à l'original.
  - Image, avec génération de la miniature.
  - Plusieurs fichiers simultanés (JPG, TXT, CSV, DOCX, WEBP).
  - Fichier `.exe` refusé.
  - Doublon détecté et ignoré.
  - Fichier de 12 Mo.
- **Consultation** :
  - Téléchargement (octets identiques à l'original).
  - Prévisualisation PDF, image et TXT/CSV.
  - Format non affichable signalé clairement, sans faux aperçu.
  - URL Blob libérées après usage.
- **Dossiers** : dossiers et sous-dossiers, renommage, déplacement, fil d'Ariane, suppression (les documents sont conservés).
- **Recherche** : par nom, catégorie, référence, tiers, mot-clé, statut et texte TXT ; filtres combinés ; raccourcis `cat:`, `tiers:`, etc.
- **Contrats** : création, modification, calcul du préavis, passage automatique à « arrivé à échéance ».
- **Échéances** : alertes, colonne « En retard », calendrier, alerte marquée comme traitée.
- **Factures** : TTC = HT + TVA, paiement partiel, solde, sur-paiement refusé, « payée ».
- **Devis** : alerte d'expiration, acceptation, facture créée depuis le devis sans doublon, Kanban au clavier.
- **Organisation** : favoris, archivage groupé et restauration, corbeille (restauration et suppression définitive avec effacement du fichier).
- **Sauvegardes** :
  - Sauvegarde complète : ZIP valide, SHA-256 de chaque fichier vérifié.
  - Restauration complète.
  - Importation complémentaire sans écrasement.
  - Sauvegarde chiffrée : mauvais mot de passe refusé, bon mot de passe accepté.
  - Sauvegarde incrémentale.
  - Export ZIP d'une sélection, export JSON et CSV.
- **Persistance** après fermeture et réouverture du navigateur.
- **Smartphone** (390 × 844) : menu repliable, modales adaptées, formulaires sur une colonne.
- **Clavier** : `/`, Entrée, Échap, Alt + N, focus maintenu dans les modales.
- **Démonstration** chargée puis supprimée, sans impact sur les données réelles.
- **Sécurité** :
  - Verrouillage par code.
  - Injection HTML neutralisée.
  - Aucune requête réseau.

## Limites techniques connues

- **Stockage** : IndexedDB, limité par l'espace disque et le quota du navigateur (en général plusieurs Go sous Chrome). La jauge « Stockage local » indique l'espace utilisé. Le bouton « Demander un stockage persistant » réduit le risque d'effacement automatique par le navigateur.
- **Archive ZIP** : 4 Go et 65 000 fichiers au maximum par archive (pas de ZIP64). Au-delà, faites des exports par sélection.
- **Chiffrement des sauvegardes** : l'archive est chiffrée en mémoire, jusqu'à 1,5 Go environ.
- **Recherche** : elle porte sur les métadonnées et le texte des fichiers TXT/CSV/MD. Le contenu des PDF, Word et Excel n'est **pas** indexé (aucune extraction de texte n'est embarquée).
- **Aperçu** : natif pour PDF, JPG, PNG, WEBP, GIF, TXT et CSV. Les autres formats (Word, Excel, HEIC…) doivent être téléchargés pour être ouverts.
- **Rappels** : ils s'affichent dans l'application uniquement. Aucun e-mail, SMS ni notification en arrière-plan n'est envoyé.
- **Verrouillage par code** : il protège l'accès à l'interface mais **ne chiffre pas** les documents stockés. Pour une protection réelle, utilisez la sauvegarde chiffrée et BitLocker.
- **Portée de l'outil** : outil de classement et de suivi. Ce n'est ni un logiciel comptable certifié, ni une solution de facturation électronique réglementaire, ni un système d'archivage à valeur probante. Il ne garantit pas la conformité RGPD.
- **Plusieurs onglets** : les onglets ouverts se synchronisent automatiquement. Il est toutefois préférable de n'en utiliser qu'un seul.
