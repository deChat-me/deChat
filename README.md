# 💬 deChat

Willkommen bei deChat - einer dezentralen Chat-Plattform.

## 📖 Über das Projekt

deChat ist eine Open-Source, dezentrale Messaging-Plattform, die Privatsphäre und Datenschutz in den Vordergrund stellt.

---

## 🤖 Arbeiten mit Claude (AI-Assistent)

Dieses Repository ist für die direkte Zusammenarbeit mit Claude konfiguriert. Claude kann selbstständig Änderungen committen und pushen.

### 📖 Vollständige Dokumentation

**➡️ [Claude Self-Push Dokumentation](README-claude-self-push.md)**

**➡️ [Claude Workflow Guide](.github/CLAUDE_WORKFLOW.md)**

### ⚡ Schnellstart

**Branch-Konvention:**
```
claude/<beschreibung>-<session-id>
```

**Beispiel:**
```
claude/add-notifications-0152SpWuL5qzWpsjRvA6DA2k
```

**Wichtig:**
- Branches für Claude **müssen** mit `claude/` beginnen
- Session-ID am Ende ist **pflicht** (sonst HTTP 403)
- Claude pusht automatisch mit Retry-Logik bei Netzwerkfehlern

### 🔑 Key Features

✅ **Self-Push**: Claude kann selbstständig committen und pushen
✅ **Branch-Schutz**: Automatischer Schutz von `main`/`master`
✅ **Retry-Logik**: Automatische Wiederholungen bei Netzwerkfehlern (4x mit exponential backoff)
✅ **Session-Isolation**: Jede Claude-Session arbeitet auf eigenem Branch

### 📚 Weitere Informationen

- **[Self-Push Funktionsweise](README-claude-self-push.md#proxy-funktionsweise)** - Wie der Proxy funktioniert
- **[Workflows](.github/CLAUDE_WORKFLOW.md#typische-workflows)** - Typische Entwicklungs-Workflows
- **[Troubleshooting](README-claude-self-push.md#troubleshooting)** - Häufige Probleme und Lösungen
- **[Branch-Konventionen](README-claude-self-push.md#branch-konventionen)** - Detaillierte Branch-Regeln

---

## 🚀 Getting Started

### Voraussetzungen

```bash
# Node.js (Version 18+)
node --version

# Git
git --version
```

### Installation

```bash
# Repository klonen
git clone https://github.com/deChat-me/deChat.git
cd deChat

# Dependencies installieren
npm install

# Entwicklungsserver starten
npm run dev
```

---

## 🏗️ Projekt-Struktur

```
deChat/
├── .github/              # GitHub-Konfiguration und Workflows
│   └── CLAUDE_WORKFLOW.md
├── src/                  # Quellcode
│   ├── components/       # UI-Komponenten
│   ├── services/         # Business-Logik
│   └── utils/            # Hilfsfunktionen
├── tests/                # Tests
├── docs/                 # Dokumentation
├── README.md             # Diese Datei
└── README-claude-self-push.md  # Claude-Dokumentation
```

---

## 🤝 Contributing

Wir freuen uns über Beiträge! Bitte lies die folgenden Richtlinien:

### Für menschliche Contributors

1. Fork das Repository
2. Erstelle einen Feature-Branch: `git checkout -b feature/neue-funktion`
3. Committe deine Änderungen: `git commit -m 'feat: Add neue Funktion'`
4. Push den Branch: `git push origin feature/neue-funktion`
5. Erstelle einen Pull Request

### Für Claude (AI-Assistent)

1. Branch wird automatisch erstellt: `claude/beschreibung-<SESSION_ID>`
2. Änderungen werden automatisch committet und gepusht
3. Pull Request kann dann manuell oder automatisch erstellt werden

Siehe **[Claude Workflow Guide](.github/CLAUDE_WORKFLOW.md)** für Details.

---

## 📝 Commit-Konventionen

Wir folgen den [Conventional Commits](https://www.conventionalcommits.org/) Konventionen:

- `feat:` - Neues Feature
- `fix:` - Bug-Fix
- `docs:` - Dokumentation
- `refactor:` - Code-Refactoring
- `test:` - Tests hinzufügen/ändern
- `chore:` - Build-Prozess, Tools, etc.

**Beispiele:**
```bash
git commit -m "feat: Add user authentication"
git commit -m "fix: Resolve login redirect issue"
git commit -m "docs: Update API documentation"
```

---

## 🧪 Testing

```bash
# Alle Tests ausführen
npm test

# Tests mit Coverage
npm run test:coverage

# Tests im Watch-Mode
npm run test:watch
```

---

## 📄 Lizenz

[Lizenz hier einfügen]

---

## 📞 Kontakt

- **Repository:** https://github.com/deChat-me/deChat
- **Issues:** https://github.com/deChat-me/deChat/issues

---

## 🙏 Danksagungen

Danke an alle Contributors, die zu diesem Projekt beitragen!

---

**Letzte Aktualisierung:** 2025-11-20
