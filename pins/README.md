# Branch `pins` — welche root-Versionen die Consumer-Apps gerade benutzen

Dieser Branch traegt **nur** Maschinendaten. Kein Maven-Inhalt, kein Code. Er ist die
Schutzinformation fuer `.github/workflows/housekeeping.yml` auf `master` (Task
`prunePackages`): der Aufraeum-Lauf loescht nur Paketversionen, von denen er hier
**beweisen** kann, dass sie niemand mehr braucht.

## Warum die Daten hier liegen

Die Wahrheit steht in der `pom.xml` der vier Consumer-Apps — und `plaintext-app`,
`plaintext-iot`, `plaintext-schuetu`, `plaintext-guild` sind **privat**. Das
`GITHUB_TOKEN` dieses oeffentlichen Repos kommt nicht heran, ein PAT dafuer wollen wir
nicht pflegen. Also dreht die Information die Richtung um: **nicht der Aufraeumer holt sie
aus dem Privaten, sondern jede App schiebt sie ins Oeffentliche.**

Geschrieben wird von `.github/workflows/publish-root-pin.yaml` im jeweiligen Consumer-Repo,
mit dem dort ohnehin vorhandenen `AUTOBUMP_TOKEN` (Write-Collaborator `Plaintext-User`).
Es musste kein Token neu eingerichtet werden.

## Warum ein eigener Branch und nicht `master`

Zwei Gruende, und der erste ist bindend:

1. **`master` ist geschuetzt** ("Changes must be made through a pull request"). Der
   Write-Collaborator kann dort nicht direkt schreiben; nur Administratoren kommen daran
   vorbei (`enforce_admins: false`) — genau so pusht der root-Release seine Artefakte.
   Die Consumer mit einem Admin-Token auszustatten, nur um eine Vier-Zeilen-Datei
   abzulegen, waere mehr Recht als noetig und wuerde den Schutz von `master` bei jedem
   Bump umgehen.
2. `master` ist das ausgelieferte Maven-Repository und wird ueber
   `raw.githubusercontent.com` gelesen. Mehrere Bot-Commits pro Tag gehoeren nicht in
   dessen Historie.

## Format

```
repo=plaintext-app
versions=1.614.0
updated=2026-08-24T07:00:00Z
commit=a1b2c3d
```

* `versions` — alle Versionen aus der `pom.xml`, die auf ein Artefakt **dieses** Repos
  zeigen: die `<parent>`-Version plus jede `<plaintext-root*.version>`-Property, sofern sie
  eine echte Versionsnummer traegt und keine `${...}`-Referenz. Mehrere durch Leerzeichen
  getrennt — plaintext-guild hatte den Interfaces-Pin schon einmal bewusst entkoppelt, das
  Format muss das koennen.
* `updated` — Zeitpunkt des letzten Schreibens, auch beim woechentlichen Heartbeat ohne
  Aenderung. Dadurch ist ein alter Pin ein erkennbarer **Defekt** und nicht bloss
  "dort war laenger Ruhe": der Aufraeumer warnt ab 10 Tagen und bricht ab 30 Tagen ab.
* `commit` — der Consumer-Commit, aus dem gelesen wurde. Nur zum Nachschauen.

## Von Hand aendern?

Nein. Der naechste Lauf des Consumers ueberschreibt es. Steht hier etwas Falsches, steht es
in der `pom.xml` des Consumers falsch.

## Wird dieser Branch geloescht?

Dann bricht der Aufraeum-Lauf ab, ohne etwas zu loeschen (fail closed). Das ist die
gewuenschte Richtung — aber der Branch gehoert wiederhergestellt, sonst wird nie wieder
aufgeraeumt.
