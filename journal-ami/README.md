# 📖💬 Mon Journal Ami

Un journal intime Android avec un(e) ami(e) intégré(e) :

- **Tu lui donnes un nom** et tu le/la personnalises (visage, accessoire, couleur, personnalité : douce, drôle, calme, motivante).
- **Il/elle te rappelle d'écrire** chaque jour (notification à l'heure de ton choix, pas de rappel si tu as déjà écrit, rappel plus doux après une page lourde).
- **Il/elle lit tes pages** (sur le téléphone, sans internet) et réagit à leur contenu.
- **Quand ça devient lourd**, il/elle vient te parler : une page très sombre, ou plusieurs jours de suite plus sombres. Conversation guidée : se confier, respirer, penser à quelqu'un de confiance.
- **Mode soin** : si une page évoque l'envie de mourir ou de se faire du mal, l'ami(e) demande si tu es en sécurité et oriente vers une vraie personne et des ressources d'aide.
- **12 thèmes** pour petits et grands, filles et garçons (Licorne, Papillons, Dino, Fusée, Lavande, Rose & Or, Nuit océan, Carbone, Papier crème, Nuit étoilée, Soleil, Arc-en-ciel). Les petits ont un décor plus riche et des textes plus gros.
- Humeur du jour, calendrier des humeurs, série de jours, recherche, code secret à 4 chiffres, blocage des captures d'écran, sauvegarde / restauration.

**Vie privée** : l'app n'a pas la permission INTERNET. Tout reste sur le téléphone.

## Obtenir l'APK avec GitHub Actions

1. Crée un dépôt GitHub (privé ou public) et envoie-y **tout le contenu de ce dossier** (y compris le dossier caché `.github`).
2. Va dans l'onglet **Actions** : le workflow **Build APK** démarre tout seul à chaque envoi (ou clique sur *Run workflow*).
3. Quand il est vert (3 à 6 minutes), ouvre l'exécution et télécharge l'artefact **journal-ami-apk** (un zip qui contient `app-debug.apk`).
4. Copie l'APK sur le téléphone et ouvre-le. Android demandera d'autoriser l'installation depuis cette source.

L'APK « debug » s'installe directement. Pour publier sur le Play Store, il faudra un APK/AAB signé avec ta propre clé.

## Compiler soi-même

Android Studio : *File → Open* sur ce dossier, puis *Build → Build APK(s)*.
En ligne de commande (JDK 17 + SDK Android 34 + Gradle 8.7) : `gradle :app:assembleDebug`.

## Comment fonctionne « l'IA » de l'ami(e)

C'est **volontairement un système simple, local et transparent**, pas un grand modèle de langage :

- `engine.js` repère des mots et expressions (tristesse, peur, fatigue, solitude, honte…), gère les négations (« pas triste »), l'intensité (« très », « trop »), et combine avec l'humeur choisie. Le résultat est un « poids » de 0 à 1 par page.
- `decide()` regarde la dernière page et la tendance des dernières : au-dessus de certains seuils, l'ami(e) prend la parole (au plus une fois toutes les 6 heures, sauf page très lourde).
- `voice.js` contient les phrases (version petits / grands, accords selon le genre, 4 personnalités).
- `chat.js` gère les parcours de conversation.

Limites à connaître : il peut se tromper (ironie, mots rares, autres langues que le français), et ce n'est ni une vraie personne ni un professionnel de santé. Les seuils se règlent dans `engine.js` (`decide`, `analyze`).

Pour brancher plus tard un vrai modèle de langage, il faudra ajouter la permission INTERNET et appeler une API depuis `chat.js` (`onFreeText`) ; attention alors à la confidentialité d'un journal intime.

## Structure

```
.github/workflows/build-apk.yml   compilation automatique
app/src/main/java/...             Kotlin : coquille WebView, rappels, redémarrage du téléphone
app/src/main/assets/www/          toute l'application (HTML / CSS / JS)
  js/engine.js   analyse du texte + décision de prendre la parole
  js/voice.js    les textes de l'ami(e)
  js/themes.js   les 12 thèmes
  js/core.js     journal, humeur, réglages, code secret, sauvegarde
  js/chat.js     discussion, mode soin, respiration
```

Changer le nom de l'app : `app/src/main/res/values/strings.xml`. Changer l'identifiant : `applicationId` dans `app/build.gradle`.
