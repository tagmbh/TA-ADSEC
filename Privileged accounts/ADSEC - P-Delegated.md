
# Delegationsschutz für Administratorenkonten (P-Delegated)

<div style="text-align: right;">
  <img src="https://www.t-alpha.ch/wp-content/uploads/logo/TA-Logov2.svg" alt="Logo" style="width: 150px;">
</div>

---

## Regelübersicht

| **Regelname** | P-Delegated                                                                      |
| ------------- | ----------------------------------------------------------------------------------- |
| **Ziel**      | Sicherstellen, dass alle Administratorenkonten gegen Delegierung geschützt sind
|

**Beschreibung**:
Ohne das Flag **"Dieses Konto ist vertraulich und darf nicht delegiert werden"** kann jedes Konto von einem Dienstkonto imitiert werden. Es ist eine bewährte Methode, dieses Flag bei Administratorenkonten zu setzen oder die Konten der integrierten Gruppe **"Geschützte Benutzer"** hinzuzufügen (ab Domänenfunktionsebene Windows Server 2012 R2).

---

## Einleitung

Diese Health-Check-Regel dient dazu, sicherzustellen, dass alle Administratorenkonten gegen unbefugte Delegierung geschützt sind. Durch das Setzen des entsprechenden Flags oder die Mitgliedschaft in der Gruppe **"Geschützte Benutzer"** wird verhindert, dass Administratorenkonten von Dienstkonten delegiert und somit potenziell kompromittiert werden können.

---

## Umsetzung

### Voraussetzungen

- **Systemanforderungen**:
    - Active Directory-Umgebung mit Administratorenkonten
    - Domänenfunktionsebene mindestens Windows Server 2003 (für das Flag) oder Windows Server 2012 R2 (für die Gruppe **"Geschützte Benutzer"**)
- **Berechtigungen**:
    - Mitglied der Gruppe **"Domänen-Admins"** oder entsprechende Berechtigungen zur Verwaltung von Benutzerkonten

### Technische Erklärung

Ohne das Flag **"Dieses Konto ist vertraulich und darf nicht delegiert werden"** kann ein Konto von einem Dienstkonto imitiert werden, was ein erhebliches Sicherheitsrisiko darstellt. Durch Aktivieren dieses Flags wird verhindert, dass das Konto delegiert werden kann. Alternativ bietet die Mitgliedschaft in der Gruppe **"Geschützte Benutzer"** zusätzlichen Schutzmechanismen gegen verschiedene Angriffe.

### Schritte zur Umsetzung

1. **Identifizieren der Administratorenkonten**:

    - Listen Sie alle Konten auf, die administrative Rechte besitzen, einschließlich Mitgliedern von Administratorgruppen.

2. **Aktivieren des Flags "Dieses Konto ist vertraulich und darf nicht delegiert werden"**:

    - Öffnen Sie **Active Directory-Benutzer und -Computer (ADUC)**.
    - Navigieren Sie zum entsprechenden Benutzerkonto.
    - Klicken Sie mit der rechten Maustaste und wählen Sie **"Eigenschaften"**.
    - Gehen Sie zur Registerkarte **"Konto"**.
    - Aktivieren Sie das Kontrollkästchen **"Dieses Konto ist vertraulich und darf nicht delegiert werden"**.

   > **Hinweis**: Bei gruppenverwalteten Dienstkonten (gMSA) ist diese Option möglicherweise nicht verfügbar. In diesem Fall müssen Sie das Flag manuell setzen, indem Sie den Wert **1048576** zum Attribut `userAccountControl` des Kontos hinzufügen.

3. **Hinzufügen zur Gruppe "Geschützte Benutzer"** (optional):

    - Wenn Ihre Domänenfunktionsebene mindestens **Windows Server 2012 R2** ist:
        - Öffnen Sie **ADUC** und navigieren Sie zur Gruppe **"Geschützte Benutzer"** unter **"Builtin"**.
        - Fügen Sie die Administratorenkonten als Mitglieder hinzu.

   > **Achtung**: Einige Funktionen könnten nach dem Hinzufügen zur Gruppe **"Geschützte Benutzer"** nicht mehr ordnungsgemäß funktionieren. Prüfen Sie die offizielle Dokumentation von Microsoft für weitere Details.

### Prüfkriterien

- **Kriterium 1**: Alle Administratorenkonten haben das Flag **"Dieses Konto ist vertraulich und darf nicht delegiert werden"** aktiviert.
- **Kriterium 2**: Alternativ sind die Administratorenkonten Mitglieder der Gruppe **"Geschützte Benutzer"**.
- **Kriterium 3**: Es gibt keine Administratorenkonten, die von Dienstkonten delegiert werden können.

---

## Punktebewertung

- **20 Punkte**, wenn ungeschützte Administratorenkonten vorhanden sind.

---

## Notizen

- **gMSA-Konten**: Bei gruppenverwalteten Dienstkonten (gMSA) ist das Setzen des Flags über die GUI nicht möglich. Sie können das Flag jedoch manuell setzen, indem Sie den Wert **1048576** zum Attribut `userAccountControl` hinzufügen.
- **Weitere Informationen**: Beachten Sie den Abschnitt **"Admin-Gruppen"** in diesem Bericht für zusätzliche Details.
- **Funktionsprüfung**: Nach dem Anwenden der Einstellungen sollten Sie überprüfen, ob alle benötigten Funktionen weiterhin ordnungsgemäß arbeiten.

---

## Referenzen

- [STIG V-36435 - Delegation von privilegierten Konten muss verboten sein](https://stigviewer.com/stig/windows_server_2012_domain_controller/2017-12-19/finding/V-36435)
- [MITRE ATT&CK - Mitigation - Active Directory Configuration](https://attack.mitre.org/mitigations/M1027/)
- [Microsoft Docs - Geschützte Benutzer Sicherheitsgruppe](https://docs.microsoft.com/de-de/windows-server/security/credentials-protection-and-management/protected-users-security-group)
- [Microsoft Docs - Attribute userAccountControl](https://docs.microsoft.com/de-de/windows/win32/adschema/a-useraccountcontrol)

---

<div style="text-align: center;">
  <img src="https://www.t-alpha.ch/wp-content/uploads/logo/TA-Logov2.svg" alt="Footer Logo" style="width: 100px;"><br>
  <p style="font-family: Arial, sans-serif; font-size: 12px; color: #333;">
    Dieses Dokument wurde erstellt von Ivan Stricker am 2024-10-09.<br>
    Version: 1.0<br>
    Für weitere Informationen besuchen Sie unsere Website: <a href="https://www.t-alpha.ch">www.t-alpha.ch</a>
  </p>
</div>