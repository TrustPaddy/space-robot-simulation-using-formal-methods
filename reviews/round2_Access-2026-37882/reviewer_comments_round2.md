# Reviewer Comments Tracking — Runde 2

**Manuskript:** Access-2026-37882 (= Submission 2, Ordner `submission_2/`) → Resubmission als **Submission 3**
(Ordner `access/` + `access_with_markers/`)
**Entscheidung:** Reject & Resubmit (Associate Editor: Dr. Alicia Fornés), erhalten 09/2026
**Wichtig:** IEEE Access erlaubt nur eine Resubmission. Bleibt ein Reviewer-Punkt offen oder hat der AE noch
substanzielle technische Bedenken, wird endgültig abgelehnt → **jeder Punkt muss beantwortet werden**.
**Vorherige Runde:** [`../round1_Access-2026-06278/reviewer_comments.md`](../round1_Access-2026-06278/reviewer_comments.md)
**Stand:** 19.09.2026. Hauptbenchmark neu gerechnet, Methodenteil (Teil A) im Paper eingearbeitet.

## Markierung (LaTeX, `access_with_markers.tex`)

| Typ | Farbe | Inline-Befehl |
|-----|-------|---------------|
| Reviewer 2 | Gelb | `\hlone{[R2.x] ...}` |
| Eigene Befunde | Gelb | `\hlone{[A#] ...}` |

Die Kopfzeile (`\markboth`) lässt sich nicht markieren → im Response Letter explizit erwähnen.
`\cite`, `\ref`, `\autoref`, `\cref`, `\eqref`, `\texttt`, `\emph` und abgesetzte Gleichungen stehen außerhalb
von `\hlone` (Paket `soul`), der markierte Text wird davor und danach fortgesetzt.

## Status-Legende

- ✅ erledigt
- ⬜ offen, ohne neue Experimente machbar
- 🔬 braucht neue MATLAB-Experimente
- Teil A / B / C: Methodenkorrekturen (sofort) / Ergebnisse Hauptbenchmark (nach Freigabe der neuen Hauptaussage) / hängt an weiteren Kampagnen

---

## Stand der Experimente (Code: Repo `space-robot-rl`)

- Modell und Code vereinheitlicht und reproduzierbar: alle Parameter in `benchmarkConfig.m`, Modellmigration
  `parametrizeSpaceRobotModel.m`, jede Ergebnisdatei speichert Git-Commit und Modell-Prüfsumme.
- **Hauptbenchmark `results/benchmark_v2` fertig** (19.09.2026): 6 Default-Agenten + optimiertes PPO, je 5 Seeds
  (0–4), 1000 Episoden, zufällige Startpose ±1° im Training; Auswertung deterministisch auf 31 Startzuständen
  (nominal + 30 zufällig, gleich für alle Agenten).
- **Ergebnis (Mittel ± Std über 5 Seeds, 30 zufällige Startzustände):**

  | Konfiguration | K1 | K2 | K4 | Seeds mit Abbruch | T3 |
  |---|---|---|---|---|---|
  | TRPO default | −0,25 ± 0,20 | 0,0078 ± 0,0076 | 0,023 ± 0,005 | 0/5 | 10,7 min |
  | PPO optimiert | −0,20 ± 0,13 | 0,0050 ± 0,0016 | 0,025 ± 0,021 | 0/5 | 8,6 min |
  | PPO default | −44 ± 40 | 0,48 ± 0,52 | 0,11 ± 0,06 | 3/5 | 7,9 min |
  | SAC default | −25 ± 26 | 0,37 ± 0,24 | 0,09 ± 0,09 | 2/5 | 35,0 min |
  | DDPG default | −21 ± 21 | 0,13 ± 0,08 | 0,30 ± 0,13 | 2/5 | 24,1 min |
  | TD3 default | −42 ± 52 | 0,64 ± 0,83 | 0,23 ± 0,14 | 3/5 (Kollisionen) | 29,3 min |
  | PG default | −59 ± 33 | 1,10 ± 0,84 | 0,15 ± 0,08 | 4/5 | 6,5 min |

  TRPO default ist bei K1/K2 gegen jeden anderen Default-Agenten signifikant besser (Mann-Whitney, Seed-Ebene,
  p = 0,008–0,016 unkorrigiert). PPO optimiert ist nicht von TRPO default zu unterscheiden (alle p ≥ 0,42).
- **⚠️ Neue Hauptaussage nötig (Entscheidung mit Prof offen):** „PPO bietet unter den Default-Agenten den besten
  Kompromiss“ hält nicht. Vorschlag: Unter Toolbox-Standardwerten ist nur TRPO über alle Seeds robust, PPO und die
  Off-Policy-Verfahren sind stark seed-abhängig, das Tuning beseitigt die Seed-Abhängigkeit von PPO und erreicht
  TRPO-Niveau bei kürzerer Trainingszeit. Einschränkung: Default-Lernrate 0,01 benachteiligt vermutlich PPO und
  Off-Policy-Verfahren.
- Offen: Reward-Sensitivität (R2.4b), Ablation, Sensitivität optimiertes PPO, Zwei-Segment-Bahn, Stresstests
  (Sensorrauschen und äußere Störung noch als Konfiguration einbauen), Impulsmonitor (A27).

---

## Reviewer 1

| # | Kommentar | Maßnahme | Status |
|---|-----------|----------|--------|
| R1 | Keine Kommentare; alle Fragen positiv beantwortet (Beitrag, technische Qualität, Darstellung, Referenzen) | Dank im Response Letter | ⬜ |

---

## Reviewer 2

Gesamturteil: relevanter Benchmark, technisch solide *als Simulationsstudie*; Beitrag vor allem als
Benchmarking- und Integrationsstudie, kein neuer Algorithmus. Kritik richtet sich hauptsächlich gegen zu breite
Formulierungen und den Umfang der Evaluation.

| # | Kommentar | Maßnahme | Stellen | Status |
|---|-----------|----------|---------|--------|
| R2.1 | „Formal verification“ enger fassen: nur Runtime-Monitore + Offline-Torque-Check auf vereinfachtem Modell; keine formale Verifikation von Closed-Loop-Stabilität, Task Completion, Kollisionsfreiheit über alle Trajektorien oder des vollen Simscape-Modells. Titel/Abstract dürfen keine vollständige formale Sicherheitszertifizierung suggerieren | Abstract: Satz zur Reichweite der Safety-Komponente; Contributions, Research Gap, Related Work: „formal-methods-based (runtime) safety monitoring“; Tabelle I „Formal Meth. = Partial†“ + Fußnote; Safety-Abschnitt: Einleitungssatz + explizit, was *nicht* verifiziert ist; SLDV-Caption „simplified verification model only“; Robustheitsfazit und Conclusion abgeschwächt. Zusätzlich (A19, A26, A27): Beschreibung der Monitore an die tatsächliche Umsetzung angepasst | Abstract, Sec. I, II, IV, V, Robustness, Discussion/Conclusion | ✅ |
| R2.2 | Idealisierte Simulation (ideal sensing, zero latency, rigid body, keine Störungen) → Hauptaussage „simulation benchmark under controlled assumptions“, nicht operational readiness | Abstract-Satz; neuer Absatz nach den Annahmen (Sec. III) inkl. Verweis auf Stress-Test; Practical Implications und Conclusion begrenzt | Abstract, Sec. III, Discussion/Conclusion | ✅ |
| R2.3a | Evaluationsumfang klein (3 Läufe, 30 Episoden, feste Startkonfiguration) → Schlussfolgerungen ausdrücklich konditional | Satz „conclusions remain conditional …“ jetzt im neuen Evaluationsabsatz (Sec. IV). Limitations-Punkt „evaluation scope“ und Robustheitsfazit beschreiben noch das alte Protokoll (3 Läufe, feste Startkonfiguration) | Sec. IV, Robustness, Limitations | ✅ Sec. IV / ⬜ Limitations, Robustheit an neues Protokoll anpassen (Teil B) |
| R2.3b | Evaluation erweitern (Seed-Sensitivität, Generalisierung über Startzustände/Trajektorien/Parameter/Störungen) | 5 Seeds je Konfiguration, alle 6 Agenten + PPO optimiert; zufällige Startpose im Training; 31 Startzustände in der Auswertung; Statistik auf Seed-Ebene (Mann-Whitney, Holm). Methodik in Sec. IV eingebaut. Ergebnisse (Tab. 5–7, Abb. 12–14) noch einzuarbeiten | Sec. IV, VI, VII, Tab. KPI-Vergleich, Statistik, Boxplots/CI-Plots | ✅ Experiment + Methodentext / ⬜ Ergebnisse (Teil B) / 🔬 Trajektorien, Störungen (Teil C) |
| R2.4a | Reward-Gewichte handgetuned → kurze Sensitivitätsstudie | Existiert, muss aber neu gerechnet werden (A18) → danach im Response Letter darauf verweisen | Sec. Sensitivity Analysis | 🔬 (mit R2.4b) |
| R2.4b | Sensitivität nicht nur für PPO, sonst ist „ranking robust“ nicht belegt | PPO, TRPO, SAC (default) mit W_ori und W_p × 0.5 / × 2, je 3 Seeds; Ranking pro Variante über K2/K4/K6 (nicht K1, da K1 den geänderten Reward misst); zusätzlich K1 mit nominalen Gewichten aus den Logs | Tab. reward_sensitivity, Absätze „Robustness of the algorithm ranking“, „Summary“ | 🔬 |
| R2.5 | „safe RL“ nur mit Bezug auf die überwachten Eigenschaften; 0 Verletzungen in 30 Episoden sind empirisch | Kopfzeile „Safe RL for …“ → „Benchmarking Deep RL for …“; Keyword „safe simulation“ → „safety monitoring“; „safety envelope“, „safety-augmented deep RL“, „no safety violations“ → „no observed violations of the monitored properties“ | Kopfzeile, Keywords, Contributions, Sec. IV, Discussion/Conclusion | ✅ (Zahlen der Verletzungen mit Teil B prüfen) |

---

## Inkonsistenzen & Fehler (eigene Befunde, kein Reviewer-Kommentar)

Nicht von den Gutachtern genannt, aber für ein neues Review-Team angreifbar → genauso abarbeiten.

| # | Befund | Fundstelle (`access.tex`) | Maßnahme | Status |
|---|--------|---------------------------|----------|--------|
| A1 | `rng(0,'twister')` vor **jedem** Lauf, trotzdem „three independent training runs“. Bestätigt: gleicher Seed ergibt bitgleiche Läufe, die 3 Läufe waren identisch | Sec. IV, „To ensure reproducibility …“ | 5 Seeds (0–4), Text neu | ✅ |
| A2 | Nominalwerte in `tab:robustness_eval` weichen vom optimierten PPO im Rest des Papers ab. Ursache: Modell zwischen Auswertungen von Hand geändert (W_ori 200/2000, Basis 5/25 kg), verschiedene Agenten-Konfigurationen | Robustness Evaluation, Tabelle | Stresstests mit den neuen Checkpoints und derselben Auswertung neu rechnen | 🔬 (Teil C) |
| A3 | Text „K7 increases substantially“, Tabelle zeigt Abnahme. Erklärt durch A8 (Text meint Jerk, Tabelle 1 − Jerk) | Robustness Evaluation | Mit neuen Werten korrigieren | 🔬 (Teil C) |
| A4 | Reviewer-Tags im clean Manuskript: „(R2.13)“, „(R2.12)“, „raised independently by both reviewers“ | Statistik-Abschnitt, Sensitivity Analysis | Entfernt | ✅ |
| A5 | Alle 8 Sensitivitätsvarianten schlechter als Default-PPO; „ranking robust“ nur für PPO geprüft. Erklärt durch A18 | Sensitivity Analysis | Mit R2.4b neu rechnen | 🔬 (Teil C) |
| A6 | Tabelle I: „Formal Ver. = Yes“ für die eigene Arbeit überzeichnet | `tab:sample` | „Formal Meth. = Partial†“ mit Fußnote | ✅ |
| A7 | Abstract nach Ergänzungen > 250 Wörter | Abstract | Gestrafft auf 243 Wörter (bei Teil B erneut prüfen) | ✅ |
| A8 | K7 im Code = 1 − mittlerer Jerk (höher = glatter), Paper-Definition umgekehrt. Betrifft alle K7-Werte, Abb. 12c/13c, Contributions, Conclusion („48.5 % smoother“) | `computeKPIsFromLogs.m`, alle K7-Stellen | Code korrigiert (Definition im Paper war richtig); alle K7-Werte neu | ✅ Code / ⬜ Werte (Teil B/C) |
| A9 | Tab. 5 und Tab. 6/Abb. 12/13 aus verschiedenen Daten (K2 PPO 0,0257 vs. Median ≈ 0,33; Tab. 6 zeigt TRPO bei K2 signifikant besser, Text das Gegenteil) | Sec. VI | Alle Tabellen/Abbildungen aus `analyzeBenchmark` | ⬜ (Teil B) |
| A10 | Ungleiche Trainingsbedingungen: 5 Agenten mit Kreis r = 0,4, PPO mit r = 0,5; je Agent anderer StopTrainingValue | Einzelskripte | Neutraining unter identischen Bedingungen; T3-Werte in Sec. V (Computational Overhead) aktualisiert | ✅ Experiment + Sec. V / ⬜ Tab. 5 (Teil B) |
| A11 | Modell: τ_max = 2 N m, d_safe = 2 cm; Paper: 25 Nm, 5 cm | Sec. IV Action Space, Sec. V Collision Monitor | Text korrigiert | ✅ |
| A12 | Behauptet, nicht implementiert: 3 Läufe, Checkpoint-Auswahl, Randomisierung, Verifikation des Startzustands, Aufzeichnung von Tracking-Fehler je Episode; DT-Absatz überzeichnet („scenarios and constraints are sampled …, improves robustness …“) | Sec. III-A, Sec. IV Training Procedure | Implementiert (5 Seeds, zufällige Startpose ±1°, letzter Agent nach 1000 Ep.) und Text angepasst | ✅ |
| A13 | Evaluation: feste Startpose + stochastische Policy; TD3/DDPG = 30 identische Episoden | Sec. IV Evaluation and Metrics, Sec. VI Performance Metrics | Deterministische Policy, 31 Startzustände, Seed-Ebene; Text angepasst | ✅ Text / ⬜ Tabellen, Statistik (Teil B) |
| A14 | Optimiertes PPO im Paper ([256,256,128]+LayerNorm, Actor 5e-4 / Critic 1e-3) ≠ trainiert (2×128, Actor 1e-3, Critic 5e-4, γ 0,995, H 1024, B 256, 10 Epochen, Gradient-Threshold 1, Mittelwert ohne tanh) | Contributions, Sec. V Computational Overhead, Sec. VII | Text korrigiert, LR-Begründung ersetzt, „Fixed settings“ ergänzt | ✅ |
| A15 | q/dq im Modell in umgekehrter Gelenkreihenfolge → Kollisionsmonitor prüfte falsche Armstellung, K6 falsche Grenzen, K9 falsche Paare τ_i·q̇_j | `SpaceRobot.slx` | Modell korrigiert | ✅ Modell / ⬜ Screenshot Abb. 4 |
| A17 | Default-Agenten hatten LR 0,01 (MATLAB-Standard), nicht 1e-3; PG war nicht auf Standardwerten; MATLAB-Version R2026a statt R2025b | Sec. VI Training Setup | Reine Toolbox-Defaults; Text + neue Tabelle `tab:hyperparameters` | ✅ |
| A18 | Ablations-/Sensitivitätsagenten mit dritter PPO-Konfiguration (Actor-LR 5,7e-5, H 600, B 200) | Sec. VII-B/C | Neu trainieren | 🔬 (Teil C) |
| A19 | Kollision beendete Episode nicht; Assertion für Gelenkgrenzen existierte nicht; „task completion“ als Abbruchbedingung gibt es nicht | Contributions, Sec. III-C, IV, V | Abbruch implementiert, Text angepasst (Joint-Limit-Monitor im Reward-Block) | ✅ Modell + Text / ⬜ Ergebnis-Sätze in Sec. V (K5/K6-Werte, Teil B) |
| A20 | Mit konstanter Terminalstrafe −1 beenden Agenten Episoden absichtlich (Pilot: 0 % vollständige Episoden) | Sec. IV Reward | r_fail = −(N − k + 1), Gl. `eq:rfail`, Begründung mit Pilot | ✅ |
| A21 | Modellparameter fehlten im Paper; URDF hatte 65 kg; Aussage „identified values from literature [b12]“ trifft nicht zu | Sec. III-B | URDF angeglichen; Tabelle `tab:robot_params`; Satz ersetzt. **[b12] ist jetzt unzitiert → Bibitem entfernen oder an anderer Stelle zitieren** | ✅ / ⬜ [b12] |
| A23 | Abb. 7: p_x steigt linear auf ≈ 1,2e-5 Ns, Text sagt „within 1e-6 Ns“ | Sec. V Momentum | Nach A27 neu erzeugen, Satz anpassen | 🔬 (Teil C) |
| A24 | Solver-Schritt 0,01 s (ode4) ≠ Agenten-Takt 0,1 s; Paper sagte „gleich“ | Sec. III-C, Sec. IV Action Space | Text korrigiert | ✅ |
| A25 | Episode = Halbkreis (T = 8,5 s, 85 Schritte), nicht „one or two laps“, nicht periodisch | Sec. III-D, Sec. IV Training Procedure | Text korrigiert | ✅ |
| A26 | d_safe = 2 cm löst nur eine Warnung aus; Moment-Null und Abbruch erst bei Kontakt | Sec. III-C, Sec. V Collision Monitor | Text korrigiert | ✅ |
| A27 | Impulsmonitor rechnet mit 5 kg Basismasse (Modell 25 kg), berechnet nur linearen Impuls; „pause training“ nicht implementiert | Sec. V Momentum, Abb. 6/7 | Text: Drehimpuls und „pause training“ gestrichen, Caption Abb. 6. Monitor-Masse im Modell noch korrigieren | ✅ Text / 🔬 Monitor + Abb. 7 |
| A28 | Tabelle I, Zeile „Ours“: RL Alg. nur „PPO“, obwohl 6 Verfahren verglichen werden | `tab:sample` | Alle 6 Verfahren eingetragen | ✅ |
| A29 | Acknowledgment „solely for language editing“ stimmt nicht mehr (KI-Unterstützung bei Code und Auswertung) | Acknowledgment | Offenlegung ergänzt, Bibitem `anthropic_claude` | ✅ |
| A30 | T2 definiert, aber nie berechnet; T1 unklar definiert | Sec. IV, Sec. VI Summary | T2 gestrichen, T1 = Std. der letzten 100 Episoden. Ggf. T3 → T2 umbenennen | ✅ / ⬜ Umbenennung (Teil B) |

### Stellen, die noch alte Ergebnisse enthalten (Teil B/C)

- Sec. V: „By the end of training, all agents avoided collisions … (K5 = 0)“ (TD3 hatte Kollisionen); Absatz zu
  Gelenkgrenzen mit PPO 5,1 % / TRPO 2,1 %; „Limitations of the Formal Methods Component“: „no violations …
  observed“; Momentum: „remained within 10^-6 Ns“.
- Sec. VI komplett (Tab. 5, 6, Abb. 12, 13, Text, Summary), Sec. VII-Einleitung (Begründung für PPO, Ziele),
  Tab. 7, Abb. 14–16, Contributions (Satz zur Wahl von PPO und zu den Verbesserungen), Abstract, Conclusion.
- Sec. VII-B bis E (Ablation, Sensitivität, Robustheit, Zwei-Segment-Bahn): Teil C.

---

## Einreichungsdateien Submission 3

- [ ] Response to Reviewers (IEEE-Vorlage: Concern / Response / Action je Punkt) → in diesem Ordner ablegen
- [ ] Highlighted PDF → `access_with_markers/access_with_markers.pdf`
- [ ] Main Manuscript: `access/access.tex` + `access/access.pdf` (+ Figures, `.cls`)
- [ ] Resubmission Checklist aus der Entscheidungs-Mail durchgehen

## Nächste Schritte

1. Neue Hauptaussage mit Prof abstimmen (siehe „Stand der Experimente“)
2. Teil B einarbeiten: Tab. 5–7 und Hyperparameter-Tabelle aus `analyzeBenchmark`, Sec. VI/VII-Text, Abstract,
   Conclusion, Limitations (R2.3a), Ergebnis-Sätze in Sec. V
3. Kampagnen Teil C: R2.4b, Ablation, Sensitivität optimiertes PPO, Zwei-Segment-Bahn, Stresstests; Impulsmonitor (A27)
4. Abbildungen neu: Abb. 3/4 (Screenshots), 7, 12–18
5. Response Letter schreiben
