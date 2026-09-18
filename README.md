# TP Git — tpgit

Rapport de travaux pratiques : prise en main de Git et GitHub (versioning, branches, fusions, gestion de conflits et travail collaboratif).

## Description

Ce projet est un dépôt d'exercice (`tpgit`) créé dans le cadre du TP "Introduction to the GIT" (UM6P). Il sert de terrain d'entraînement pour manipuler Git de bout en bout : initialisation d'un dépôt, gestion de fichiers de listes (`fruits.txt`, `vegetables.txt`, `sauces.txt`, `spices.txt`, `herbs.txt`), création et fusion de branches, résolution de conflits (y compris un conflit d'encodage), connexion à un dépôt distant sur GitHub, et enfin travail collaboratif à plusieurs.

Dépôt GitHub : [github.com/momo226-code/tpgit-](https://github.com/momo226-code/tpgit-)

## Auteurs

- **Mohamed Idriss NANA**
- **Taha Thiam**

## Objectifs

- Apprendre à utiliser un logiciel de gestion de versions (Git).
- Maîtriser le versioning d'un projet logiciel.
- Partager un projet et travailler en équipe via GitHub.

---

## 1. Installation et configuration de Git

Avant toute chose, Git a été installé depuis [git-scm.com](https://git-scm.com/downloads), puis vérifié avec :

```powershell
git --version
```

L'identité Git (nom + email) a ensuite été configurée, car Git l'attache à chaque commit :

```powershell
git config --global user.name "Votre Nom"
git config --global user.email "votre.email@exemple.com"
```

## 2. Initialisation du dépôt local

Un dossier `tpgit` a été créé et transformé en dépôt Git avec `git init`. Un premier fichier `fruits.txt` a été créé, puis suivi via le cycle classique **status → add → commit → status → log**.

![Premier commit sur fruits.txt](screenshots/01-premier-commit-apple.png)
*Premier commit : ajout de "Apple" dans `fruits.txt`, avec vérification du statut avant/après.*

![Ajout d'un second fruit](screenshots/02-ajout-orange.png)
*Ajout de "Orange" : nouveau cycle add → commit.*

Trois commits supplémentaires ont suivi le même schéma (Banana, Lemon, Ananas), chacun visible dans l'historique détaillé :

![Historique détaillé des commits](screenshots/03-git-log-detaille.png)
*`git log` : chaque commit affiche son hash, son auteur, sa date et son message.*

## 3. Branches

### Branche `vegetables`

Une branche `vegetables` a été créée et basculée avec `git branch` / `git checkout`, puis un fichier `vegetables.txt` a été ajouté et alimenté par 3 commits séparés (Tomato, Concomber, Carotte).

![Création de la branche vegetables](screenshots/04-creation-branche-vegetables.png)
*Création et bascule sur la branche `vegetables`, confirmée par `git branch`.*

![Historique de la branche vegetables](screenshots/05-log-graph-vegetables.png)
*`git log --graph --oneline --decorate --all` : la branche `vegetables` a divergé de `master` après le commit "Ananas".*

### Branche `sauces`

Même principe pour la branche `sauces`, avec un fichier `sauces.txt` alimenté par les commits "SauceArrachide" et "Djoumblé".

![Branche sauces](screenshots/06-branche-sauces.png)
*Création de la branche `sauces` et ses deux premiers commits, avec le graphe complet montrant la divergence des branches `sauces` et `vegetables` depuis `master`.*

### Branches `spices` et `herbs` (travail à deux)

Chaque membre du binôme a travaillé sur sa propre branche (`spices` pour Mohamed Idriss, `herbs` pour Taha), avec ses propres fichiers et commits, avant fusion dans `main`.

![Historique des branches spices et herbs](screenshots/08-log-detaille-herbs-spices.png)
*`git log` détaillé montrant les commits des deux contributeurs sur leurs branches respectives.*

![Visualisation gitk des branches](screenshots/10-gitk-branches-herbs-spices.png)
*Visualisation graphique (gitk) de l'avancement parallèle des branches `spices` et `herbs` avant leur fusion dans `main`.*

## 4. Fusions (merges)

Une fois le travail terminé sur chaque branche, les fusions ont été effectuées vers `master`/`main`, en suivant le principe : **on se place sur la branche destination, puis on `merge` la branche source**.

```powershell
git checkout main
git pull
git merge <branche>
git push origin main
```

![Fusion réussie et historique final](screenshots/09-historique-complet-final.png)
*Graphe final montrant les branches `vegetables` et `sauces` fusionnées dans `main` via un commit de fusion.*

## 5. Résolution de conflits

Deux types de conflits ont été rencontrés et résolus au cours du TP.

### a) Conflit de contenu classique (`vegetables.txt`)

En supprimant "Carotte" localement pendant qu'une autre modification existait côté distant, un conflit est apparu au moment du `git pull` :

![Conflit sur vegetables.txt](screenshots/14-conflit-vegetables.png)
*`CONFLICT (content): Merge conflict in vegetables.txt` après un `git pull`.*

### b) Conflit lié à l'encodage (`sauces.txt`)

Un cas plus particulier est survenu sur `sauces.txt` : Git indiquait `Cannot merge binary files`, alors qu'il s'agissait bien d'un fichier texte. La cause identifiée était l'**encodage UTF-16** (avec BOM) généré par défaut par PowerShell lors de l'écriture avec `echo >>`, que Git interprète comme du binaire et ne sait pas fusionner ligne à ligne.

![Conflit et divergence sur sauces.txt](screenshots/17-conflit-sauces-diverge.png)
*Tentative de `git pull` provoquant un conflit binaire, suivie d'un `git merge --abort` (annulation propre de la tentative, sans résoudre le problème de fond) et confirmation de la divergence des branches (`git status`).*

**Solution appliquée** : réécriture manuelle et complète du fichier en UTF-8, puis résolution explicite du conflit :

```powershell
Remove-Item sauces.txt
"SauceArrachide" | Set-Content sauces.txt -Encoding utf8
"Djoumblé"        | Add-Content sauces.txt -Encoding utf8
"SauceTomate"     | Add-Content sauces.txt -Encoding utf8

git add sauces.txt
git commit -m "Resolve conflict in sauces.txt and fix encoding to UTF-8"
git pull
```

![Résolution finale du conflit d'encodage](screenshots/18-resolution-finale-encodage.png)
*Réécriture du fichier en UTF-8, `git add` + `git commit` pour clore le conflit, puis `git pull` confirmant que tout est synchronisé (`Already up to date`).*

> **Point clé retenu** : réécrire un fichier proprement sur le disque ne suffit pas — tant que `git add` n'est pas fait, Git considère toujours le conflit comme non résolu.

## 6. Gestion des fichiers (ajouts / suppressions)

Plusieurs opérations classiques ont été pratiquées sur `fruits.txt` : ajout de nouveaux éléments (Grenadille, Fraise, Papaye) et suppression ciblée d'une ligne précise sans éditeur, directement en PowerShell.

![Suppression d'un élément dans une liste](screenshots/11-suppression-fruit.png)
*Suppression de "Banana" puis de "Ananas" avec `Where-Object { $_ -ne "..." }`, sans passer par un éditeur de texte.*

![Ajout de nouveaux fruits](screenshots/12-ajout-grenadille.png)
*Ajout de "Grenadille" à la liste, suivi d'un commit dédié.*

## 7. Dépôt distant (GitHub)

Le dépôt local a été relié à un dépôt distant créé sur GitHub, puis synchronisé via `push`/`pull` :

```powershell
git remote add origin https://github.com/momo226-code/tpgit-.git
git push -u origin main
```

![Push vers GitHub](screenshots/13-push-github.png)
*Envoi des commits locaux vers le dépôt distant `tpgit-` sur GitHub.*

![Page du dépôt sur GitHub](screenshots/19-page-github-repository.png)
*Le dépôt `tpgit` visible en ligne sur GitHub, avec son historique de commits.*

Une vérification finale des branches locales et distantes a permis de confirmer que `main` était bien la branche par défaut, entièrement synchronisée :

![Vérification des branches locales et distantes](screenshots/20-verification-branches-locales.png)
*`git branch` : branches locales `main`, `sauces`, `spices`, `vegetables`, avec `origin/main` comme référence distante.*

## 8. Travail collaboratif

Les deux membres du binôme (Mohamed Idriss NANA et Taha Thiam) ont été ajoutés comme collaborateurs sur le dépôt GitHub (Settings → Collaborators → Add people), permettant à chacun de push directement sur le projet partagé.

![Historique avec les deux contributeurs](screenshots/16-gitk-auteurs-main.png)
*Visualisation gitk de la branche `main` montrant les commits des deux auteurs, avec horodatage et identifiants respectifs.*

Chaque contributeur a travaillé sur sa propre branche, avant de fusionner son travail dans `main` — reproduisant un flux de travail d'équipe réaliste : isolement du travail en cours, puis intégration une fois celui-ci finalisé et testé.

---

## Difficultés rencontrées

| Problème | Cause | Solution |
|---|---|---|
| `fatal: unable to auto-detect email address` | Identité Git non configurée | `git config --global user.name/user.email` |
| `cannot do a partial commit during a merge` | Tentative de commit d'un seul fichier pendant un merge en cours | `git add .` (tout stager) avant de commit |
| `Cannot merge binary files: sauces.txt` | Fichier écrit en UTF-16 (via `echo >>` sous PowerShell), interprété comme binaire par Git | Réécriture du fichier en UTF-8 avec `Set-Content -Encoding utf8` |
| Conflit répété après `git merge --abort` | `--abort` annule la tentative de fusion, mais ne règle jamais le désaccord de fond | Refaire le `pull`/`merge` et résoudre réellement le conflit (édition + `add` + `commit`) |

## Conclusion

Ce TP a permis de manipuler concrètement l'ensemble du cycle de vie Git : du suivi local d'un simple fichier texte jusqu'à la collaboration à plusieurs sur un dépôt distant, en passant par la création de branches, la fusion et la résolution de conflits — y compris un conflit inhabituel lié à l'encodage des fichiers sous Windows/PowerShell. Ces manipulations reproduisent fidèlement le flux de travail utilisé au quotidien dans un projet de développement réel en équipe.

## Ressources

- [Documentation officielle Git](https://git-scm.com/book/en/v2)
- [Learn Git Branching](https://learngitbranching.js.org/)
- [Try GitHub](https://try.github.io/)
