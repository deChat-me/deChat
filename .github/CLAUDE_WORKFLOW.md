# 🤖 Claude Workflow Guide

Praktischer Leitfaden für die Zusammenarbeit mit Claude im deChat Repository.

## 📋 Inhaltsverzeichnis

- [Setup](#setup)
- [Typische Workflows](#typische-workflows)
- [Pull Request Erstellung](#pull-request-erstellung)
- [Code Review](#code-review)
- [Best Practices](#best-practices)

---

## 🛠️ Setup

### Neue Session starten

**1. Repository klonen/öffnen:**
```bash
cd deChat
```

**2. Aktuelle Session-ID ermitteln:**
```bash
# Die Session-ID steht im Branch-Namen
git branch --show-current
# Beispiel: claude/add-self-push-dechat-0152SpWuL5qzWpsjRvA6DA2k
#                                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^
#                                       Session-ID
```

**3. Claude instruieren:**
```
Ich arbeite an [Beschreibung des Tasks].
Bitte erstelle einen Branch und implementiere folgende Änderungen:
- [Liste der Anforderungen]

Wenn fertig, committe und pushe die Änderungen.
```

**4. Claude wird automatisch:**
- Korrekten Branch erstellen (`claude/<beschreibung>-<SESSION_ID>`)
- Änderungen implementieren
- Committen mit aussagekräftiger Message
- Pushen mit Retry-Logik

---

## 🔄 Typische Workflows

### Workflow 1: Feature-Entwicklung

**Szenario:** Neues Feature hinzufügen

**Instruktion an Claude:**
```
Implementiere ein neues Feature für [Beschreibung].

Anforderungen:
- [Anforderung 1]
- [Anforderung 2]
- [Anforderung 3]

Bitte:
1. Erstelle die notwendigen Dateien
2. Implementiere die Funktionalität
3. Füge Tests hinzu
4. Aktualisiere die Dokumentation
5. Committe und pushe alles
```

**Claude's Prozess:**
1. Branch erstellen: `claude/add-feature-name-<SESSION_ID>`
2. Feature implementieren
3. Tests schreiben
4. Dokumentation aktualisieren
5. Commit: `feat: Add feature-name`
6. Push mit Retry-Logik

**Nach dem Push:**
```bash
# Pull Request erstellen
gh pr create --title "feat: Add feature-name" \
             --body "Implements feature XYZ with tests and docs"
```

---

### Workflow 2: Bug-Fix

**Szenario:** Bug beheben

**Instruktion an Claude:**
```
Behebe folgenden Bug: [Beschreibung]

Schritte zur Reproduktion:
1. [Schritt 1]
2. [Schritt 2]

Erwartetes Verhalten: [...]
Tatsächliches Verhalten: [...]

Bitte:
1. Identifiziere die Ursache
2. Behebe den Bug
3. Füge einen Test hinzu, der den Bug abdeckt
4. Committe und pushe
```

**Claude's Prozess:**
1. Code analysieren
2. Bug-Ursache identifizieren
3. Branch erstellen: `claude/fix-bug-description-<SESSION_ID>`
4. Bug beheben
5. Regression-Test hinzufügen
6. Commit: `fix: Resolve bug in component X`
7. Push

**Commit-Message Beispiel:**
```
fix: Resolve notification delivery issue

- Fix race condition in notification queue
- Add test for concurrent notification handling
- Update error logging

Fixes #123
```

---

### Workflow 3: Refactoring

**Szenario:** Code verbessern ohne Funktionalität zu ändern

**Instruktion an Claude:**
```
Refaktoriere [Komponente/Modul].

Ziele:
- Verbessere Lesbarkeit
- Reduziere Komplexität
- Halte bestehende Tests grün

Bitte:
1. Analysiere den aktuellen Code
2. Schlage Verbesserungen vor
3. Implementiere das Refactoring
4. Stelle sicher, dass alle Tests bestehen
5. Committe und pushe
```

**Claude's Prozess:**
1. Code-Analyse
2. Branch erstellen: `claude/refactor-component-name-<SESSION_ID>`
3. Refactoring durchführen
4. Tests ausführen und sicherstellen, dass alle bestehen
5. Commit: `refactor: Simplify component X logic`
6. Push

---

### Workflow 4: Dokumentation

**Szenario:** Dokumentation hinzufügen/aktualisieren

**Instruktion an Claude:**
```
Erstelle/aktualisiere die Dokumentation für [Thema].

Bitte dokumentiere:
- [Aspekt 1]
- [Aspekt 2]
- Code-Beispiele
- Best Practices

Committe und pushe wenn fertig.
```

**Claude's Prozess:**
1. Branch: `claude/docs-topic-name-<SESSION_ID>`
2. Dokumentation schreiben
3. Code-Beispiele hinzufügen
4. Commit: `docs: Add documentation for feature X`
5. Push

---

### Workflow 5: Multi-File Changes

**Szenario:** Änderungen über mehrere Dateien hinweg

**Instruktion an Claude:**
```
Benenne die Funktion `oldName` in `newName` um.

Die Funktion wird in folgenden Bereichen verwendet:
- API Client
- UI Components
- Tests

Bitte:
1. Finde alle Vorkommen
2. Benenne konsistent um
3. Stelle sicher, dass Tests bestehen
4. Committe und pushe
```

**Claude's Prozess:**
1. Suche nach allen Vorkommen
2. Branch: `claude/rename-function-<SESSION_ID>`
3. Systematisches Umbenennen
4. Tests ausführen
5. Commit: `refactor: Rename oldName to newName across codebase`
6. Push

---

## 📝 Pull Request Erstellung

### Automatisch durch Claude

Claude kann PRs direkt erstellen (wenn `gh` CLI verfügbar):

**Instruktion:**
```
Nach dem Push, erstelle bitte einen Pull Request mit:
- Titel: [Titel]
- Beschreibung: [Was wurde geändert und warum]
```

**Claude's Befehl:**
```bash
gh pr create --title "feat: Feature name" \
             --body "$(cat <<'EOF'
## Summary
- Implemented feature X
- Added tests
- Updated documentation

## Test plan
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed
EOF
)"
```

### Manuell erstellen

**Nach Claude's Push:**
```bash
# Branch-Name aus Claude's Output kopieren
git fetch origin
git checkout claude/feature-name-<SESSION_ID>

# PR erstellen
gh pr create --web
```

**PR-Template:**
```markdown
## 🤖 Claude-Generated PR

### Summary
[Beschreibung der Änderungen]

### Changes
- Change 1
- Change 2
- Change 3

### Test Plan
- [ ] Unit tests added/updated
- [ ] All tests passing
- [ ] Manual testing completed

### Claude Session
- Session-ID: [ID aus Branch-Name]
- Branch: claude/[branch-name]

### Review Notes
[Besondere Punkte für Review]
```

---

## 👀 Code Review

### Review-Checkliste für Claude-Code

#### Funktionalität
- [ ] Implementiert die Anforderungen vollständig
- [ ] Edge Cases werden behandelt
- [ ] Fehlerbehandlung ist vorhanden

#### Code-Qualität
- [ ] Code ist lesbar und verständlich
- [ ] Folgt Projekt-Konventionen
- [ ] Keine unnötige Komplexität
- [ ] Gute Benennung von Variablen/Funktionen

#### Tests
- [ ] Tests sind vorhanden
- [ ] Tests decken wichtige Szenarien ab
- [ ] Tests sind wartbar

#### Dokumentation
- [ ] Wichtige Funktionen sind dokumentiert
- [ ] README/Docs bei Bedarf aktualisiert
- [ ] Commit-Messages sind aussagekräftig

#### Sicherheit
- [ ] Keine offensichtlichen Sicherheitslücken
- [ ] Input-Validierung wo nötig
- [ ] Keine Secrets im Code

### Häufige Review-Punkte

**1. Over-Engineering**
Claude tendiert manchmal zu umfangreichen Lösungen.
```
❌ Zu komplex: Abstrakte Factory mit 5 Interfaces
✅ Einfacher: Direkte Implementierung für den Use Case
```

**2. Fehlerbehandlung**
Prüfe, ob alle Fehler sinnvoll behandelt werden.
```typescript
❌ try { } catch (e) { console.log(e) }
✅ try { } catch (e) { logger.error('Context:', e); throw new SpecificError(e) }
```

**3. Performance**
Bei größeren Datenmengen Performance überprüfen.
```typescript
❌ items.filter(...).map(...).filter(...).map(...)
✅ items.reduce((acc, item) => { /* einmal durchlaufen */ })
```

---

## ✨ Best Practices

### Klare Instruktionen geben

**Gut:**
```
Implementiere eine Notification-Service-Klasse:
- Methode sendNotification(userId, message)
- Methode getNotificationHistory(userId)
- Error-Handling für Netzwerkfehler
- Unit-Tests für beide Methoden
```

**Schlecht:**
```
Mach mal was mit Notifications
```

### Iterativ arbeiten

**Ansatz:**
1. Einfache Version implementieren lassen
2. Review durchführen
3. Verbesserungen anweisen
4. Wiederholen bis zufrieden

### Session-Kontext nutzen

Claude behält Kontext der Session:
```
"Basierend auf der vorherigen Implementierung,
füge jetzt noch [zusätzliches Feature] hinzu."
```

### Tests explizit anfordern

```
"Bitte füge Unit-Tests hinzu für:
- Happy path
- Error cases
- Edge cases"
```

### Dokumentation verlangen

```
"Aktualisiere bitte auch:
- API-Dokumentation in docs/api.md
- Beispiel in README.md
- Inline-Kommentare für komplexe Logik"
```

---

## 🚨 Wichtige Hinweise

### Branch-Naming

⚠️ **Claude muss IMMER das korrekte Format verwenden:**
```
claude/<beschreibung>-<SESSION_ID>
```

Sonst schlägt der Push mit HTTP 403 fehl!

### Keine direkten Pushes auf main

Claude kann und wird NICHT auf `main` oder `master` pushen.
Alle Änderungen gehen über Pull Requests.

### Session-Isolation

Jede Claude-Session ist isoliert:
- Eigene Branch mit Session-ID
- Keine Überschneidungen mit anderen Sessions
- Saubere Nachverfolgbarkeit

---

## 📚 Weitere Ressourcen

- [Self-Push Dokumentation](../README-claude-self-push.md)
- [Troubleshooting](../README-claude-self-push.md#troubleshooting)
- [Branch-Konventionen](../README-claude-self-push.md#branch-konventionen)

---

## 💡 Tipps

### Produktivität maximieren

1. **Konkrete Anforderungen**: Je klarer deine Anforderungen, desto besser das Ergebnis
2. **Schrittweise vorgehen**: Große Tasks in kleinere aufteilen
3. **Review früh**: Lieber kleine PRs häufig reviewen als große PRs selten
4. **Tests verlangen**: Explizit nach Tests fragen
5. **Kontext geben**: Claude hilft besser mit Kontext über das Projekt

### Zeit sparen

- **Templates verwenden**: Standardisierte Anfragen für wiederkehrende Tasks
- **Parallel arbeiten**: Mehrere unabhängige Tasks parallel von Claude bearbeiten lassen
- **Refactorings delegieren**: Zeitintensive Refactorings an Claude übergeben

### Qualität sichern

- **Code Review**: Immer reviewen, auch wenn Code von Claude kommt
- **Tests prüfen**: Sicherstellen, dass Tests sinnvoll sind
- **Dokumentation**: Bei komplexen Changes Dokumentation verlangen

---

**Letzte Aktualisierung:** 2025-11-20
**Version:** 1.0.0
