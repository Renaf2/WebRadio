# WebRadio

Application de webradio pour Android (Kotlin, Jetpack Compose, Media3/ExoPlayer).

- Aucune publicité
- Aucun traceur, aucune analytique, aucun compte
- Aucun envoi de données : l'app ne contacte que les serveurs des flux audio que vous choisissez
- Permissions : Internet, service de lecture en arrière-plan, notification de lecture
- Sauvegarde cloud Android désactivée (`allowBackup=false`)

## Ouvrir et lancer

1. Android Studio (Ladybug 2024.2 ou plus récent) > **File > Open** > dossier `WebRadio`
2. Laisser la synchronisation Gradle se terminer (connexion Internet nécessaire la première fois)
3. Brancher un téléphone (débogage USB) ou lancer un émulateur, puis **Run**

Si Android Studio propose de mettre à jour le plugin Android Gradle ou Gradle, vous pouvez accepter.

## Dépannage : « The daemon has terminated unexpectedly »

Erreur du compilateur Kotlin (souvent un manque de mémoire), pas une erreur de code. Le fichier
`gradle.properties` du projet contient déjà le correctif (`kotlin.compiler.execution.strategy=in-process`
et plus de mémoire). Si elle persiste :

1. Fermez Android Studio, puis dans le Gestionnaire des tâches (Windows) terminez les processus
   « OpenJDK Platform binary » / « java » restants (anciens daemons Gradle et Kotlin), et rouvrez le projet.
2. **File > Invalidate Caches > Invalidate and Restart**.
3. **Settings > Build, Execution, Deployment > Build Tools > Gradle > Gradle JDK** : choisissez le JDK
   fourni avec Android Studio (jbr-17 ou jbr-21).
4. Fermez les applications lourdes (navigateur, jeux) pendant la compilation, et vérifiez que votre
   antivirus n'isole pas le dossier du projet ni `C:\Users\<vous>\.gradle`.
5. Puis **Build > Clean Project** et **Build > Rebuild Project**.

## Fonctions

- Favoris : touchez le cœur d'une station (ou celui du lecteur en bas) pour la retrouver dans l'onglet
  « ⭐ Favoris », quel que soit son pays, y compris celles de l'onglet Monde. Les favoris restent
  uniquement sur l'appareil. L'app s'ouvre sur Favoris s'il y en a, sinon sur France.
- Interface : thème sombre « nuit violette » ou clair « crème » (suit le téléphone), pastilles de pays,
  cartes de stations, lecteur flottant en bas avec gros bouton lecture/pause et barres animées pendant l'écoute.

- Plus de 70 stations publiques classées par pays dans des onglets (drapeau + nom) : France, Royaume-Uni, États-Unis, Allemagne, Suisse, Belgique, Pays-Bas, Italie, Autriche, Suède, Australie, Japon, et un onglet « Mes stations »
- Radios publiques ou indépendantes dont le flux est offert à l'écoute gratuite par leur diffuseur (BBC, Radio France, SRG SSR, RTBF, NPO, RAI, ORF, Sveriges Radio, ABC, Deutschlandfunk, SomaFM, Radio Paradise, KEXP...). « Libre de droits » ne s'applique pas à leur musique : l'app ne fait que lire les flux officiels, sans les copier ni les enregistrer
- Certaines chaînes sont limitées à leur pays (ex. plusieurs chaînes BBC nationales)
- Onglet « 🌍 Monde » (désactivé par défaut) : radios de presque tous les pays via l'annuaire ouvert
  radio-browser.info, avec recherche de pays. Une fois activé, l'app interroge ce service (il voit votre
  adresse IP et le pays consulté, rien d'autre). Les flux `https` sont affichés par défaut ; un interrupteur
  permet d'inclure les flux `http` non chiffrés (plus de stations, moins sécurisé). L'app l'autorise donc
  techniquement (`usesCleartextTraffic="true"`) ; remettez-le à `false` si vous n'utilisez jamais cette option.
  Les stations de l'annuaire sont communautaires : certaines peuvent être hors service.
- Logo (ou pastille d'initiales) devant chaque nom
- Lecture en arrière-plan, écran éteint, avec notification et commandes (écran de verrouillage, casque Bluetooth)
- Ajout / suppression de vos propres stations (URL en `https://`), stockées uniquement sur l'appareil

## Logos des stations

Les logos sont gérés 100 % en local (aucune requête réseau, rien n'est téléchargé) :

- **Logos fournis par les stations (option, désactivée par défaut)** : dans l'onglet « Monde », l'interrupteur
  « Afficher les logos des stations » affiche pour chaque radio l'icône que son propre site publie
  (indiquée par l'annuaire). Chaque logo est téléchargé une seule fois depuis le site de la radio
  (même destinataire que le flux audio, jamais un service tiers, jamais Google), puis gardé dans le
  cache de l'app ; le bouton « Vider le cache des logos » l'efface. Sans cette option, aucun logo
  n'est téléchargé. Les logos restent la propriété de leurs radios : usage personnel conseillé ; en cas de
  publication de l'app, demandez l'accord des diffuseurs.
- Par défaut (ou si aucun logo n'est disponible), chaque station affiche un badge style BD / cartoon dessiné en code (`Cartoon.kt`) :
  forme variable (rond, carré arrondi, explosion), contour épais, ombre portée, reflet et un pictogramme original selon le genre (saxophone pour le jazz, guitare pour le rock, violon pour le classique, platine DJ pour l'électro, cassette pour le rétro, radio-cassette pour le hip-hop, journal pour l'info, micro, globe, soleil, lune, piano, vinyle, casque, étoile...). Les règles de choix sont dans `GLYPH_RULES` de `Cartoon.kt`,
  dans la couleur principale de la station. Les couleurs sont approximatives : modifiez-les dans
  la fonction `brandColor` de `Cartoon.kt`.
- Pour afficher le vrai logo d'une station, créez le dossier `app/src/main/res/drawable-nodpi/` et
  déposez-y une image PNG carrée (256x256 conseillé) nommée `logo_<id>.png`, où `<id>` est
  l'identifiant de la station dans `Station.kt` (3e paramètre). Exemples : `logo_france_inter.png`,
  `logo_fip.png`, `logo_nrj.png`, `logo_skyrock.png`.
- Les noms de fichiers doivent être en minuscules, avec chiffres et underscores uniquement.
- Les logos de radios sont des marques déposées : réservez-les à un usage personnel.

## Modifier les stations

Les stations par défaut sont dans `app/src/main/java/fr/webradio/app/Station.kt` (liste `DEFAULT_STATIONS`).
Les onglets sont définis par la liste `TABS` du même fichier : pour ajouter un pays, ajoutez une ligne
dans `TABS` (code ISO + nom) puis ses stations avec le même code (`s("ES", "Nom", "https://...", "id")`).
Les URL de flux peuvent changer : si une station ne fonctionne plus, remplacez son URL.

## Flux en http (non chiffré)

Les stations ajoutées à la main doivent être en `https`. Pour accepter aussi le `http` à cet endroit,
retirez la vérification `https://` dans `AddStationDialog` (`MainActivity.kt`).

## Idées d'évolution

Minuteur de sommeil, favoris, affichage du titre en cours (métadonnées ICY).
