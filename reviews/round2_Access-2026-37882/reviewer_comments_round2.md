# Reviewer Comments Tracking — Runde 2

**Manuskript:** Access-2026-37882 (= Submission 2, Ordner `submission_2/`) → Resubmission als **Submission 3**
(Ordner `access/` + `access_with_markers/`)
**Entscheidung:** Reject & Resubmit (Associate Editor: Dr. Alicia Fornés), erhalten 09/2026
**Wichtig:** IEEE Access erlaubt nur eine Resubmission. Bleibt ein Reviewer-Punkt offen oder hat der AE noch
substanzielle technische Bedenken, wird endgültig abgelehnt → **jeder Punkt muss beantwortet werden**.
**Vorherige Runde:** [`../round1_Access-2026-06278/reviewer_comments.md`](../round1_Access-2026-06278/reviewer_comments.md)
**Stand:** 19.09.2026. Hauptbenchmark neu gerechnet, Methodenteil (Teil A) im Paper eingearbeitet. Sprachliche
Überarbeitung und Korrekturen A31–A41 in beiden Fassungen.

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
- **Hauptbenchmark `results/benchmark_v2` fertig mit 10 Seeds** (20.09.2026): 6 Default-Agenten + optimiertes PPO,
  je 10 Seeds (0–9) = 70 Läufe, 1000 Episoden, zufällige Startpose ±1° im Training; Auswertung deterministisch auf
  31 Startzuständen (nominal + 30 zufällig, gleich für alle Agenten). Keine Fehler, Laufzeiten 6,5–36 min je Lauf.
  Optimiertes PPO = Hyperparameter aus `PPO_1.mat` (10.12.2025), Agenten neu trainiert.
- **Tabelle 1 (Robustheit, alle Läufe), Fisher-Test gegen TRPO default nach Holm:**

  | Konfiguration | erfolgreiche Läufe | Abbruchrate | Abbruchgrund | p (Holm) |
  |---|---|---|---|---|
  | TRPO default | 10/10 | 0 % | – | – |
  | PPO optimiert | 10/10 | 0 % | – | 1,0 |
  | SAC default | 6/10 | 40 % | Gelenkgrenze | 0,17 |
  | TD3 default | 5/10 | 48 % | 25 % Kollisionen | 0,098 |
  | DDPG default | 4/10 | 52 % | 22 % Kollisionen | 0,043 |
  | PPO default | 3/10 | 70 % | 14 % Kollisionen | 0,015 |
  | PG default | 2/10 | 80 % | Gelenkgrenze | 0,0043 |

- **Tabelle 2 (nur erfolgreiche Läufe), Mann-Whitney gegen TRPO default nach Holm:**

  | Konfiguration | n | K2 Mittel ± Std | K2 Median [min–max] | K4 Median | T2 | p (K2) |
  |---|---|---|---|---|---|---|
  | PPO optimiert | 10 | 0,0041 ± 0,0023 | 0,0035 [0,0016–0,0082] | 0,014 | 9,2 min | 0,089 |
  | TRPO default | 10 | 0,0145 ± 0,0196 | 0,0061 [0,0025–0,0656] | 0,025 | 11,3 min | – |
  | PPO default | 3 | 0,0134 ± 0,0081 | 0,0092 | 0,054 | 8,4 min | zu wenige Läufe |
  | PG default | 2 | 0,0177 ± 0,0036 | 0,0177 | 0,030 | 6,9 min | zu wenige Läufe |
  | TD3 default | 5 | 0,0886 ± 0,0560 | 0,0911 | 0,070 | 29,1 min | 0,0093 |
  | DDPG default | 4 | 0,1599 ± 0,0919 | 0,1340 | 0,324 | 24,3 min | 0,0060 |
  | SAC default | 6 | 0,2168 ± 0,1098 | 0,1866 | 0,159 | 35,2 min | 0,0010 |

  TRPO streut schief (drei schwache Seeds), deshalb ist der Median aussagekräftiger als Mittel ± Std. Offen: ob
  Tab. 2 im Paper Mittel ± Std, Median oder beides zeigt (mit Prof abstimmen).
- **Neue Hauptaussage (Vorschlag, mit Prof abzustimmen):** Unter Toolbox-Standardwerten schließt nur TRPO alle
  Läufe ohne Sicherheitsabbruch ab. Unter den erfolgreichen Läufen erreicht das optimierte PPO den kleinsten
  Bahnfehler bei kürzerer Trainingszeit, ohne dass der Abstand zu TRPO statistisch gesichert ist (p = 0,089).
  SAC, DDPG und TD3 sind auch im Erfolgsfall signifikant schlechter. Einschränkung: Default-Lernrate 0,01
  benachteiligt vermutlich PPO und die Off-Policy-Verfahren.
- **Offene Frage zum optimierten PPO:** Die Hyperparameter stammen aus einer Rastersuche vom Dezember 2025, also vor
  der Vereinheitlichung von Modell und Protokoll, ausgewählt über je einen Seed. Im Repo liegt zusätzlich eine
  Bayes-Optimierung (Juli 2026, 60 Trials) mit ganz anderen Werten, die nicht verwendet wird. Entweder im Text
  offenlegen oder die Suche unter dem finalen Modell wiederholen (8 Konfigurationen × 3 Seeds, ca. 40 min).
- **Modell erweitert (19.09.2026, Code-Stand nach Commit 982ffe2):** Stresstest-Bedingungen als Konfiguration in
  `benchmarkConfig`: Messrauschen auf allen 23 Beobachtungen (`noise.obs_std`, Block „obs noise“, neue Folge je
  Episode, in der Auswertung gleich für alle Agenten), äußeres Gelenkmoment (`dist.tau`, `dist.t_on/t_off`, Block
  „joint disturbance“ nach der Saturation; Reward, K8, K9 sehen nur das Agentenmoment), dazu wie bisher
  `tau_sat_scale` und `robot.param_scale`. Impulsmonitor rechnet mit `robotP`, `p_tot` wird geloggt (A27).
  Regressionstest: Mit Nominalkonfiguration sind KPI-Tabellen, alle geloggten Signale (6 Agenten × 31 Episoden) und
  Kurztrainings bitgleich zum Stand vorher, Trainings-Rewards gleich benchmark_v2.
- **Befund A42 (Massen):** Die Solid-Blöcke „Visual“ aus dem URDF-Import tragen eigene Masse. Simuliert wurden
  immer 30 kg Basis (I = 6 kg m²) und 2 kg je Glied (I = 0,2 kg m²), nicht 25 kg / 1 kg wie in Tab. `robot_params`.
  `benchmarkConfig` enthält jetzt die Gesamtwerte (cfg.version = 2), `upgradeConfig` rechnet alte Agentendateien um,
  Ergebnisse bitgleich. benchmark_v2 bleibt gültig.
- **Teil-C-Skripte bereit:** `runPartC` (Studien in `partCJobs`: stress, twoseg, ablation, sensitivity, r24b),
  Fortschritt je Episode in `results/<Kampagne>/progress/`, Übersicht mit `campaignStatus("<Kampagne>")`.
- **Rückmeldung Prof. (20.09.2026):** Abbruchrate und KPIs trennen, 10 Seeds statt 5. Umgesetzt: `runBenchmark`
  mit Seeds 0–9 (Seeds 0–4 bleiben gültig, es fehlen nur 5–9, ca. 1,7 h), `partCJobs` mit 10 Seeds für die
  Zwei-Segment-Bahn und 5 Seeds für r24b, Ablation und Sensitivität, `analyzeBenchmark` mit beiden Tabellen.
  Zahlen mit den vorhandenen 5 Seeds als Vorschau: TRPO default und PPO optimiert 5/5 erfolgreich, DDPG und SAC 3/5,
  PPO default und TD3 2/5, PG 1/5. KPIs der erfolgreichen Läufe: PPO optimiert K2 = 0,0050, TRPO 0,0078,
  PPO default 0,016, PG 0,020, TD3 0,098, DDPG 0,118, SAC 0,219. Die Trennung entschärft die Aussage über PPO.
  Die Frage nach einem zweiten Roboter (7 DoF) ist verneint, das Paper bleibt beim 4-DoF-Arm.
- **Abbildungen neu (21.09.2026, `makeFigures`):** Boxplots und Konfidenzintervalle K2/K4/K7/K9 jetzt über die
  erfolgreichen Läufe (n unter jeder Box), Lernkurven PPO default/optimiert als Mittel ± Std über 10 Seeds mit
  gleicher y-Achse, EE-Bahn und Basis-Quaternion (Vektorteil) für Kreis und Zwei-Segment-Bahn, Impuls (Abb. 7).
  Gezeigte Läufe = erfolgreicher Lauf mit mittlerem K2: Kreis PPO default s2, PPO optimiert s0, TRPO s7
  (zusätzlich `soll_vs_ist_kreisbahn_trpo.png`); Zwei-Segment PPO default s9 (einziger erfolgreicher), PPO
  optimiert s5. Basisrotation nur um z (q_x = q_y = 0), min q_w = 0,9927. Captions in Teil B anpassen.
  Noch von Patrick: Modell-Screenshots (Spacerobot_slx, Robot, Reference, Impuls-Monitor, Kollisionsmonitor),
  weil das Modell neue Blöcke hat.
- Offen: Satz zu Abb. 7 in Sec. V (A23), Captions der neuen Abbildungen.

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
| R2.3a | Evaluationsumfang klein (3 Läufe, 30 Episoden, feste Startkonfiguration) → Schlussfolgerungen ausdrücklich konditional | Satz „conclusions remain conditional …“ jetzt im neuen Evaluationsabsatz (Sec. IV). Limitations-Punkt „evaluation scope“ und Robustheitsfazit beschreiben noch das alte Protokoll (3 Läufe, feste Startkonfiguration) | Sec. IV, Robustness, Limitations | ✅ (Sec. IV, Limitations, Stresstest-Abschnitt VII-D mit Scope-Absatz, 21.09.2026) |
| R2.3b | Evaluation erweitern (Seed-Sensitivität, Generalisierung über Startzustände/Trajektorien/Parameter/Störungen) | **10 Seeds** je Konfiguration (Absprache mit Prof., 20.09.2026), alle 6 Agenten + PPO optimiert; zufällige Startpose im Training; 31 Startzustände in der Auswertung; Statistik auf Seed-Ebene (Mann-Whitney, Holm) nur über die erfolgreichen Läufe, Abbruchraten mit Fisher. Methodik in Sec. IV eingebaut. Ergebnisse Hauptbenchmark in Sec. VI/VII-A eingebaut (21.09.2026) | Sec. IV, VI, VII, Tab. KPI-Vergleich, Statistik, Boxplots/CI-Plots | ✅ (Methodik, Hauptbenchmark, Zwei-Segment-Bahn, Stresstests, 21.09.2026) |
| — | **Trennung Abbruchrate / KPIs (Prof., 20.09.2026)** | Zwei Tabellen statt einer: Tab. 1 Robustheit über ALLE Läufe (erfolgreiche Läufe, Abbruchrate, Anteil Kollisionen), Tab. 2 KPIs NUR über die erfolgreichen Läufe (K2, K4, T3, plus vollständige KPI-Tabelle). Erfolgreich = keine der 31 Auswertungsepisoden bricht ab. Umgesetzt in `analyzeBenchmark`; Methodiktext in Sec. IV in beiden Fassungen ergänzt (in den bestehenden `\hlone`-Blöcken [R2.3, A1, A12] und [R2.3, A13]) | Sec. IV, Sec. VI | ✅ (Tab. `robustness_seeds`, `kpi_comp`, auch in Teil C angewandt) |
| R2.4a | Reward-Gewichte handgetuned → kurze Sensitivitätsstudie | Existiert, muss aber neu gerechnet werden (A18) → danach im Response Letter darauf verweisen | Sec. Sensitivity Analysis | ✅ Sec. VII-B/C neu (Ablation und Sensitivität mit 5 Seeds, Auswertung mit nominalen Gewichten), im Response Letter darauf verweisen |
| R2.4b | Sensitivität nicht nur für PPO, sonst ist „ranking robust“ nicht belegt | PPO, TRPO, SAC (default) mit W_ori und W_p × 0.5 / × 2, je 3 Seeds; Ranking pro Variante über K2/K4/K6 (nicht K1, da K1 den geänderten Reward misst); zusätzlich K1 mit nominalen Gewichten aus den Logs | Tab. reward_sensitivity, Absätze „Robustness of the algorithm ranking“, „Summary“ | ✅ Tab. `reward_ranking` (neu): TRPO in allen 20 Läufen der Varianten erfolgreich, Reihenfolge TRPO < PPO < SAC nach K2 in jeder Variante unverändert |
| R2.5 | „safe RL“ nur mit Bezug auf die überwachten Eigenschaften; 0 Verletzungen in 30 Episoden sind empirisch | Kopfzeile „Safe RL for …“ → „Benchmarking Deep RL for …“; Keyword „safe simulation“ → „safety monitoring“; „safety envelope“, „safety-augmented deep RL“, „no safety violations“ → „no observed violations of the monitored properties“ | Kopfzeile, Keywords, Contributions, Sec. IV, Discussion/Conclusion | ✅ (Zahlen der Verletzungen mit Teil B prüfen) |

---

## Inkonsistenzen & Fehler (eigene Befunde, kein Reviewer-Kommentar)

Nicht von den Gutachtern genannt, aber für ein neues Review-Team angreifbar → genauso abarbeiten.

| # | Befund | Fundstelle (`access.tex`) | Maßnahme | Status |
|---|--------|---------------------------|----------|--------|
| A1 | `rng(0,'twister')` vor **jedem** Lauf, trotzdem „three independent training runs“. Bestätigt: gleicher Seed ergibt bitgleiche Läufe, die 3 Läufe waren identisch | Sec. IV, „To ensure reproducibility …“ | 5 Seeds (0–4), Text neu | ✅ |
| A2 | Nominalwerte in `tab:robustness_eval` weichen vom optimierten PPO im Rest des Papers ab. Ursache: Modell zwischen Auswertungen von Hand geändert (W_ori 200/2000, Basis 5/25 kg), verschiedene Agenten-Konfigurationen | Robustness Evaluation, Tabelle | Stresstests mit den neuen Checkpoints und derselben Auswertung neu rechnen. Bedingungen jetzt in `benchmarkConfig` (Rauschen 0,005, τ_sat × 0,75, Störmoment 2 N m an Gelenk 1/2 für 2,0–2,5 s, Parameter × 1,5, kombiniert), Auswertung aller 35 benchmark_v2-Agenten in `runPartC` | ✅ Stresstests mit allen 70 Agenten neu (Tab. `robustness_eval`) |
| A3 | Text „K7 increases substantially“, Tabelle zeigt Abnahme. Erklärt durch A8 (Text meint Jerk, Tabelle 1 − Jerk) | Robustness Evaluation | Mit neuen Werten korrigieren | ✅ (Text neu, K7-Aussage jetzt: Rauschen erhöht K7 um etwa zwei Drittel) |
| A4 | Reviewer-Tags im clean Manuskript: „(R2.13)“, „(R2.12)“, „raised independently by both reviewers“ | Statistik-Abschnitt, Sensitivity Analysis | Entfernt | ✅ |
| A5 | Alle 8 Sensitivitätsvarianten schlechter als Default-PPO; „ranking robust“ nur für PPO geprüft. Erklärt durch A18 | Sensitivity Analysis | Mit R2.4b neu rechnen | ✅ (mit A18 neu gerechnet: alle 40 Läufe erfolgreich) |
| A6 | Tabelle I: „Formal Ver. = Yes“ für die eigene Arbeit überzeichnet | `tab:sample` | „Formal Meth. = Partial†“ mit Fußnote | ✅ |
| A7 | Abstract nach Ergänzungen > 250 Wörter | Abstract | Gestrafft; nach den Änderungen vom 20.09.2026 bei 252 Wörtern, beim Neuschreiben in Teil B unter 250 bringen | ✅ (243 Wörter, 21.09.2026) |
| A8 | K7 im Code = 1 − mittlerer Jerk (höher = glatter), Paper-Definition umgekehrt. Betrifft alle K7-Werte, Abb. 12c/13c, Contributions, Conclusion („48.5 % smoother“) | `computeKPIsFromLogs.m`, alle K7-Stellen | Code korrigiert (Definition im Paper war richtig); alle K7-Werte neu | ✅ |
| A9 | Tab. 5 und Tab. 6/Abb. 12/13 aus verschiedenen Daten (K2 PPO 0,0257 vs. Median ≈ 0,33; Tab. 6 zeigt TRPO bei K2 signifikant besser, Text das Gegenteil) | Sec. VI | Alle Tabellen/Abbildungen aus `analyzeBenchmark` bzw. `makeFigures` (Tab. robustness_seeds, kpi_comp, significance, ppo_optimization_kpi, Abb. 12–16) | ✅ |
| A10 | Ungleiche Trainingsbedingungen: 5 Agenten mit Kreis r = 0,4, PPO mit r = 0,5; je Agent anderer StopTrainingValue | Einzelskripte | Neutraining unter identischen Bedingungen; T3-Werte in Sec. V (Computational Overhead) aktualisiert | ✅ |
| A11 | Modell: τ_max = 2 N m, d_safe = 2 cm; Paper: 25 Nm, 5 cm | Sec. IV Action Space, Sec. V Collision Monitor | Text korrigiert | ✅ |
| A12 | Behauptet, nicht implementiert: 3 Läufe, Checkpoint-Auswahl, Randomisierung, Verifikation des Startzustands, Aufzeichnung von Tracking-Fehler je Episode; DT-Absatz überzeichnet („scenarios and constraints are sampled …, improves robustness …“) | Sec. III-A, Sec. IV Training Procedure | Implementiert (5 Seeds, zufällige Startpose ±1°, letzter Agent nach 1000 Ep.) und Text angepasst | ✅ |
| A13 | Evaluation: feste Startpose + stochastische Policy; TD3/DDPG = 30 identische Episoden | Sec. IV Evaluation and Metrics, Sec. VI Performance Metrics | Deterministische Policy, 31 Startzustände, Seed-Ebene; Text angepasst | ✅ |
| A14 | Optimiertes PPO im Paper ([256,256,128]+LayerNorm, Actor 5e-4 / Critic 1e-3) ≠ trainiert (2×128, Actor 1e-3, Critic 5e-4, γ 0,995, H 1024, B 256, 10 Epochen, Gradient-Threshold 1, Mittelwert ohne tanh) | Contributions, Sec. V Computational Overhead, Sec. VII | Text korrigiert, LR-Begründung ersetzt, „Fixed settings“ ergänzt | ✅ |
| A15 | q/dq im Modell in umgekehrter Gelenkreihenfolge → Kollisionsmonitor prüfte falsche Armstellung, K6 falsche Grenzen, K9 falsche Paare τ_i·q̇_j | `SpaceRobot.slx` | Modell korrigiert | ✅ Modell / ⬜ Screenshot Abb. 4 |
| A17 | Default-Agenten hatten LR 0,01 (MATLAB-Standard), nicht 1e-3; PG war nicht auf Standardwerten; MATLAB-Version R2026a statt R2025b | Sec. VI Training Setup | Reine Toolbox-Defaults; Text + neue Tabelle `tab:hyperparameters` | ✅ |
| A18 | Ablations-/Sensitivitätsagenten mit dritter PPO-Konfiguration (Actor-LR 5,7e-5, H 600, B 200) | Sec. VII-B/C | Neu trainieren | ✅ Ablation und Sensitivität mit der Konfiguration aus Tab. 7 neu trainiert |
| A19 | Kollision beendete Episode nicht; Assertion für Gelenkgrenzen existierte nicht; „task completion“ als Abbruchbedingung gibt es nicht | Contributions, Sec. III-C, IV, V | Abbruch implementiert, Text angepasst (Joint-Limit-Monitor im Reward-Block). Ergebnissätze in Sec. V: 108 von 2170 Episoden mit Kontakt (6 Läufe), 791 durch Gelenkgrenze, TRPO und PPO optimiert ohne Verletzung | ✅ |
| A20 | Mit konstanter Terminalstrafe −1 beenden Agenten Episoden absichtlich (Pilot: 0 % vollständige Episoden) | Sec. IV Reward | r_fail = −(N − k + 1), Gl. `eq:rfail`, Begründung mit Pilot | ✅ |
| A21 | Modellparameter fehlten im Paper; URDF hatte 65 kg; Aussage „identified values from literature [b12]“ trifft nicht zu | Sec. III-B | URDF angeglichen; Tabelle `tab:robot_params`; Satz ersetzt. [b12] jetzt in Related Work zitiert (Satz zur Modellidentifikation) | ✅ |
| A23 | Abb. 7: p_x steigt linear auf ≈ 1,2e-5 Ns, Text sagt „within 1e-6 Ns“ | Sec. V Momentum | Abb. 7 neu aus `p_tot` (PPO optimiert, Seed 0, nominale Startpose): max \|p\| = 2,7e-7 N s, Versatz im ersten Agentenschritt, danach konstant. Über alle ausgewerteten Episoden bis 3e-4 N s. Satz in Sec. V anpassen | ✅ |
| A24 | Solver-Schritt 0,01 s (ode4) ≠ Agenten-Takt 0,1 s; Paper sagte „gleich“ | Sec. III-C, Sec. IV Action Space | Text korrigiert | ✅ |
| A25 | Episode = Halbkreis (T = 8,5 s, 85 Schritte), nicht „one or two laps“, nicht periodisch | Sec. III-D, Sec. IV Training Procedure | Text korrigiert | ✅ |
| A26 | d_safe = 2 cm löst nur eine Warnung aus; Moment-Null und Abbruch erst bei Kontakt | Sec. III-C, Sec. V Collision Monitor | Text korrigiert | ✅ |
| A27 | Impulsmonitor rechnet mit 5 kg Basismasse (Modell 25 kg), berechnet nur linearen Impuls; „pause training“ nicht implementiert | Sec. V Momentum, Abb. 6/7 | Text: Drehimpuls und „pause training“ gestrichen, Caption Abb. 6. Monitor rechnet jetzt mit `robotP` (Gesamtmassen, siehe A42), `p_tot` geloggt. Kontrolle: vorher max \|p\| = 0,3–1,9 N s (p = 10·v_Basis), jetzt 7e-8 bis 3e-4 N s (Rundungs-/Integrationsfehler ode4) | ✅ Text + Monitor / 🔬 Abb. 7 |
| A28 | Tabelle I, Zeile „Ours“: RL Alg. nur „PPO“, obwohl 6 Verfahren verglichen werden | `tab:sample` | Alle 6 Verfahren eingetragen | ✅ |
| A29 | Acknowledgment „solely for language editing“ stimmt nicht mehr (KI-Unterstützung bei Code und Auswertung) | Acknowledgment | Offenlegung ergänzt, Bibitem `anthropic_claude` | ✅ |
| A30 | T2 definiert, aber nie berechnet; T1 unklar definiert | Sec. IV, Sec. VI Summary | T2 gestrichen, T1 = Std. der letzten 100 Episoden. T3 → T2 umbenannt (beide Fassungen und `analyzeBenchmark`) | ✅ |
| A31 | DDPG mit [b7] (Sutton & Barto) zitiert statt mit [b16] (Lillicrap) | Contributions | Zitat korrigiert | ✅ |
| A32 | Caption Tab. 7 schreibt PPO den niedrigsten Wert bei K3 zu, fett markiert ist DDPG | `tab:kpi_comp` | Tabelle in Teil B neu aufgebaut | ✅ |
| A33 | K9 im Text als ∫\|τ·q̇\|dt, in der Definition als Σ_j \|τ_j q̇_j\| | Sec. IV, Begründung K7/K9 | Text an Definition angepasst | ✅ |
| A34 | Kollisionsmonitor als O(n_links) angegeben, der paarweise Abstandscheck skaliert mit O(n_links²) | Sec. V Computational Overhead | Korrigiert | ✅ |
| A35 | Ablation: „K2 sinkt ohne W_ori leicht“, laut Tabelle steigt K2 von 0,0083 auf 0,017 | Sec. VII-B Dominant Weights | Aussage an Tabelle angepasst | ✅ (Text neu: ohne W_ori sinkt K2 auf 0,0030, K4 steigt auf 0,104) |
| A36 | Sensitivität: „vier dominante Gewichte“, laut Ablation sind nur W_p und W_ori dominant. Verweise für die Hyperparameter und das Ranking zeigten auf die Ablation | Sec. VII-C, Caption `tab:reward_sensitivity` | Formulierung und Verweise korrigiert | ✅ |
| A37 | „tailored network design“ bzw. „network tailoring“ widerspricht A14 (Hidden Layers unverändert), ebenso „tuning its network architecture“ im Abstract | Abstract, Sec. VII-A, VII-E, Conclusion | Gestrichen | ✅ |
| A38 | „excessive orientation error“ als Abbruchgrund genannt (gibt es nicht, A19). „Joint-limit assertion“ und Offline-Check als Laufzeit-„shield“ bezeichnet | Sec. VII-B Note on K9, Practical Implications | Korrigiert | ✅ |
| A39 | Stresstest: „K2 stays close to the nominal level“, laut Tabelle 0,0021 → 0,0078 | Sec. VII-D | Neutral formuliert | ✅ |
| A40 | „Safety metrics remain within limits“ und „does not compromise the monitored safety limits“, obwohl K6 bis 0,53 % reicht. Kausale Aussage zum Monitor nicht belegt | Sec. VII-C Safety Metrics | Neutral formuliert | ✅ |
| A41 | Absatz „Computational Overhead“ stand hinter dem Übergangssatz „The next section …“ | Sec. V | Vor die Zusammenfassung verschoben | ✅ |
| A42 | Solid-Blöcke „Visual“ (URDF-Import) tragen Masse: 5 kg / I = 1 kg m² an der Basis, 1 kg / 0,1 kg m² je Glied. Simuliert (auch benchmark_v2): Basis 30 kg, 6 kg m²; Glieder 2 kg, 0,2 kg m². Tab. `robot_params` und URDF nannten 25 / 1 kg; `param_scale` skalierte nur den Inertia-Anteil | Sec. III-B, Tab. `robot_params` | Code: Gesamtwerte in `benchmarkConfig` (version 2), exakte Aufteilung auf beide Blöcke, `upgradeConfig` für alte Agentendateien, URDF angeglichen; Ergebnisse bitgleich. Paper: Tabelle `robot_params` auf 30 kg / 6 kg m² / 2 kg / 0,2 kg m² geändert (20.09.2026) | ✅ |
| A43 | Hyperparameter des optimierten PPO stammen aus einer Rastersuche vom Dezember 2025 (ein Lauf je Kandidat, früherer Modellstand, vor Reward-Gewichten, Abbruchregel und Auswertungsprotokoll). Bayes-Optimierung Juli 2026 im Repo mit anderen Werten, nicht verwendet | Sec. VII Hyperparameter Tuning, Limitations | Offengelegt: Suche mit einem Lauf je Kandidat auf früherem Modellstand, Werte als feste Konfiguration, nicht als Optimum. In den Limitations zusätzlich: andere Algorithmen nur mit Default-Hyperparametern | ✅ |
| A44 | Abb. 1 (`oos0`) und Abb. 19 (`oos1`) zeigten fast dasselbe KI-generierte Bild | Sec. I, Sec. VIII | Nur `oos1` behalten, als Abb. 1 (`fig:oos`) in die Einleitung. Neue Caption: Nur die Simulationsseite ist Teil dieser Arbeit (R2.2). Verweise in Future Work zeigen auf Abb. 1 (24.09.2026) | ✅ |
| A45 | SLDV-Check falsch beschrieben („simplified closed-loop model of the agent“, „trained policy block“, „for any admissible observation input“, „plant replaced by a worst-case abstraction“). `verify_tau.slx` enthält nur Eingang τ → Sättigung ±2 → \|u\| ≤ 2 → Proof Objective, keine Policy, keine Strecke | Abstract, Beiträge, Sec. I/II, Sec. V (Absatz, Abb. 10/11), Tab. 1, Limitations, Conclusion | Neu: Policy-Ausgabe ist beliebiger Eingang, die Schranke gilt daher für jede Ausgabe der Policy, weil der Stellpfad sie konstruktiv begrenzt. Keine Aussage über den geschlossenen Kreis. Störmoment im Stresstest liegt hinter der Sättigung und ist nicht abgedeckt. Überschriften „Formal Check of the Torque Bound“ und „Scope of the Safety Components“ (24.09.2026) | ✅ |
| A46 | „Formal-methods-based runtime monitoring“ zu stark: Kollisions- und Gelenkgrenzen-Monitor sind MATLAB-Function-Blöcke mit Schwellwertvergleichen. Stateflow genannt, in `SpaceRobot.slx` gibt es keinen Stateflow-Chart (nur EML-Charts der MATLAB-Function-Blöcke). Tab. 3 „Fidelity High/Medium/High“ ohne Beleg | Sec. I, II, III-C (Simulatorwahl, Tab. 3), Sec. V, Conclusion | „Runtime safety monitors, complemented by a formal check of the torque bound“. Stateflow gestrichen, Zeile „Free-floating multibody fidelity“ und Gazebo-Satz ohne Beleg gestrichen, Caption Tab. 3 neutral | ✅ |
| A47 | Zwei-Segment-Bahn „significantly better than TRPO“ (Abstract, Diskussion): p = 0,026–0,038 unkorrigiert über 7 KPIs, mit Holm über die KPIs nichts signifikant. Widerspruch zur Kreisbahn („with care“). Nirgends stand, worüber Holm korrigiert (Code: je KPI über die verglichenen Agenten) | Abstract, Beiträge, Sec. IV Evaluation, Tab. 7/9, Sec. VII-A, VII-E, Diskussion | Sec. IV nennt jetzt den Umfang der Korrektur. Bei Einzelvergleich mit TRPO: p unkorrigiert, zusätzlich Holm über die 7 getesteten KPIs → Kreis und Zwei-Segment-Bahn nicht signifikant. Tab. 7: Holm über 5 Agenten, Tab. 9: über 3 Agenten | ✅ |
| A48 | Ranking-Aussage „Reihenfolge TRPO, PPO, SAC bleibt in allen Varianten gleich“ zu stark: bei W_p × 0,5 hat PPO 0/5, nach erfolgreichen Läufen kippt PPO/SAC (nominal 2:3, W_p × 2 2:1) | Sec. VII-C, Diskussion | Neu: TRPO in allen Varianten meiste erfolgreiche Läufe und niedrigstes K2. Reihenfolge PPO/SAC nicht stabil (SAC mehr erfolgreiche Läufe in 4 von 5 Einstellungen, PPO niedrigeres K2 auf 1–2 Läufen, bei W_p × 0,5 keine Reihenfolge) | ✅ |
| A49 | Optimiertes PPO unterscheidet sich auch im Netz (Mittelwert ohne tanh), Beitrag sprach nur von Hyperparametern | Beiträge, Tab. 6 Caption, Sec. VII Einleitung und Tuning, Limitations | Überall genannt. Effekt von Hyperparametern und Ausgabeschicht nicht getrennt (Limitation) | ✅ |
| A50 | PG: Tab. 6 „critic as baseline“, Text „without variance-reduction techniques“. Code: `UseBaseline` = true | Sec. VI | Text: REINFORCE mit gelernter Zustandswertfunktion als Baseline, ohne Trust Region oder Clipping | ✅ |
| A51 | Aufgabe ist eben (Referenz in XY-Ebene, alle vier Gelenkachsen parallel zu z laut URDF, Basis dreht nur um z), stand nirgends als Einschränkung | Abstract, Sec. III-D, Limitations, Future Work, Conclusion | „planar“ ergänzt, Limitation: keine Bewegung aus der Ebene, keine Drei-Achsen-Rotation der Basis | ✅ |
| A52 | „Parameters × 1,5“ skaliert Massen, Trägheiten und Dämpfung gleich (`param_scale`). Massenverhältnisse bleiben, ohne Gelenkgrenz- und Kontaktkräfte äquivalent zu τ/1,5, also schwächere Antriebe | Abstract, Sec. VII-D (Text, Tab. 14 Caption, Scope), Diskussion | So beschrieben. Deutung für den großen K2-Anstieg als „one possible reason“. Scope: keine veränderte Massenverteilung oder Nutzlast getestet | ✅ |
| A53 | Sec. III-A beschrieb mehr, als simuliert wird: „DTs of the target satellite“ (kein Zielsatellit im Modell), „additional DTs of representative in-space operation scenarios“, „range of admissible initial conditions“ (nur ±1°). Einleitung: „the DTs considered here … contact-rich interactions“ | Sec. I, Sec. III-A | DT umfasst nur den Roboter (URDF + Tab. 2), ±1° als kleine Störung benannt. Einleitung allgemein formuliert, Satz „The DT in this work covers only the space robot …“ | ✅ |
| A54 | KPI-Tabelle der Default-Agenten zeigte nur K2, K4, T2. Text zitierte K7, K9, T1 ohne Tabelle | Sec. VI, Tab. 8 | Tab. 8 vollständig aus `nominal_defaults_table2_full.csv` (K1–K4, K7–K9, T1, T2), Layout wie Tab. 10 | ✅ |
| A55 | Lernkurven nur für PPO | Sec. VI, neue Abb. 12 | `trainingsverlauf_defaults.png` (`makeFigures`, Gruppe `trainall`, alle 60 Default-Läufe aus `agents/*.mat`) eingebunden, zwei Sätze im Text | ✅ |
| A56 | Kleinkorrekturen: K6-Text „any joint“ (Formel mittelt über Gelenke), Gelenkgrenzen „together with the observation“ (nicht Teil der 23 Beobachtungen), e_ori „well-defined for q_err ≠ ±1“ (Code setzt 0 bei q_v = 0), Aktionsraum „ℝ⁴“ trotz Sättigung, Tab. 7 „Aborts by contact“ ist Anteil an den Abbrüchen (TD3: 37 von 150), Abb. 3 „PPO-based RL agent“ | Sec. III, IV, VI | Korrigiert | ✅ |
| A57 | Zitate als Schmuck: [b23], [b1, b7], [b8], [b15], [b22, b24], [b5, b22] an eigenen Ergebnissen oder Aussagen, [b2, b7] an eigener Aktionsdefinition, [b28]/[b34]/[b3] an Impulserhaltung, [b31] „Spatial Contact Force“ (im Modell nicht verwendet, Monitor nutzt `checkCollision` der Robotics System Toolbox) | Beiträge, Sec. III, IV, V, VI, VIII | Entfernt. [b28] jetzt an der Schwerkraft im Mechanism-Configuration-Block, [b25–b27] am URDF-Satz, [b31] ersetzt durch MathWorks-Doku `checkCollision`, [b23] nur noch für Shielding im Ausblick | ✅ |
| A58 | KI-Offenlegung: GPT-Image-2 nur in der Caption von Abb. 1, IEEE verlangt Angabe in den Acknowledgments | Acknowledgment | Satz ergänzt. Offen: Bibitem `anthropic_claude` ist nicht zitiert (erscheint als [48] ohne Bezug), Entscheidung Patrick | ⬜ |
| A59 | „Ours“ in Tab. 1 (erste Person), „IROS“ kollidiert mit IEEE/RSJ IROS | Tab. 1, Future Work | „This work“, Akronym gestrichen | ✅ |

**Abstract nach A45–A52 (24.09.2026):** 247 Wörter (`wc -w`), Tab. 8 im Querformat mit allen KPIs, neue Abb. 12,
Manuskript 27 statt 26 Seiten.

**Sprachliche Überarbeitung (19.09.2026, nicht markiert):** ganzes Manuskript überarbeitet (kürzere Sätze, keine
Doppelpunkte und Semikolons im Fließtext, vorsichtigere Formulierungen, einheitliche Notation $K_1$–$K_9$,
KPI-Namen, Verweise „Fig.“/„Table“/„Section“/„(n)“, Captions). Nicht gelb markiert, im Response Letter pauschal
erwähnen. Nur die inhaltlichen Korrekturen A31–A41 sind markiert.

### Stand der Ergebnistexte

- Teil B (21.09.2026) und Teil C (21.09.2026) sind in beiden Fassungen eingebaut. Keine Stelle enthält mehr
  Ergebnisse des alten Protokolls (Suche nach alten Zahlen und Aussagen ohne Treffer).
- Teil C: Sec. VII-B Ablation (Tab. `reward_ablation`), VII-C Sensitivität (Tab. `reward_sensitivity`) und
  Ranking unter veränderten Gewichten (Tab. `reward_ranking`, R2.4b), VII-D Stresstests aller 70 Agenten
  (Tab. `robustness_eval`), VII-E Zwei-Segment-Bahn mit PPO default, PPO optimiert und TRPO
  (Tab. `ppo_linear_kpi`), zweiter Diskussionsabsatz.
- Seitenbreite Gleitobjekte in VII-A und VII-B bis VII-E ohne erzwungene Platzierung (`[t]`), damit keine
  Seite überläuft.

---

## Einreichungsdateien Submission 3

- [ ] Response to Reviewers (IEEE-Vorlage: Concern / Response / Action je Punkt) → in diesem Ordner ablegen
- [ ] Highlighted PDF → `access_with_markers/access_with_markers.pdf`
- [ ] Main Manuscript: `access/access.tex` + `access/access.pdf` (+ Figures, `.cls`)
- [ ] Resubmission Checklist aus der Entscheidungs-Mail durchgehen

## Nächste Schritte

1. ~~Teil C im Text~~ erledigt (21.09.2026)
2. Modell-Screenshots neu (Patrick): Spacerobot_slx, Robot, Reference, Impuls-Monitor, Kollisionsmonitor,
   dabei die automatisch platzierten neuen Blöcke aufräumen. `tau_collision.png` prüfen (altes Modell)
3. Response Letter (Concern / Response / Action je Punkt, Hinweis auf die nicht markierte sprachliche
   Überarbeitung und die Kopfzeile)
4. Endkontrolle: Zahlen Text gegen Tabellen, beide PDFs, Resubmission-Checkliste
