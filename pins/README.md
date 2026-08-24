# pins/ — die Daten liegen auf dem Branch `pins`

Hier auf `master` steht nur dieser Zeiger. Die eigentlichen Dateien
(`pins/plaintext-app.env` und die drei Geschwister) liegen auf dem Branch
[`pins`](../../tree/pins/pins) und werden von den Consumer-Apps selbst geschrieben.

Sie sind die Schutzinformation fuer `.github/workflows/housekeeping.yml` (Task
`prunePackages`): der Aufraeum-Lauf loescht nur Paketversionen, von denen er daraus
**beweisen** kann, dass sie niemand mehr braucht. Er liest sie ueber die Contents-API mit
`?ref=pins`, checkt diesen Branch also gar nicht aus.

**Warum ein eigener Branch:** `master` ist geschuetzt ("Changes must be made through a
pull request"). Der Write-Collaborator, mit dessen Token die Apps schreiben, kann hier
nicht direkt committen — nur Administratoren kommen daran vorbei. Die vier Consumer mit
einem Admin-Token auszustatten, um eine Vier-Zeilen-Datei abzulegen, waere mehr Recht als
noetig. Ausserdem ist `master` das ausgelieferte Maven-Repository; mehrere Bot-Commits pro
Tag gehoeren nicht in dessen Historie.

Alles Weitere — Format, Heartbeat, was bei einem veralteten Pin passiert — steht in der
README auf dem Branch `pins` selbst.
