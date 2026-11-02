# JARVIS iPhone XR — architecture cible

## Objectif et état initial

Adapter progressivement JARVIS, initialement conçu pour macOS, à un client iPhone XR. Le dépôt initial utilise Python/FastAPI, TypeScript/Vite/Three.js, WebSocket, un processus Claude Code, Fish Audio, AppleScript, SQLite et Markdown. Aucun projet Xcode, code Swift ou cible iOS native n'a été identifié dans l'arborescence examinée.

AppleScript, Terminal, Chrome desktop et le processus Claude Code local ne sont pas portables tels quels sur iOS.

## Architecture hybride

- **iPhone** : interface, microphone, restitution vocale, réglages, permissions iOS, cache local et fonctions hors ligne.
- **Serveur JARVIS** : orchestration des modèles distants, mémoire synchronisée et tâches lourdes.
- **Modèles locaux** : piste expérimentale, activée uniquement après benchmark réel sur iPhone XR.

```text
iPhone XR (SwiftUI, Speech, AVAudioSession, Keychain)
          | HTTPS + WebSocket TLS
          v
API JARVIS (authentification, orchestration, mémoire,
permissions, file d'attente des actions à approuver)
          |
          v
Fournisseurs IA / services tiers
(Gemini, Mistral, OpenRouter, modèles locaux testés)
```

## Règles de sécurité non négociables

1. **Deny by default** : toute application ou capacité non explicitement connectée est indisponible.
2. **Approbation par action** : connecter une application n'autorise pas toutes ses actions futures.
3. Toute action externe, écriture, envoi, publication, suppression, achat ou commande crée une demande d'approbation affichant application, action et données concernées.
4. Le serveur vérifie l'approbation immédiatement avant l'exécution. Une protection uniquement dans l'interface ne suffit pas.
5. Approbation à usage unique, liée aux paramètres exacts et à une expiration courte. Toute modification des paramètres invalide l'approbation.
6. Révocation immédiate des connexions et permissions.
7. Ne jamais utiliser `--dangerously-skip-permissions` dans un parcours mobile ou exposé à distance.
8. Secrets conservés côté serveur ou dans le Trousseau iOS, jamais dans le code, les journaux, le stockage web ou les réponses API.
9. Pages web, fichiers, messages et résultats d'outils sont des données non fiables, jamais des instructions de sécurité.
10. Journaliser demande, décision, exécution et résultat sans secrets.

## Cycle d'une action

```text
Proposition du modèle
  -> validation serveur de l'outil et des paramètres
  -> demande en attente
  -> affichage sur iPhone
  -> acceptation ou refus explicite
  -> validation serveur du jeton à usage unique
  -> exécution strictement limitée
  -> journalisation du résultat
```

Refus, expiration, déconnexion ou erreur d'authentification = aucune exécution.

## Capacités iOS réalistes

- Interface native SwiftUI.
- Reconnaissance vocale via frameworks Apple, selon permissions et langues disponibles.
- Synthèse vocale locale via AVSpeechSynthesizer.
- Notifications après autorisation iOS.
- Raccourcis Siri, App Intents, liens profonds et feuille de partage pour intégrations compatibles.
- Accès aux photos, fichiers, contacts ou calendrier via API et autorisations Apple.
- Pas de contrôle arbitraire d'autres applications, pas d'AppleScript iOS, pas de lecture universelle de leurs données.
- Pas de serveur permanent garanti en arrière-plan sur iOS.

## Mode hors ligne

Afficher l'historique synchronisé, consulter les réglages, préparer des brouillons et utiliser les fonctions locales disponibles. Les requêtes distantes restent en attente et ne sont jamais exécutées automatiquement à la reconnexion. Toute action est réévaluée et approuvée au moment de son exécution.

## Optimisation iPhone XR

Limiter animations coûteuses, cache, traitements audio simultanés et tâches en arrière-plan. Ne pas annoncer de modèle local compatible avant un benchmark sur appareil réel.

## Phases

1. Documenter architecture et frontières de confiance.
2. Auditer les points d'exécution backend et supprimer les contournements dangereux.
3. Définir API mobile authentifiée avec approbation serveur et tests.
4. Créer le client iOS natif et ses tests.
5. Ajouter les intégrations une par une, désactivées par défaut.
6. Mesurer les performances sur iPhone XR.

## Critères d'acceptation

- Une action non approuvée ne s'exécute jamais.
- Un jeton d'approbation ne s'utilise qu'une fois.
- Une approbation expirée ou modifiée est rejetée.
- Une permission révoquée bloque immédiatement les nouvelles actions.
- Une perte réseau ne déclenche jamais une exécution différée automatique.
- Les secrets ne sont jamais exposés au client ou aux journaux.
