```
Zusammenfassung
Klare, umsetzbare Anforderungen für ein autounattend-generator.html (für Windows 11). Ziel: eine Web‑GUI, die eine autounattend.xml erzeugt und nur zwei Konfigurationsbereiche bietet: Basics und Users. Zusätzlich sollen optionale PowerShell‑Skripte für jeden Benutzer und ein optionales System‑Kontext‑PowerShell‑Skript unterstützt werden. Alle Hilfsartefakte und temporären Änderungen müssen nach der Erstinitialisierung vollständig entfernt werden.

Hauptfunktionen
Generiere autounattend.xml aus GUI‑Eingaben.

Zwei Bereiche in der GUI

Basics: Sprache, Region, Tastatur‑Layout auswählbar.

Users: Liste von Benutzern mit Name und Passwort; pro Benutzer optionales PowerShell‑Skript auswählbar.

Systemskript: Möglichkeit, ein PowerShell‑Skript im Systemkontext (LocalSystem) auszuführen.

Temporäre Ausführung: Alle Skripte und Hilfsdateien dürfen nur temporär während der Initialisierung existieren und müssen nach Abschluss entfernt werden.

Detaillierte funktionale Anforderungen
Basics
Eingabefelder: Sprache, Region, Tastaturlayout (Dropdowns mit aktuellen Windows 11‑Werten).

Validierung: Pflichtfelder prüfen; sinnvolle Standardwerte setzen.

Output Mapping: Werte müssen korrekt in die entsprechenden Unattend‑Abschnitte (z. B. Microsoft-Windows-International-Core, InputLocale, SystemLocale, UILanguage) gemappt werden.

Users
Benutzer hinzufügen/entfernen: UI zum Anlegen mehrerer Benutzer.

Felder pro Benutzer: Benutzername, Passwort (mit Bestätigung), Administratorrechte (Checkbox), optional: Pfad zu PS1-Datei (Datei‑Upload).

Passwortrichtlinien: Validierung nach Windows 11 Mindestanforderungen.

Mapping: Benutzer müssen korrekt in autounattend.xml (z. B. Microsoft-Windows-Shell-Setup\UserAccounts) abgebildet werden.

PowerShell Skripte
Pro‑User Skript: Optionales PS1, das im Benutzerkontext beim ersten Login ausgeführt wird.

Systemskript: Optionales PS1, das im Systemkontext während OOBE oder Setup ausgeführt wird.

Ausführungsmechanismus:

Skripte werden temporär auf das Zielsystem kopiert (z. B. %WINDIR%\Temp\...) und dort ausgeführt.

Nach erfolgreicher Ausführung müssen Skripte und alle temporären Artefakte gelöscht werden.

Ausführungsreihenfolge: Systemskript vor Benutzer‑Initialisierung; Benutzer‑Skripte beim ersten Benutzerlogin.

Sicherheitsmaßnahmen: Signatur/ExecutionPolicy‑Handling, temporäre Anpassung von Defender‑Regeln falls nötig, danach Rücksetzen.

Sicherheits- und Temporäre Anforderungen
Keine dauerhaften Änderungen: Generator darf nur Einstellungen in der finalen autounattend.xml schreiben; alle Hilfsänderungen auf Zielsystem müssen temporär sein und nach Abschluss rückgängig gemacht werden.

Defender und ExecutionPolicy:

Prüfen, ob Windows PowerShell ExecutionPolicy oder Defender Script‑Blocker Skriptausführung verhindert.

Falls temporäre Anpassungen nötig sind (z. B. ExecutionPolicy Bypass, Defender‑Ausnahme), müssen diese nur temporär gesetzt und nach Skriptausführung wiederhergestellt.

Keine dauerhaften Registry‑Keys offen lassen: Alle Registry‑Änderungen zur Ermöglichung der Ausführung müssen entfernt werden.

Least Privilege: Systemskript nur wenn nötig; Benutzerrechte klar deklarieren.

Logging: Temporäre Logs in Temp; nach Erfolg/Fehler löschen; Fehlerberichte in der GUI anzeigen.

Kompatibilität und Aktualität
Zielplattform: Windows 11 (aktuelle, unterstützte Builds).

Keine veralteten/unsupporteten APIs: Nur moderne, unterstützte Unattend‑Settings und PowerShell‑Mechanismen verwenden.

Locale/Language Lists: Dropdowns mit aktuellen, von Microsoft dokumentierten Locale‑IDs.

Test auf aktuellen Windows 11‑Versionen: Verhalten mit aktuellen Defender/SmartScreen/PowerShell‑Versionen prüfen.

GUI und UX Anforderungen
Zweispaltiges Layout: Oben/links Basics, darunter/ rechts Users; klare Trennung.

Schönes, modernes Design: Responsive, klare Formularelemente, Icons, Inline‑Validierung.

Datei‑Upload: PS1 Upload per Drag&Drop oder Dateiauswahl; Anzeige Dateiname und Größe.

Vorschau: Live‑Vorschau der generierten autounattend.xml (lesbar, optionaler Download).

Fehler‑ und Warnmeldungen: Klar formuliert, z. B. bei Passwortproblemen oder fehlenden Rechten.

Export: Button zum Herunterladen der autounattend.xml und optional eines ZIPs mit temporären Hilfsdateien (nur wenn nötig).

Technische Details und Output
Output Datei: valide autounattend.xml kompatibel mit Windows 11 Setup.

Skript‑Einbettung:

Skripte nicht inline in XML einbetten, sondern als separate Dateien referenzieren, die Setup temporär ausführt.

Mechanismus in Unattend: FirstLogonCommands, RunSynchronous, RunSynchronousCommand oder passende moderne Hooks verwenden.

Sauberkeit: Generator selbst erzeugt keine persistente Konfiguration auf dem Erstellungsrechner.

Clientseitig: HTML/JS (keine serverseitige Speicherung). Alle Uploads bleiben lokal im Browser bis Export.

Test- und Abnahmekriterien
Funktional:

autounattend.xml wird aus GUI‑Eingaben korrekt erzeugt.

Benutzer werden korrekt in XML abgebildet.

System‑ und Benutzer‑PS1 werden referenziert und können auf einem Test‑VM ausgeführt werden.

Sicherheit:

Nach Abschluss sind keine temporären Dateien, Registry‑Änderungen oder Defender‑Ausnahmen mehr vorhanden.

ExecutionPolicy/Defender Anpassungen werden zurückgesetzt.

Kompatibilität:

Test auf mindestens zwei aktuellen Windows 11 Builds erfolgreich.

Usability:

GUI validiert Eingaben; Vorschau und Download funktionieren.

Deliverables
autounattend-generator.html (einzelne Datei oder minimaler Asset‑Ordner) mit:

Voll funktionsfähiger GUI.

JS‑Logik zur Erzeugung der autounattend.xml.

Clientseitige Validierung und Vorschau.

Anleitung/README in Deutsch: wie die erzeugte XML und Skripte in eine Windows 11 Installation integriert werden (z. B. auf USB im Root).

Testplan: Schritte zur Verifikation auf einer Windows 11 VM.

Checkliste: Sicherheits‑ und Cleanup‑Checks, die während Tests durchlaufen werden müssen.

Risiken und wichtige Hinweise
Sicherheitsrisiko: Temporäres Deaktivieren von Defender/ExecutionPolicy kann Sicherheitsrisiken bergen; nur temporär und dokumentiert durchführen.

Windows‑Verhalten: Manche Defender/SmartScreen‑Regeln oder Enterprise‑Policies können Skriptausführung blockieren; Generator kann nur temporär Maßnahmen vorschlagen, nicht garantieren.

Rechtliches: Keine persistente Umgehung von Sicherheitsmechanismen implementieren.

Beispielseiten: Orientierung an existierenden Generatoren ist erlaubt, aber vermeide deren negative Praktiken (dauerhafte Änderungen, unsichere Defaults).

Priorisierte To‑Do Liste für die Implementierung
GUI‑Layout und Basics‑Dropdowns mit aktuellen Locale‑Werten.

User‑Management UI mit Passwortvalidierung.

PS1 Upload und Referenzmechanik implementieren.

XML‑Generator implementieren und Live‑Vorschau.

Mechanismus für temporäre Skriptausführung und Cleanup designen.

Defender/ExecutionPolicy Prüf‑ und Rücksetzlogik implementieren.

Tests auf Windows 11 VMs durchführen und Cleanup verifizieren.

README und Testplan fertigstellen.
```

```
Kurzfassung
Ein kompaktes Anforderungsprofil für autounattend-generator.html (Windows 11): Web‑GUI erzeugt autounattend.xml mit zwei Bereichen Basics und Users, unterstützt pro‑User und System PowerShell‑Skripte, alle Hilfsartefakte müssen nach Erstinitialisierung vollständig entfernt werden.

Hauptfunktionen
Basics: Sprache, Region, Tastatur wählbar; Werte in Unattend korrekt mappen.

Users: Mehrere Benutzer anlegen; Name, Passwort, Admin‑Flag, optionales PS1‑Upload.

Skripte: System‑PS1 und User‑PS1 ausführen; temporär kopieren, ausführen, danach löschen.

Output: Downloadbare, valide autounattend.xml.

Sicherheitsanforderungen
Keine dauerhaften Änderungen: Registry, Defender‑Ausnahmen, ExecutionPolicy nur temporär und rücksetzbar.

Cleanup: Alle temporären Dateien, Logs und temporären Registry‑Änderungen müssen nach Abschluss entfernt werden.

UX und Technik
Schöne, responsive GUI mit Drag&Drop für PS1; Inline‑Validierung (Passwortregeln).

Clientseitig: HTML/JS, keine serverseitige Speicherung.

Kompatibilität: Nur aktuelle, unterstützte Windows‑Unattend‑Settings verwenden.

Lieferumfang kurz
autounattend-generator.html (funktional).
```
