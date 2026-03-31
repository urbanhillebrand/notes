# git

## Basics

```
git status
git status -s (short -> ??=untracked, A=staged, M=modified)
git log
git log --oneline
git show <sha1>
```

## Einfacher Workflow

```
git init
git add <filenames oder *>
git commit -m "commit-nachricht"
```

## Einfacher 2-stage-Workflow Urban

```
git init
git checkout -b develop
```

-> entwickeln...

```
git add *
git commit -m "msg"
git checkout master
git merge --no-ff develop
git branch -d develop
git tag -a -m "Version 1.0.0" 1.0.0
git push origin --tags
git push
```

## git Repo auf neuen Server moven

* Beinhaltet alle Branches u. Tags
* Das neue Repo muss auf dem Zielserver vorher angelegt werden!

```
git clone --mirror <altesrepo>
git push --mirror <newrepo>
```

## remote-URL ändern

```
git remote set-url origin git@old-server:old/path git@new-server:new/path.git

# oder manuell:
git remote -v
git remote rm origin
git remote add origin new-server:git@new-server:new/path.git
git push --set-upstream origin master
```

## Mögliche weitere Branches bei größeren Projekten

| Name | z.B. | ausgehend von | merge nach | Anmerkungen |
| --- | --- | --- | --- | --- |
| Hotfix | hotfix-1.2.1 | master | master, develop | produktionskritische Fixes |
| Feature | mycoolfeature | develop | develop |  |
| Release | release-1.3 | develop | master, develop | feature freeze, letzte Tests |

## Undoing

* **commit zu früh (fehlende Files)**
```
git commit -m "initial commit"
git add forgotten_file
git commit --amend
```

* **unstaging**\
`git reset HEAD filename`
* **auf Status des letzten Commits zurücksetzen**\
`git checkout -- filename` (oder `git checkout HEAD filename`)
* **auf älteren Status zurücksetzen**
```
git log --oneline
7e0c0f2 (HEAD -> master, origin/master, origin/HEAD) Vers. 1.5.1: Bugfix f. Verwendung mit Ubuntu 16.04
d2e4579 changelog aktualisiert
213065a Versionsnummer auf 1.5.0 erhöht
8c22660 Timestamp im Verz.namen von tar-incremental-Backups geändert (kein Doppelpunkt mehr -> Problem mit aktuelleren Linux-Kerneln)
[...]
-->

git checkout 8c22660 filename
git add filename
git commit -m "..."
```

## Branches

* **anzeigen**\
`git branch`
* **erstellen**\
`git branch <branch>`
* **nur wechseln**\
`git checkout <branch>`
* **erstellen & wechseln**\
`git checkout -b <branch>`
* **Änderungen zusammenfügen**\
`git checkout master && git merge <branch>`
* **löschen (geht nur, wenn der branch gemerged wurde -> sonst -D)**\
`git branch -d <branch>`
* **hochladen**\
`git push origin <branch>`
* **Branches anzeigen, die merged wurden**\
`git branch --merged`
* **umgekehrt**\
`git branch --no-merged`
* **nur einen Branch auschecken**\
`git clone -b <branch>`
* **Branches zusammenführen**\
`git merge <branch>` (danach `git add`)

Statt merge kann man auch rebase verwenden (-> es werden die commits des einzupflegenden Branches nicht im log angezeigt)
 
== Merge conflict

z.B. wenn 2 unterschiedliche branches Änderungen im gleichen File machen (z.B. develop & hotfix) -> beide Varianten werden im konfliktbehafteten File angezeigt

* editieren und bereinigen, danach `git add`

oder

* `git mergetool`
 
== Tagging

* annotated tags haben Kommentar & Infos über den Editierer, lightweight tags nicht
  * **auflisten**\
  `git tag`
  * **nur Untermenge**\
  `git tag -l 'v1.4.*'`
  * **annotated tag anlegen (oder mit "-s" statt "-a" -> GPG signiert)**\
  `git tag -a -m "tag-msg" 1.0.0`

**Nachträglich taggen**

* mit `git log --pretty=oneline` commit herausfinden
* `git tag -a v.1.3 -m "version 1.3" 9fab736`

**Alle Tags (auch remote) löschen**

* `git tag -l | xargs git tag -d # alle lokalen Tags löschen`
* `git fetch`
* `git tag -l | xargs -n 1 git push --delete origin`
* `git tag -l | xargs git tag -d # aufräumen`
`
Tags werden nicht automatisch auf remote server gepusht -> entweder mit `git push origin --tags` alle übergeben, oder einen einzelnen mit `git push origin [tagname]`

## Unterschiede anzeigen

* **zwischen Branches**\
`git diff <srcbranch> <dstbranch>`
* **zwischen Files und staged**\
`git diff`
* **zwischen staged und HEAD**\
`git diff --staged`

## Logging
* **Einfach**\
`git log`
* **Nur die diffs der Änderungen**\
`git log -p`
* **Nur für die letzten beiden commits**\
`git log -p -2`
* **Nur 1 Zeile pro commit**\
`git log --pretty=oneline`

## Weitere

* **Repo klonen (URL mit git: oder http, oder einfach Filepfad**\
`git clone </path/to/repo>`
* **via ssh**\
`git clone user@host:/path/to/repo`
* **mit anderem lokalen Namen für das Repo**\
`git clone <repo> alternativer_name`
* **lok. Änderungen an anderen Branch senden**\
`git push origin master`
* **Nachträglich lokales Repo mit remote Repo verbinden**\
`git remote add origin <server>`
* **Letzte Änderungen von remote-Repo holen**\
`git pull`
* **Staging area überspringen (geht nur wenn veränderte Dateien bereits getracked werden)**\
`git commit -a`
* **Dateien entfernen**\
`git rm`
* **Dateien aus staging area entfernen**\
`git rm --cached`
* **Dateien umbenennen**\
`mv alt neu && git rm alt && git add neu`
* **kürzer**\
`git mv alt neu`

## Remote Repos

```
anzeigen mit: git remote -v

Remote-Repository via ssh:
leeres Repo (mit extension .git) am Server mit "git init --bare" erstellen
am Client: git clone ssh://sldmst02.i.c/opt/git/test2
einmalig leeres repo am server von einem Client initialisieren: git push origin master
(ein mit --bare erstelltes leeres Repo hat noch keinen Branch!)

danach können Änderungen von einem lokalen Client einfach mit "git push" hochgeladen werden
Repository neu vom Server abgleichen: git pull ← macht ein git fetch, und einen merge in den aktuellen Branch
(schöner: git fetch && git merge -> man kann vorher prüfen was passiert, z.B. mit git diff)
Per Default wird hier immer nur "master" abgeglichen; man kann manuell aber auch zu einem anderen Branch pushen (z.B. git push origin develop)
Änderungen in anderen Branches kommen nicht automatisch auf den Server - also wie üblich merge & tag & push
Anzeigen der remote repo URL: git remote -v
Anzeigen mehrerer Remote-Details: git remote show origin
Nachträglich ein lokales Repo mit einem remote Repo verbinden: git remote add origin <server> ← ????

Tracking Branches:
Ein Tracking Branch ist ein "normaler", lokaler Branch, welcher mit einem remote-Branch verknüpft ist. Somit funktioniert z.B. "git push" oder "git pull" out of the box.
"git branch -vv" zeigt Link zw. lokalen und remote tracking branches
üblicherweise werden die Branches gleich benannt, das ist aber kein Muss (alles andere ist aber sehr verwirrend!)
Lokalen Branch mit remote tracking branch verbinden:
- git fetch (holt alle remote tracking branches, ändert aber nichts lokal)
- git checkout -b <lokaler_branch> -t origin/<remote_branch>

Remote Branches:
git clone: erstellt einen lokalen Branch "master", nennt das remote-repo "origin", und macht ein "git pull"
alle Remote Branches anzeigen: git remote show origin, oder besser git branch -r, oder git branch -a (lokale + remote)
alle Remote-Branches holen (nur die Pointer, nicht die Daten): git fetch origin
danach "git checkout <branch>" (<-- ist automat. ein tracking branch)
einen Remote-Branch in einen neuen lokalen Branch mergen: git checkout -b hotfix77 origin/hotfix77 (Namen müssten gar nicht gleich sein)
Kürzel dafür (geht davon aus dass Name gleich ist): git checkout --track origin/hotfix77 (--> erstellt neuen Branch hotfix77, und holt den gleichnamigen Branch vom Server)
Kürzel: der Tracking-Branch am Server kann auch als @{u} abgekürzt werden:
statt: git merge origin/master
geht auch: git merge ${u}
Tracking Branches anzeigen: git branch -vv
lokalen Branch auf Server pushen: git push origin <branch>
remote branch auf Server löschen: git push origin --delete <branch>
lokale tracking-branches löschen, die es auf dem Server nicht mehr gibt: git remote prune origin

Rollback:
https://github.com/blog/2019-how-to-undo-almost-anything-with-git
git commit --amend # macht letzten commit rückgängig
git reset HEAD <filename> # unstaging of file(s)
git checkout — <filename> # zur.setzen auf status vom letzten commit (i.e. aktuelle änderungen verwerfen)
git revert <SHA> # commit ungeschehen machen
git reset <last good SHA> # rollback zu genanntem commit, working dir bleibt aber unberührt
git reset --hard <last good SHA> # wie oben, aber working directory wird zurückgesetzt
git reflog # recover von "reset --hard"

Irrtümliche Änderungen in master in branch verlegen:
git branch <feature>, git reset --hard origin/master, git checkout <feature>
```
