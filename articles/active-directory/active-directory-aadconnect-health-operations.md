<properties
	pageTitle="Azure AD Connect Health-Vorgänge"
	description="In diesem Artikel werden zusätzliche Vorgänge beschrieben, die nach der Bereitstellung von Azure AD Connect Health ausgeführt werden können."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="billmath"/>

# Azure AD Connect Health-Vorgänge

Das folgende Thema beschreibt die verschiedenen Vorgänge, die mithilfe von Azure AD Connect Health ausgeführt werden können.

## Aktivieren von E-Mail-Benachrichtigungen
Sie können Azure AD Connect Health zum Senden von E-Mail-Benachrichtigungen konfigurieren, die beim Generieren von Warnungen gesendet werden und angeben, dass die Identitätsinfrastruktur nicht fehlerfrei ist. Benachrichtigungen werden gesendet, wenn eine Warnung generiert und wenn sie als gelöst markiert wird. Folgen Sie den unten stehenden Anweisungen, um E-Mail-Benachrichtigungen zu konfigurieren.
>[AZURE.NOTE] E-Mail-Benachrichtigungen sind standardmäßig deaktiviert.


### So aktivieren Sie Azure AD Connect Health-E-Mail-Benachrichtigungen

1. Öffnen Sie das Blatt "Warnungen" für den Dienst, für den Sie E-Mail-Benachrichtigungen empfangen möchten.
2. Klicken Sie in der Aktionsleiste auf die Schaltfläche "Benachrichtigungseinstellungen".
3. Aktivieren Sie die Option "E-Mail-Benachrichtigung".
4. Aktivieren Sie das Kontrollkästchen, um alle globalen Administratoren zum Empfangen von E-Mail-Benachrichtigungen zu konfigurieren.
5. Wenn Sie E-Mail-Benachrichtigungen an andere E-Mail-Adressen senden lassen möchten, können Sie diese im Feld "Weitere E-Mail-Empfänger" angeben. Um eine E-Mail-Adresse aus dieser Liste zu entfernen, klicken Sie mit der rechten Maustaste auf den Eintrag, und wählen Sie "Löschen".
6. Klicken Sie zum Abschließen der Änderungen auf "Speichern". Alle Änderungen werden erst nach der Auswahl von "Speichern" wirksam.

## Löschen eines Servers oder einer Serverinstanz

### Löschen eines Servers aus einem Azure AD Connect Health-Dienst
In einigen Fällen möchten Sie möglicherweise einen Server aus der Überwachung entfernen. Folgen Sie den unten stehenden Anweisungen, um einen Server aus dem Azure AD Connect Health-Dienst zu entfernen.

Beim Löschen eines Servers sind folgende Punkte zu beachten:

- Diese Aktion BEENDET das Erfassen von jeglichen Daten von diesem Server. Dieser Server wird aus dem Überwachungsdienst entfernt. Nach dieser Aktion können Sie keine neuen Warnungen oder Überwachungs- bzw. Nutzungsanalysedaten für diesen Server anzeigen.
- Diese Aktion deinstalliert oder entfernt den Connect Health-Agent NICHT vom Server. Wenn Sie vor Ausführung dieses Schritts den Connect Health-Agent nicht deinstalliert haben, werden auf dem Server möglicherweise Fehlerereignisse in Zusammenhang mit dem Connect Health-Agent angezeigt.
- Diese Aktion löscht NICHT die Daten, die von diesem Server bereits erfasst wurden. Diese Daten werden gemäß der Microsoft Azure-Datenaufbewahrungsrichtlinie gelöscht.
- Wenn Sie nach Ausführung dieser Aktion die Überwachung des gleichen Servers erneut starten möchten, müssen Sie den Connect Health-Agent von diesem Server deinstallieren und anschließend neu installieren.


#### So löschen Sie einen Server aus dem Azure AD Connect Health-Dienst

1. Öffnen Sie das Blatt "Server", indem Sie auf dem Blatt "Serverliste" den Namen des Servers auswählen, den Sie entfernen möchten.
2. Klicken Sie auf der Aktionsleiste des Blatts "Server" auf die Schaltfläche "Löschen".
3. Bestätigen Sie die Aktion zum Löschen des Servers, indem Sie den Namen des Servers in das Bestätigungsfeld eingeben.
4. Klicken Sie auf die Schaltfläche "Löschen".


### Löschen einer Dienstinstanz aus dem Azure AD Connect Health-Dienst

In einigen Fällen möchten Sie möglicherweise eine Dienstinstanz entfernen. Folgen Sie den unten stehenden Anweisungen, um eine Dienstinstanz aus dem Azure AD Connect Health-Dienst zu entfernen.

Beim Löschen einer Dienstinstanz sind folgende Punkte zu beachten:

- Diese Aktion entfernt die aktuelle Dienstinstanz aus dem Überwachungsdienst.
- Diese Aktion deinstalliert oder entfernt den Connect Health-Agent NICHT von einem der Server, die im Rahmen dieser Dienstinstanz überwacht wurden. Wenn Sie vor Ausführung dieses Schritts den Connect Health-Agent nicht deinstalliert haben, werden auf dem Server bzw. den Servern möglicherweise Fehlerereignisse in Zusammenhang mit dem Connect Health-Agent angezeigt.
- Alle Daten aus dieser Dienstinstanz werden gemäß der Microsoft Azure-Datenaufbewahrungsrichtlinie gelöscht.
- Wenn Sie nach Ausführung dieser Aktion die Überwachung dieses Diensts erneut starten möchten, deinstallieren Sie den Connect Health-Agent von allen zu überwachenden Servern, und installieren Sie ihn anschließend erneut. Wenn Sie nach Ausführung dieser Aktion die Überwachung des gleichen Servers erneut starten möchten, müssen Sie den Connect Health-Agent von diesem Server deinstallieren und anschließend neu installieren.


#### So löschen Sie eine Dienstinstanz aus dem Azure AD Connect Health-Dienst

1. Öffnen Sie das Blatt "Dienst", indem Sie auf dem Blatt "Dienstliste" die ID des Diensts (Name der Farm) auswählen, den Sie entfernen möchten.
2. Klicken Sie auf der Aktionsleiste des Blatts "Server" auf die Schaltfläche "Löschen".
3. Bestätigen Sie den Dienstnamen, indem Sie ihn in das Bestätigungsfeld eingeben (Beispiel: sts.contoso.com).
4. Klicken Sie auf die Schaltfläche "Löschen". <br><br>


[//]: # "Beginn des RBAC-Abschnitts"
## Verwalten des Zugriffs mit rollenbasierter Zugriffssteuerung
### Übersicht
Die [Rollenbasierte Zugriffssteuerung](role-based-access-control-configure.md) für Azure AD Connect Health stellt den Azure AD Connect Health-Dienst für Benutzer und/oder Gruppen außerhalb der globalen Administratorgruppe bereit. Dies wird erreicht, indem Sie den entsprechenden Benutzern und/oder Gruppen Rollen zuweisen, und bietet einen Mechanismus, um die globalen Administratoren in Ihrem Verzeichnis einzugrenzen.

#### Roles (Rollen)
Azure AD Connect Health unterstützt die folgenden integrierten Rollen.

| Rolle | Berechtigungen |
| ----------- | ---------- |
| Besitzer | Besitzer können innerhalb von Azure AD Connect Health den ***Zugriff verwalten*** (z. B. einem Benutzer/einer Gruppe Rollen zuweisen), im Portal ***alle Informationen anzeigen*** (z. B. Warnungen) und ***Einstellungen ändern*** (z. B. E-Mail-Benachrichtigungen). <br>Standardmäßig wird diese Rolle globalen Azure AD-Administratoren zugewiesen und kann nicht geändert werden. |
|Mitwirkender| Beitragende können innerhalb von Azure AD Connect Health im Portal ***alle Informationen anzeigen*** und ***Einstellungen ändern*** (z. B. E-Mail-Benachrichtigungen).|
|Leser| Leser können innerhalb von Azure AD Connect Health ***alle Informationen anzeigen*** (z. B. Warnungen).|

Alle anderen Rollen (z. B. "User Access Administrators" oder "DevTest Lab Users") haben keine Auswirkung auf den Zugriff innerhalb von Azure AD Connect Health, auch wenn sie in der Portalumgebung verfügbar sind.

#### Zugriffsbereich

Azure AD Connect unterstützt Zugriffsverwaltung auf zwei Ebenen:

- ***Alle Dienstinstanzen***: Dies ist für die meisten Kunden empfohlen und steuert den Zugriff auf alle Dienstinstanzen (z. B. eine AD FS-Farm) für alle Arten von Rollen, die von Azure AD Connect Health überwacht werden.

- ***Dienstinstanz***: In einigen Fällen müssen Sie möglicherweise Zugriff basierend auf Rollentypen oder nach Dienstinstanz aufteilen. In diesem Fall können Sie den Zugriff auf Dienstinstanzebene verwalten.

Berechtigungen werden erteilt, wenn ein Benutzer Zugriff auf Verzeichnisebene oder Dienstinstanzebene hat.


### So erteilen Sie Benutzern oder Gruppen Zugriff auf Azure AD Connect Health
#### Schritt 1: Auswählen des entsprechenden Zugriffsbereichs
Um einem Benutzer Zugriff auf der Ebene *Alle Dienstinstanzen* innerhalb von Azure AD Connect Health zu gewähren, öffnen Sie das Hauptblatt in Azure AD Connect Health.<br>
#### Schritt 2: Hinzufügen von Benutzern und Gruppen sowie Zuweisen von Rollen
1. Klicken Sie im Abschnitt „Konfigurieren“ auf den Bereich „Benutzer“.<br> 
![Azure AD Connect Health – RBAC Hauptblatt](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Wählen Sie "Hinzufügen" aus.
3. Wählen Sie „Rolle“ aus, beispielsweise „Besitzer“<br> 
![Azure AD Connect Health – RBAC Benutzer hinzufügen](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Geben Sie den Namen oder die Kennung des entsprechenden Benutzers oder der Gruppe ein. Sie können einen oder mehrere Benutzer oder Gruppen gleichzeitig auswählen. Klicken Sie auf „Auswählen“. 
![Azure AD Connect Health – RBAC Benutzer auswählen](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Klicken Sie auf „OK“.<br>

6. Nach Abschluss der Rollenzuordnung werden die Benutzer und/oder Gruppen in der Liste angezeigt.<br>
![Azure AD Connect Health – RBAC Benutzerliste](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Diese Schritte ermöglichen den aufgeführten Benutzern und der Gruppe Zugriff basierend auf den zugewiesenen Rollen.
>[AZURE.NOTE]
- Globale Administratoren haben immer Vollzugriff auf alle Vorgänge, globale Administratorkonten sind jedoch nicht in der obigen Liste aufgeführt. - Das Feature "Benutzer einladen" wird in Azure AD Connect Health NICHT unterstützt.

#### Schritt 3: Freigeben des Blattspeicherorts für Benutzer oder Gruppen
1. Nach dem Zuweisen von Berechtigungen kann ein Benutzer auf Azure AD Connect Health zugreifen, indem er [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth) aufruft.
2. Auf dem Blatt kann der Benutzer das Blatt oder verschiedene Teile an das Dashboard heften, indem er auf „An Dashboard anheften“ klickt<br> 
![Azure AD Connect Health – RBAC Blatt anhaften](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)

>[AZURE.NOTE] Ein Benutzer mit der Rolle "Leser" kann den Vorgang "Erstellen" nicht ausführen, um die Azure AD Connect Health-Erweiterung aus dem Azure Marketplace abzurufen. Dieser Benutzer kann weiterhin über den oben aufgeführten Link auf das Blatt zugreifen. Für nachfolgende Verwendungen kann der Benutzer das Blatt an das Dashboard anheften.

### Entfernen von Benutzern und/oder Gruppen
Sie können einen der rollenbasierten Azure AD Connect Health-Zugriffsteuerung hinzugefügten Benutzer oder eine Gruppe entfernen, indem Sie mit der rechten Maustaste darauf klicken und „Entfernen“ auswählen.<br> 
![Azure AD Connect Health – RBAC Benutzer entfernen](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # "Ende des RBAC-Abschnitts"

## Verwandte Links

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installieren des Azure AD Connect Health-Agents](active-directory-aadconnect-health-agent-install.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Verwenden von Azure AD Connect Health für die Synchronisierung](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health – FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health: Versionsverlauf](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0224_2016-->