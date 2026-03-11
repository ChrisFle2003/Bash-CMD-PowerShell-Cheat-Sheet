# CMD & Batch Komplettreferenz — Windows Command Line

> Umfassende Referenz fuer CMD.exe und Batch-Scripting unter Windows.
> Wissensbasis fuer Claude Code + Ollama MCP Tools.

---

## 1. Dateisystem — Navigation & Verzeichnisse

```cmd
:: Aktuelles Verzeichnis anzeigen
cd

:: Verzeichnis wechseln
cd C:\Users\chris\Desktop
cd ..
cd /d D:\Projekte                     :: Laufwerk + Verzeichnis wechseln

:: Verzeichnis erstellen
mkdir MeinOrdner
mkdir "Ordner mit Leerzeichen"
mkdir A\B\C                           :: Verschachtelt erstellen

:: Verzeichnis loeschen
rmdir MeinOrdner                      :: Nur wenn leer
rmdir /s /q MeinOrdner                :: Rekursiv + ohne Nachfrage

:: Verzeichnisinhalt auflisten
dir                                   :: Standard
dir /b                                :: Nur Namen (bare)
dir /s                                :: Rekursiv
dir /a                                :: Inkl. versteckte Dateien
dir /o:n                              :: Sortiert nach Name
dir /o:s                              :: Sortiert nach Groesse
dir /o:d                              :: Sortiert nach Datum
dir /a:d                              :: Nur Verzeichnisse
dir /a:-d                             :: Nur Dateien
dir *.txt                             :: Nur .txt Dateien
dir /s /b *.py                        :: Rekursiv alle .py (nur Pfade)

:: Verzeichnisstruktur als Baum
tree                                  :: Nur Ordner
tree /f                               :: Mit Dateien
tree /a                               :: ASCII-Zeichen
```

---

## 2. Dateien — Kopieren, Verschieben, Loeschen

```cmd
:: Kopieren
copy Quelle.txt Ziel.txt
copy *.txt C:\Backup\
copy /y Quelle.txt Ziel.txt           :: Ohne Nachfrage ueberschreiben

:: Robustes Kopieren (rekursiv, mit Struktur)
xcopy Quelle Ziel /e /i /h /y         :: Alles inkl. leer + versteckt
xcopy C:\Projekt D:\Backup /e /i /h /y /d  :: Nur neuere Dateien

:: Robocopy (beste Wahl fuer Backups)
robocopy Quelle Ziel                   :: Standard
robocopy Quelle Ziel /mir              :: Spiegeln (Ziel = exakte Kopie)
robocopy Quelle Ziel /e                :: Inkl. leere Unterverzeichnisse
robocopy Quelle Ziel /z                :: Wiederaufnehmbar
robocopy Quelle Ziel /mt:8             :: 8 Threads parallel
robocopy Quelle Ziel /xf *.log *.tmp   :: Dateien ausschliessen
robocopy Quelle Ziel /xd .git node_modules  :: Ordner ausschliessen
robocopy Quelle Ziel /log:copy.log     :: Log-Datei
robocopy Quelle Ziel /mir /mt:16 /r:3 /w:5  :: Mirror, 16 Threads, 3 Retries

:: Verschieben
move Datei.txt C:\Ziel\
move Alter_Name.txt Neuer_Name.txt     :: Umbenennen
move /y *.txt C:\Archiv\               :: Ohne Nachfrage

:: Umbenennen
ren Datei.txt NeueDatei.txt
ren *.jpeg *.jpg                       :: Bulk-Umbenennung

:: Loeschen
del Datei.txt
del /q *.tmp                           :: Ohne Nachfrage
del /s /q C:\Temp\*.log                :: Rekursiv
del /f /q Datei.txt                    :: Schreibgeschuetzte erzwingen
```

---

## 3. Dateiattribute, Suche & Vergleich

```cmd
:: Attribute anzeigen/setzen
attrib Datei.txt                       :: Anzeigen
attrib +r Datei.txt                    :: Schreibschutz setzen
attrib -r Datei.txt                    :: Schreibschutz entfernen
attrib +h Datei.txt                    :: Versteckt
attrib -h -s -r Datei.txt /s /d       :: Rekursiv alles entfernen

:: Dateien suchen
where /r C:\Projekt *.py               :: Rekursiv nach Pattern
dir /s /b C:\*.log                     :: Alle .log Dateien auf C:

:: In Dateiinhalt suchen
find "Suchbegriff" Datei.txt
find /i "suchbegriff" Datei.txt        :: Case-insensitive
find /c "Begriff" Datei.txt            :: Nur Anzahl Treffer
find /n "Begriff" Datei.txt            :: Mit Zeilennummern

:: Regulaere Ausdruecke mit FINDSTR
findstr "Pattern" Datei.txt
findstr /s /i "error" *.log            :: Rekursiv, case-insensitive
findstr /r "^Error.*[0-9]" Datei.txt   :: Regex
findstr /n /s /i "TODO" *.py           :: Zeilennummern, rekursiv
findstr /m "Passwort" *.txt            :: Nur Dateinamen

:: Dateivergleich
fc Datei1.txt Datei2.txt               :: Textvergleich
fc /b Datei1.bin Datei2.bin            :: Binaervergleich

:: Programmpfad finden
where python                           :: In PATH suchen
where /r C:\ python.exe                :: Rekursiv auf Laufwerk
```

---

## 4. Text & Dateiinhalt

```cmd
:: Text ausgeben
echo Hallo Welt
echo.                                  :: Leerzeile

:: Dateiinhalt anzeigen
type Datei.txt
type Datei.txt | more                  :: Seitenweise

:: In Datei schreiben
echo Text > Datei.txt                  :: Ueberschreiben
echo Mehr Text >> Datei.txt            :: Anfuegen

:: Mehrzeilige Datei erstellen
(
echo Zeile 1
echo Zeile 2
echo Zeile 3
) > Datei.txt

:: Dateien verketten
copy /b Datei1.txt+Datei2.txt Gesamt.txt

:: Zwischenablage
echo Text | clip                       :: Text in Zwischenablage
clip < Datei.txt                       :: Dateiinhalt in Zwischenablage

:: Sortieren
sort Datei.txt
sort /r Datei.txt                      :: Umgekehrt
```

---

## 5. Netzwerk

### IP & DNS

```cmd
:: IP-Konfiguration
ipconfig                               :: Basis
ipconfig /all                          :: Detailliert
ipconfig /release                      :: DHCP freigeben
ipconfig /renew                        :: DHCP erneuern
ipconfig /flushdns                     :: DNS-Cache leeren
ipconfig /displaydns                   :: DNS-Cache anzeigen

:: DNS-Abfragen
nslookup google.com
nslookup -type=mx google.com           :: MX-Records
nslookup -type=ns google.com           :: Nameserver
nslookup google.com 8.8.8.8            :: Bestimmter DNS-Server
```

### Erreichbarkeit & Routing

```cmd
:: Ping
ping google.com
ping -n 10 192.168.1.1                 :: 10 Pakete
ping -t 192.168.1.1                    :: Endlos (Ctrl+C stoppt)
ping -l 1500 -f 192.168.1.1           :: MTU-Test

:: Traceroute
tracert google.com
tracert -d google.com                  :: Ohne DNS-Aufloesung (schneller)
pathping google.com                    :: Detailliert mit Paketverlust

:: Routing
route print                            :: Routing-Tabelle
route add 10.0.0.0 mask 255.0.0.0 192.168.1.1

:: ARP
arp -a                                 :: ARP-Cache
```

### Verbindungen & Ports

```cmd
:: Netzwerkverbindungen
netstat                                :: Aktive Verbindungen
netstat -an                            :: Alle, numerisch
netstat -ano                           :: Mit PID
netstat -b                             :: Mit Prozessnamen (Admin)
netstat -e                             :: Ethernet-Statistik
netstat -r                             :: Routing-Tabelle
netstat -ano | findstr "LISTENING"     :: Offene Ports
netstat -ano | findstr ":8080"         :: Bestimmter Port
```

### Freigaben & Netzlaufwerke

```cmd
:: Netzlaufwerk verbinden
net use Z: \\Server\Freigabe
net use Z: \\Server\Freigabe /user:Domain\User Passwort
net use Z: /delete                     :: Trennen
net use                                :: Alle Zuordnungen

:: Freigaben
net share                              :: Lokale Freigaben anzeigen
net view \\Server                      :: Freigaben eines Servers
```

### Firewall & WiFi

```cmd
:: Firewall
netsh advfirewall show allprofiles     :: Status
netsh advfirewall set allprofiles state off   :: Deaktivieren
netsh advfirewall set allprofiles state on    :: Aktivieren
netsh advfirewall firewall add rule name="HTTP" dir=in action=allow protocol=tcp localport=80
netsh advfirewall firewall delete rule name="HTTP"
netsh advfirewall firewall show rule name=all  :: Alle Regeln

:: WiFi
netsh wlan show profiles               :: Gespeicherte Netzwerke
netsh wlan show profile name="SSID" key=clear  :: Passwort anzeigen
netsh wlan show interfaces             :: Aktuelle Verbindung
netsh wlan disconnect                  :: Trennen
```

### Download

```cmd
:: Download (ab Windows 10)
curl -o Datei.zip https://example.com/datei.zip
curl -L -o Datei.zip https://example.com/redirect     :: Redirects folgen
curl -s https://api.example.com/data                   :: Stille Ausgabe (API)
certutil -urlcache -split -f "https://example.com/datei.zip" Datei.zip
bitsadmin /transfer "Job" https://example.com/datei.zip C:\Ziel\Datei.zip
```

---

## 6. System & Hardware

```cmd
:: Systeminformationen
systeminfo                             :: Ausfuehrlich
systeminfo | find "OS"                 :: Nur OS-Info
hostname                               :: Computername
ver                                    :: Windows-Version

:: Hardware via WMIC
wmic cpu get name,numberofcores,maxclockspeed
wmic memorychip get capacity,speed,manufacturer
wmic diskdrive get model,size,status
wmic baseboard get product,manufacturer
wmic os get lastbootuptime             :: Letzter Boot

:: Umgebungsvariablen
set                                    :: Alle anzeigen
echo %PATH%
echo %USERPROFILE%
echo %APPDATA%
echo %TEMP%
echo %COMPUTERNAME%
echo %USERNAME%
echo %CD%                              :: Aktuelles Verzeichnis
echo %DATE%                            :: Datum
echo %TIME%                            :: Zeit
echo %RANDOM%                          :: Zufallszahl 0-32767
echo %ERRORLEVEL%                      :: Letzter Exit-Code

:: Setzen (Sitzung)
set MEINE_VAR=Wert
set PATH=%PATH%;C:\NeuerPfad

:: Permanent setzen (benoetigt Admin)
setx MEINE_VAR "Wert"
setx PATH "%PATH%;C:\NeuerPfad" /m     :: System-PATH
```

---

## 7. Prozesse & Dienste

```cmd
:: Prozesse anzeigen
tasklist                               :: Alle
tasklist /v                            :: Ausfuehrlich
tasklist /fi "imagename eq chrome.exe" :: Filtern nach Name
tasklist /fi "memusage gt 100000"      :: >100MB RAM
tasklist /fi "status eq not responding" :: Haengende Prozesse
tasklist /svc                          :: Mit Services

:: Prozess beenden
taskkill /im notepad.exe               :: Nach Name
taskkill /pid 1234                     :: Nach PID
taskkill /f /im chrome.exe             :: Erzwungen
taskkill /f /t /im cmd.exe             :: Mit Kindprozessen

:: Programm starten
start notepad.exe                      :: Neues Fenster
start "" "C:\Program Files\App\app.exe"  :: Pfad mit Leerzeichen
start /min cmd /c script.bat           :: Minimiert
start /wait setup.exe                  :: Warten bis beendet
start /b long_task.exe                 :: Hintergrund (gleiche Konsole)

:: Dienste
sc query                               :: Alle laufenden
sc query state=all                     :: Alle inkl. gestoppte
sc start "ServiceName"                 :: Starten
sc stop "ServiceName"                  :: Stoppen
sc config "ServiceName" start=auto     :: Autostart setzen
sc config "ServiceName" start=disabled :: Deaktivieren

:: Net-Befehle (einfacher)
net start                              :: Laufende Dienste
net start "ServiceName"
net stop "ServiceName"
```

---

## 8. Benutzer & Berechtigungen

```cmd
:: Aktueller Benutzer
whoami
whoami /groups                         :: Gruppenmitgliedschaften
whoami /priv                           :: Privilegien

:: Benutzerverwaltung
net user                               :: Alle Benutzer
net user Benutzername                  :: Details
net user Neuer_User Passwort /add      :: Erstellen
net user Benutzer /delete              :: Loeschen

:: Gruppen
net localgroup                         :: Alle lokalen Gruppen
net localgroup Administrators          :: Mitglieder anzeigen
net localgroup Administrators User /add :: Hinzufuegen

:: NTFS-Berechtigungen
icacls Datei.txt                       :: Anzeigen
icacls Ordner /grant User:(OI)(CI)F    :: Vollzugriff rekursiv
icacls Ordner /remove User             :: Entfernen
icacls Ordner /reset /t                :: Zuruecksetzen rekursiv
takeown /f Datei.txt                   :: Besitz uebernehmen
takeown /f Ordner /r                   :: Rekursiv

:: Admin-Ausfuehrung
runas /user:Administrator "cmd.exe"
```

---

## 9. Energie, Shutdown & Geplante Aufgaben

```cmd
:: Herunterfahren
shutdown /s /t 0                       :: Sofort ausschalten
shutdown /r /t 0                       :: Neustart sofort
shutdown /l                            :: Abmelden
shutdown /h                            :: Ruhezustand
shutdown /a                            :: Geplantes Shutdown abbrechen
shutdown /s /t 3600 /c "Wartung"       :: In 1h mit Nachricht

:: Energie
powercfg /batteryreport                :: Akku-Bericht (HTML)
powercfg /energy                       :: Energiediagnose

:: Geplante Aufgaben
schtasks /create /tn "MeinTask" /tr "C:\Scripts\backup.bat" /sc daily /st 22:00
schtasks /create /tn "Startup" /tr "script.bat" /sc onstart /ru SYSTEM
schtasks /query                        :: Alle anzeigen
schtasks /run /tn "MeinTask"           :: Sofort ausfuehren
schtasks /delete /tn "MeinTask" /f     :: Loeschen
:: Haeufigkeiten: MINUTE, HOURLY, DAILY, WEEKLY, MONTHLY, ONCE, ONSTART, ONLOGON
```

---

## 10. Datentraeger & Registry

### Datentraeger

```cmd
:: Laufwerke
wmic logicaldisk get name,size,freespace,filesystem
fsutil volume diskfree C:

:: Festplattencheck
chkdsk C:                              :: Pruefen
chkdsk C: /f                           :: Fehler beheben
chkdsk C: /r                           :: Sektoren pruefen + reparieren

:: Defragmentierung
defrag C: /O                           :: Optimieren

:: Systemreparatur
sfc /scannow                           :: Systemdateien pruefen
dism /online /cleanup-image /checkhealth
dism /online /cleanup-image /restorehealth
```

### Registry

```cmd
:: Abfragen
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion
reg query HKCU\Software /s             :: Rekursiv
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v ProductName

:: Wert setzen
reg add HKCU\Software\MeineApp /v Setting1 /t REG_SZ /d "Wert" /f
reg add HKLM\SOFTWARE\MeineApp /v Nummer /t REG_DWORD /d 42 /f

:: Loeschen
reg delete HKCU\Software\MeineApp /v Setting1 /f
reg delete HKCU\Software\MeineApp /f   :: Ganzer Schluessel

:: Exportieren / Importieren
reg export HKCU\Software\MeineApp backup.reg
reg import backup.reg

:: Typen: REG_SZ, REG_DWORD, REG_QWORD, REG_EXPAND_SZ, REG_MULTI_SZ, REG_BINARY
```

---

## 11. Komprimierung & Hashes

```cmd
:: ZIP (via PowerShell-Aufruf)
powershell Compress-Archive -Path "C:\Ordner" -DestinationPath "C:\archiv.zip"
powershell Expand-Archive -Path "C:\archiv.zip" -DestinationPath "C:\Ziel"

:: TAR (ab Windows 10)
tar -cf archiv.tar Ordner/             :: Erstellen
tar -czf archiv.tar.gz Ordner/        :: Erstellen + gzip
tar -xf archiv.tar                     :: Entpacken
tar -xzf archiv.tar.gz -C Ziel/      :: Entpacken nach Ziel

:: Hashes berechnen
certutil -hashfile Datei.txt SHA256
certutil -hashfile Datei.txt MD5
certutil -hashfile Datei.txt SHA1
```

---

## 12. Batch-Scripting — Grundlagen

### Variablen & Arithmetik

```cmd
@echo off

:: Variablen
set NAME=Welt
echo Hallo %NAME%

:: Benutzereingabe
set /p EINGABE=Bitte eingeben:
echo Du hast eingegeben: %EINGABE%

:: Arithmetik
set /a ERGEBNIS=5+3
set /a ERGEBNIS=%ERGEBNIS%*2
set /a COUNTER+=1
echo %ERGEBNIS%

:: String-Operationen
set TEXT=Hallo Welt
echo %TEXT:~0,5%                       :: Substring: "Hallo"
echo %TEXT:~6%                         :: Ab Position 6: "Welt"
echo %TEXT:Welt=Erde%                  :: Ersetzen: "Hallo Erde"
```

### IF / ELSE

```cmd
:: String-Vergleich
if "%VAR%"=="Wert" (
    echo Gleich
) else (
    echo Ungleich
)

:: Numerischer Vergleich
if %ZAHL% GEQ 10 echo Groesser oder gleich 10
:: EQU (==), NEQ (!=), LSS (<), LEQ (<=), GTR (>), GEQ (>=)

:: Datei/Ordner existiert?
if exist "Datei.txt" echo Datei existiert
if not exist "Ordner\" mkdir Ordner

:: Errorlevel pruefen
command.exe
if %errorlevel% neq 0 (
    echo Fehler: %errorlevel%
    exit /b 1
)

:: Kurzform
command.exe && echo Erfolg || echo Fehler
```

### Schleifen

```cmd
:: FOR mit Liste
for %%i in (Apfel Birne Kirsche) do echo %%i

:: FOR mit Dateien
for %%f in (*.txt) do echo %%f

:: FOR rekursiv
for /r C:\Projekt %%f in (*.py) do echo %%f

:: FOR mit Verzeichnissen
for /d %%d in (C:\Users\*) do echo %%d

:: FOR mit Zahlenbereich (Start, Schritt, Ende)
for /l %%i in (1,1,10) do echo Nummer %%i

:: FOR mit Dateiinhalt (CSV parsen)
for /f "tokens=1,2 delims=," %%a in (daten.csv) do (
    echo Spalte1: %%a  Spalte2: %%b
)

:: FOR mit Befehlsausgabe
for /f "delims=" %%i in ('dir /b *.txt') do echo Datei: %%i

:: FOR /F Optionen:
::   tokens=1,2,3*    Welche Felder extrahieren
::   delims=,;        Trennzeichen
::   skip=1           Erste N Zeilen ueberspringen
::   eol=;            Kommentarzeichen
::   usebackq         Backticks fuer Befehle
```

### Funktionen & Subroutinen

```cmd
@echo off
call :MeineFunktion "Parameter1" "Parameter2"
echo Rueckgabewert: %ERRORLEVEL%
goto :eof

:MeineFunktion
echo Param 1: %~1
echo Param 2: %~2
exit /b 0
```

### Delayed Expansion

```cmd
@echo off
setlocal enabledelayedexpansion

set COUNTER=0
for %%f in (*.txt) do (
    set /a COUNTER+=1
    echo !COUNTER!: %%f
)
echo Gesamt: !COUNTER!
endlocal
```

### Fehlerbehandlung

```cmd
@echo off
command1.exe
if %errorlevel% neq 0 goto :error

command2.exe
if %errorlevel% neq 0 goto :error

echo Alles erfolgreich
goto :end

:error
echo FEHLER (Code: %errorlevel%)
exit /b 1

:end
exit /b 0
```

---

## 13. Pipes & Umleitung

```cmd
:: Ausgabe umleiten
command > datei.txt                    :: stdout ueberschreiben
command >> datei.txt                   :: stdout anfuegen
command 2> fehler.txt                  :: stderr in Datei
command 2>&1                           :: stderr nach stdout
command > datei.txt 2>&1               :: Alles in eine Datei
command > nul                          :: stdout verwerfen
command 2> nul                         :: stderr verwerfen
command > nul 2>&1                     :: Alles verwerfen

:: Pipes
dir | find "txt"
dir | sort
type log.txt | findstr /i "error"
tasklist | findstr "chrome"

:: Befehlsverkettung
command1 & command2                    :: Immer beide
command1 && command2                   :: Nur wenn 1 OK
command1 || command2                   :: Nur wenn 1 FEHLER
```

---

## 14. Nuetzliche Patterns & Einzeiler

```cmd
:: Zeitstempel generieren
set TIMESTAMP=%date:~6,4%-%date:~3,2%-%date:~0,2%_%time:~0,2%-%time:~3,2%

:: Temporaere Datei
set TMPFILE=%TEMP%\mcp_%RANDOM%.tmp

:: Admin-Check
net session >nul 2>&1
if %errorlevel% neq 0 (
    echo Bitte als Administrator ausfuehren!
    exit /b 1
)

:: Batch-Datei-Verzeichnis
set SCRIPTDIR=%~dp0

:: Alle Dateien >100MB finden
forfiles /s /m *.* /c "cmd /c if @fsize GEQ 104857600 echo @path @fsize"

:: Dateien aelter als 30 Tage loeschen
forfiles /p C:\Logs /s /m *.log /d -30 /c "cmd /c del @path"

:: Lokale IP-Adresse extrahieren
for /f "tokens=2 delims=:" %%a in ('ipconfig ^| findstr /i "IPv4"') do echo%%a

:: Schnelles Backup mit Zeitstempel
set BKP=Backup_%date:~6,4%%date:~3,2%%date:~0,2%
robocopy C:\Projekt "D:\Backups\%BKP%" /mir /mt:8

:: Installierte Programme auflisten
wmic product get name,version | sort

:: RAM-Top-Verbraucher
tasklist /fi "memusage gt 50000" /fo table /nh | sort /r

:: Windows-Lizenzstatus
slmgr /xpr
```

---

## 15. Entwickler-Tools via CMD

```cmd
:: Python
python --version
python script.py
python -m pip install paket
python -m venv .venv
.venv\Scripts\activate.bat

:: Node.js
node --version
npm install
npm run build
npx create-react-app app

:: Git
git status
git add .
git commit -m "Nachricht"
git push origin main
git log --oneline -10
git diff
git branch -a
git checkout -b feature
git stash && git stash pop

:: Docker
docker ps
docker images
docker run -d -p 8080:80 nginx
docker-compose up -d
docker exec -it container cmd

:: WSL
wsl --list --verbose
wsl --install -d Ubuntu
wsl -d Ubuntu -- ls -la
```

---

## 16. Exit-Codes

| Code | Bedeutung |
|------|-----------|
| `0` | Erfolg |
| `1` | Allgemeiner Fehler |
| `2` | Datei nicht gefunden |
| `3` | Pfad nicht gefunden |
| `5` | Zugriff verweigert |
| `9009` | Programm nicht in PATH |
| `-1073741819` | Access Violation / Absturz |

---

## 17. Sonderzeichen & Escaping

| Zeichen | Escape | Beispiel |
|---------|--------|----------|
| `%` | `%%` | `echo 100%%` |
| `&` | `^&` | `echo Tom ^& Jerry` |
| `<` | `^<` | `echo 5 ^< 10` |
| `>` | `^>` | `echo ^>Pfeil` |
| `\|` | `^\|` | `echo A ^\| B` |
| `^` | `^^` | `echo ^^Caret` |
| `!` | `^^!` | (bei delayed expansion) |

---

## 18. Diagnose & Reparatur

```cmd
:: Event-Log
wevtutil qe System /c:10 /f:text      :: Letzte 10 System-Events
wevtutil qe Application /c:5 /f:text  :: Letzte 5 App-Events

:: Performance-Counter
typeperf "\Processor(_Total)\% Processor Time" -sc 5

:: Treiber
driverquery
driverquery /v /fo csv > treiber.csv

:: System File Checker
sfc /scannow

:: DISM
dism /online /cleanup-image /checkhealth
dism /online /cleanup-image /restorehealth

:: Boot-Konfiguration
bcdedit                                :: Boot-Eintraege anzeigen
```
