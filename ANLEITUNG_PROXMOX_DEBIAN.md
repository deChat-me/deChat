# Delta Chat Benachrichtigungen für Proxmox/Debian Server

**Verschlüsselte Server-Alerts aufs Smartphone - Einfacher Ersatz für unverschlüsselte E-Mails**

---

## Überblick

Diese Anleitung zeigt dir, wie du auf deinem Proxmox VE Host (oder jedem Debian 12/13 Server) verschlüsselte Benachrichtigungen über Delta Chat versenden kannst.

**Was du bekommst:**
- 🔒 **Ende-zu-Ende verschlüsselte** Server-Alerts
- 📱 **Push-Benachrichtigungen** aufs Smartphone
- ⚡ **Schnell**: Nachrichten kommen in <5 Sekunden an
- 🆓 **Kostenlos**: Keine API-Keys, keine Limits
- 🔓 **Open Source**: Alles transparent und selbst-hostbar

**Anwendungsfälle:**
- Monit/Nagios Alerts
- Cronjob-Benachrichtigungen
- Backup-Status
- System-Warnungen (Disk Space, CPU, RAM)
- Script-Ausgaben

---

## Voraussetzungen

### Server
- Debian 12/13 oder Proxmox VE 8.x
- Python 3.9+ (standardmäßig vorhanden)
- Ausgehender Zugriff auf IMAP/SMTP (Ports 993/587)
- ~100 MB freier Speicher

### Delta Chat Account
Du brauchst einen Delta Chat Account auf deinem Smartphone. Zwei Möglichkeiten:

**Option 1: Chatmail (Empfohlen für Server-Bots)**
- Schnellste Zustellung
- Kein Passwort nötig
- Optimiert für Bots
- Kostenlos

**Option 2: Dein bestehender E-Mail-Account**
- Gmail, Posteo, Mailbox.org, etc.
- Nutzt deinen vorhandenen Account
- Funktioniert genauso

---

## Installation

### Schritt 1: System vorbereiten

```bash
# Als root auf dem Proxmox/Debian Server
apt update
apt install -y python3 python3-venv python3-pip

# Dedicated User erstellen
useradd -r -m -d /opt/dechat -s /bin/bash dechat

# Verzeichnisse anlegen
mkdir -p /opt/dechat/{accounts,scripts}
chown -R dechat:dechat /opt/dechat
chmod 700 /opt/dechat/accounts  # Wichtig für Sicherheit!
```

### Schritt 2: Python Environment einrichten

```bash
# Als dechat User arbeiten
sudo -u dechat bash

# Virtual Environment erstellen
cd /opt/dechat
python3 -m venv venv
source venv/bin/activate

# Delta Chat Packages installieren
pip install deltachat-rpc-server==2.24.0 deltachat-rpc-client==2.4.0

# Prüfen ob Installation geklappt hat
python3 -c "from deltachat_rpc_client import Rpc; print('✓ Installation erfolgreich')"
```

### Schritt 3: send.py Script installieren

```bash
# Immer noch als dechat User
cd /opt/dechat

# Script von GitHub holen
curl -o send.py https://raw.githubusercontent.com/deChat-me/deChat/main/examples/delta-chat-notifications/send.py

# Executable machen
chmod +x send.py

# Shebang anpassen (zeigt auf unser venv)
sed -i '1c#!/opt/dechat/venv/bin/python3' send.py

# Test (wird noch fehlschlagen wegen fehlendem Account)
./send.py "Test"
# Sollte: "❌ No accounts found" zeigen
```

### Schritt 4: Account konfigurieren

**Jetzt brauchst du Delta Chat auf deinem Smartphone!**

#### Variante A: Chatmail (Empfohlen)

```bash
# Als dechat User
cd /opt/dechat
source venv/bin/activate

# Interaktives Python-Script erstellen
cat > setup-account.py << 'EOF'
#!/opt/dechat/venv/bin/python3
from deltachat_rpc_client import Rpc, DeltaChat, Bot
import sys

# Eindeutigen Bot-Namen generieren (z.B. mit Hostname)
import socket
hostname = socket.gethostname()
email = f"bot-{hostname}@testrun.org"

print(f"🤖 Konfiguriere Delta Chat Account für Server-Bot...")
print(f"   E-Mail: {email}")
print()

# RPC starten
rpc = Rpc(accounts_dir="/opt/dechat/accounts")
rpc.start()

dc = DeltaChat(rpc)
account = dc.add_account()

# Bot konfigurieren (Chatmail braucht kein Passwort)
bot = Bot(account)
print("⏳ Account wird konfiguriert... (kann 10-30 Sekunden dauern)")
bot.configure(email=email, password="")

configured_email = account.get_config("addr")
print()
print(f"✅ Account erfolgreich konfiguriert!")
print(f"   E-Mail: {configured_email}")
print()
print("📱 NÄCHSTER SCHRITT:")
print(f"   1. Öffne Delta Chat App auf deinem Smartphone")
print(f"   2. Sende eine Nachricht an: {configured_email}")
print(f"   3. Warte auf Antwort (der Bot antwortet nicht)")
print(f"   4. Jetzt kann der Server dir Nachrichten senden!")
print()

rpc.close()
EOF

chmod +x setup-account.py
./setup-account.py
```

**Wichtig:** Notiere dir die E-Mail-Adresse (z.B. `bot-proxmox01@testrun.org`)!

#### Variante B: Bestehender E-Mail-Account

```bash
# Nutze dein Gmail/Posteo/etc. Account
cat > setup-account.py << 'EOF'
#!/opt/dechat/venv/bin/python3
from deltachat_rpc_client import Rpc, DeltaChat, Bot
import getpass

email = input("E-Mail-Adresse: ")
password = getpass.getpass("Passwort: ")

rpc = Rpc(accounts_dir="/opt/dechat/accounts")
rpc.start()

dc = DeltaChat(rpc)
account = dc.add_account()
bot = Bot(account)

print("⏳ Konfiguriere Account...")
bot.configure(email=email, password=password)

print(f"✅ Account {email} konfiguriert!")
rpc.close()
EOF

chmod +x setup-account.py
./setup-account.py
```

### Schritt 5: Ersten Chat erstellen

**Super wichtig:** Der Bot kann nur an Chats senden, die bereits existieren!

```bash
# Auf deinem Smartphone:
# 1. Öffne Delta Chat App
# 2. Erstelle neuen Chat mit bot-proxmox01@testrun.org (oder deiner Bot-Adresse)
# 3. Sende irgendeine Nachricht (z.B. "Hi Bot")
# 4. Fertig! Der Chat existiert jetzt
```

### Schritt 6: Test-Nachricht senden

```bash
# Als dechat User
cd /opt/dechat
./send.py "🚀 Test vom Proxmox Server $(hostname)"

# Erwartete Ausgabe:
# ✓ Using account: bot-proxmox01@testrun.org
# ✓ Sending to chat: Dein Name
# ✓ Message sent successfully (msg_id: 42)

# Prüfe dein Smartphone - die Nachricht sollte da sein!
```

**Falls "No chats found":**
- Hast du einen Chat in der Delta Chat App erstellt?
- Hast du eine Nachricht an den Bot gesendet?
- Warte 30 Sekunden und versuche es nochmal

---

## Systemweite Installation

Damit alle Scripts das Tool nutzen können:

```bash
# Als root
ln -s /opt/dechat/send.py /usr/local/bin/delta-notify

# Test als root
delta-notify "Test von root"

# Jetzt von überall nutzbar:
delta-notify "Meine Nachricht"
```

**Wrapper-Script für Metadaten:**

```bash
# Als root
cat > /usr/local/bin/notify << 'EOF'
#!/bin/bash
# Universal Notification Wrapper mit Hostname

MESSAGE="$*"
HOST=$(hostname)
DATE=$(date '+%H:%M:%S')

# Nachricht mit Metadaten
sudo -u dechat /opt/dechat/send.py "[$HOST $DATE] $MESSAGE"
EOF

chmod +x /usr/local/bin/notify

# Nutzung:
notify "Backup fertig"
# Sendet: "[proxmox01 14:23:45] Backup fertig"
```

---

## Anwendungsbeispiele

### Beispiel 1: Monit Integration

**Monit** ist ein System-Monitoring-Tool auf Proxmox/Debian Servern.

```bash
# /etc/monit/monitrc

# Nginx Monitoring
check process nginx with pidfile /var/run/nginx.pid
    start program = "/usr/sbin/service nginx start"
    stop program = "/usr/sbin/service nginx stop"
    if failed host localhost port 80 then exec "/usr/local/bin/notify 'Nginx down, starte neu...'"
    if 3 restarts within 5 cycles then exec "/usr/local/bin/notify '❌ Nginx stirbt wiederholt ab!'"

# Disk Space Monitoring
check filesystem rootfs with path /
    if space usage > 85% then exec "/usr/local/bin/notify '⚠️ Disk Space bei 85%'"
    if space usage > 95% then exec "/usr/local/bin/notify '🔴 KRITISCH: Disk Space bei 95%!'"

# CPU Monitoring
check system $HOST
    if cpu usage > 90% for 5 cycles then exec "/usr/local/bin/notify '🔥 CPU Auslastung >90% seit 5 Minuten'"
    if loadavg (5min) > 8 for 3 cycles then exec "/usr/local/bin/notify '⚠️ Load Average sehr hoch'"

# Memory Monitoring
    if memory usage > 90% then exec "/usr/local/bin/notify '🧠 RAM Auslastung kritisch'"

# LXC Container (Proxmox spezifisch)
check process lxc-100 with pidfile /var/run/lxc/100.pid
    start program = "/usr/sbin/pct start 100"
    stop program = "/usr/sbin/pct stop 100"
    if failed then exec "/usr/local/bin/notify '📦 LXC Container 100 down'"

# VM Monitoring (Proxmox spezifisch)
check process vm-101 with pidfile /var/run/qemu-server/101.pid
    if failed then exec "/usr/local/bin/notify '💻 VM 101 nicht erreichbar'"
```

**Monit neu laden:**

```bash
# Nach Änderungen in monitrc
monit reload

# Status prüfen
monit status

# Test-Alarm auslösen (optional)
monit stop nginx
# Warte ~1 Minute, Monit sollte Alert senden und Nginx neustarten
```

### Beispiel 2: Cronjob Benachrichtigungen

```bash
# /etc/cron.d/backup-notify

# Täglicher Backup um 2 Uhr nachts
0 2 * * * root /opt/scripts/backup-with-notify.sh
```

**Backup-Script mit Benachrichtigung:**

```bash
# /opt/scripts/backup-with-notify.sh
#!/bin/bash

BACKUP_DIR="/backup"
DATE=$(date +%Y-%m-%d)
LOG="/var/log/backup.log"

# Start-Benachrichtigung
notify "🔄 Backup gestartet..."

# Backup durchführen
if /usr/local/bin/backup-script.sh > "$LOG" 2>&1; then
    SIZE=$(du -sh "$BACKUP_DIR/$DATE" | cut -f1)
    notify "✅ Backup erfolgreich ($SIZE)"
else
    ERROR=$(tail -5 "$LOG")
    notify "❌ Backup fehlgeschlagen! Fehler: $ERROR"
fi
```

### Beispiel 3: Terminal-Nutzung

```bash
# Einfache Nachricht
delta-notify "Test Nachricht"

# Mit Emojis
delta-notify "✅ Deployment erfolgreich"

# Via Pipe
echo "Server gestartet um $(date)" | delta-notify

# Aus Script
if systemctl is-active --quiet nginx; then
    delta-notify "✓ Nginx läuft"
else
    delta-notify "✗ Nginx ist down!"
fi

# Multiline-Nachrichten
delta-notify "Server Status:
CPU: 45%
RAM: 67%
Disk: 23%"

# Variablen in Nachricht
USAGE=$(df -h / | tail -1 | awk '{print $5}')
delta-notify "Disk Usage: $USAGE"

# Exit Code weitergeben
delta-notify "Command finished" && ./my-script.sh
```

### Beispiel 4: Proxmox Backup Server Integration

```bash
# Nach Proxmox Backup
# /etc/pve/vzdump.conf Hook

script: /usr/local/bin/backup-hook.sh
```

**Hook Script:**

```bash
#!/bin/bash
# /usr/local/bin/backup-hook.sh

# Wird von Proxmox mit Umgebungsvariablen aufgerufen
VMID="${VMID:-unknown}"
PHASE="${PHASE:-unknown}"

case "$PHASE" in
    job-start)
        notify "🔄 Backup Job gestartet"
        ;;
    backup-start)
        notify "📦 Backup VM $VMID gestartet"
        ;;
    backup-end)
        SIZE="${BACKUP_SIZE:-unknown}"
        notify "✅ Backup VM $VMID fertig ($SIZE)"
        ;;
    job-end)
        notify "✅ Backup Job komplett"
        ;;
    job-abort)
        notify "❌ Backup Job abgebrochen!"
        ;;
esac
```

### Beispiel 5: ZFS Pool Monitoring

```bash
# /usr/local/bin/check-zfs.sh
#!/bin/bash

# ZFS Pool Status prüfen
STATUS=$(zpool status -x)

if [ "$STATUS" != "all pools are healthy" ]; then
    notify "⚠️ ZFS Problem erkannt!

$STATUS"
fi
```

```bash
# In crontab: Stündlich prüfen
0 * * * * /usr/local/bin/check-zfs.sh
```

### Beispiel 6: Systemd Service Alerts

```bash
# Erstelle Service-Überwachung
cat > /etc/systemd/system/notify-failure@.service << 'EOF'
[Unit]
Description=Send Delta Chat notification on %i failure

[Service]
Type=oneshot
ExecStart=/usr/local/bin/notify "❌ Service %i ist fehlgeschlagen auf $(hostname)"
EOF

# Aktiviere für wichtige Services
# In /etc/systemd/system/wichtiger-dienst.service:
[Unit]
OnFailure=notify-failure@%n.service
```

### Beispiel 7: Disk Space Warning

```bash
#!/bin/bash
# /usr/local/bin/check-disk.sh

THRESHOLD=85
CRITICAL=95

for MOUNT in / /backup /var/lib/vz; do
    USAGE=$(df -h "$MOUNT" | tail -1 | awk '{print $5}' | sed 's/%//')

    if [ $USAGE -gt $CRITICAL ]; then
        notify "🔴 KRITISCH: $MOUNT bei ${USAGE}%"
    elif [ $USAGE -gt $THRESHOLD ]; then
        notify "⚠️ Warnung: $MOUNT bei ${USAGE}%"
    fi
done
```

```bash
# In crontab: Alle 15 Minuten
*/15 * * * * /usr/local/bin/check-disk.sh
```

---

## Erweiterte Konfiguration

### Mehrere Chats / Empfänger

Aktuell sendet `send.py` an den **ersten Chat**. Für mehrere Empfänger:

**Option 1: Gruppen-Chat erstellen**

```bash
# In Delta Chat App:
# 1. Erstelle Gruppe "Server Alerts"
# 2. Füge alle Admins hinzu
# 3. Sende Nachricht in Gruppe
# 4. Bot sendet jetzt an diese Gruppe
```

**Option 2: Script für spezifischen Chat** (TODO: Erweiterung)

```bash
# Zukünftige Funktion:
delta-notify --chat "Server Alerts" "Nachricht"
delta-notify --contact admin@example.org "Direkt-Nachricht"
```

### Logging aktivieren

```bash
# Wrapper mit Logging
cat > /usr/local/bin/notify-logged << 'EOF'
#!/bin/bash

MESSAGE="$*"
LOGFILE="/var/log/delta-notify.log"

{
    echo "[$(date -Iseconds)] Sending: $MESSAGE"
    /usr/local/bin/notify "$MESSAGE"
    EXIT=$?
    echo "[$(date -Iseconds)] Exit: $EXIT"
} | tee -a "$LOGFILE"

exit $EXIT
EOF

chmod +x /usr/local/bin/notify-logged
```

### Environment Variables

```bash
# Custom accounts directory
export ACCOUNTS_DIR="/custom/path/accounts"
delta-notify "Message"

# In Scripts:
ACCOUNTS_DIR=/other/path /opt/dechat/send.py "Message"
```

---

## Troubleshooting

### Problem: "No accounts found"

```bash
# Prüfen ob Account existiert
ls -la /opt/dechat/accounts/

# Sollte zeigen:
# accounts.toml
# accounts.lock
# <uuid>/

# Account neu konfigurieren
cd /opt/dechat
source venv/bin/activate
./setup-account.py
```

### Problem: "No chats found"

```bash
# Häufigste Ursache: Kein Chat in Delta Chat App erstellt!

# Lösung:
# 1. Öffne Delta Chat App
# 2. Erstelle neuen Chat mit Bot-Adresse
# 3. Sende irgendeine Nachricht
# 4. Versuche send.py erneut
```

### Problem: "Permission denied"

```bash
# send.py nicht executable
chmod +x /opt/dechat/send.py

# Oder direkt mit Python:
/opt/dechat/venv/bin/python3 /opt/dechat/send.py "Test"
```

### Problem: Nachrichten kommen verzögert an

```bash
# Bei normalen E-Mail-Accounts (Gmail etc.):
# - Kann 1-5 Minuten dauern (IMAP Polling)

# Lösung: Chatmail verwenden!
# - Nachrichten kommen in <5 Sekunden
# - Optimiert für Instant Delivery
```

### Problem: Script funktioniert als root, aber nicht im Cronjob

```bash
# Cron hat andere Umgebungsvariablen
# Lösung: Absoluter Pfad und User spezifizieren

# In crontab:
0 * * * * sudo -u dechat /opt/dechat/send.py "Cronjob läuft"

# Oder in Script:
#!/bin/bash
export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
sudo -u dechat /opt/dechat/send.py "$*"
```

### Problem: Nachrichten werden nicht gesendet (kein Fehler)

```bash
# Firewall blockiert IMAP/SMTP?
# Prüfen:
nc -zv testrun.org 993  # IMAP
nc -zv testrun.org 587  # SMTP

# UFW Firewall (falls aktiv):
ufw allow out 993/tcp
ufw allow out 587/tcp
```

### Debugging aktivieren

```bash
# Python Verbose Mode
/opt/dechat/venv/bin/python3 -v /opt/dechat/send.py "Test"

# RPC Server Logs ansehen
journalctl -u deltachat-rpc-server -f

# Account-Datenbank prüfen
sqlite3 /opt/dechat/accounts/<uuid>/dc.db
sqlite> SELECT * FROM chats;
sqlite> .quit
```

---

## Sicherheit

### Berechtigungen

```bash
# Restriktive Permissions für Account-Daten
chmod 700 /opt/dechat/accounts
chown dechat:dechat /opt/dechat/accounts

# Script kann von allen ausgeführt werden
chmod 755 /opt/dechat/send.py
```

### Firewall

```bash
# Nur ausgehende Verbindungen nötig
ufw allow out 993/tcp  # IMAP (verschlüsselt)
ufw allow out 587/tcp  # SMTP (STARTTLS)

# Keine eingehenden Ports nötig!
```

### Verschlüsselung

- **Transport:** TLS 1.2+ (IMAP/SMTP)
- **Ende-zu-Ende:** Autocrypt (PGP)
- **Automatisch:** Keys werden automatisch ausgetauscht
- **Vertrauenswürdig:** Nach erstem Kontakt

### Best Practices

```bash
# 1. Dedicated User verwenden
# ✓ Wir haben: dechat User
# ✗ Nicht als root laufen lassen

# 2. SELinux/AppArmor (optional)
# Für Paranoia-Level: Platinum

# 3. Keine Passwörter in Scripts
# ✓ Delta Chat speichert sie verschlüsselt in DB

# 4. Regelmäßige Updates
apt update && apt upgrade
pip install --upgrade deltachat-rpc-server deltachat-rpc-client
```

---

## Performance

### Typische Laufzeiten

```bash
# Erste Nachricht (Cold Start):
time delta-notify "Test"
# real: 0m2.5s  (~2.5 Sekunden)

# Nachfolgende Nachrichten:
# real: 0m1.2s  (~1.2 Sekunden)
```

**Breakdown:**
- RPC Start: 100ms
- Account Load: 50ms
- I/O Start: 500ms
- Message Send: 500ms
- Cleanup: 100ms

### Skalierung

**Geeignet für:**
- ✅ Monitoring Alerts (1-10/Stunde)
- ✅ Cronjob Status (10-50/Tag)
- ✅ Manuelle Scripts (beliebig)

**Nicht geeignet für:**
- ❌ High-Frequency Messaging (>100/Stunde)
- ❌ Real-Time Streaming
- ❌ Chat-Bots mit Interaktion

**Für High-Frequency:** Siehe Whitepaper Kapitel "Daemon Mode"

---

## Deinstallation

```bash
# Falls du es wieder entfernen willst:

# Systemweite Links entfernen
rm /usr/local/bin/delta-notify
rm /usr/local/bin/notify

# Dechat Installation entfernen
rm -rf /opt/dechat

# User entfernen
userdel dechat

# Monit Config bereinigen
# (manuell Abschnitte aus /etc/monit/monitrc entfernen)
```

---

## Weiterführende Dokumentation

### In diesem Repository

- **Whitepaper:** `WHITEPAPER_DELTA_CHAT_NOTIFICATIONS.md`
  - Vollständige technische Dokumentation
  - Architektur-Details
  - API-Analyse

- **API-Referenz:** `examples/delta-chat-notifications/SEND_API_ANALYSIS.md`
  - Detaillierte API-Dokumentation
  - Source Code Verweise

- **Englische Anleitung:** `examples/delta-chat-notifications/README_SEND.md`
  - Ausführliche englische Version
  - Mehr Integration-Beispiele

### Externe Links

- **Delta Chat:** https://delta.chat
- **Chatmail:** https://delta.chat/en/chatmail
- **Test-Server:** https://testrun.org
- **GitHub:** https://github.com/deChat-me/deChat

---

## Support

**Probleme oder Fragen?**

- GitHub Issues: https://github.com/deChat-me/deChat/issues
- Delta Chat Forum: https://support.delta.chat

**Community:**
- Delta Chat Support: https://support.delta.chat
- Matrix: #deltachat:matrix.org

---

## Changelog

| Version | Datum | Änderungen |
|---------|-------|------------|
| 1.0 | 2025-11-21 | Initiale Version |
| | | - Proxmox/Debian Anleitung |
| | | - Monit Integration |
| | | - Terminal-Beispiele |

---

## Credits

**Entwickelt von:** deChat-me Organization
**Basiert auf:** Delta Chat Core (https://delta.chat)
**Lizenz:** Mozilla Public License 2.0
**Contributor:** Claude (Anthropic AI Assistant)

---

**Viel Erfolg mit verschlüsselten Server-Benachrichtigungen! 🚀🔒**

*Fragen? → GitHub Issues oder Delta Chat Forum*
