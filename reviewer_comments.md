# Reviewer Comments Tracking

## Farbschema (LaTeX)

| Reviewer | Farbe | LaTeX-Umgebung | Inline-Befehl |
|----------|-------|----------------|---------------|
| Reviewer 2 | Rot | `\begin{rtwohl}...\end{rtwohl}` | `\hltwo{[R2.x]}` |
| Reviewer 3 | Blau | `\begin{rthreehl}...\end{rthreehl}` | `\hlthree{[R3.x]}` |
| R2 + R3 Überschneidung | Grün | `\begin{roverlaphl}...\end{roverlaphl}` | `\hloverlap{[R2.x/R3.x]}` |

## Status-Legende

- ✅ erledigt
- ⬜ offen, ohne neue Experimente machbar
- 🔬 braucht neue MATLAB-Experimente
- 📊 möglich wenn bestehende Eval-Daten (30 Episoden / Trainingslogs) vorhanden

---

## Reviewer 2 — Rot

| # | Kommentar | Status |
|---|-----------|--------|
| R2.1 | Gaps in der bestehenden Forschung zu RL-basierter Space-Robotics identifizieren | ✅ |
| R2.2 | Related Work in 4 Kategorien gliedern: Classical Control, RL-based, Hybrid Safety-Aware, Benchmarking — jeweils mit Vor-/Nachteilen | ✅ |
| R2.3 | Tabelle um Spalten erweitern: DOF, Simulator, Obs./Act.-Dim., Safety Limits, Eval. Metriken | ✅ |
| R2.4 | Reward-Funktion mit konkreten Gewichtswerten definieren + physikalische Intuition erklären | ✅ |
| R2.5 | Sensitivitätsanalyse der Reward-Gewichte (Stabilität der Algorithmen-Reihenfolge prüfen) | ✅ (grün, überschneidet R3.4) |
| R2.6 | Ablation Study: Einfluss jeder Reward-Komponente auf das Ergebnis | ✅ |
| R2.7 | Failure Penalty im Verhältnis zur Reward-Skala begründen + Einfluss auf Policy-Lernen erklären | ✅ |
| R2.8 | Anzahl Trainingsläufe, Seed-Werte und Policy-Selection-Prozedur klar beschreiben | ✅ |
| R2.9 | Gleichwertige Hyperparameter-Optimierung für alle RL-Algorithmen (nicht nur PPO) | 🔬 |
| R2.10 | PPO-Optimierungsprozess beschreiben: Suchraum, Optimierungsmethode, Tuning | ✅ (grün, überschneidet R3.8) |
| R2.11 | Robustheitsevaluation: Störungen, Sensorrauschen, Parameterunsicherheiten, Aktuatorsättigungen | 🔬 |
| R2.12 | Lernkurven, Fehlerverteilungen und Vergleichs-Performance-Charts hinzufügen | ✅ |
| R2.13 | Boxplots, Violin Plots, Konfidenzintervalle zur Darstellung der Performance-Variabilität | ✅ |
| R2.14 | Fazit mit tatsächlichen experimentellen Ergebnissen abgleichen | ✅ |
| R2.15 | Grammar, Satzstruktur und Klarheit durch sorgfältiges Proofreading verbessern | ⬜ |
| R2.16 | Qualität aller Abbildungen verbessern | ⬜ |

---

## Reviewer 3 — Blau

| # | Kommentar | Status |
|---|-----------|--------|
| R3.1 | Novelty gegenüber bestehenden Arbeiten expliziter hervorheben, klare Abgrenzung zu Tab. 1 | ✅ |
| R3.2 | Simulationsannahmen explizit nennen und begründen (ideal sensing, kein Rauschen, perfekte Simulation) | ✅ |
| R3.3 | Vergleich mit anderen Simulationsplattformen (Gazebo, MuJoCo) — Vor-/Nachteile von MATLAB/Simulink | ⬜ |
| R3.4 | Sensitivitätsanalyse der Reward-Gewichte (= R2.5) | ✅ (grün, überschneidet R2.5) |
| R3.5 | Skalierbarkeit der Safety-Mechanismen für Onboard-Systeme diskutieren (Computational Overhead) | ✅ |
| R3.6 | Energie- und Smoothness-KPIs im Kontext von Weltraummissionen besser begründen | ✅ |
| R3.7 | Statistische Signifikanztests (Konfidenzintervalle, Hypothesentests) für Leistungsvergleiche | ✅ |
| R3.8 | Systematische Hyperparameter-Suche beschreiben (Grid Search, Bayesian Opt.) | ✅ (grün, überschneidet R2.10) |
| R3.9 | Sim-to-Real Transfer: Domain Shift, Onboard Compute, Hardware Constraints diskutieren | ✅ |
| R3.10 | Dichte Abschnitte (Reward-Gleichungen, KPI-Definitionen) leserfreundlicher gestalten | ✅ |
