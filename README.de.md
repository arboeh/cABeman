[🇬🇧 English](README.md) | 🇩🇪 **Deutsch**

# lesstokens_for_vscode
Basiert auf caveman. Globale Prompt-Dateien für VS Code.

> Deutsche Übersetzung von [birght/lesstokens_for_vscode](https://github.com/birght/lesstokens_for_vscode).



# Token-sparende Prompts für Copilot Chat

> Basiert auf den Ideen von [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman), angepasst für VS Code GitHub Copilot Chat.  
> Kernidee: Mit Prompts auf Systemebene wird der Antwortstil des LLM eingeschränkt. Füllwörter und redundante Formulierungen fallen weg, die gesamte technische Substanz bleibt erhalten. Das spart ca. 65 % der Ausgabe-Tokens.

---

## 1. Dateiliste

Alle Dateien liegen im **Benutzerverzeichnis von VS Code**. Sie gelten projektübergreifend und verschmutzen kein Repository.

```
%APPDATA%\Code\User\prompts\
├── caveman-lite.instructions.md   ← 🔄 Automatisch eingebunden (gilt in jedem Chat)
├── caveman-lite.prompt.md         ← 📋 Bei Bedarf: 🪶 Lite
├── caveman-full.prompt.md         ← 📋 Bei Bedarf: 🪨 Full
├── caveman-ultra.prompt.md        ← 📋 Bei Bedarf: 🔥 Ultra
├── caveman-micro.prompt.md        ← 📋 Bei Bedarf: 🤏 Micro
└── caveman-wenyan.prompt.md       ← 📋 Bei Bedarf: 📜 Wenyan (Klassisches Chinesisch)
```

> Windows-Pfad: `C:\Users\{Benutzername}\AppData\Roaming\Code\User\prompts\`

| Datei | Typ | Zweck |
|-------|-----|-------|
| `caveman-lite.instructions.md` | Automatisch eingebunden | `applyTo: "**"` trifft auf alle Dateien zu, wirkt also **automatisch in jedem Copilot-Chat** |
| `caveman-lite.prompt.md` | Bei Bedarf | 🪶 Füllwörter weg, Grammatik bleibt, professionell und knapp |
| `caveman-full.prompt.md` | Bei Bedarf | 🪨 Keine Artikel, Satzfragmente, klassischer Höhlenmenschen-Stil |
| `caveman-ultra.prompt.md` | Bei Bedarf | 🔥 Telegrammstil, maximale Kompression, Abkürzungen + Pfeile für Ursache und Wirkung |
| `caveman-micro.prompt.md` | Bei Bedarf | 🤏 Minimales Regelwerk in sechs Zeilen, die ganze Caveman-Idee kompakt |
| `caveman-wenyan.prompt.md` | Bei Bedarf | 📜 Klassisch-chinesische Kompression, 80-90 % weniger Zeichen |

**Unterschied zwischen den beiden Dateitypen**:
- `.instructions.md`: wird automatisch eingebunden; `applyTo` wählt Dateitypen aus, kein manuelles Auslösen nötig
- `.prompt.md`: wird bei Bedarf gewählt; wirkt nur, wenn du die Datei im Chat-Eingabefeld ausdrücklich referenzierst

**Warum nur Lite automatisch eingebunden wird**:
- Eine Datei mit `applyTo: "**"` gilt für jeden Chat und jeden Dateityp, die Stufe muss also risikoarm sein. Lite behält die normale Grammatik und bleibt in fachlichen Diskussionen gut lesbar.
- Stärkere Stufen (Full, Ultra, Wenyan) können Erklärungen mehrdeutig oder verwirrend machen, und Wenyan würde dauerhaft auf Klassisch-Chinesisch antworten.
- Mehrere `.instructions.md`-Dateien mit `applyTo: "**"` würden alle gleichzeitig eingebunden. Welche bei einem Konflikt gewinnt, ist nicht garantiert, das Umschalten pro Chat mit `#` wäre dann nicht mehr zuverlässig.

**Standardstufe ändern**:
- Ersetze den Inhalt von `caveman-lite.instructions.md` durch eine andere Stufe, z. B. die Regeln aus `caveman-micro`. Benenne die Datei dann um (z. B. `caveman-default.instructions.md`), damit Name und Inhalt zusammenpassen.
- Eine strengere Stufe nur für bestimmte Dateien: Geltungsbereich einschränken, z. B. `applyTo: "**/*.md"` oder `applyTo: "src/**"`.
- Nur für ein einzelnes Projekt: Lege die Regeln stattdessen in `.github/copilot-instructions.md` (siehe Tipp in Abschnitt 3).

**Hinweis zur Sprache**: Die Prompt-Dateien sind auf Englisch geschrieben. Ihre Regeln (Artikel weglassen, englische Füllwörter streichen) zielen auf englische Antworten. Fragst du auf Deutsch, antwortet Copilot in der Regel auch auf Deutsch, aber die Wirkung der Regeln ist dann weniger genau, weil deutsche Füllwörter (z. B. „eigentlich“, „im Grunde“) nicht aufgezählt sind. Für Deutsch kannst du die Wortlisten in den Dateien anpassen.

---

## 2. Einrichtung

### Schritt 1: Verzeichnis anlegen

```powershell
mkdir "$env:APPDATA\Code\User\prompts"
```

### Schritt 2: Automatisch eingebundene Datei anlegen (der Kern; wirkt sofort)

Lege `caveman-lite.instructions.md` an:

```markdown
---
applyTo: "**"
---

Reply concisely and efficiently. Drop filler words (just/really/basically/simply put), pleasantries (sure thing/happy to help), and hedging. Keep sentences short and punchy; prefer short synonyms. Keep technical terms exact and code blocks unchanged. Pattern: [thing] [action] [reason]. [next step]. Return to normal wording for security warnings and irreversible operations.
```

### Schritt 3 (optional): Stufen-Dateien für den Bedarfsfall anlegen

Lege die 5 `.prompt.md`-Dateien im selben Verzeichnis ab. Ihre Inhalte stehen im Anhang dieses Dokuments.

### Schritt 4 (optional): settings.json

Instructions und Prompt Files im Benutzerverzeichnis funktionieren in aktuellen VS-Code-Versionen ohne zusätzliche Einstellung. Sinnvoll ist höchstens die Anfragegrenze für den Agent-Modus (Standard: 25). Trage sie in die `.vscode/settings.json` des Projekts oder in deine Benutzereinstellungen ein:

```jsonc
{
  // Max. Anfragen pro Sitzung im Agent-Modus (Standard: 25)
  "chat.agent.maxRequests": 50
}
```

> Die ursprüngliche Anleitung setzte außerdem `chat.promptFiles` und `chat.useAgentSkills`. Laut aktueller VS-Code-Dokumentation ist `chat.promptFiles` keine dokumentierte Einstellung, und `chat.useAgentSkills` (Standard: `true`) schaltet nur **Agent Skills** (`SKILL.md`-Ordner) ein. Das ist ein eigenes Feature und hat mit `.prompt.md`-Dateien nichts zu tun, deshalb werden beide Einstellungen hier nicht mehr gebraucht.

---

## 3. Verwendung

### Standardverhalten (automatisch)

`caveman-lite.instructions.md` wird über `applyTo: "**"` global eingebunden und **wirkt, sobald du irgendeinen Copilot-Chat öffnest**. Du musst nichts tun.

### Prompt-Dateien mit `#` referenzieren (bei Bedarf hochschalten)

Im Copilot-Chat-Eingabefeld kannst du `.prompt.md`-Dateien mit dem Befehl `#` referenzieren:

```
# Schritte:
1. Im Chat-Eingabefeld # tippen
2. In der Liste "Prompt..." wählen (oder den Dateinamen tippen, um unscharf zu suchen)
3. Die .prompt.md-Datei der gewünschten Stufe auswählen
4. Frage eingeben und absenden
```

**Beispiele**:

```
#caveman-full Explain how Vue's nextTick works
```

```
#caveman-ultra What performance problems does this code have?
```

```
#caveman-wenyan pinia vs vuex
```

> Eine per `#` referenzierte `.prompt.md` gilt nur für die **aktuelle Unterhaltung** und beeinflusst andere nicht. Nach dem Schließen der Unterhaltung gilt wieder die Standardstufe Lite.

### Schnellübersicht

| Situation | Aktion | Wirkung |
|-----------|--------|---------|
| **Alltag (Standard)** | Nichts tun | `.instructions.md` bindet die Lite-Stufe automatisch ein |
| **Auf Full hochschalten** | `#` tippen → `caveman-full` wählen | Aktuelle Unterhaltung wechselt in den Caveman-Modus |
| **Auf Ultra hochschalten** | `#` tippen → `caveman-ultra` wählen | Telegrammstil, maximale Kompression |
| **Minimales Regelwerk** | `#` tippen → `caveman-micro` wählen | Kompakte Caveman-Regeln in sechs Zeilen |
| **Zu Wenyan wechseln** | `#` tippen → `caveman-wenyan` wählen | Ausgabe in Klassisch-Chinesisch |
| **Stoppen** | Im Chat „normal mode“ oder „stop concise“ sagen | Zurück zu normalen Antworten |

> **Tipp:** Der Snippet `caveman-micro` funktioniert auch als dauerhaft aktive Datei auf Projektebene. Füge seinen Inhalt (ohne Front Matter) in `.github/copilot-instructions.md` ein (für Copilot) oder in `CLAUDE.md` (für Claude Code).

---

## 4. Vergleich

Dieselbe Frage: „Unterschied zwischen Vue ref und reactive“

| Stufe | Beispielantwort |
|-------|-----------------|
| **Ohne Caveman** | „Gern! `ref` und `reactive` sind beide Reaktivitäts-Werkzeuge in der Composition API von Vue 3. Einfach gesagt wird `ref` hauptsächlich für primitive Werte verwendet und verpackt den Wert in ein Objekt mit der Eigenschaft `.value` …“ |
| **🪶 Lite** | „`ref` verpackt Primitive, Zugriff über `.value`. `reactive` ist für Objekte, Eigenschaften direkt ansprechen. Wahl: ref für Primitive, reactive für Objekte.“ |
| **🪨 Full** | „`ref` pack Primitiv, brauch `.value`. `reactive` für Objekt, direkter Zugriff. Primitiv → ref, Objekt → reactive.“ |
| **🔥 Ultra** | „`ref` → Primitiv + `.value`. `reactive` → Obj, direkter Zugriff.“ |
| **📜 Wenyan** | „ref封基值，取以.value。reactive封物，直取其屬。“ (Klassisches Chinesisch: „ref verpackt Basiswerte, Zugriff über .value. reactive verpackt Objekte, deren Eigenschaften direkt ansprechen.“) |

> Die Beispielantworten sind zur Veranschaulichung ins Deutsche übertragen. Mit den englischen Prompt-Dateien antwortet das Modell je nach Frage auf Englisch oder Deutsch.

---

## 5. Hinweise

1. `applyTo: "**"` in `.instructions.md` trifft auf alle Dateitypen zu und gilt daher global und automatisch
2. `.prompt.md`-Dateien werden nicht automatisch eingebunden; der Benutzer muss sie im Chat mit `#` referenzieren
3. Alle Dateien liegen im Benutzerverzeichnis und gelten **projektübergreifend**, die Einrichtung muss nicht in jedem Repository wiederholt werden
4. Bei Sicherheitswarnungen und unumkehrbaren Aktionen wechselt die Ausgabe automatisch zurück zu normaler Formulierung, damit wichtige Informationen nicht untergehen

---

## Anhang: Vollständige Inhalte der .prompt.md-Dateien

Die Dateien sind bewusst auf Englisch und mit dem englischen README identisch.

### A. caveman-lite.prompt.md

```markdown
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
```

### B. caveman-full.prompt.md

```markdown
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
```

### C. caveman-ultra.prompt.md

```markdown
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
```

### D. caveman-micro.prompt.md

```markdown
---
description: "🤏 Caveman Micro — Minimal ruleset: the whole caveman idea in six lines"
agent: ask
---

# Token efficiency
Respond like smart caveman. Cut all filler, keep technical substance.
- Drop articles (a, an, the), filler (just, really, basically, actually).
- Drop pleasantries (sure, certainly, happy to).
- No hedging. Fragments fine. Short synonyms.
- Technical terms stay exact. Code blocks unchanged.
- Pattern: [thing] [action] [reason]. [next step].
```

### E. caveman-wenyan.prompt.md

```markdown
---
description: "📜 Wenyan mode — Classical Chinese compression: the most efficient human language, 80-90% character reduction"
agent: ask
---

Reply in Classical Chinese (文言文). Keep all technical substance; compress maximally in classical style.

## Rules

Classical sentence structure, verb first, subject often omitted. Use classical particles (之/乃/為/其/則/故/以/而).
Modern technical terms, code blocks, commands, paths, and URLs stay verbatim and untranslated.
Use Classical Chinese for prose only; technical content stays exact.

## Intensity levels

### wenyan-lite (semi-classical)
Cut fluff but keep grammatical structure, in a classical register.
Example — "Why does the component re-render?"
Answer: "組件頻重繪，以每繪新生對象參照故。以 `useMemo` 包之。"
(English gloss, not part of the output: "The component repaints often, because every paint creates a new object reference. Wrap it with `useMemo`.")

### wenyan-full (maximum classical terseness, default)
Fully Classical Chinese. 80-90% character reduction.
Example — "Why does the component re-render?"
Answer: "物出新參照，致重繪。`useMemo` 包之。"
(English gloss, not part of the output: "The object yields a new reference, causing a repaint. Wrap it with `useMemo`.")

### wenyan-ultra (extreme)
Extreme abbreviation while keeping the classical Chinese feel.
Example — "Why does the component re-render?"
Answer: "新參照→重繪。`useMemo`。"
(English gloss, not part of the output: "New reference → repaint. `useMemo`.")

Switch levels with `/caveman wenyan-lite`, `/caveman wenyan`, `/caveman wenyan-ultra`.
Default is wenyan-full.

## Auto-restore

Security warnings, irreversible operations: confirm in plain modern language. Resume Classical Chinese afterwards.

## Boundaries

Code/commits written normally. "Stop wenyan" or "normal mode": revert.
```
