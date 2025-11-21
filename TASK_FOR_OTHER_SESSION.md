# Auftrag: Delta Chat Notification Examples zu deChat Repository hinzufügen

## 🎯 Ziel

Kopiere 3 Delta Chat Notification-Dateien vom `deChat-me/core` Repository ins `deChat-me/deChat` Repository unter `examples/delta-chat-notifications/`.

---

## 📥 Quell-Repository

**Repository:** https://github.com/deChat-me/core
**Branch:** `claude/delta-chat-notifications-019PyLsRPiLu8TUCGBYBwFck`
**Quell-Pfad:** `deltachat-rpc-client/examples/`

### Zu kopierende Dateien (3 Dateien):

1. **send.py** (4784 bytes, executable)
   - Pfad: `deltachat-rpc-client/examples/send.py`
   - One-Shot Notification Sender Script

2. **README_SEND.md** (9265 bytes)
   - Pfad: `deltachat-rpc-client/examples/README_SEND.md`
   - Deutsche Dokumentation mit Beispielen

3. **SEND_API_ANALYSIS.md** (8613 bytes)
   - Pfad: `deltachat-rpc-client/examples/SEND_API_ANALYSIS.md`
   - API-Dokumentation

---

## 📤 Ziel-Repository

**Repository:** https://github.com/deChat-me/deChat
**Ziel-Pfad:** `examples/delta-chat-notifications/`
**Branch:** Erstelle neuen Branch `claude/add-notification-examples-<DEINE_SESSION_ID>`

⚠️ **WICHTIG:** Der Branch MUSS mit `claude/` beginnen und mit deiner Session-ID enden, sonst schlägt der Push mit HTTP 403 fehl!

---

## 🚀 Ausführungsschritte

Führe folgende Schritte der Reihe nach aus:

### Schritt 1: Source Repository klonen
```bash
git clone https://github.com/deChat-me/core /tmp/core-source
cd /tmp/core-source
git checkout claude/delta-chat-notifications-019PyLsRPiLu8TUCGBYBwFck
```

### Schritt 2: Zum deChat Repository wechseln
```bash
cd /home/user/deChat
# oder wo auch immer dein Working Directory ist
```

### Schritt 3: Feature Branch erstellen
```bash
# WICHTIG: Ersetze <DEINE_SESSION_ID> mit deiner tatsächlichen Session-ID!
git checkout -b claude/add-notification-examples-<DEINE_SESSION_ID>
```

### Schritt 4: Zielverzeichnis erstellen
```bash
mkdir -p examples/delta-chat-notifications
```

### Schritt 5: Dateien kopieren
```bash
cp /tmp/core-source/deltachat-rpc-client/examples/send.py examples/delta-chat-notifications/
cp /tmp/core-source/deltachat-rpc-client/examples/README_SEND.md examples/delta-chat-notifications/
cp /tmp/core-source/deltachat-rpc-client/examples/SEND_API_ANALYSIS.md examples/delta-chat-notifications/
```

### Schritt 6: Executable-Flag setzen
```bash
chmod +x examples/delta-chat-notifications/send.py
```

### Schritt 7: Änderungen verifizieren
```bash
# Prüfe ob alle Dateien da sind
ls -lh examples/delta-chat-notifications/

# Sollte zeigen:
# -rwxr-xr-x send.py (ca. 4.8K)
# -rw-r--r-- README_SEND.md (ca. 9.3K)
# -rw-r--r-- SEND_API_ANALYSIS.md (ca. 8.6K)
```

### Schritt 8: Commit erstellen
```bash
git add examples/delta-chat-notifications/

git commit -m "$(cat <<'EOF'
feat: Add Delta Chat notification examples

- Add send.py: One-shot notification sender script
- Add README_SEND.md: German documentation with usage examples
- Add SEND_API_ANALYSIS.md: API reference documentation

These examples demonstrate how to send notifications via Delta Chat
using the deltachat-rpc-client. Includes comprehensive documentation
in German and technical API analysis.

Source: deChat-me/core @ claude/delta-chat-notifications-019PyLsRPiLu8TUCGBYBwFck
EOF
)"
```

### Schritt 9: Push mit Retry-Logik
```bash
git push -u origin claude/add-notification-examples-<DEINE_SESSION_ID>
```

**Falls Netzwerkfehler auftreten**, versuche es bis zu 4 mal mit exponential backoff:
- 1. Versuch: Sofort
- 2. Versuch: Nach 2 Sekunden
- 3. Versuch: Nach 4 Sekunden
- 4. Versuch: Nach 8 Sekunden

---

## ✅ Erwartetes Ergebnis

Nach erfolgreichem Push sollte das deChat-me/deChat Repository diese Struktur haben:

```
examples/
└── delta-chat-notifications/
    ├── send.py              (executable, ~4.8K)
    ├── README_SEND.md       (~9.3K)
    └── SEND_API_ANALYSIS.md (~8.6K)
```

**Verifizierung auf GitHub:**
1. Gehe zu: https://github.com/deChat-me/deChat/branches
2. Dein Branch sollte sichtbar sein: `claude/add-notification-examples-<DEINE_SESSION_ID>`
3. Navigiere zu: https://github.com/deChat-me/deChat/tree/claude/add-notification-examples-<DEINE_SESSION_ID>/examples/delta-chat-notifications
4. Alle 3 Dateien sollten dort sichtbar sein

---

## 🔍 Troubleshooting

### HTTP 403 beim Push
**Problem:** Push wird abgelehnt
**Ursache:** Branch-Name entspricht nicht dem Format
**Lösung:** Stelle sicher, dass der Branch mit `claude/` beginnt und deine Session-ID enthält

### Dateien nicht gefunden beim Kopieren
**Problem:** `cp: cannot stat...`
**Ursache:** Source-Repository nicht korrekt ausgecheckt
**Lösung:**
```bash
cd /tmp/core-source
git branch --show-current  # Sollte den claude-Branch zeigen
ls -la deltachat-rpc-client/examples/  # Sollte die Dateien zeigen
```

### Branch existiert bereits
**Problem:** Branch-Name bereits verwendet
**Lösung:** Verwende deine aktuelle Session-ID (nicht die aus dem Beispiel)

---

## 📚 Kontext

Diese Dateien wurden ursprünglich im deChat-me/core Repository entwickelt und sollen nun als Beispiele ins deChat-me/deChat Repository übernommen werden, damit Nutzer einfachen Zugriff auf Notification-Beispiele haben.

---

## ⚠️ WICHTIG - Session-ID

Deine Session-ID findest du normalerweise im Branch-Namen, wenn du bereits auf einem Claude-Branch arbeitest:

```bash
git branch --show-current
# Beispiel-Ausgabe: claude/some-feature-019PyLsRPiLu8TUCGBYBwFck
#                                        ^^^^^^^^^^^^^^^^^^^^^^^
#                                        Diese ID verwenden!
```

Falls du noch auf keinem Claude-Branch bist, wird dir die Session-ID beim Start der Session mitgeteilt.

---

**Viel Erfolg! 🚀**
