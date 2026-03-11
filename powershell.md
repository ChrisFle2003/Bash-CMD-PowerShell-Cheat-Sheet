# PowerShell Komplettreferenz — Windows Automation

> Umfassende Referenz fuer Windows PowerShell 5.1 und PowerShell 7+.
> Wissensbasis fuer Claude Code + Ollama MCP Tools.

---

## 1. Grundlagen & Navigation

```powershell
# Hilfe & Discovery
Get-Command                            # Alle verfuegbaren Befehle
Get-Command -Verb Get                  # Alle Get-* Befehle
Get-Command -Noun Process              # Alle *-Process Befehle
Get-Command -Module Microsoft.PowerShell.Management
Get-Help Get-Process                   # Hilfe zu Befehl
Get-Help Get-Process -Examples         # Nur Beispiele
Get-Help Get-Process -Full             # Komplette Doku
Get-Help about_*                       # Konzept-Hilfe
Update-Help                            # Hilfe aktualisieren

# Version & Host
$PSVersionTable                        # PowerShell-Version
$Host                                  # Host-Info
Get-Module -ListAvailable              # Installierte Module

# Navigation
Get-Location                           # Aktuelles Verzeichnis (pwd)
Set-Location C:\Users                  # Verzeichnis wechseln (cd)
Push-Location C:\Temp                  # Verzeichnis merken + wechseln
Pop-Location                           # Zurueck zum gemerkten Verzeichnis

# Aliase
Get-Alias                              # Alle Aliase anzeigen
Set-Alias -Name ll -Value Get-ChildItem
New-Alias -Name grep -Value Select-String

# History
Get-History                            # Befehlsverlauf
Get-History -Count 20                  # Letzte 20
Invoke-History 5                       # Befehl #5 erneut ausfuehren
Clear-History                          # Verlauf loeschen
```

---

## 2. Dateisystem

### Verzeichnisse & Dateien auflisten

```powershell
# Auflisten
Get-ChildItem                          # ls / dir
Get-ChildItem -Path C:\Users           # Bestimmter Pfad
Get-ChildItem -Recurse                 # Rekursiv
Get-ChildItem -Force                   # Inkl. versteckte
Get-ChildItem -Filter *.txt            # Nur .txt
Get-ChildItem -Include *.py,*.js -Recurse  # Mehrere Pattern
Get-ChildItem -Exclude *.log -Recurse  # Ausschliessen
Get-ChildItem -Directory               # Nur Verzeichnisse
Get-ChildItem -File                    # Nur Dateien
Get-ChildItem -Depth 2                 # Max. 2 Ebenen tief

# Sortieren & Filtern
Get-ChildItem | Sort-Object Length -Descending            # Nach Groesse
Get-ChildItem | Sort-Object LastWriteTime -Descending     # Nach Datum
Get-ChildItem | Where-Object { $_.Length -gt 1MB }        # Groesser 1MB
Get-ChildItem | Where-Object { $_.Extension -eq ".py" }   # Nur .py
Get-ChildItem -Recurse | Measure-Object -Property Length -Sum  # Gesamtgroesse
```

### Erstellen, Kopieren, Verschieben, Loeschen

```powershell
# Verzeichnisse
New-Item -ItemType Directory -Path "C:\MeinOrdner"
New-Item -ItemType Directory -Path "A\B\C" -Force         # Verschachtelt

# Dateien erstellen
New-Item -ItemType File -Path "datei.txt"
Set-Content -Path "datei.txt" -Value "Inhalt"
"Zeile 1`nZeile 2" | Out-File -FilePath "datei.txt"
Add-Content -Path "datei.txt" -Value "Neue Zeile"         # Anfuegen

# Kopieren
Copy-Item "quelle.txt" "ziel.txt"
Copy-Item "Ordner" "Backup" -Recurse                      # Rekursiv
Copy-Item "*.txt" "C:\Backup\"                             # Pattern

# Verschieben & Umbenennen
Move-Item "alt.txt" "neu.txt"                              # Umbenennen
Move-Item "datei.txt" "C:\Ziel\"                           # Verschieben

# Loeschen
Remove-Item "datei.txt"
Remove-Item "Ordner" -Recurse -Force                       # Rekursiv + erzwungen
Remove-Item "*.tmp" -Force                                 # Pattern

# Papierkorb leeren
Clear-RecycleBin -Force

# Test ob existiert
Test-Path "C:\datei.txt"                                   # True/False
Test-Path "C:\Ordner" -PathType Container                  # Ist Verzeichnis?
Test-Path "C:\datei.txt" -PathType Leaf                    # Ist Datei?
```

### Dateiinhalt lesen & schreiben

```powershell
# Lesen
Get-Content "datei.txt"                                    # Ganze Datei
Get-Content "datei.txt" -First 10                          # Erste 10 Zeilen
Get-Content "datei.txt" -Last 5                            # Letzte 5 Zeilen
Get-Content "datei.txt" -Tail 5 -Wait                     # tail -f (live)
Get-Content "datei.txt" -Encoding UTF8
(Get-Content "datei.txt").Count                            # Zeilenanzahl

# Schreiben
Set-Content "datei.txt" "Neuer Inhalt"                     # Ueberschreiben
Add-Content "datei.txt" "Zeile anfuegen"                   # Anfuegen
"Text" | Out-File "datei.txt"                              # Via Pipeline
"Text" | Out-File "datei.txt" -Append                      # Anfuegen

# Ersetzen
(Get-Content "datei.txt") -replace "alt","neu" | Set-Content "datei.txt"
(Get-Content "datei.txt").Replace("alt","neu") | Set-Content "datei.txt"
```

### Suchen

```powershell
# In Dateien suchen (grep-Equivalent)
Select-String -Path "*.txt" -Pattern "Suchbegriff"
Select-String -Path "*.log" -Pattern "error" -CaseSensitive
Select-String -Path "*.py" -Pattern "TODO" -Recurse
Select-String -Path "*.cs" -Pattern "class\s+\w+" -AllMatches  # Regex

# Dateien nach Kriterien finden
Get-ChildItem -Recurse | Where-Object { $_.Length -gt 100MB }
Get-ChildItem -Recurse | Where-Object { $_.LastWriteTime -gt (Get-Date).AddDays(-7) }
Get-ChildItem -Recurse -Filter "*.log" | Where-Object { $_.Length -gt 10MB }
```

### Berechtigungen & Attribute

```powershell
# NTFS-Berechtigungen
Get-Acl "C:\Ordner"
(Get-Acl "C:\Ordner").Access                               # Detailliert
Set-Acl -Path "C:\Ordner" -AclObject $acl

# ACL-Regel hinzufuegen
$acl = Get-Acl "C:\Ordner"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "User","FullControl","ContainerInherit,ObjectInherit","None","Allow"
)
$acl.SetAccessRule($rule)
Set-Acl "C:\Ordner" $acl

# Besitz uebernehmen
$acl = Get-Acl "C:\Datei.txt"
$acl.SetOwner([System.Security.Principal.NTAccount]"$env:USERNAME")
Set-Acl "C:\Datei.txt" $acl

# Attribute
(Get-Item "datei.txt").Attributes
Set-ItemProperty "datei.txt" -Name Attributes -Value "ReadOnly"
Set-ItemProperty "datei.txt" -Name Attributes -Value "Normal"     # Zuruecksetzen
```

---

## 3. Prozesse

```powershell
# Prozesse auflisten
Get-Process                                                # Alle
Get-Process -Name "chrome"                                 # Nach Name
Get-Process -Id 1234                                       # Nach PID
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10
Get-Process | Sort-Object WorkingSet64 -Descending | Select-Object -First 10
Get-Process | Where-Object { $_.CPU -gt 60 }               # CPU-Last > 60s
Get-Process | Where-Object { $_.WorkingSet64 -gt 500MB }   # RAM > 500MB

# Details
Get-Process -Name "chrome" | Format-List *                 # Alle Eigenschaften
Get-Process -Name "chrome" | Select-Object Name,Id,CPU,WorkingSet64,StartTime

# Prozess starten
Start-Process "notepad.exe"
Start-Process "notepad.exe" -ArgumentList "C:\datei.txt"
Start-Process "setup.exe" -Wait                            # Warten bis beendet
Start-Process "cmd.exe" -Verb RunAs                        # Als Admin
Start-Process "script.bat" -WindowStyle Hidden             # Versteckt
Start-Process "powershell" -ArgumentList "-File script.ps1" -NoNewWindow

# Prozess beenden
Stop-Process -Name "notepad"
Stop-Process -Id 1234
Stop-Process -Name "chrome" -Force                         # Erzwungen
Get-Process -Name "node" | Stop-Process                    # Via Pipeline

# Warten
Wait-Process -Name "setup"
Wait-Process -Id 1234 -Timeout 60                          # Max 60s

# Debugging
Debug-Process -Name "Explorer"
```

---

## 4. Dienste (Services)

```powershell
# Auflisten
Get-Service                                                # Alle
Get-Service -Name "wuauserv"                               # Windows Update
Get-Service -DisplayName "*SQL*"                           # Nach Anzeigename
Get-Service | Where-Object { $_.Status -eq "Running" }     # Nur laufende
Get-Service | Where-Object { $_.StartType -eq "Automatic" -and $_.Status -ne "Running" }

# Steuern
Start-Service -Name "wuauserv"
Stop-Service -Name "wuauserv"
Restart-Service -Name "wuauserv"
Suspend-Service -Name "Dienst"                             # Pausieren
Resume-Service -Name "Dienst"                              # Fortsetzen

# Konfigurieren
Set-Service -Name "Dienst" -StartupType Automatic
Set-Service -Name "Dienst" -StartupType Disabled
Set-Service -Name "Dienst" -DisplayName "Neuer Name"

# Abhaengigkeiten
Get-Service -Name "wuauserv" -DependentServices            # Was haengt davon ab
Get-Service -Name "wuauserv" -RequiredServices              # Was wird benoetigt
```

---

## 5. Netzwerk

### IP, DNS & Adapter

```powershell
# IP-Konfiguration
Get-NetIPAddress                                           # Alle IP-Adressen
Get-NetIPAddress -AddressFamily IPv4                        # Nur IPv4
Get-NetIPAddress -InterfaceAlias "Ethernet"                 # Bestimmter Adapter
Get-NetIPConfiguration                                     # Ausfuehrlich (ipconfig-Equivalent)

# Adapter
Get-NetAdapter                                             # Alle Adapter
Get-NetAdapter | Where-Object { $_.Status -eq "Up" }       # Nur aktive
Enable-NetAdapter -Name "Ethernet"
Disable-NetAdapter -Name "WiFi" -Confirm:$false

# DNS
Resolve-DnsName "google.com"
Resolve-DnsName "google.com" -Type MX                      # MX-Records
Resolve-DnsName "google.com" -Type NS                      # Nameserver
Resolve-DnsName "google.com" -Server 8.8.8.8               # Bestimmter DNS
Clear-DnsClientCache                                       # DNS-Cache leeren
Get-DnsClientCache                                         # DNS-Cache anzeigen

# DNS-Konfiguration
Get-DnsClientServerAddress
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "8.8.8.8","8.8.4.4"
```

### Verbindungstest & Ports

```powershell
# Ping
Test-Connection "google.com"
Test-Connection "google.com" -Count 5                      # 5 Pakete
Test-Connection "192.168.1.1" -Quiet                       # Nur True/False
Test-Connection "server" -IPv6

# Port-Test
Test-NetConnection "google.com" -Port 443
Test-NetConnection "server" -Port 3389 -InformationLevel Detailed
Test-NetConnection "localhost" -Port 8080

# TCP-Verbindungen
Get-NetTCPConnection                                       # Alle
Get-NetTCPConnection -State Listen                         # Listening
Get-NetTCPConnection -LocalPort 80                         # Port 80
Get-NetTCPConnection | Where-Object { $_.State -eq "Established" }

# Traceroute
Test-NetConnection "google.com" -TraceRoute
```

### Firewall

```powershell
# Status
Get-NetFirewallProfile                                     # Alle Profile
Get-NetFirewallProfile -Name Domain                        # Domain-Profil

# Regeln
Get-NetFirewallRule | Where-Object { $_.Enabled -eq $true }
Get-NetFirewallRule -DisplayName "*Remote*"

# Regel erstellen
New-NetFirewallRule -DisplayName "HTTP" -Direction Inbound -Protocol TCP -LocalPort 80 -Action Allow
New-NetFirewallRule -DisplayName "App" -Direction Inbound -Program "C:\App\app.exe" -Action Allow

# Regel aendern/loeschen
Set-NetFirewallRule -DisplayName "HTTP" -Enabled False
Remove-NetFirewallRule -DisplayName "HTTP"

# Profil ein/ausschalten
Set-NetFirewallProfile -Profile Domain -Enabled True
Set-NetFirewallProfile -Profile Public -Enabled False
```

### Downloads & Web-Requests

```powershell
# Datei herunterladen
Invoke-WebRequest -Uri "https://example.com/datei.zip" -OutFile "datei.zip"
Invoke-WebRequest -Uri "https://example.com/datei.zip" -OutFile "datei.zip" -UseBasicParsing

# REST-API aufrufen
Invoke-RestMethod -Uri "https://api.example.com/data"
Invoke-RestMethod -Uri "https://api.example.com/data" -Method Post -Body (@{key="value"} | ConvertTo-Json) -ContentType "application/json"
Invoke-RestMethod -Uri "https://api.example.com/data" -Headers @{Authorization="Bearer TOKEN"}

# Web-Scraping
$page = Invoke-WebRequest -Uri "https://example.com"
$page.Links                                                # Alle Links
$page.Images                                               # Alle Bilder
$page.Forms                                                # Alle Formulare
$page.Content                                              # HTML-Inhalt
```

---

## 6. System & Hardware

### Systeminformationen

```powershell
# OS-Info
Get-ComputerInfo
Get-CimInstance Win32_OperatingSystem | Select-Object Caption,Version,BuildNumber,OSArchitecture,LastBootUpTime
[System.Environment]::OSVersion
$env:COMPUTERNAME
$env:USERNAME
$env:USERPROFILE

# CPU
Get-CimInstance Win32_Processor | Select-Object Name,NumberOfCores,NumberOfLogicalProcessors,MaxClockSpeed,LoadPercentage

# RAM
Get-CimInstance Win32_OperatingSystem | Select-Object `
    @{N='TotalGB';E={[math]::Round($_.TotalVisibleMemorySize/1MB,2)}},
    @{N='FreeGB';E={[math]::Round($_.FreePhysicalMemory/1MB,2)}},
    @{N='UsedPercent';E={[math]::Round((1-$_.FreePhysicalMemory/$_.TotalVisibleMemorySize)*100,1)}}

# Festplatten
Get-CimInstance Win32_LogicalDisk -Filter "DriveType=3" | Select-Object DeviceID,
    @{N='SizeGB';E={[math]::Round($_.Size/1GB,1)}},
    @{N='FreeGB';E={[math]::Round($_.FreeSpace/1GB,1)}},
    @{N='UsedPercent';E={[math]::Round((1-$_.FreeSpace/$_.Size)*100,1)}}

# BIOS & Mainboard
Get-CimInstance Win32_BIOS
Get-CimInstance Win32_BaseBoard | Select-Object Manufacturer,Product

# GPU
Get-CimInstance Win32_VideoController | Select-Object Name,AdapterRAM,DriverVersion

# Uptime
(Get-Date) - (Get-CimInstance Win32_OperatingSystem).LastBootUpTime
```

### Umgebungsvariablen

```powershell
# Lesen
$env:PATH
$env:USERPROFILE
$env:APPDATA
$env:TEMP
$env:COMPUTERNAME
[System.Environment]::GetEnvironmentVariables()            # Alle

# Setzen (Sitzung)
$env:MEINE_VAR = "Wert"
$env:PATH += ";C:\NeuerPfad"

# Permanent setzen
[System.Environment]::SetEnvironmentVariable("MEINE_VAR","Wert","User")
[System.Environment]::SetEnvironmentVariable("MEINE_VAR","Wert","Machine")  # Admin

# Loeschen
Remove-Item Env:\MEINE_VAR
[System.Environment]::SetEnvironmentVariable("MEINE_VAR",$null,"User")
```

### Energie & Shutdown

```powershell
# Herunterfahren / Neustart
Stop-Computer                                              # Herunterfahren
Stop-Computer -Force                                       # Erzwungen
Restart-Computer                                           # Neustart
Restart-Computer -Force

# Abmelden
logoff

# Energie
powercfg /batteryreport                                    # Akku-Bericht
powercfg /energy                                           # Energiediagnose
Get-CimInstance Win32_Battery | Select-Object EstimatedChargeRemaining
```

---

## 7. Benutzer & Sicherheit

```powershell
# Aktueller Benutzer
whoami
[System.Security.Principal.WindowsIdentity]::GetCurrent().Name
# Admin-Check
([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)

# Lokale Benutzer
Get-LocalUser                                              # Alle
Get-LocalUser -Name "chris"                                # Bestimmter
New-LocalUser -Name "TestUser" -Password (ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force)
Remove-LocalUser -Name "TestUser"
Enable-LocalUser -Name "TestUser"
Disable-LocalUser -Name "TestUser"
Set-LocalUser -Name "TestUser" -Password (ConvertTo-SecureString "Neu!" -AsPlainText -Force)

# Lokale Gruppen
Get-LocalGroup
Get-LocalGroupMember -Group "Administrators"
Add-LocalGroupMember -Group "Administrators" -Member "TestUser"
Remove-LocalGroupMember -Group "Administrators" -Member "TestUser"

# Credentials
$cred = Get-Credential                                     # Dialog
$cred = New-Object PSCredential("User",(ConvertTo-SecureString "Pass" -AsPlainText -Force))
```

---

## 8. Registry

```powershell
# Navigation (Registry als Laufwerk)
Set-Location HKCU:\Software
Get-ChildItem HKLM:\SOFTWARE\Microsoft
Get-ChildItem HKCU:\Software -Recurse -Depth 2

# Werte lesen
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion" -Name "ProductName"
(Get-ItemProperty "HKCU:\Software\MeineApp").Setting1

# Schluessel erstellen
New-Item -Path "HKCU:\Software\MeineApp"
New-Item -Path "HKCU:\Software\MeineApp\SubKey" -Force     # Verschachtelt

# Werte setzen
Set-ItemProperty -Path "HKCU:\Software\MeineApp" -Name "Setting1" -Value "Wert"
New-ItemProperty -Path "HKCU:\Software\MeineApp" -Name "Nummer" -Value 42 -PropertyType DWord

# Loeschen
Remove-ItemProperty -Path "HKCU:\Software\MeineApp" -Name "Setting1"
Remove-Item -Path "HKCU:\Software\MeineApp" -Recurse       # Ganzer Schluessel

# Registry-Typen: String, ExpandString, Binary, DWord, QWord, MultiString
```

---

## 9. Geplante Aufgaben

```powershell
# Auflisten
Get-ScheduledTask
Get-ScheduledTask -TaskName "MeinTask"
Get-ScheduledTask | Where-Object { $_.State -eq "Running" }

# Erstellen
$trigger = New-ScheduledTaskTrigger -Daily -At "22:00"
$action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-File C:\Scripts\backup.ps1"
Register-ScheduledTask -TaskName "Backup" -Trigger $trigger -Action $action -RunLevel Highest

# Weitere Trigger
New-ScheduledTaskTrigger -Once -At "2025-12-31 23:59"
New-ScheduledTaskTrigger -Weekly -DaysOfWeek Monday -At "08:00"
New-ScheduledTaskTrigger -AtStartup
New-ScheduledTaskTrigger -AtLogOn

# Steuern
Start-ScheduledTask -TaskName "Backup"
Stop-ScheduledTask -TaskName "Backup"
Disable-ScheduledTask -TaskName "Backup"
Enable-ScheduledTask -TaskName "Backup"
Unregister-ScheduledTask -TaskName "Backup" -Confirm:$false
```

---

## 10. Event-Log & Monitoring

```powershell
# Event-Logs auflisten
Get-WinEvent -ListLog *
Get-WinEvent -ListLog * | Where-Object { $_.RecordCount -gt 0 }

# Events abfragen
Get-WinEvent -LogName System -MaxEvents 20
Get-WinEvent -LogName Application -MaxEvents 10
Get-WinEvent -LogName Security -MaxEvents 5

# Filtern
Get-WinEvent -FilterHashtable @{LogName='System'; Level=2}          # Fehler
Get-WinEvent -FilterHashtable @{LogName='System'; Level=2,3}        # Fehler + Warnungen
Get-WinEvent -FilterHashtable @{LogName='Application'; StartTime=(Get-Date).AddHours(-1)}
Get-WinEvent -FilterHashtable @{LogName='System'; Id=6005,6006}     # Start/Stop Events

# Performance-Counter
Get-Counter "\Processor(_Total)\% Processor Time"
Get-Counter "\Memory\Available MBytes"
Get-Counter "\PhysicalDisk(_Total)\% Disk Time"
Get-Counter -Counter "\Processor(_Total)\% Processor Time" -SampleInterval 2 -MaxSamples 5
```

---

## 11. Komprimierung & Hashes

```powershell
# ZIP komprimieren
Compress-Archive -Path "C:\Ordner" -DestinationPath "C:\archiv.zip"
Compress-Archive -Path "*.txt" -DestinationPath "texte.zip"
Compress-Archive -Path "C:\Ordner" -DestinationPath "archiv.zip" -CompressionLevel Optimal
Compress-Archive -Path "C:\Ordner" -Update -DestinationPath "archiv.zip"  # Hinzufuegen

# ZIP entpacken
Expand-Archive -Path "archiv.zip" -DestinationPath "C:\Ziel"
Expand-Archive -Path "archiv.zip" -DestinationPath "C:\Ziel" -Force       # Ueberschreiben

# Hashes
Get-FileHash "datei.txt"                                   # SHA256 (default)
Get-FileHash "datei.txt" -Algorithm MD5
Get-FileHash "datei.txt" -Algorithm SHA1
Get-FileHash "datei.txt" -Algorithm SHA512

# Hash vergleichen
(Get-FileHash "datei1.txt").Hash -eq (Get-FileHash "datei2.txt").Hash
```

---

## 12. Scripting — Grundlagen

### Variablen & Datentypen

```powershell
# Variablen
$name = "Welt"
$zahl = 42
$bool = $true
$null_wert = $null
$array = @(1, 2, 3, 4, 5)
$hash = @{Name="Chris"; Alter=30}

# Datentypen
[string]$text = "Hallo"
[int]$nummer = 42
[double]$dezimal = 3.14
[bool]$flag = $true
[datetime]$datum = Get-Date
[array]$liste = @()
[hashtable]$dict = @{}

# String-Operationen
"Hallo $name"                          # Variable interpolieren
'Hallo $name'                          # Literal (keine Interpolation)
"Hallo" + " Welt"                      # Konkatenation
"Hallo Welt".ToUpper()                 # HALLO WELT
"Hallo Welt".ToLower()                 # hallo welt
"Hallo Welt".Replace("Welt","Erde")    # Hallo Erde
"Hallo Welt".Split(" ")               # Array: Hallo, Welt
"Hallo Welt".Substring(6)             # Welt
"Hallo Welt".Contains("Welt")         # True
"Hallo Welt".StartsWith("Hallo")      # True
"Hallo Welt" -match "H\w+"            # True (Regex)
"Hallo Welt" -replace "Welt","Erde"   # Hallo Erde
"text".PadLeft(10)                     # Rechtsbuendig
"text".PadRight(10)                    # Linksbuendig

# Arrays
$arr = @(1,2,3)
$arr += 4                              # Hinzufuegen (erstellt neues Array)
$arr[0]                                # Erstes Element
$arr[-1]                               # Letztes Element
$arr[1..3]                             # Slice
$arr.Count                             # Laenge
$arr -contains 3                       # Enthalten?
$arr | ForEach-Object { $_ * 2 }      # Jedes Element verdoppeln
$arr | Where-Object { $_ -gt 2 }      # Filtern

# Hashtables
$h = @{Name="Chris"; Alter=30}
$h.Name                                # Zugriff
$h["Name"]                             # Zugriff (Alternative)
$h.Add("Stadt","Berlin")               # Hinzufuegen
$h.Remove("Stadt")                     # Entfernen
$h.Keys                                # Alle Schluessel
$h.Values                              # Alle Werte
$h.ContainsKey("Name")                 # True
```

### Kontrollstrukturen

```powershell
# IF / ELSEIF / ELSE
if ($zahl -gt 10) {
    "Groesser als 10"
} elseif ($zahl -eq 10) {
    "Genau 10"
} else {
    "Kleiner als 10"
}

# Vergleichsoperatoren
# -eq (gleich), -ne (ungleich), -gt (groesser), -ge (groesser/gleich)
# -lt (kleiner), -le (kleiner/gleich)
# -like (Wildcard), -notlike, -match (Regex), -notmatch
# -contains, -notcontains, -in, -notin
# -is (Typ), -isnot

# Switch
switch ($status) {
    "OK"    { "Alles gut" }
    "WARN"  { "Warnung!" }
    "ERROR" { "Fehler!" }
    default { "Unbekannt" }
}

# Switch mit Regex
switch -Regex ($text) {
    "^Error"   { "Fehler erkannt" }
    "Warning"  { "Warnung erkannt" }
    "\d{4}"    { "Nummer erkannt" }
}

# Ternary (PS 7+)
$ergebnis = $zahl -gt 10 ? "gross" : "klein"
```

### Schleifen

```powershell
# ForEach-Object (Pipeline)
1..10 | ForEach-Object { $_ * 2 }
Get-ChildItem | ForEach-Object { $_.Name }

# foreach (Statement)
foreach ($item in $array) {
    Write-Output $item
}

# for
for ($i = 0; $i -lt 10; $i++) {
    Write-Output $i
}

# while
$i = 0
while ($i -lt 10) {
    Write-Output $i
    $i++
}

# do-while / do-until
do {
    $input = Read-Host "Eingabe"
} while ($input -ne "quit")

do {
    $result = Test-Connection "server" -Count 1 -Quiet
} until ($result -eq $true)

# Schleifen-Kontrolle
break                                  # Schleife verlassen
continue                               # Naechste Iteration
```

### Funktionen

```powershell
# Einfache Funktion
function Get-Greeting {
    param(
        [string]$Name = "Welt"
    )
    return "Hallo $Name!"
}
Get-Greeting -Name "Chris"

# Erweiterte Funktion mit Validierung
function Set-Config {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$Key,

        [Parameter(Mandatory=$true)]
        [string]$Value,

        [ValidateSet("Debug","Info","Warning","Error")]
        [string]$LogLevel = "Info"
    )

    Write-Verbose "Setze $Key = $Value (Level: $LogLevel)"
    # ... Implementation
}

# Pipeline-Funktion
function Get-FileSize {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$true)]
        [System.IO.FileInfo]$File
    )
    process {
        [PSCustomObject]@{
            Name = $File.Name
            SizeMB = [math]::Round($File.Length / 1MB, 2)
        }
    }
}
Get-ChildItem -File | Get-FileSize
```

### Fehlerbehandlung

```powershell
# Try / Catch / Finally
try {
    $result = Get-Content "existiert_nicht.txt" -ErrorAction Stop
    Write-Output "Gelesen: $result"
} catch [System.IO.FileNotFoundException] {
    Write-Error "Datei nicht gefunden: $_"
} catch {
    Write-Error "Unerwarteter Fehler: $_"
} finally {
    Write-Output "Aufraeumen..."
}

# ErrorAction
Get-Process -Name "gibts_nicht" -ErrorAction SilentlyContinue
Get-Process -Name "gibts_nicht" -ErrorAction Stop              # Wirft Exception

# Fehler-Variable
Get-Process -Name "gibts_nicht" -ErrorAction SilentlyContinue -ErrorVariable err
if ($err) { Write-Output "Fehler: $err" }

# $ErrorActionPreference (global)
$ErrorActionPreference = "Stop"        # Alles wirft Exceptions
$ErrorActionPreference = "Continue"    # Default: Fehler anzeigen, weitermachen
$ErrorActionPreference = "SilentlyContinue"  # Fehler ignorieren

# Letzte Fehler
$Error[0]                              # Letzter Fehler
$Error.Clear()                         # Fehler-Buffer leeren
$LASTEXITCODE                          # Exit-Code des letzten nativen Befehls
$?                                     # True wenn letzter Befehl OK
```

---

## 13. Pipeline & Objektverarbeitung

```powershell
# Pipeline-Grundprinzip: Objekte fliessen von links nach rechts
Get-Process | Where-Object { $_.CPU -gt 10 } | Sort-Object CPU -Descending | Select-Object -First 5

# Filtern
Where-Object { $_.Status -eq "Running" }
Where-Object Status -eq "Running"      # Vereinfachte Syntax

# Sortieren
Sort-Object Name
Sort-Object Name -Descending
Sort-Object @{Expression="Status";Ascending=$true}, @{Expression="Name";Descending=$true}

# Auswahl
Select-Object Name, Status, StartTime
Select-Object -First 10
Select-Object -Last 5
Select-Object -Skip 2
Select-Object -Unique
Select-Object -ExpandProperty Name     # Nur Werte (kein Objekt)

# Gruppieren
Group-Object Status
Group-Object Extension | Sort-Object Count -Descending

# Messen / Zaehlen
Measure-Object                         # Count
Measure-Object -Property Length -Sum -Average -Minimum -Maximum

# Format
Format-Table -AutoSize                 # Tabelle
Format-Table Name, @{N='SizeMB';E={[math]::Round($_.Length/1MB,2)}} -AutoSize
Format-List                            # Liste
Format-List *                          # Alle Eigenschaften
Format-Wide -Column 4                  # Breit

# Export
Export-Csv -Path "daten.csv" -NoTypeInformation
Export-Csv -Path "daten.csv" -Delimiter ";" -Encoding UTF8
ConvertTo-Json | Out-File "daten.json"
ConvertTo-Json -Depth 5               # Tiefe fuer verschachtelte Objekte
ConvertTo-Html | Out-File "report.html"
ConvertTo-Xml

# Import
Import-Csv "daten.csv"
Import-Csv "daten.csv" -Delimiter ";"
Get-Content "daten.json" | ConvertFrom-Json

# Tee (Pipeline + Datei)
Get-Process | Tee-Object -FilePath "prozesse.txt" | Where-Object { $_.CPU -gt 10 }
```

---

## 14. Jobs & Parallelverarbeitung

```powershell
# Background Jobs
Start-Job -ScriptBlock { Get-Process }
Start-Job -ScriptBlock { param($p) Get-ChildItem $p -Recurse } -ArgumentList "C:\Users"
$job = Start-Job -ScriptBlock { Start-Sleep 10; "Fertig!" }

# Job verwalten
Get-Job                                                    # Alle Jobs
Get-Job -State Running                                     # Laufende
Receive-Job -Id 1                                          # Ergebnis abholen
Wait-Job -Id 1                                             # Warten
Stop-Job -Id 1                                             # Abbrechen
Remove-Job -Id 1                                           # Entfernen

# ForEach-Object -Parallel (PS 7+)
1..10 | ForEach-Object -Parallel {
    "Processing $_"
    Start-Sleep 1
} -ThrottleLimit 5                                         # Max 5 gleichzeitig

# Invoke-Command (lokal parallel)
Invoke-Command -ScriptBlock { Get-Process } -ComputerName localhost
```

---

## 15. Module & Pakete

```powershell
# Module
Get-Module                                                 # Geladene
Get-Module -ListAvailable                                  # Installierte
Import-Module ActiveDirectory
Remove-Module ModulName

# Module installieren (PSGallery)
Find-Module -Name "Pester"
Install-Module -Name "Pester" -Scope CurrentUser
Update-Module -Name "Pester"
Uninstall-Module -Name "Pester"

# Nuetzliche Module
Install-Module PSReadLine              # Bessere Kommandozeile
Install-Module posh-git                # Git-Integration
Install-Module Terminal-Icons          # Datei-Icons
Install-Module PSScriptAnalyzer        # Code-Analyse
```

---

## 16. Datum & Zeit

```powershell
# Aktuell
Get-Date                                                   # Jetzt
Get-Date -Format "yyyy-MM-dd"                              # ISO-Datum
Get-Date -Format "yyyy-MM-dd HH:mm:ss"                    # ISO-Datetime
Get-Date -Format "dd.MM.yyyy"                              # Deutsch
Get-Date -UFormat "%s"                                     # Unix-Timestamp

# Rechnen
(Get-Date).AddDays(7)                                      # In 7 Tagen
(Get-Date).AddHours(-2)                                    # Vor 2 Stunden
(Get-Date).AddMonths(1)                                    # In 1 Monat
New-TimeSpan -Start (Get-Date "2025-01-01") -End (Get-Date)  # Differenz

# Vergleiche
(Get-Date) -gt (Get-Date "2025-01-01")
(Get-Item "datei.txt").LastWriteTime -gt (Get-Date).AddDays(-7)  # Neuer als 7 Tage

# Messen
Measure-Command { Get-ChildItem -Recurse C:\Windows }     # Ausfuehrungsdauer
$sw = [System.Diagnostics.Stopwatch]::StartNew()
# ... Code ...
$sw.Stop()
$sw.Elapsed
```

---

## 17. Regex & Pattern Matching

```powershell
# Match-Operator
"Hallo 2025" -match "\d+"             # True, $Matches[0] = "2025"
$Matches                               # Treffer-Hashtable

# Named Groups
"IP: 192.168.1.1" -match "IP:\s*(?<ip>[\d.]+)"
$Matches.ip                            # 192.168.1.1

# Replace
"Error 404" -replace "\d+","XXX"       # "Error XXX"
"CamelCase" -creplace "[A-Z]"," $0"   # Case-sensitive

# Select-String (grep)
Get-Content "log.txt" | Select-String -Pattern "Error|Warning"
Select-String -Path "*.log" -Pattern "(\d{4}-\d{2}-\d{2})" -AllMatches

# Split
"a,b,c" -split ","                     # Array: a, b, c
"one  two  three" -split "\s+"         # Regex-Split

# .NET Regex
[regex]::Match("Text 123", "\d+").Value
[regex]::Matches("a1 b2 c3", "\d").Count
[regex]::Replace("Hallo 123","[0-9]","X")
```

---

## 18. Objekte erstellen & manipulieren

```powershell
# Custom Object
$obj = [PSCustomObject]@{
    Name   = "Server01"
    IP     = "192.168.1.10"
    Status = "Online"
}

# Eigenschaft hinzufuegen
$obj | Add-Member -NotePropertyName "Port" -NotePropertyValue 8080

# Array von Objekten
$servers = @(
    [PSCustomObject]@{Name="S1"; Status="Online"},
    [PSCustomObject]@{Name="S2"; Status="Offline"},
    [PSCustomObject]@{Name="S3"; Status="Online"}
)
$servers | Where-Object { $_.Status -eq "Online" }

# Select berechnet Felder
Get-Process | Select-Object Name,
    @{N='MemMB';E={[math]::Round($_.WorkingSet64/1MB,1)}},
    @{N='CPUs';E={$_.CPU}}
```

---

## 19. Remote-Verwaltung

```powershell
# PS-Remoting aktivieren (Admin)
Enable-PSRemoting -Force

# Remote-Befehl ausfuehren
Invoke-Command -ComputerName "Server01" -ScriptBlock { Get-Process }
Invoke-Command -ComputerName "Server01","Server02" -ScriptBlock { hostname }
Invoke-Command -ComputerName "Server01" -Credential (Get-Credential) -ScriptBlock { Get-Service }

# Interaktive Remote-Sitzung
Enter-PSSession -ComputerName "Server01"
# ... Befehle ...
Exit-PSSession

# Persistente Sitzung
$session = New-PSSession -ComputerName "Server01"
Invoke-Command -Session $session -ScriptBlock { Get-Process }
Copy-Item -Path "C:\local.txt" -Destination "C:\remote.txt" -ToSession $session
Remove-PSSession $session
```

---

## 20. Nuetzliche Patterns & Einzeiler

```powershell
# Groesste Dateien finden
Get-ChildItem -Recurse -File | Sort-Object Length -Descending | Select-Object -First 20 Name,
    @{N='SizeMB';E={[math]::Round($_.Length/1MB,1)}}, DirectoryName

# Dateien aelter als 30 Tage loeschen
Get-ChildItem -Path "C:\Logs" -Recurse -File | Where-Object {
    $_.LastWriteTime -lt (Get-Date).AddDays(-30)
} | Remove-Item -Force

# Duplikate finden (nach Hash)
Get-ChildItem -Recurse -File | Group-Object { (Get-FileHash $_.FullName).Hash } |
    Where-Object { $_.Count -gt 1 } | ForEach-Object { $_.Group }

# Leere Ordner finden
Get-ChildItem -Directory -Recurse | Where-Object { (Get-ChildItem $_.FullName).Count -eq 0 }

# Installierte Programme
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
    Select-Object DisplayName, DisplayVersion, Publisher | Sort-Object DisplayName

# Offene Ports mit Prozessnamen
Get-NetTCPConnection -State Listen | ForEach-Object {
    $proc = Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue
    [PSCustomObject]@{Port=$_.LocalPort; Process=$proc.Name; PID=$_.OwningProcess}
} | Sort-Object Port

# WiFi-Passwort anzeigen
netsh wlan show profile name="SSID" key=clear | Select-String "Key Content"

# System-Uptime
$os = Get-CimInstance Win32_OperatingSystem
"Uptime: $((Get-Date) - $os.LastBootUpTime)"

# Schnelles Backup
$date = Get-Date -Format "yyyy-MM-dd"
Compress-Archive -Path "C:\Projekt" -DestinationPath "C:\Backups\Projekt_$date.zip"

# CSV Report
Get-Process | Select-Object Name,Id,CPU,WorkingSet64 | Export-Csv "prozesse.csv" -NoTypeInformation

# Batch-Umbenennung
Get-ChildItem -Filter "*.jpeg" | Rename-Item -NewName { $_.Name -replace '\.jpeg$','.jpg' }

# Ordnergroessen
Get-ChildItem -Directory | ForEach-Object {
    $size = (Get-ChildItem $_.FullName -Recurse -File | Measure-Object -Property Length -Sum).Sum
    [PSCustomObject]@{Name=$_.Name; SizeMB=[math]::Round($size/1MB,1)}
} | Sort-Object SizeMB -Descending

# Port-Scan (einfach)
1..1024 | ForEach-Object {
    $result = Test-NetConnection -ComputerName "localhost" -Port $_ -WarningAction SilentlyContinue
    if ($result.TcpTestSucceeded) { "Port $_ offen" }
}
```

---

## 21. Execution Policy & Sicherheit

```powershell
# Aktuelle Policy
Get-ExecutionPolicy
Get-ExecutionPolicy -List                                  # Alle Scopes

# Setzen
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
Set-ExecutionPolicy Bypass -Scope Process                  # Nur aktuelle Sitzung
Set-ExecutionPolicy Restricted                             # Keine Scripts

# Policies:
# Restricted       Keine Scripts erlaubt
# AllSigned        Nur signierte Scripts
# RemoteSigned     Lokale OK, Remote muss signiert sein
# Unrestricted     Alles erlaubt (warnt bei Remote)
# Bypass           Alles erlaubt (keine Warnung)

# Script signieren
$cert = Get-ChildItem Cert:\CurrentUser\My -CodeSigningCert
Set-AuthenticodeSignature -FilePath "script.ps1" -Certificate $cert
```

---

## 22. Encoding & Konvertierung

```powershell
# Encoding setzen
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
$OutputEncoding = [System.Text.Encoding]::UTF8

# Base64
$text = "Hallo Welt"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($text)
$base64 = [Convert]::ToBase64String($bytes)
$decoded = [System.Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($base64))

# JSON
$obj | ConvertTo-Json -Depth 5
$json | ConvertFrom-Json

# XML
$obj | ConvertTo-Xml -Depth 2
[xml]$doc = Get-Content "config.xml"
$doc.root.element

# CSV
$data | Export-Csv "out.csv" -NoTypeInformation -Encoding UTF8
Import-Csv "data.csv" -Delimiter ";"

# SecureString
$secure = ConvertTo-SecureString "Geheim" -AsPlainText -Force
$plain = [Runtime.InteropServices.Marshal]::PtrToStringAuto(
    [Runtime.InteropServices.Marshal]::SecureStringToBSTR($secure)
)
```

---

## 23. Profil & Konfiguration

```powershell
# Profil-Pfade
$PROFILE                               # Aktuelles Profil
$PROFILE.CurrentUserCurrentHost        # User + Host
$PROFILE.CurrentUserAllHosts           # User, alle Hosts
$PROFILE.AllUsersCurrentHost           # Alle User, aktueller Host
$PROFILE.AllUsersAllHosts              # Global

# Profil erstellen
if (!(Test-Path $PROFILE)) {
    New-Item -Path $PROFILE -ItemType File -Force
}
notepad $PROFILE                       # Bearbeiten

# Typisches Profil
# Set-Alias ll Get-ChildItem
# Set-Location C:\Projekte
# Import-Module posh-git
# function prompt { "PS $($PWD.Path)> " }

# PSReadLine (Kommandozeile verbessern)
Set-PSReadLineOption -PredictiveViewSource History
Set-PSReadLineOption -EditMode Emacs
Set-PSReadLineKeyHandler -Key Tab -Function Complete
```
