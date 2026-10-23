[🇬🇧 English](README.md) | 🇩🇪 **Deutsch**

# cABeman
Basiert auf caveman. Token-sparende Prompt-Dateien für **Claude Code** und **GitHub Copilot Chat** in VS Code.

> Deutsche Fassung von [birght/lesstokens_for_vscode](https://github.com/birght/lesstokens_for_vscode), erweitert um eine Claude-Code-Variante.

# Token-sparende Prompts für Claude Code und Copilot Chat

> Basiert auf den Ideen von [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman).  
> Die Idee zu diesem Paket stammt von [birght/lesstokens_for_vscode](https://github.com/birght/lesstokens_for_vscode) (die Prompt-Dateien für Copilot Chat, hier übersetzt) und [kuba-guzik/caveman-micro](https://github.com/kuba-guzik/caveman-micro) (das Micro-Regelwerk, das hier der Standard ist). Siehe [Quellen](#quellen).  
> Kernidee: Mit Prompts wird der Antwortstil des LLM eingeschränkt. Füllwörter und redundante Formulierungen fallen weg, die technische Substanz bleibt erhalten. Das spart ca. 65 % der Ausgabe-Tokens (Zahl aus der ursprünglichen Anleitung, hier nicht gemessen).

Das Paket gibt es in zwei Varianten. Beide folgen derselben Idee: ein kleines **dauerhaft aktives** Regelwerk (**Micro**) plus **Stufen auf Abruf**, wenn es knapper sein soll:

| Stufe | Stil |
|-------|------|
| 🤏 **Micro** (Standard, immer aktiv) | Regelwerk in sechs Zeilen: keine Artikel/Füllwörter/Höflichkeitsfloskeln, Fragmente erlaubt, Fachbegriffe exakt |
| 🪶 **Lite** | Füllwörter weg, Grammatik bleibt, professionell und knapp |
| 🪨 **Full** | Keine Artikel, Satzfragmente, klassischer Höhlenmenschen-Stil |
| 🔥 **Ultra** | Telegrammstil, maximale Kompression, Abkürzungen + Pfeile für Ursache und Wirkung |

`less_tokens.zip` enthält beide Varianten:

```
less_tokens.zip
├── claude-code/                    ← Claude Code
│   ├── rules/caveman.md            ← 🔄 immer aktiv (Micro)
│   └── skills/
│       ├── caveman-lite/SKILL.md   ← 📋 bei Bedarf: /caveman-lite
│       ├── caveman-full/SKILL.md
│       └── caveman-ultra/SKILL.md
└── copilot/                        ← GitHub Copilot Chat
    ├── prompts/
    │   ├── caveman-micro.instructions.md   ← 🔄 immer aktiv (Micro)
    │   ├── caveman-lite.prompt.md          ← 📋 bei Bedarf: /caveman-lite
    │   ├── caveman-full.prompt.md
    │   └── caveman-ultra.prompt.md
    └── .vscode/settings.json               ← optional
```

Für Claude Code liegen dieselben Skills und die Regel zusätzlich als normale Dateien in diesem Repository (`skills/` und `rules/`), damit Skill-Manager wie Skill-Dock sie direkt importieren können (siehe Option B).

---

# Teil 1: Claude Code

## 1.1 Wohin die Dateien kommen

Claude Code liest Dateien auf Benutzerebene aus dem Ordner `.claude` in deinem Home-Verzeichnis. Die Pfadlogik ist auf jedem System gleich (`~` ist dein Home-/Benutzerprofil-Ordner):

| Datei | Ziel |
|-------|------|
| `claude-code/rules/caveman.md` | `~/.claude/rules/caveman.md` (Windows: `%USERPROFILE%\.claude\rules\caveman.md`) |
| `claude-code/skills/caveman-<Stufe>/` | `~/.claude/skills/caveman-<Stufe>/SKILL.md` |

Beide Orte sind in der Claude-Code-Dokumentation beschrieben:
- **Rules:** Alle `.md`-Dateien in `~/.claude/rules/` gelten für jedes Projekt. Rules ohne `paths`-Front-Matter werden beim Start geladen.
- **Skills:** Ein persönlicher Skill `~/.claude/skills/<name>/SKILL.md` wird zum Slash-Befehl `/<name>`. Mit `disable-model-invocation: true` (in diesen Dateien gesetzt) läuft er nur, wenn du ihn eintippst; Claude lädt ihn nicht von selbst.

## 1.2 Einrichtung

Zwei Möglichkeiten. Option A installiert alles (Regel + Skills). Option B installiert nur die Skills, über eine VS-Code-Erweiterung; die dauerhafte Regel musst du weiterhin von Hand kopieren.

### Option A: Manuell aus dem ZIP kopieren (alles)

Entpacke `less_tokens.zip` zuerst irgendwo und kopiere dann die Dateien aus dem Ordner `claude-code`. Existieren die Zielordner schon, werden die Dateien hinzugefügt; gleichnamige Dateien werden überschrieben.

**Windows (PowerShell)**, im entpackten Ordner ausführen:

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\rules","$HOME\.claude\skills" | Out-Null
Copy-Item .\claude-code\rules\caveman.md "$HOME\.claude\rules\" -Force
Copy-Item .\claude-code\skills\* "$HOME\.claude\skills\" -Recurse -Force
```

**macOS / Linux (Shell)**, im entpackten Ordner ausführen:

```bash
mkdir -p ~/.claude/rules ~/.claude/skills
cp claude-code/rules/caveman.md ~/.claude/rules/
cp -r claude-code/skills/* ~/.claude/skills/
```

Oder von Hand im Dateimanager. Starte danach eine **neue Claude-Code-Sitzung**: Rules werden beim Start gelesen.

### Option B: Per URL mit einem Skill-Manager importieren (nur Skills)

Die Skills liegen zusätzlich als normale Dateien in diesem Repository (`skills/caveman-<Stufe>/SKILL.md`, ein Ordner pro Skill). Jedes Werkzeug, das Skills per GitHub-URL importiert, kann sie lesen. Ein bestimmtes Werkzeug ist nicht nötig.

1. Nimm einen Skill-Manager oder eine Erweiterung deiner Wahl, die Skills aus GitHub-Repositories in den Skill-Ordner deines Tools importiert (bei Claude Code: `~/.claude/skills/` oder `.claude/skills/` eines Projekts).
2. Füge dieses Repository als Quelle hinzu: `https://github.com/arboeh/cABeman`. Will das Werkzeug einen Link auf einen einzelnen Skill, nimm den Ordner, z. B. `https://github.com/arboeh/cABeman/tree/main/skills/caveman-full`.
3. Importiere die `caveman-*`-Skills.
4. Kopiere die dauerhafte Regel von Hand: [`rules/caveman.md`](rules/caveman.md) nach `~/.claude/rules/caveman.md` (Windows: `%USERPROFILE%\.claude\rules\caveman.md`). Skill-Manager installieren nur Skills.

Ein Beispiel ist die VS-Code-Erweiterung [Skill-Dock](https://github.com/yen0304/Skill-Dock): Laut ihrer README kann sie ein GitHub-Repository als Quelle hinzufügen (**Skill Dock: Add Marketplace Source** oder die Einstellung `skilldock.marketplaceSources`) und Skills für Claude Code importieren. Dieser Ablauf wurde für diese Anleitung nicht getestet.

> Das Format des Ordner-Links und ob ein Werkzeug die Einstellung `disable-model-invocation` dieser Skills beibehält, hängt vom Werkzeug ab. Prüfe nach dem Import die `SKILL.md`-Dateien; fehlt die Einstellung, kann Claude eine Stufe auch von sich aus laden. Option A umgeht diese Frage.

### Dauerhafte Regel (Micro)

`~/.claude/rules/caveman.md`:

```markdown
# Token efficiency
Respond like smart caveman. Cut all filler, keep technical substance.
- Drop articles (a, an, the), filler (just, really, basically, actually).
- Drop pleasantries (sure, certainly, happy to).
- No hedging. Fragments fine. Short synonyms.
- Technical terms stay exact. Code blocks unchanged.
- Pattern: [thing] [action] [reason]. [next step].
- Security warnings and irreversible actions: write normally, full sentences.
```

Wenn du lieber eine einzige Datei willst, füge den Text in `~/.claude/CLAUDE.md` ein, oder in die `CLAUDE.md` eines Projekts, damit er nur dort gilt.

## 1.3 Verwendung

- **Standard:** Nichts tun. Die Micro-Regel gilt in jeder Sitzung.
- **Stufe wechseln:** Tippe den Skill als Slash-Befehl, danach deine Frage:

```
/caveman-full Explain how Vue's nextTick works
/caveman-ultra What performance problems does this code have?
```

| Situation | Aktion | Wirkung |
|-----------|--------|---------|
| **Alltag (Standard)** | Nichts tun | `rules/caveman.md` wendet Micro an |
| **Lite** | `/caveman-lite` | Grammatik bleibt, Füllwörter weg |
| **Full** | `/caveman-full` | Caveman-Modus |
| **Ultra** | `/caveman-ultra` | Telegrammstil, maximale Kompression |
| **Stoppen** | „normal mode“ oder „stop concise“ sagen | Zurück zu normalen Antworten |

Wie lange eine Stufe innerhalb einer Sitzung aktiv bleibt, steht nicht in der Dokumentation; das entscheidet das Modell. Nenne die Stufe oder „normal mode“ erneut, wenn es abdriftet.

## 1.4 Standard oder Geltungsbereich ändern

- Eine andere Stufe als Dauerregel: Ersetze den Inhalt von `~/.claude/rules/caveman.md`. Benenne die Datei um, wenn der Name nicht mehr passt.
- Eine Regel auf bestimmte Dateien begrenzen, mit einem `paths`-Front-Matter (Claude Code lädt sie, wenn es an passenden Dateien arbeitet):

```markdown
---
paths:
  - "src/**"
---
```

---

# Teil 2: GitHub Copilot Chat

## 2.1 Dateiliste

Alle Dateien liegen im **Benutzerverzeichnis von VS Code**. Sie gelten projektübergreifend und verschmutzen kein Repository.

```
<VS-Code-Benutzerdaten>/Code/User/prompts/
├── caveman-micro.instructions.md  ← 🔄 Automatisch eingebunden (gilt in jedem Chat)
├── caveman-lite.prompt.md         ← 📋 Bei Bedarf: 🪶 Lite
├── caveman-full.prompt.md         ← 📋 Bei Bedarf: 🪨 Full
└── caveman-ultra.prompt.md        ← 📋 Bei Bedarf: 🔥 Ultra
```

> Der Ordner `prompts` liegt im Benutzerdatenverzeichnis von VS Code (`Code/User`):
>
> | System | Pfad |
> |--------|------|
> | Windows | `%APPDATA%\Code\User\prompts\` (also `C:\Users\{Benutzername}\AppData\Roaming\Code\User\prompts\`) |
> | macOS | `$HOME/Library/Application Support/Code/User/prompts/` |
> | Linux | `$HOME/.config/Code/User/prompts/` |
>
> Die VS-Code-Dokumentation bestätigt das Verzeichnis `Code/User` für jedes System (dort liegt die Benutzer-`settings.json`). Dass der Unterordner `prompts` darin liegt, stammt aus der ursprünglichen Anleitung; die Doku sagt nur, dass Prompt-Dateien auf Benutzerebene in den Benutzerdaten des Profils gespeichert werden. Andere VS-Code-Editionen (z. B. Insiders, das `Code - Insiders` nutzt) können einen anderen Ordner verwenden.
>
> Prompt-Dateien auf Benutzerebene gehören zu deinem **VS-Code-Profil**. Arbeitest du mit mehreren Profilen, hat jedes Profil seinen eigenen Satz, die Dateien müssen also in dem Profil abgelegt werden, das du nutzen willst.

| Datei | Typ | Zweck |
|-------|-----|-------|
| `caveman-micro.instructions.md` | Automatisch eingebunden | `applyTo: "**"` trifft auf alle Dateien zu, wirkt also **automatisch in jedem Copilot-Chat** |
| `caveman-lite.prompt.md` | Bei Bedarf | 🪶 Füllwörter weg, Grammatik bleibt, professionell und knapp |
| `caveman-full.prompt.md` | Bei Bedarf | 🪨 Keine Artikel, Satzfragmente, klassischer Höhlenmenschen-Stil |
| `caveman-ultra.prompt.md` | Bei Bedarf | 🔥 Telegrammstil, maximale Kompression, Abkürzungen + Pfeile für Ursache und Wirkung |

**Unterschied zwischen den beiden Dateitypen**:
- `.instructions.md`: wird automatisch eingebunden; `applyTo` wählt Dateitypen aus, kein manuelles Auslösen nötig
- `.prompt.md`: wird bei Bedarf gewählt; wirkt nur, wenn du sie im Chat-Eingabefeld ausdrücklich ausführst

**Warum nur eine Stufe automatisch eingebunden wird**:
- Eine Datei mit `applyTo: "**"` gilt für jeden Chat und jeden Dateityp, die Stufe muss also risikoarm sein. Micro ist das mildeste Regelwerk, das noch Tokens spart.
- Stärkere Stufen (Full, Ultra) können Erklärungen mehrdeutig oder verwirrend machen.
- Mehrere `.instructions.md`-Dateien mit `applyTo: "**"` würden alle gleichzeitig eingebunden. Welche bei einem Konflikt gewinnt, ist nicht garantiert, das Umschalten pro Chat mit einem Slash-Befehl wäre dann nicht mehr zuverlässig.

**Standardstufe ändern**:
- Ersetze den Inhalt von `caveman-micro.instructions.md` durch eine andere Stufe. Benenne die Datei dann um, damit Name und Inhalt zusammenpassen.
- Eine strengere Stufe nur für bestimmte Dateien: Geltungsbereich einschränken, z. B. `applyTo: "**/*.md"` oder `applyTo: "src/**"`.
- Nur für ein einzelnes Projekt: Lege die Regeln stattdessen in `.github/copilot-instructions.md`.

## 2.2 Einrichtung

### Schritt 1: Verzeichnis anlegen

Lege den Ordner `prompts` aus der Tabelle oben an, falls er noch nicht existiert. Existiert er schon, überspringe diesen Schritt; vorhandene Dateien darin bleiben unberührt. Möglich ist jeweils:

- **Dateimanager:** Ordner von Hand anlegen (Explorer, Finder oder dein Linux-Dateimanager).
- **VS Code:** Führe in der Befehlspalette **Chat: New Prompt File** aus und wähle den Ort für die Benutzerebene (Profil). VS Code legt die Datei am richtigen Ort für das aktive Profil an, so siehst du, wo sie landet.
- **Windows (PowerShell):** `New-Item -ItemType Directory -Force "$env:APPDATA\Code\User\prompts"`
- **macOS / Linux (Shell):** `mkdir -p` mit dem Pfad aus der Tabelle, z. B. `mkdir -p "$HOME/.config/Code/User/prompts"` unter Linux

Durch `-Force` und `-p` laufen die Befehle ohne Fehler durch, wenn der Ordner schon existiert.

### Schritt 2: Automatisch eingebundene Datei kopieren (der Kern; wirkt sofort)

Dieser Schritt ist manuell: Entpacke `less_tokens.zip` und kopiere `copilot/prompts/caveman-micro.instructions.md` in den Ordner `prompts`, oder lege die Datei selbst mit diesem Inhalt an:

```markdown
---
applyTo: "**"
---

# Token efficiency
Respond like smart caveman. Cut all filler, keep technical substance.
- Drop articles (a, an, the), filler (just, really, basically, actually).
- Drop pleasantries (sure, certainly, happy to).
- No hedging. Fragments fine. Short synonyms.
- Technical terms stay exact. Code blocks unchanged.
- Pattern: [thing] [action] [reason]. [next step].
- Security warnings and irreversible actions: write normally, full sentences.
```

### Schritt 3 (optional): Stufen-Dateien kopieren

Kopiere die 4 `.prompt.md`-Dateien aus `copilot/prompts/` in denselben Ordner. Ihre Inhalte stehen im Anhang.

### Schritt 4 (optional): settings.json

Instructions und Prompt Files im Benutzerverzeichnis funktionieren in aktuellen VS-Code-Versionen ohne zusätzliche Einstellung. Sinnvoll ist höchstens die Anfragegrenze für den Agent-Modus (Standard: 25). Trage sie in die `.vscode/settings.json` des Projekts oder in deine Benutzereinstellungen ein:

```jsonc
{
  // Max. Anfragen pro Sitzung im Agent-Modus (Standard: 25)
  "chat.agent.maxRequests": 50
}
```

> Die ursprüngliche Anleitung setzte außerdem `chat.promptFiles` und `chat.useAgentSkills`. Laut aktueller VS-Code-Dokumentation ist `chat.promptFiles` keine dokumentierte Einstellung, und `chat.useAgentSkills` (Standard: `true`) schaltet nur **Agent Skills** (`SKILL.md`-Ordner) ein. Das ist ein eigenes Feature und hat mit `.prompt.md`-Dateien nichts zu tun, deshalb werden beide Einstellungen hier nicht mehr gebraucht.

## 2.3 Verwendung

### Standardverhalten (automatisch)

`caveman-micro.instructions.md` wird über `applyTo: "**"` global eingebunden und **wirkt, sobald du irgendeinen Copilot-Chat öffnest**. Du musst nichts tun.

### Prompt-Dateien mit `/` ausführen (bei Bedarf hochschalten)

Tippe im Copilot-Chat-Eingabefeld `/` und den Namen des Prompts, um eine `.prompt.md`-Datei als Slash-Befehl auszuführen:

```
Schritte:
1. Im Chat-Eingabefeld / tippen
2. Den Prompt aus der Liste wählen (oder den Namen weitertippen, z. B. /caveman-full)
3. Frage hinter den Befehl schreiben und absenden
```

Alternativen: **Chat: Run Prompt** in der Befehlspalette ausführen oder die `.prompt.md`-Datei öffnen und oben im Editor auf die Wiedergabe-Schaltfläche klicken.

> `#` ist **nicht** der Weg, um eine Prompt-Datei auszuführen. In VS Code dient `#` dazu, Kontext hinzuzufügen, z. B. `#file:` oder `#tool:`.

**Beispiele**:

```
/caveman-full Explain how Vue's nextTick works
/caveman-ultra What performance problems does this code have?
```

> Eine per `/` ausgeführte `.prompt.md` ist für den Chat gedacht, in dem du sie ausführst. In einem neuen Chat gilt wieder die Standardstufe Micro, solange du dort nicht ebenfalls einen Prompt ausführst. Die Doku sagt nicht, ob Prompt-Dateien auf Benutzerebene immer in der `/`-Liste erscheinen; fehlt eine, nutze **Chat: Run Prompt**.

| Situation | Aktion | Wirkung |
|-----------|--------|---------|
| **Alltag (Standard)** | Nichts tun | `.instructions.md` bindet Micro automatisch ein |
| **Lite** | `/caveman-lite` tippen | Grammatik bleibt, Füllwörter weg |
| **Full** | `/caveman-full` tippen | Aktueller Chat wechselt in den Caveman-Modus |
| **Ultra** | `/caveman-ultra` tippen | Telegrammstil, maximale Kompression |
| **Stoppen** | Im Chat „normal mode“ oder „stop concise“ sagen | Zurück zu normalen Antworten |

---

# Vergleich

Dieselbe Frage: „Unterschied zwischen Vue ref und reactive“

| Stufe | Beispielantwort |
|-------|-----------------|
| **Ohne Caveman** | „Gern! `ref` und `reactive` sind beide Reaktivitäts-Werkzeuge in der Composition API von Vue 3. Einfach gesagt wird `ref` hauptsächlich für primitive Werte verwendet und verpackt den Wert in ein Objekt mit der Eigenschaft `.value` …“ |
| **🪶 Lite** | „`ref` verpackt Primitive, Zugriff über `.value`. `reactive` ist für Objekte, Eigenschaften direkt ansprechen. Wahl: ref für Primitive, reactive für Objekte.“ |
| **🪨 Full** | „`ref` pack Primitiv, brauch `.value`. `reactive` für Objekt, direkter Zugriff. Primitiv → ref, Objekt → reactive.“ |
| **🔥 Ultra** | „`ref` → Primitiv + `.value`. `reactive` → Obj, direkter Zugriff.“ |

Diese Beispielantworten stammen aus der ursprünglichen Anleitung und wurden ins Deutsche übertragen, nicht neu erzeugt. Mit den englischen Prompt-Dateien antwortet das Modell je nach Frage auf Englisch oder Deutsch.

---

# Hinweise

1. Dauerhaft aktive Dateien (`rules/caveman.md`, `*.instructions.md`) wirken automatisch. Stufen-Dateien (Skills, `.prompt.md`) laufen nur, wenn du ihren Slash-Befehl eintippst.
2. Alle Dateien liegen in Ordnern auf Benutzerebene und gelten **projektübergreifend**, die Einrichtung muss nicht in jedem Repository wiederholt werden.
3. Bei Sicherheitswarnungen und unumkehrbaren Aktionen soll die Ausgabe zu normaler Formulierung zurückkehren, damit wichtige Informationen nicht untergehen.
4. Die Regeln sind für englische Antworten geschrieben. Fragst du in einer anderen Sprache, überträgt sich der Stil meist trotzdem, die Wortlisten für Füllwörter passen aber weniger genau.

---

# Quellen

- [birght/lesstokens_for_vscode](https://github.com/birght/lesstokens_for_vscode): die ursprüngliche Idee und die Prompt-Dateien für Copilot Chat (Lite, Full, Ultra, Wenyan, auf Chinesisch). Dieses Repository ist eine englische und deutsche Übersetzung, erweitert um eine Claude-Code-Variante. Das ursprüngliche Repository hat keine Lizenzdatei.
- [kuba-guzik/caveman-micro](https://github.com/kuba-guzik/caveman-micro) (MIT): das Micro-Regelwerk, das hier als dauerhaft aktiver Standard verwendet wird.
- [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman): der Caveman-Stil, auf dem diese Prompts basieren.

---

# Anhang: Vollständige Dateiinhalte

Alle Dateien sind bewusst auf Englisch und im ZIP identisch mit den Inhalten hier.

## Claude Code

### claude-code/rules/caveman.md

````markdown
# Token efficiency
Respond like smart caveman. Cut all filler, keep technical substance.
- Drop articles (a, an, the), filler (just, really, basically, actually).
- Drop pleasantries (sure, certainly, happy to).
- No hedging. Fragments fine. Short synonyms.
- Technical terms stay exact. Code blocks unchanged.
- Pattern: [thing] [action] [reason]. [next step].
- Security warnings and irreversible actions: write normally, full sentences.
````

### claude-code/skills/caveman-lite/SKILL.md

````markdown
---
name: caveman-lite
description: "🪨 Caveman Lite — Concise mode: cut filler, keep grammar, professional but tight"
disable-model-invocation: true
---

Reply concisely and efficiently. Keep all technical substance; remove only the fluff.

## Rules

Remove: filler words (just/really/basically/simply put/in fact), pleasantries (sure thing/happy to help/no problem), hedging.
Keep full grammar and articles. Keep sentences short and punchy. Prefer short synonyms (big, not extensive; fix, not implement a solution).
Technical terms stay exact. Code blocks stay unchanged. Quote error messages exactly.

Pattern: `[thing] [action] [reason]. [next step].`

Not: "Sure, I'd be happy to help you with that. The issue you're experiencing is most likely caused by..."
Yes: "Auth middleware has a bug. Token expiry check uses `<` instead of `<=`. Fix:"

## Auto-restore clarity

Pause concise mode and use normal wording for: security warnings, confirmation of irreversible operations, multi-step sequences where fragmented phrasing could be misread, and when the user seems confused. Resume concise mode once the clear part is done.

## Boundaries

Code/commits/PRs: write normally. Revert when the user says "stop concise" or "normal mode". The level persists until switched or the session ends.
````

### claude-code/skills/caveman-full/SKILL.md

````markdown
---
name: caveman-full
description: "🪨 Caveman Full — Caveman mode: no articles, fragments, classic caveman style"
disable-model-invocation: true
---

Reply lean, like a smart caveman. All technical substance stay. Only fluff die.

## Rules

Drop: articles (a/an/the), filler (just/really/basically/simply put), pleasantries (sure/no problem/happy to help), hedging.
Fragments OK. Short synonyms (big not extensive, fix not "implement a solution"). Technical terms exact. Code blocks unchanged. Errors quoted exact.

Pattern: `[thing] [action] [reason]. [next step].`

Not: "Sure, I'd be happy to help you with that. The issue you're experiencing is most likely caused by..."
Yes: "Bug in auth middleware. Token expiry check use `<` not `<=`. Fix:"

## Intensity examples

Q: "Why does my React component re-render?"
A: "New object ref each render. Inline object prop = new ref = re-render. Wrap in `useMemo`."

Q: "Explain database connection pooling."
A: "Pool reuse open DB connections. No new connection per request. Skip handshake overhead."

## Auto-restore

Security warnings, irreversible-operation confirmations, user confusion: drop caveman, speak normally. Resume after clear part done.

## Boundaries

Code/commits/PRs: write normally. "Stop caveman" or "normal mode": revert. Level persists until switched or session ends.
````

### claude-code/skills/caveman-ultra/SKILL.md

````markdown
---
name: caveman-ultra
description: "🔥 Caveman Ultra — Maximum compression: telegraphic, abbreviate everything, arrow causality"
disable-model-invocation: true
---

Compress replies to the limit. Keep all technical substance. Cut words ruthlessly.

## Rules

Abbreviate everything (DB/auth/config/req/res/fn/impl/env/dep/pkg/init/param/cb).
Cut conjunctions. Use arrows for causality (X → Y). One word when one word enough.
Technical terms exact. Code blocks unchanged. Errors quoted exact.

Pattern: `[abbrev] → [result]. [action].`

Not: "The component re-renders because a new object reference is created on every render"
Yes: "Inline obj prop → new ref → re-render. `useMemo`."

## Examples

Q: "Explain database connection pooling"
A: "Pool = reuse DB conn. Skip handshake → fast under load."

Q: "Fix auth middleware token expiry"
A: "L42: token check `<` → `<=`. Off by 1s at expiry."

## Auto-restore

Security warnings/irreversible operations: speak normally. Resume ultra afterwards.

## Boundaries

Code written normally. "Stop": revert.
````

## GitHub Copilot Chat

### copilot/prompts/caveman-micro.instructions.md

````markdown
---
applyTo: "**"
---

# Token efficiency
Respond like smart caveman. Cut all filler, keep technical substance.
- Drop articles (a, an, the), filler (just, really, basically, actually).
- Drop pleasantries (sure, certainly, happy to).
- No hedging. Fragments fine. Short synonyms.
- Technical terms stay exact. Code blocks unchanged.
- Pattern: [thing] [action] [reason]. [next step].
- Security warnings and irreversible actions: write normally, full sentences.
````

### copilot/prompts/caveman-lite.prompt.md

````markdown
---
description: "🪨 Caveman Lite — Concise mode: cut filler, keep grammar, professional but tight"
agent: ask
---

Reply concisely and efficiently. Keep all technical substance; remove only the fluff.

## Rules

Remove: filler words (just/really/basically/simply put/in fact), pleasantries (sure thing/happy to help/no problem), hedging.
Keep full grammar and articles. Keep sentences short and punchy. Prefer short synonyms (big, not extensive; fix, not implement a solution).
Technical terms stay exact. Code blocks stay unchanged. Quote error messages exactly.

Pattern: `[thing] [action] [reason]. [next step].`

Not: "Sure, I'd be happy to help you with that. The issue you're experiencing is most likely caused by..."
Yes: "Auth middleware has a bug. Token expiry check uses `<` instead of `<=`. Fix:"

## Auto-restore clarity

Pause concise mode and use normal wording for: security warnings, confirmation of irreversible operations, multi-step sequences where fragmented phrasing could be misread, and when the user seems confused. Resume concise mode once the clear part is done.

## Boundaries

Code/commits/PRs: write normally. Revert when the user says "stop concise" or "normal mode". The level persists until switched or the session ends.
````

### copilot/prompts/caveman-full.prompt.md

````markdown
---
description: "🪨 Caveman Full — Caveman mode: no articles, fragments, classic caveman style"
agent: ask
---

Reply lean, like a smart caveman. All technical substance stay. Only fluff die.

## Rules

Drop: articles (a/an/the), filler (just/really/basically/simply put), pleasantries (sure/no problem/happy to help), hedging.
Fragments OK. Short synonyms (big not extensive, fix not "implement a solution"). Technical terms exact. Code blocks unchanged. Errors quoted exact.

Pattern: `[thing] [action] [reason]. [next step].`

Not: "Sure, I'd be happy to help you with that. The issue you're experiencing is most likely caused by..."
Yes: "Bug in auth middleware. Token expiry check use `<` not `<=`. Fix:"

## Intensity examples

Q: "Why does my React component re-render?"
A: "New object ref each render. Inline object prop = new ref = re-render. Wrap in `useMemo`."

Q: "Explain database connection pooling."
A: "Pool reuse open DB connections. No new connection per request. Skip handshake overhead."

## Auto-restore

Security warnings, irreversible-operation confirmations, user confusion: drop caveman, speak normally. Resume after clear part done.

## Boundaries

Code/commits/PRs: write normally. "Stop caveman" or "normal mode": revert. Level persists until switched or session ends.
````

### copilot/prompts/caveman-ultra.prompt.md

````markdown
---
description: "🔥 Caveman Ultra — Maximum compression: telegraphic, abbreviate everything, arrow causality"
agent: ask
---

Compress replies to the limit. Keep all technical substance. Cut words ruthlessly.

## Rules

Abbreviate everything (DB/auth/config/req/res/fn/impl/env/dep/pkg/init/param/cb).
Cut conjunctions. Use arrows for causality (X → Y). One word when one word enough.
Technical terms exact. Code blocks unchanged. Errors quoted exact.

Pattern: `[abbrev] → [result]. [action].`

Not: "The component re-renders because a new object reference is created on every render"
Yes: "Inline obj prop → new ref → re-render. `useMemo`."

## Examples

Q: "Explain database connection pooling"
A: "Pool = reuse DB conn. Skip handshake → fast under load."

Q: "Fix auth middleware token expiry"
A: "L42: token check `<` → `<=`. Off by 1s at expiry."

## Auto-restore

Security warnings/irreversible operations: speak normally. Resume ultra afterwards.

## Boundaries

Code written normally. "Stop": revert.
````
