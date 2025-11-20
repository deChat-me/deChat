# 🤖 Claude Self-Push Dokumentation

Diese Dokumentation erklärt, wie Claude (Anthropic's AI-Assistent) selbstständig Änderungen im deChat Repository committen und pushen kann.

## 📋 Inhaltsverzeichnis

- [Proxy-Funktionsweise](#proxy-funktionsweise)
- [Claude Self-Push Workflow](#claude-self-push-workflow)
- [Branch-Konventionen](#branch-konventionen)
- [Sicherheitshinweise](#sicherheitshinweise)
- [Troubleshooting](#troubleshooting)

---

## 🔌 Proxy-Funktionsweise

### Wie funktioniert der lokale Proxy?

Das deChat Repository verwendet einen lokalen Proxy-Server, der es Claude ermöglicht, Git-Operationen auszuführen, ohne direkt auf GitHub zugreifen zu müssen.

**Proxy-Konfiguration:**
```
URL: http://local_proxy@127.0.0.1:<PORT>/git/deChat-me/deChat
Port: Dynamisch zugewiesen (z.B. 49346)
Authentifizierung: über local_proxy User
```

### URL-Format

```bash
http://local_proxy@127.0.0.1:<PORT>/git/<OWNER>/<REPO>
```

**Beispiel:**
```bash
http://local_proxy@127.0.0.1:49346/git/deChat-me/deChat
```

### Authentifizierung und Autorisierung

1. **Lokaler Proxy**: Der Proxy läuft auf `127.0.0.1` und ist nur lokal erreichbar
2. **Repository-Zugriff**: Nur autorisierte Repositories können gepusht werden
3. **Branch-Validierung**: Nur Branches mit korrektem Format werden akzeptiert (siehe Branch-Konventionen)

### Fehlerbehandlung

| Fehlercode | Bedeutung | Lösung |
|------------|-----------|--------|
| **403 Forbidden** | Branch-Name ungültig | Branch muss mit `claude/` beginnen und korrekte Session-ID enthalten |
| **502 Bad Gateway** | Repository nicht autorisiert | Repository muss in der Proxy-Konfiguration freigegeben sein |
| **Network Error** | Verbindungsproblem | Retry-Logik wird automatisch angewendet (4 Versuche) |

---

## 🚀 Claude Self-Push Workflow

### Schritt-für-Schritt Anleitung

#### 1. Änderungen vornehmen

Claude nimmt die gewünschten Code-Änderungen vor (z.B. neue Features, Bug-Fixes, Refactoring).

#### 2. Änderungen stagen

```bash
git add .
# oder selektiv:
git add path/to/file
```

#### 3. Committen mit aussagekräftiger Message

```bash
git commit -m "$(cat <<'EOF'
feat: Add user notification system

- Implement push notification service
- Add notification preferences UI
- Create notification history view
EOF
)"
```

**Commit-Message Format:**
```
<type>: <subject>

<body>
```

**Typen:**
- `feat`: Neues Feature
- `fix`: Bug-Fix
- `docs`: Dokumentation
- `refactor`: Code-Refactoring
- `test`: Tests hinzufügen/ändern
- `chore`: Build-Prozess, Tools, etc.

#### 4. Pushen mit Retry-Logik

```bash
git push -u origin claude/feature-name-<SESSION_ID>
```

**Retry-Logik bei Netzwerkfehlern:**
- **1. Versuch**: Sofort
- **2. Versuch**: Nach 2 Sekunden
- **3. Versuch**: Nach 4 Sekunden
- **4. Versuch**: Nach 8 Sekunden
- **5. Versuch**: Nach 16 Sekunden

Diese Retry-Logik wird automatisch von Claude angewendet.

### Best Practices für Commit-Messages

✅ **Gut:**
```
feat: Add Delta Chat notification support

- Implement Delta Chat API integration
- Add notification preferences for Delta Chat
- Create UI for Delta Chat account linking
```

❌ **Schlecht:**
```
update stuff
fix bug
changes
```

**Regeln:**
1. Erste Zeile max. 72 Zeichen
2. Imperativ ("Add feature" statt "Added feature")
3. Body erklärt **warum**, nicht **was**
4. Referenziere Issues wenn relevant: `Fixes #123`

---

## 🌿 Branch-Konventionen

### ⚠️ KRITISCHE REGEL

**Alle Claude-Branches MÜSSEN mit `claude/` beginnen und mit der Session-ID enden!**

### Branch-Format

```
claude/<beschreibung>-<session-id>
```

**Komponenten:**
- `claude/` - **Pflicht-Präfix** (sonst HTTP 403)
- `<beschreibung>` - Kurze, kebab-case Beschreibung der Änderung
- `<session-id>` - **Aktuelle Session-ID** (z.B. `0152SpWuL5qzWpsjRvA6DA2k`)

### Beispiele

✅ **Korrekt:**
```
claude/add-notifications-0152SpWuL5qzWpsjRvA6DA2k
claude/fix-login-bug-0152SpWuL5qzWpsjRvA6DA2k
claude/refactor-api-client-0152SpWuL5qzWpsjRvA6DA2k
```

❌ **Falsch:**
```
feature/add-notifications          ❌ Kein claude/ Präfix
claude/add-notifications           ❌ Keine Session-ID
add-notifications-0152SpWuL5qzWpsjRvA6DA2k  ❌ Kein claude/ Präfix
claude/add-notifications-WRONG-ID  ❌ Falsche Session-ID
```

### Session-ID herausfinden

Die Session-ID findest du im aktuellen Branch-Namen:

```bash
git branch --show-current
# Ausgabe: claude/add-self-push-dechat-0152SpWuL5qzWpsjRvA6DA2k
#                                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^
#                                       Diese ID verwenden!
```

### Warum diese Konvention?

1. **Sicherheit**: Verhindert, dass Claude versehentlich auf wichtige Branches (main/master) pusht
2. **Isolation**: Claude-Änderungen sind klar erkennbar und isoliert
3. **Traceability**: Session-ID ermöglicht Nachverfolgung welche Claude-Session welche Änderungen gemacht hat
4. **Automation**: Der Proxy kann automatisch validieren, ob der Push erlaubt ist

---

## 🔒 Sicherheitshinweise

### Branch-Schutz

Der lokale Proxy **verhindert** automatisch:

- ❌ Push auf `main` oder `master`
- ❌ Push auf Branches ohne `claude/` Präfix
- ❌ Push auf Branches mit falscher Session-ID
- ❌ Force-Push ohne explizite Autorisierung

### Review-Prozess

**Empfohlener Workflow:**

1. **Claude pushed** auf `claude/*` Branch
2. **Developer reviewed** die Änderungen
3. **Pull Request** wird erstellt
4. **Code Review** durch Team
5. **Merge** nach Approval

### Berechtigungen

Claude kann:
- ✅ Neue `claude/*` Branches erstellen
- ✅ Auf eigene `claude/*` Branches pushen
- ✅ Pull Requests erstellen

Claude kann **nicht**:
- ❌ Direkt auf `main`/`master` pushen
- ❌ Branches löschen
- ❌ Force-Push ohne Autorisierung
- ❌ Repository-Settings ändern

---

## 🔧 Troubleshooting

### HTTP 403 Forbidden

**Problem:** Push wird mit HTTP 403 abgelehnt

**Ursache:** Branch-Name entspricht nicht dem Format

**Lösung:**
```bash
# Aktuellen Branch prüfen
git branch --show-current

# Falls falsch, korrekten Branch erstellen
git checkout -b claude/feature-name-0152SpWuL5qzWpsjRvA6DA2k

# Änderungen übertragen (falls nötig)
git cherry-pick <commit-hash>
```

### HTTP 502 Bad Gateway

**Problem:** Proxy antwortet mit 502

**Ursache:** Repository nicht in Proxy-Whitelist

**Lösung:**
- Prüfe Proxy-Konfiguration
- Stelle sicher, dass `deChat-me/deChat` autorisiert ist
- Kontaktiere Administrator

### Network Errors

**Problem:** Verbindung zum Proxy schlägt fehl

**Ursache:** Proxy nicht erreichbar oder Netzwerkprobleme

**Lösung:**
- Retry-Logik läuft automatisch
- Bei wiederholten Fehlern: Proxy-Status prüfen
```bash
# Proxy-Verbindung testen
curl http://127.0.0.1:49346/health
```

### Merge-Konflikte

**Problem:** Push schlägt fehl wegen Konflikten

**Ursache:** Remote-Branch hat Änderungen

**Lösung:**
```bash
# Remote-Änderungen holen
git fetch origin

# Mit Remote-Branch mergen
git merge origin/claude/your-branch-name

# Konflikte lösen (Claude wird dich informieren)
# Dann erneut pushen
git push -u origin claude/your-branch-name
```

### Branch existiert bereits

**Problem:** Branch existiert bereits im Remote

**Ursache:** Vorherige Session mit gleicher Session-ID

**Lösung:**
```bash
# Entweder: Auf existierenden Branch wechseln
git checkout claude/existing-branch

# Oder: Neue Session-ID verwenden (wenn neue Session)
git checkout -b claude/feature-name-<NEUE_SESSION_ID>
```

---

## 📚 Weitere Ressourcen

- [Claude Workflow Guide](.github/CLAUDE_WORKFLOW.md) - Detaillierte Workflows
- [Contributing Guide](CONTRIBUTING.md) - Allgemeine Contribution-Guidelines
- [Git Branching Model](docs/git-workflow.md) - Branching-Strategie

---

## ❓ Fragen?

Bei Fragen oder Problemen:
1. Prüfe diese Dokumentation
2. Schaue in [Troubleshooting](#troubleshooting)
3. Konsultiere [Claude Workflow Guide](.github/CLAUDE_WORKFLOW.md)
4. Erstelle ein Issue im Repository

---

**Letzte Aktualisierung:** 2025-11-20
**Version:** 1.0.0
