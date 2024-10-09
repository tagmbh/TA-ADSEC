<div style="text-align: right;">
  <img src="https://www.t-alpha.ch/wp-content/uploads/logo/TA-Logov2.svg" alt="Logo" style="width: 150px;">
</div>

---

## Regelübersicht

| **Regelname** | T-SIDFiltering                                                                       |
| ------------- | ------------------------------------------------------------------------------------ |
| **Ziel**      | Sicherstellen, dass alle Vertrauensstellungen durch die SID-Filterung geschützt sind |


**Beschreibung**:
Die **SID-Filterung** blockiert Konten, die das Attribut `SID-History` verwenden. `SID-History` wird genutzt, um ein bestehendes Konto mit einem anderen zu verknüpfen und kann missbraucht werden, um über Vertrauensstellungen Kompromittierungen zu verbreiten.

- Bei **Domänen-zu-Domänen-Vertrauensstellungen** wird die SID-Filterung als **Quarantäne** bezeichnet und ist standardmäßig **deaktiviert**.
- Bei **Gesamtstruktur-Vertrauensstellungen** ist die SID-Filterung standardmäßig **aktiviert**; das Deaktivieren wird als **"Aktivieren der SID-History"** bezeichnet.

---

## Einleitung

Diese Health-Check-Regel dient dazu, zu überprüfen, ob alle Vertrauensstellungen durch die SID-Filterung geschützt sind. Die SID-Filterung ist ein wichtiger Sicherheitsmechanismus in Active Directory, der verhindert, dass Sicherheitslücken über Vertrauensstellungen ausgenutzt werden können. Ein fehlender SID-Filter kann dazu führen, dass ein kompromittiertes Konto Zugriff auf Ressourcen in der vertrauenden Domäne erhält.
___
## Umsetzung

### Voraussetzungen

- **Systemanforderungen**:
  - Active Directory-Umgebung mit bestehenden Vertrauensstellungen
  - Zugriff auf die Domänencontroller und Active Directory-Verwaltungstools
- **Berechtigungen**:
  - Mitglied der Gruppe **"Domänen-Admins"** oder entsprechende Berechtigungen zur Verwaltung von Vertrauensstellungen

### Schritte zur Umsetzung

1. **Überprüfen der Vertrauensstellungsattribute**:

   - **Ermitteln Sie die Attribute** `trustDirection` und `trustAttributes` des Vertrauensobjekts.
   - **Fallunterscheidung**:

     - **Interne Gesamtstruktur-Vertrauensstellung**:

       - Wenn die Richtung `0` oder `1` ist **oder**
       - Wenn `trustAttributes & 32 != 0` gilt,
       - **Dann** ist die SID-Filterung **nicht anwendbar**.

     - **Gesamtstruktur-Vertrauensstellung**:

       - Wenn `trustAttributes & 8 != 0` gilt,
       - **Überprüfen Sie**, ob `/enablesidhistory` aktiviert ist (`trustAttributes & 64 != 0`).
       - **Wenn aktiviert**: SID-Filterung ist **deaktiviert**.
       - **Wenn nicht aktiviert**: SID-Filterung ist **aktiviert**.

     - **Domänen-Vertrauensstellung**:

       - Wenn `trustAttributes & 8 == 0` gilt,
       - **Überprüfen Sie** das Quarantäne-Attribut (`trustAttributes & 4 != 0`).
       - **Wenn das Quarantäne-Flag gesetzt ist**: SID-Filterung ist **aktiviert**.
       - **Wenn nicht gesetzt**: SID-Filterung ist **deaktiviert**.

2. **Verwendung des PowerShell-Kommandos zur Statusüberprüfung**:

   Führen Sie folgendes PowerShell-Kommando aus, um den Status der SID-Filterung für eine bestimmte Domäne zu ermitteln:

   ```powershell
   [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().GetSidFilteringStatus('domain.zum.testen.local')
   ```

   > **Hinweis**: Ersetzen Sie `'domain.zum.testen.local'` durch den FQDN der zu überprüfenden Domäne.

3. **Aktivieren der SID-Filterung bei Vertrauensstellungen ohne Schutz**:

   - **Für Domänen-Vertrauensstellungen**:

     Verwenden Sie das `netdom`-Kommando mit dem `/quarantine`-Parameter und setzen Sie ihn auf `Yes`:

     ```shell
     netdom trust <vertrauende_domäne> /domain:<vertrauenswürdige_domäne> /quarantine:Yes
     ```

   - **Für Gesamtstruktur-Vertrauensstellungen**:

     Verwenden Sie das `netdom`-Kommando mit dem `/enablesidhistory`-Parameter und setzen Sie ihn auf `No`:

     ```shell
     netdom trust <vertrauende_domäne> /domain:<vertrauenswürdige_domäne> /enablesidhistory:No
     ```

   > **Wichtig**: Wenden Sie `/quarantine` **nicht** auf eine Gesamtstruktur-Vertrauensstellung an, da dies die Transitivität der Vertrauensstellung beeinträchtigen würde.
___
### Prüfkriterien

- **Kriterium 1**: Alle Vertrauensstellungen verfügen über aktivierte SID-Filterung.
- **Kriterium 2**: Keine Vertrauensstellungen weisen deaktivierte SID-Filterung auf, es sei denn, es liegt ein aktueller Migrationsprozess vor.
- **Kriterium 3**: Die verwendeten Einstellungen entsprechen den empfohlenen Sicherheitsrichtlinien.
___
## Notizen

- Eine fehlende SID-Filterung kann darauf hinweisen, dass eine **Migration im Gange** ist oder dass die Domäne über die Vertrauensstellung **kompromittiert** werden kann.
- Es ist essenziell, bestehende Migrationen **so schnell wie möglich abzuschließen** und die SID-Filterung zu aktivieren.
- Die Anwendung von `/quarantine` auf **Gesamtstruktur-Vertrauensstellungen** kann die Vertrauensstellung beeinträchtigen und sollte **vermieden** werden.
___
## Referenzen

- [Microsoft Dokumentation zu Trust Attributes](https://msdn.microsoft.com/en-us/library/cc237940.aspx)
- [STIG V-8538 - SIDs müssen konfiguriert sein](https://stigviewer.com/stig/windows_server_2012_domain_controller/2017-12-19/finding/V-8538)
- [ANSSI - Empfehlungen zur Sicherheit von Active Directory - R16](https://www.ssi.gouv.fr/guide/recommandations-de-securite-relatives-a-active-directory/)
- [MITRE ATT&CK - Mitigation - Active Directory Configuration](https://attack.mitre.org/mitigations/M1027/)
- [MITRE ATT&CK - T1134.005 Access Token Manipulation: SID-History Injection](https://attack.mitre.org/techniques/T1134/005/)

---

<div style="text-align: center;">
  <img src="https://www.t-alpha.ch/wp-content/uploads/logo/TA-Logov2.svg" alt="Footer Logo" style="width: 100px;"><br>
  <p style="font-family: Arial, sans-serif; font-size: 12px; color: #333;">
    Dieses Dokument wurde erstellt von Ivan Stricker am 2024-10-09.<br>
    Version: 1.0<br>
    Für weitere Informationen besuchen Sie unsere Website: <a href="https://www.t-alpha.ch">www.t-alpha.ch</a>
  </p>
</div>
