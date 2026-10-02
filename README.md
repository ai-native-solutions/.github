# `ai-native-solutions/.github`

Meta-Repo der GitHub-Organisation **[AI Native Solutions](https://github.com/ai-native-solutions)**.

GitHub rendert [`profile/README.md`](profile/README.md) auf der [Organisationsseite](https://github.com/ai-native-solutions), **sobald dieses Repo öffentlich ist**. Bis dahin gilt diese Datei als interne Quelle für Konventionen, Workflow und offene Punkte.

Die Org ist das Software-Herz der Firma: Code, Templates und Deploy-Ideen an einem Ort. Was nicht in Git gehört, aber trotzdem für alle einsehbar sein soll, liegt außerhalb (siehe [Shared Workspace](#shared-workspace-kein-git)).

## Mitglieder

| Name    | GitHub                                                       | Org-Rolle | Hinweis                                                                    |
| ------- | ------------------------------------------------------------ | --------- | -------------------------------------------------------------------------- |
| Klemens | [@kwisser](https://github.com/kwisser)                       | Admin     | Konventionen, Repos anlegen/hochladen, Agent-Defaults                      |
| Lukas   | [@overhueslukas-hash](https://github.com/overhueslukas-hash) | Admin     | GitHub lernen, bestehende Repos lesen. Kontakt: `overhues.lukas@gmail.com` |

Team: [AI-Native](https://github.com/orgs/ai-native-solutions/teams/ai-native). Default-Branch überall: `main`.

## Produktbild (noch grob)

Das sind Absichten, keine fertigen Produkte. Platzhalter-Repos dürfen existieren, müssen aber eine ehrliche Description haben.

- **Organisation / Task Management** — Arbeit in der Firma planen und nachverfolgen.
- **Ideen-Dashboard** — Ideen erfassen, priorisieren, nicht verlieren.
- **Hosting / Deploy-Service** — Satz der Art _„publish App X auf Domain X“_. Interner Bezugspunkt: Hermes (GitHub Actions → Image-Registry → SSH → `docker compose` auf dem Host). Das ist die Zielrichtung, kein verbindliches Prod-Setup für jedes Repo.
- **Standard-Setup** — ja: auf einem Feature-Branch oder Template des anderen aufsetzen, statt jedes Projekt bei null zu beginnen. Dafür sind `template-*`-Repos und kurze, rebasbare Branches da.

## Repo-Konventionen

Jedes neue Repo in der Org folgt demselben äußeren Schnitt. Klemens pflegt das Schema; Abweichungen nur mit Begründung in der Description.

### Namen

Kleinbuchstaben, kebab-case, englische oder etablierte Produktwörter, keine Füllwörter wie `app` ohne Bedarf (bestehende `*-app`-Repos bleiben).

| Präfix / Form       | Bedeutung                        | Beispiel                   |
| ------------------- | -------------------------------- | -------------------------- |
| `{kunde}-{produkt}` | Kundenprojekt                    | `sartorius-gemba-walk-app` |
| `int-{produkt}`     | Internes Produkt                 | `int-bestattungssoftware`  |
| `template-{thema}`  | Startpunkt zum Forken / Kopieren | `template-int-agents`      |
| `.github`           | Nur dieses Meta-Repo             | —                          |

Keine Personennamen im Repo-Namen. Tippfehler in bestehenden Namen (`maintainance`) nicht stillschweigend „korrigieren“ — Rename ist ein eigener, abgesprochener Schritt.

### Pflichtinhalt eines Repos

| Datei                  | Für wen       | Muss                                    |
| ---------------------- | ------------- | --------------------------------------- |
| `README.md`            | Menschen      | Zweck, Start, Status, Link zum Tracking |
| `AGENTS.md`            | Coding-Agents | siehe unten                             |
| Description auf GitHub | Org-Übersicht | Ein Satz, auch bei Platzhaltern         |
| Default-Branch `main`  | alle          | immer                                   |

Optional, sobald es Code gibt: CI, Issue-/PR-Templates, `CODEOWNERS`. Secrets nie committen; Deploy-Geheimnisse liegen in GitHub Secrets / der Host-Env.

Private by default. Public nur, wenn das Produkt wirklich öffentlich sein soll — oder dieses `.github`-Repo, damit das Org-Profil erscheint.

### Wie ein Repo aussehen soll

```text
repo/
  README.md          # Menschen
  AGENTS.md          # Agents; repo-spezifisch, kurz
  src/ oder app/     # ein klarer Einstieg, kein gemischter Dump
  tests/
  .github/workflows/ # sobald es etwas zu prüfen oder zu deployen gibt
```

Frontend neuer Arbeit: React + TypeScript (z. B. Vite). Python: `uv`. Keine zweiten Stacks „nur zum Ausprobieren“ im selben Produkt-Repo.

## Branch-Namen

Abgeleitet von den [GitHub Branching Name Best Practices](https://dev.to/jps27cse/github-branching-name-best-practices-49ei), festgehalten als Org-Standard:

```text
<typ>/<issue>-<kurzbeschreibung>
```

`issue` ist die GitHub-Issue-Nummer, sobald es eine gibt. Sonst nur die Beschreibung.

| Präfix      | Wann                         |
| ----------- | ---------------------------- |
| `feature/`  | neue Funktion                |
| `bugfix/`   | Fehler, nicht dringend prod  |
| `hotfix/`   | dringender Prod-Fix          |
| `refactor/` | Struktur, gleiches Verhalten |
| `test/`     | Tests                        |
| `doc/`      | Dokumentation                |
| `design/`   | UI/UX ohne große Logik       |

Regeln:

- Bindestriche, keine Underscores oder CamelCase: `feature/12-add-login`
- Kurz und konkret. Verboten als ganzer Name: `update`, `changes`, `wip`, `klemi`, `lukas`
- Ein Branch = ein Thema. Große Arbeit in Issues / PRs schneiden
- `main` ist immer deploybar bzw. der stabile Stand. Kein Direktcommit auf `main`, sobald das Repo mehr als eine Person anfasst (Protect-Rules nachziehen)

Beispiele: `feature/4-gemba-checklist`, `bugfix/15-fix-date-display`, `doc/update-agents-md`, `hotfix/security-patch`.

## Entwicklungsworkflow

Leitplanken, an denen PRs und Agent-Sessions gemessen werden:

1. **Issue zuerst**, sobald die Arbeit mehr als ein klarer Einzeiler ist. PR verweist auf das Issue.
2. **Kleiner PR.** Ein Thema, reviewbar in einem Durchgang. Lieber zwei PRs als ein Sammel-Diff.
3. **Branch nach dem Schema oben**, von aktuellem `main`.
4. **Commit-Messages** beschreiben die Änderung (Imperativ, gern `type: summary`). Keine Platzhalter (`wip`, `d`, `update`). `git log --oneline` muss ohne Diff lesbar sein.
5. **Review.** Auch intern: zweite Person oder bewusst „self-merge nach Checkliste“, nie stilles Force-Push auf `main`.
6. **Issue schließen**, wenn erledigt (`gh issue close <n> --comment "Fixed: …"`).
7. **Auf dem Setup des anderen aufsetzen.** Template-Repo oder bestehenden Feature-Branch weiterbauen, statt Parallelwelt.
8. **Kein Secret im Git.** Kein `.env` committen. Zugänge in GitHub Secrets oder dem Shared Workspace, der nicht öffentlich ist.

CI und Protect-Rules kommen repo-weise, sobald dort regelmäßig Code landet. Dieses `.github`-Repo kann später Default-Community-Dateien für Repos ohne eigene Kopie liefern (`CONTRIBUTING`, Issue-Templates).

## Was in `AGENTS.md` steht

Jedes Produkt-Repo bekommt eine eigene `AGENTS.md`. Sie ist die Leitplanke für Coding-Agents (Pi, Claude Code, Codex, …) **in diesem Repo**. Globale Privatregeln der Entwickler (z. B. Klemens’ `~/.pi/agent/AGENTS.md`) gelten zusätzlich, ersetzen die Repo-Datei aber nicht.

**Rein:**

- Wofür das Repo da ist, in 2–3 Sätzen
- Stack und Tooling (`uv`, Node-Version, wie Tests/Lint starten)
- Befehle mit absoluten Erwartungen (`uv run pytest`, `npm test`) — keine „irgendwo im Wiki“-Verweise
- Repo-spezifische Konventionen (Ordner, API-Stil, was nicht angefasst werden darf)
- Wo Secrets herkommen (Name der GitHub Secrets / Env-Vars), nie die Werte
- Was „fertig“ bedeutet (Tests, Types, README-Satz)

**Raus:**

- Lebensläufe, Meeting-Notizen, Firmenstrategie
- Lange Tutorials (dafür `docs/` oder die menschliche README)
- Kopien der globalen Agent-Bibel, wenn sie hier nicht gelten
- Offene Todos der Firma (die stehen hier im `.github`-README oder im Ideen-Dashboard)

Faustregel: Ein Agent ohne Chat-Historie muss nach dem Lesen von `README.md` + `AGENTS.md` das Repo sinnvoll ändern können.

`template-int-agents` ist der Ort für eine vorgefüllte `AGENTS.md`, sobald das Template Inhalt hat. Neue Repos kopieren sie und streichen Unpassendes.

## Shared Workspace (kein Git)

GitHub ist für Code, Issues, PRs. Für Dateien, die nicht ins Repo gehören, aber **jede:r in der Firma** sehen können soll (Briefings, Zugänge-Übersicht ohne Secret-Werte, Folien, Verträge-Entwürfe):

**Offen:** gemeinsames Google Drive / Google Workspace — ja, das ist der bevorzugte Kandidat, solange nichts Besseres steht. Ordnerstruktur und Link fehlen noch; wer den Drive anlegt, verlinkt ihn hier.

Nicht ins Drive: Passwörter, private Keys, Kundendaten ohne Bedarf. Nicht nach Git: dieselben Dinge plus große Binaries und „mal eben“-Exports.

## Aktuelle Repos

| Repo                                                                                            | Stand                                                               |
| ----------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| [sartorius-gemba-walk-app](https://github.com/ai-native-solutions/sartorius-gemba-walk-app)     | Kundenprojekt, TypeScript — Guided Audit Shop Floor                 |
| [sartorius-maintainance-app](https://github.com/ai-native-solutions/sartorius-maintainance-app) | Kundenprojekt, TypeScript — Instandhaltung                          |
| [int-bestattungssoftware](https://github.com/ai-native-solutions/int-bestattungssoftware)       | intern, unsicher ob Start; Description nachziehen sobald Code kommt |
| [template-int-agents](https://github.com/ai-native-solutions/template-int-agents)               | Template, noch ohne Description                                     |
| [demo-repository](https://github.com/ai-native-solutions/demo-repository)                       | GitHub-Demo, kein Produkt — löschen oder klar als Sandbox labeln    |
| [.github](https://github.com/ai-native-solutions/.github)                                       | dieses Repo                                                         |

## Offene Todos

- [ ] **Klemens** — Maintenance-App: README (und `AGENTS.md`) so, dass jemand das Repo lesen und weiterbauen kann
- [ ] **Klemens** — restliche / eigene Repos in die Org legen, Description setzen, diesem Katalog nachziehen
- [ ] **Lukas** — GitHub-Grundlagen; bestehende Repos lesen (`overhues.lukas@gmail.com` ist der Kontakt dafür)
- [ ] Shared Drive anlegen und hier verlinken
- [ ] `.github` öffentlich machen, wenn die [Org-Seite](https://github.com/ai-native-solutions) das Profil zeigen soll
- [ ] `demo-repository`: behalten als Übungsrepo oder entfernen
- [ ] Branch protection auf Repos mit aktiver Entwicklung
- [ ] Org-Description auf GitHub setzen (steht noch leer)
