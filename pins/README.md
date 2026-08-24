# pins/ — welche root-Versionen die Consumer-Apps gerade benutzen

Diese Dateien sind **Maschinendaten**, nicht Dokumentation. Sie sind die Schutzinformation
fuer `.github/workflows/housekeeping.yml` (Task `prunePackages`): der Aufraeum-Lauf loescht
nur Paketversionen, von denen er hier **beweisen** kann, dass sie niemand mehr braucht.

## Warum sie hier liegen und nicht dort, wo sie entstehen

Die Wahrheit steht in der `pom.xml` der vier Consumer-Apps — und die Repos
`plaintext-app`, `plaintext-iot`, `plaintext-schuetu`, `plaintext-guild` sind **privat**.
Das `GITHUB_TOKEN` dieses oeffentlichen Repos kommt nicht heran; ein PAT dafuer wollen wir
nicht pflegen. Also dreht die Information die Richtung um: **nicht der Aufraeumer holt sie
aus dem Privaten, sondern jede App schiebt sie ins Oeffentliche.**

Geschrieben wird von `.github/workflows/publish-root-pin.yaml` im jeweiligen Consumer-Repo,
und zwar bei jedem Push auf dessen `master`, der die `pom.xml` beruehrt — also genau dann,
wenn ein Auto-Bump-PR gemergt wurde. Dazu kommt ein woechentlicher Heartbeat, der `updated`
auch dann auffrischt, wenn sich nichts geaendert hat. Ein Pin, der zu lange nicht mehr
angefasst wurde, ist damit ein *Defekt* und kein Normalzustand — der Aufraeumer kann daran
erkennen, dass er seiner eigenen Datenlage nicht mehr trauen darf.

Das Token dafuer existiert bereits: `AUTOBUMP_TOKEN` gehoert dem Write-Collaborator
`Plaintext-User`, der auch auf dieses Repo pushen darf. Es musste nichts eingerichtet werden.

## Format

```
repo=plaintext-app
versions=1.614.0
updated=2026-08-24T07:00:00Z
commit=a1b2c3d
```

* `versions` — alle Versionen aus der `pom.xml`, die auf ein Artefakt **dieses** Repos zeigen:
  die `<parent>`-Version plus jede `<plaintext-root*.version>`-Property, sofern sie eine
  echte Versionsnummer traegt und keine `${...}`-Referenz. Mehrere durch Leerzeichen
  getrennt (plaintext-guild hat den Interfaces-Pin schon einmal bewusst entkoppelt gehabt —
  das Format muss das koennen).
* `updated` — Zeitpunkt des letzten Schreibens (auch beim Heartbeat ohne Aenderung).
* `commit` — der Consumer-Commit, aus dem die Versionen gelesen wurden. Nur zum Nachschauen.

## Von Hand aendern?

Nein. Der naechste Lauf des Consumers ueberschreibt es. Wenn hier etwas falsch steht, steht
es in der `pom.xml` des Consumers falsch.
