# Client mobile JARVIS

Ce dossier est réservé au futur client iOS natif. Le client n'est pas encore implémenté : aucun projet Xcode n'est ajouté à ce stade.

## Cible technique

- SwiftUI pour l'interface.
- URLSession pour HTTPS et WebSocket sécurisé.
- Keychain pour les jetons de session.
- Speech framework pour la reconnaissance vocale, avec permissions iOS.
- AVSpeechSynthesizer pour la synthèse vocale locale.
- App Intents, Raccourcis et Share Extension pour les intégrations prises en charge.
- Tests unitaires du cycle d'approbation et tests UI.

## Sécurité

Le client mobile ne décide jamais seul qu'une action est autorisée. Le backend vérifie une approbation explicite, à usage unique et liée aux paramètres exacts de l'action, avant toute exécution.

Voir [l'architecture iOS](../docs/IOS-ARCHITECTURE.md).
