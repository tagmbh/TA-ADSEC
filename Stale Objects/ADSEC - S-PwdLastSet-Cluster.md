# Regelmäßige Passwortänderung für Cluster-Konten (S-PwdLastSet-Cluster)

<div style="text-align: right;">
  <img src="https://www.t-alpha.ch/wp-content/uploads/logo/TA-Logov2.svg" alt="Logo" style="width: 150px;">
</div>

---
## Regelübersicht

| **Regelname** | S-PwdLastSet-Cluster                                                                      |
| ------------- | ------------------------------------------------------------------------------------ |
| **Ziel**      | Überprüfen, ob alle Cluster-Konten regelmäßige Passwortänderungen durchführen
|


**Beschreibung**:

Diese Regel stellt sicher, dass die regelmäßige Änderung der Passwörter für Cluster-Konten aktiv ist. Einige Cluster-Konten in Windows Server-Clustern haben seit über drei Jahren keine Passwortänderung durchgeführt, was auf fehlende Aktualisierungen der Geheimnisse hindeutet. Dies kann ein Sicherheitsrisiko darstellen, da veraltete Passwörter die Angriffsfläche erhöhen.

---

## Einleitung

Die regelmäßige Aktualisierung von Passwörtern für Cluster-Konten ist entscheidend für die Sicherheit einer Active Directory-Umgebung. Dieser Health Check überprüft, ob die Passwörter von Cluster-Konten ordnungsgemäß und in regelmäßigen Abständen geändert werden.

---

## Umsetzung

### Voraussetzungen

- **Systemanforderungen**:
  - Active Directory-Umgebung mit Windows Server-Clustern
  - Zugriff auf die Domänencontroller und Cluster-Verwaltungstools
- **Berechtigungen**:
  - Mitglied der Gruppe **"Domänen-Admins"** oder entsprechende Berechtigungen zur Verwaltung von Benutzerkonten und Clustern

### Technische Erklärung

Der Algorithmus identifiziert Cluster-Konten anhand von drei Bedingungen:

1. **Aktives Konto**: Das Konto ist aktiv, d.h., das `userAccountControl`-Flag ist **nicht** auf `2` gesetzt (das Konto ist nicht deaktiviert).
2. **Nutzung innerhalb der letzten 45 Tage**: Das Konto wurde vor mindestens drei Jahren erstellt und wurde innerhalb der letzten 45 Tage genutzt (`lastLogonTimestamp`).
3. **Passwort nicht geändert seit mehr als drei Jahren**: Das Passwort wurde zuletzt vor mindestens drei Jahren geändert (`pwdLastSet`).

### Schritte zur Umsetzung

1. **Identifizieren der Cluster-Konten**:

   - Führen Sie ein Skript oder eine Abfrage aus, um alle Konten zu finden, die die oben genannten Bedingungen erfüllen.

2. **Überprüfen der Passwortänderung**:

   - Prüfen Sie das Attribut `pwdLastSet` der identifizierten Konten.
   - Stellen Sie fest, ob das Passwort seit mehr als drei Jahren nicht geändert wurde.

3. **Analyse der Cluster-Konfiguration**:

   - Untersuchen Sie die Cluster-Konfiguration und die Ereignisprotokolle, um festzustellen, warum das Passwort nicht aktualisiert wurde.
   - Überprüfen Sie, ob automatische Passwortänderungen konfiguriert sind und ob Fehler bei der Aktualisierung auftreten.

4. **Aktualisierung der Passwörter**:

   - Führen Sie eine manuelle Passwortänderung durch oder konfigurieren Sie den Cluster so, dass er Passwörter regelmäßig aktualisiert.
   - Stellen Sie sicher, dass alle Änderungen ordnungsgemäß im System protokolliert werden.

   > **Hinweis**: Beachten Sie die Empfehlungen in der Microsoft-Dokumentation zur Konfiguration von AD-Konten für Failover-Clustering.

### Prüfkriterien

- **Kriterium 1**: Alle Cluster-Konten haben ein Passwort, das innerhalb der letzten drei Jahre geändert wurde.
- **Kriterium 2**: Es gibt keine aktiven Cluster-Konten mit veralteten Passwörtern.
- **Kriterium 3**: Die Cluster sind so konfiguriert, dass sie Passwörter regelmäßig aktualisieren.

---

## Punktebewertung

- **5 Punkte**, wenn veraltete Cluster-Konten gefunden werden.

---

## Notizen

> **Hinweis**:

- Veraltete Passwörter für Cluster-Konten können ein erhebliches Sicherheitsrisiko darstellen.
- Die regelmäßige Aktualisierung von Passwörtern hilft, unbefugten Zugriff zu verhindern und die Sicherheit der Cluster zu gewährleisten.
- Überprüfen Sie die Cluster-Einstellungen und stellen Sie sicher, dass automatische Passwortaktualisierungen aktiviert sind.

---

## Referenzen

- [Microsoft Docs - Konfigurieren von AD-Konten für Failover-Clustering](https://learn.microsoft.com/de-de/windows-server/failover-clustering/configure-ad-accounts)
- [ANSSI - Windows Server Cluster-Konten mit Passwörtern, die seit mehr als 3 Jahren nicht geändert wurden](https://www.ssi.gouv.fr/guide/recommandations-de-securite-relatives-a-active-directory/)
- [STIG V-63653 - Das Computerkonto-Passwort darf nicht daran gehindert werden, zurückgesetzt zu werden](https://stigviewer.com/stig/windows_server_2012_domain_controller/2017-12-19/finding/V-63653)
- [STIG V-3373 - Das maximale Alter für Maschinenkonto-Passwörter ist nicht auf Anforderungen eingestellt](https://stigviewer.com/stig/windows_server_2012_domain_controller/2017-12-19/finding/V-3373)
- [MITRE ATT&CK - Mitigation - Active Directory Configuration](https://attack.mitre.org/mitigations/M1027/)

---

<div style="text-align: center;">
  <img src="https://www.t-alpha.ch/wp-content/uploads/logo/TA-Logov2.svg" alt="Footer Logo" style="width: 100px;"><br>
  <p style="font-family: Arial, sans-serif; font-size: 12px; color: #333;">
    Dieses Dokument wurde erstellt von Ivan Stricker am 2024-10-09.<br>
    Version: 1.0<br>
    Für weitere Informationen besuchen Sie unsere Website: <a href="https://www.t-alpha.ch">www.t-alpha.ch</a>
  </p>
</div>
