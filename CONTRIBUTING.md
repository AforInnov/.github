# Guide de Contribution

Ce guide est la clé pour une collaboration efficace et sereine. Il a pour but d'assurer la **qualité du code, la lisibilité de l'historique Git et l'efficacité de nos déploiements**.

## Prérequis

Avant de commencer, assurez-vous que votre environnement est correctement configuré.

1. **Configurer Git** : Votre nom et votre email (celle associée à votre compte GitHub) doivent être configurés.

    ```bash
    git config --global user.name "Votre Nom"
    git config --global user.email "votre.email@example.com"
    ```

2. **Signer vos Commits** : Pour des raisons de sécurité et de traçabilité, tous les commits poussés sur GitHub **doivent être signés**. Veuillez suivre la [documentation officielle de GitHub pour configurer la signature de vos commits](https://docs.github.com/fr/authentication/managing-commit-signature-verification/signing-commits).


## Workflow de Développement

Chaque nouvelle fonctionnalité ou correction passe par ce processus.

### 1. Création de la Branche

Les branches `main`, `test` et `dev` sont **protégées**. Il est interdit d'y pousser du code directement.

Toute contribution commence par la création d'une branche à partir de la version la plus récente de `dev`. 

**Convention de Nommage des Branches :**
    
Le nom de votre branche doit être clair et suivre ce format : `type/description-courte`
- type: feature, fix, chore, docs...
- description-courte: Un résumé concis en kebab-case (mots séparés par des tirets).

**Exemple :**

```bash
# 1. Allez sur la branche 'dev' et mettez-la à jour
git checkout dev
git pull --rebase origin dev

# 2. Créez votre nouvelle branche
git checkout -b feature/nouvelle-authentification
```

### 2. Développement et Commits 

- **Commits Atomiques** : Chaque commit doit correspondre à une seule modification logique. Ne mélangez pas une nouvelle fonctionnalité, une correction de bug et du refactoring dans le même commit.

- **Conventional Commits** : Le message de chaque commit doit suivre la spécification [Conventional Commits](https://www.conventionalcommits.org/). C'est **obligatoire** car ça nous permet de générer automatiquement le `CHANGELOG` et de versionner automatiquement l'application.

    **Format** : `type(scope): description`

    ```bash
      # Exemple de nouvelle fonctionnalité
      git commit -m "feat(auth): add password reset endpoint" 
      # Exemple de correction de bug
      git commit -m "fix(auth): fix password reset bug"
      # Exemple de refactoring
      git commit -m "refactor(auth): refactor password reset code"
      # Exemple de documentation
      git commit -m "docs(auth): add password reset documentation"
      # Exemple de test
      git commit -m "test(auth): add password reset test"
    ```

### 3. Synchronisation et Préparation de la Pull Request

- **Toujours `pull --rebase`** pour récupérer les modifications des branches distantes.

```bash
git pull --rebase
``` 

- **Avant de créer une Pull Request**, assurez-vous que votre branche est à jour avec la branche cible (`dev`). Cela évite les conflits et assure que votre code est à jour avec les derniers changements.

```bash 
# 1. Récupérer les dernières modifications de l'origine
git fetch origin 

# 2. Rebaser votre branche sur dev
git rebase origin/dev
```

S'il y a des conflits, résolvez-les et poursuivez le rebase avec `git rebase --continue`.

### 4. La Pull Request (PR)

Une fois votre fonctionnalité terminée et votre branche à jour :

1. **Poussez votre branche** sur le dépôt distant :

```bash
git push origin feature/ma-nouvelle-fonctionnalite
```

> **⚠️ Attention au `push --force`**
> 
> Le `push --force` est strictement interdit sur les branches partagées (`main`, `test`, `dev`). Vous pouvez l'utiliser avec précaution sur *votre propre branche* si vous êtes le seul à travailler dessus (par exemple, pour nettoyer votre historique local avant de créer la PR).

2. **Créez une Pull Request** sur GitHub vers la branche `dev`.

3. **Titre de la PR** : Le titre doit inclure le [numéro du ticket Jira](https://support.atlassian.com/jira-cloud-administration/docs/use-the-github-for-jira-app/) si applicable. Format suggéré : `[TICKET-123] Description de la fonctionnalité`

4. **Rédigez une description claire** : Décrivez ce que vous avez fait, pourquoi et comment le tester.

5. **Validation** : Une PR ne peut être mergée que si deux conditions sont remplies :

    - ✅ **Tests Automatisés (CI)** : Tous les tests (unitaires, intégration...) doivent réussir.
    - ✅ **Validation (Code Review)** : Au moins un autre développeur doit relire votre code et l'approuver (à l'exception de la branche `dev`).

## Déploiement Automatique

Le merge de code dans les branches principales déclenche des déploiements sur les environnements associés.

- **Branche `dev`** ➡️ Déploiement sur l'environnement de **Développement**.
- **Branche `test`** ➡️ Déploiement sur l'environnement de **Test** (ou QA).
- **Branche `main`** ➡️ Déploiement sur l'environnement de **Staging** (pré-production).

Le déploiement en **Production** est une action **manuelle** à partir de la branche `main`.

## Guides

### Configurer les Commits Signés

Si vous n'avez pas de clé GPG existante, vous pouvez [générer une nouvelle clé GPG](https://docs.github.com/fr/authentication/managing-commit-signature-verification/generating-a-new-gpg-key) à utiliser pour signer les commits. Pour [signer les commits localement](https://docs.github.com/fr/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key), vous devez informer Git qu'il y a une clé GPG, SSH ou X.509 que vous souhaitez utiliser. Vous pouvez suivre [ce guide pour configurer les commits signés dans VS Code](https://dev.to/devmount/signed-git-commits-in-vs-code-36do).

### Gestion des Hotfixes 🔥

Pour les bugs critiques en production, le workflow standard est trop lent. Voici la procédure d'urgence à suivre :

1. **Créez une branche** `hotfix` directement depuis main (ou le tag de la version en production).

  ```bash
  # Exemple
  git checkout -b hotfix/fix-login-critique main
  ```

2. **Travaillez sur la correction** et faites vos commits comme d'habitude.

3. **Créez une Pull Request** de votre branche `hotfix/*` vers `main`. Le processus de validation (CI, revue) reste obligatoire mais doit être traité en priorité absolue.

4. **Déployez en production** une fois la PR fusionnée dans main.

5. **Rétro-portez la correction** dans les autres branches (`dev` et `test`). C'est une étape critique pour éviter les régressions et les conflits lors des prochains merges.
