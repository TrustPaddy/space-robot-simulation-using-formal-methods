# Benchmarking Deep RL for Free-Floating Space Robot Manipulators — IEEE Access

## Ordnerübersicht

| Ordner | Inhalt | Manuskript-ID | Status |
|--------|--------|---------------|--------|
| `submission_1/` | Erste Einreichung (`orig_submitted.tex/.pdf`) | Access-2026-06278 | abgelehnt (resubmit) |
| `submission_2/` | Zweite Einreichung (`submission_2.tex/.pdf`), `access-submission.zip` = hochgeladenes Paket | Access-2026-37882 | abgelehnt (resubmit) |
| `access/` | Arbeitsfassung Submission 3 — **clean** („Main Manuscript“) | – | in Bearbeitung |
| `access_with_markers/` | Submission 3 mit gelb markierten Änderungen („Highlighted PDF“) | – | in Bearbeitung |
| `reviews/round1_Access-2026-06278/` | Tracking der Gutachten Runde 1 + Response Letter | Access-2026-06278 | abgeschlossen |
| `reviews/round2_Access-2026-37882/` | Tracking der Gutachten Runde 2 (+ Response Letter, sobald erstellt) | Access-2026-37882 | offen |
| `Figures/` | Gemeinsame Abbildungen für alle Versionen | – | – |

`ieeeaccess.cls`, `IEEEtran.cls`, `logo.png`, `notaglinelogo.png` und `bullet.png` liegen gemeinsam auf oberster
Ebene. Jede Manuskriptversion liegt genau eine Ebene tiefer und findet Abbildungen und Logos über
`\graphicspath{{../}}`. Neue Versionen daher ebenfalls direkt unter diesem Ordner anlegen.

## Kompilieren

Im jeweiligen Ordner:

```
latexmk -pdf <name>.tex
```

## Arbeitsablauf Submission 3

1. Änderungen zuerst in `access/access.tex` (clean) umsetzen.
2. Dieselben Änderungen in `access_with_markers/access_with_markers.tex` gelb markieren
   (`\hlone{[R2.x] ...}` für kurze Stellen, `\begin{ronehl}\hlone{[R2.x]} ... \end{ronehl}` für Absätze,
   `[A#]` für eigene Korrekturen).
3. Status in `reviews/round2_Access-2026-37882/reviewer_comments_round2.md` pflegen.

## Nicht referenzierte Abbildungen

Folgende Dateien in `Figures/` werden weder von `access/access.tex` noch von `submission_1/orig_submitted.tex`
eingebunden (nicht gelöscht): `SPART.png`, `oos.png`, `kreisbahn_3.png`, `Collsidion_detected_cw.png`,
`collison_detected_scope.png`, `spacerobot_slx_redardfcn.png`, `rlPPOAgent_09_10_16_59.png`,
`fra-uas_logo.pdf`, `YourSignature_w.jpg`, `ethnomethodologisches Beziehungsmodell_25.02.2022_2.pdf`,
`ethnomethodologisches Einflussmodell_25.02.2022_.pdf`.
