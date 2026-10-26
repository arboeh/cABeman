[🇬🇧 English](README.md) | 🇩🇪 **Deutsch**

# cABeman

Token-sparende Prompt-Dateien für **Claude Code** und **GitHub Copilot Chat**. Basiert auf [caveman](https://github.com/JuliusBrussee/caveman): Antwortstil einschränken, Füllwörter weg, technische Substanz bleibt.

Ein kleines **dauerhaft aktives** Regelwerk (Micro) plus **Stufen auf Abruf**, wenn es knapper sein soll:

| Stufe | Stil |
|-------|------|
| 🤏 **Micro** (Standard, immer aktiv) | Sechs Zeilen: keine Artikel/Füllwörter/Floskeln, Fragmente erlaubt, Fachbegriffe exakt |
| 🪶 **Lite** | Füllwörter weg, Grammatik bleibt, professionell und knapp |
| 🪨 **Full** | Keine Artikel, Satzfragmente, klassischer Höhlenmenschen-Stil |
| 🔥 **Ultra** | Telegrammstil, Abkürzungen, Pfeile für Ursache und Wirkung |

## Aufbau

```
rules/caveman.md                       Claude Code, immer aktiv (Micro)
skills/caveman-{lite,full,ultra}/      Claude Code, bei Bedarf (/caveman-full ...)
copilot/prompts/                       Copilot Chat: caveman-micro.instructions.md + 3 .prompt.md-Stufen
docs/                                  Ausführliche Anleitungen (englisch)
```

## Installation

**Claude Code** (Repo klonen oder herunterladen, im Repo ausführen):

```bash
mkdir -p ~/.claude/rules ~/.claude/skills
cp rules/caveman.md ~/.claude/rules/
cp -r skills/* ~/.claude/skills/
```

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\rules","$HOME\.claude\skills" | Out-Null
Copy-Item .\rules\caveman.md "$HOME\.claude\rules\" -Force
Copy-Item .\skills\* "$HOME\.claude\skills\" -Recurse -Force
```

Neue Sitzung starten: Regeln werden beim Start gelesen.

**Copilot Chat:** `copilot/prompts/*` in den `prompts`-Ordner des VS-Code-Benutzerverzeichnisses kopieren (Windows: `%APPDATA%\Code\User\prompts\`, macOS: `~/Library/Application Support/Code/User/prompts/`, Linux: `~/.config/Code/User/prompts/`).

Details, Skill-Manager-Import, Standardstufe ändern: [docs/claude-code.md](docs/claude-code.md), [docs/copilot.md](docs/copilot.md) (englisch).

## Nutzung

Micro gilt automatisch. Stufe wechseln per Slash-Befehl:

```
/caveman-full Erkläre, wie Vues nextTick funktioniert
/caveman-ultra Welche Performance-Probleme hat dieser Code?
```

"normal mode" oder "stop concise" sagen, um zu normalen Antworten zurückzukehren.

## Beispiel

"Unterschied zwischen Vue ref und reactive":

| Stufe | Antwort |
|-------|---------|
| Ohne caveman | "Gerne! `ref` und `reactive` sind beides Reaktivitäts-Werkzeuge der Composition API von Vue 3. Vereinfacht gesagt wird `ref` vor allem für primitive Werte genutzt…" |
| 🪶 Lite | "`ref` verpackt Primitive, Zugriff über `.value`. `reactive` für Objekte, direkter Zugriff." |
| 🪨 Full | "`ref` wrap Primitive, braucht `.value`. `reactive` für Objekt, direkter Zugriff." |
| 🔥 Ultra | "`ref` → Primitive + `.value`. `reactive` → Obj, direkter Zugriff." |

Beispiele sind illustrativ, nicht gemessen. Die Angabe "ca. 65 % weniger Ausgabe-Tokens" aus der ursprünglichen Anleitung wurde hier ebenfalls nicht gemessen.

## Hinweise

- Sicherheitswarnungen und unumkehrbare Aktionen werden wieder normal formuliert, damit nichts Kritisches verschleiert wird.
- Die Regeln sind für englische Antworten geschrieben; bei anderen Sprachen greift der Stil weniger genau.

## Quellen

- [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman): der caveman-Stil.
- [kuba-guzik/caveman-micro](https://github.com/kuba-guzik/caveman-micro) (MIT): das Micro-Regelwerk.
- [birght/lesstokens_for_vscode](https://github.com/birght/lesstokens_for_vscode): ursprüngliche Copilot-Chat-Prompts (Lite, Full, Ultra), hier übersetzt. Das Repository hat keine Lizenzdatei.

## Lizenz

[MIT](LICENSE) für den Inhalt dieses Repositorys, ausgenommen das oben genannte Material Dritter.
