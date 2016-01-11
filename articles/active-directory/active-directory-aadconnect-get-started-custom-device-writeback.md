<properties
	pageTitle="Aktivieren des Geräterückschreibens in Azure AD Connect | Microsoft Azure"
	description="Dieses Dokument erläutert das Aktivieren des Geräterückschreibens mit Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="StevenPo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/18/2015"
	ms.author="billmath;andkjell"/>

# Aktivieren des Geräterückschreibens in Azure AD Connect

Die folgende Dokumentation enthält Informationen zum Aktivieren des Features "Geräterückschreiben" in Azure AD Connect. Das Geräterückschreiben wird in den folgenden Szenarien verwendet:

Aktivieren des bedingten Zugriffs basierend auf Geräten auf von ADFS (2012 R2 oder höher) geschützte Anwendungen (Vertrauensstellungen für vertrauende Seiten).

Dies bietet zusätzliche Sicherheit und die Gewissheit, dass nur vertrauenswürdige Geräte auf die Anwendung zugreifen können. Weitere Informationen zum bedingten Zugriff finden Sie unter [Verwalten von Risiken mit bedingtem Zugriff](active-directory-conditional-access.md) und [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.NOTE]Für das Geräterückschreiben ist ein Azure AD Premium-Abonnement erforderlich.

.

>[AZURE.IMPORTANT]Geräte müssen sich in der gleichen Gesamtstruktur befinden wie die Benutzer. Da Geräte mit einer einzelnen Gesamtstruktur zurückgeschrieben werden müssen, unterstützt diese Funktion derzeit keine Bereitstellung mit mehreren Gesamtstrukturen für Benutzer.

## Teil 1: Installieren von Azure AD Connect
1. Installieren Sie Azure AD Connect mit benutzerdefinierten Einstellungen oder Expresseinstellungen. Es wird empfohlen, zuerst alle Benutzer und Gruppen erfolgreich zu synchronisieren, bevor Sie das Geräterückschreiben aktivieren.

## Teil 2: Vorbereiten von Active Directory
Gehen Sie folgendermaßen vor, um die Verwendung des Geräterückschreibens vorzubereiten.

1.	Starten Sie auf dem Computer, auf dem Azure AD Connect installiert ist, PowerShell im erweiterten Modus.

2.	Wenn das Active Directory-PowerShell-Modul NICHT installiert ist. Installieren Sie es mithilfe des folgenden Befehls:

	`Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools`

3.	Führen Sie mit Enterprise-Administratoranmeldeinformationen die folgenden Befehle aus, und beenden Sie dann PowerShell.

	`Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

	`Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`


![PowerShell](./media/active-directory-aadconnect-get-started-custom-device-writeback/powershell.png)

Beschreibung:

- Falls sie nicht vorhanden sind, werden die neuen Container und Objekte unter CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn] erstellt und konfiguriert.
- Falls sie nicht vorhanden sind, werden die neuen Container und Objekte unter CN=RegisteredDevices,[domain-dn] erstellt und konfiguriert. Geräteobjekte werden in diesem Container erstellt.
- Legt die erforderlichen Berechtigungen für das Azure AD Connector-Konto fest, um Geräte in Active Directory zu verwalten.
- Muss nur in einer Gesamtstruktur ausgeführt werden, auch wenn Azure AD Connect in mehreren Gesamtstrukturen installiert ist.

Parameter:

- DomainName: Active Directory-Domäne, in der Geräteobjekte erstellt werden. Hinweis: Alle Geräte für eine bestimmte Active Directory-Gesamtstruktur werden in einer einzelnen Domäne erstellt.
- AdConnectorAccount: Active Directory-Konto, das von Azure AD Connect zum Verwalten von Objekten im Verzeichnis verwendet wird. Dies ist das Konto, das von der Azure AD Connect-Synchronisierung zum Herstellen der Verbindung mit AD verwendet wird. Wenn Sie die Installation mit den Expresseinstellungen durchgeführt haben, ist es das Konto mit dem Präfix „MSOL\_“.

## Teil 3: Aktivieren des Geräterückschreibens in Azure AD Connect
Verwenden Sie das folgende Verfahren, um das Geräterückschreiben in Azure AD Connect zu aktivieren.

1.	Führen Sie den Installations-Assistenten erneut aus. Wählen Sie auf der Seite „Weitere Aufgaben“ die Option **Synchronisierungsoptionen anpassen** aus, und klicken Sie auf **Weiter**. ![Benutzerdefinierte Installation](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
2.	Auf der Seite "Optionale Features" wird das Geräterückschreiben nicht mehr abgeblendet dargestellt. Beachten Sie, dass auf der Seite "Optionale Features" das Geräterückschreiben abgeblendet dargestellt wird, wenn vorbereitende Azure AD Connect-Schritte nicht abgeschlossenen sind. Aktivieren Sie das Kontrollkästchen für das Geräterückschreiben und klicken Sie auf **Weiter**. Wenn das Kontrollkästchen noch deaktiviert ist, helfen Ihnen die Informationen im [Abschnitt zur Problembehandlung](#the-writeback-checkbox-is-still-disabled) weiter. ![Geräterückschreiben](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
3.	Auf der Seite "Rückschreiben" sehen Sie die angegebene Domäne als die standardmäßige Gesamtstruktur für das Geräterückschreiben. ![Benutzerdefinierte Installation](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
4.	Schließen Sie die Installation des Assistenten ohne zusätzliche Konfigurationsänderungen ab. Bei Bedarf finden Sie weitere Informationen unter [Benutzerdefinierte Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md).


## Aktivieren des bedingten Zugriffs
Ausführliche Informationen zum Aktivieren dieses Szenarios finden Sie unter [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## Überprüfen, ob die Geräte mit Active Directory synchronisiert werden
Das Geräterückschreiben sollte jetzt ordnungsgemäß ausgeführt werden. Bedenken Sie, dass es bis zu drei Stunden dauern kann, bis Geräteobjekte in Active Directory zurückgeschrieben werden. Um sicherzustellen, dass Ihre Geräte ordnungsgemäß synchronisiert werden, gehen Sie nach Abschluss der Synchronisierungsregeln wie folgt vor:

1.	Starten Sie das Active Directory-Verwaltungscenter.
2.	Erweitern Sie "RegisteredDevices" innerhalb der Verbunddomäne. ![Benutzerdefinierte Installation](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	Die gegenwärtig registrierten Geräte werden hier aufgeführt.

![Benutzerdefinierte Installation](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Problembehandlung

### Kontrollkästchen für das Rückschreiben ist weiterhin deaktiviert
Wenn das Kontrollkästchen für das Geräterückschreiben nicht aktiviert ist, obwohl Sie die oben beschriebenen Schritte befolgt haben, können Sie mit den folgenden Schritten nachvollziehen, was der Installations-Assistent überprüft, bevor das Kontrollkästchen aktiviert wird.

Zuerst die wichtigen Dinge:

- Stellen Sie sicher, dass mindestens eine Gesamtstruktur über Windows Server 2012 R2 verfügt. Der Geräteobjekttyp muss vorhanden sein.
- Wenn der Installations-Assistent bereits ausgeführt wird, werden keine Änderungen erkannt. Führen Sie den Installations-Assistenten in diesem Fall aus, und führen Sie ihn dann erneut aus.
- Stellen Sie sicher, dass das von Ihnen im Initialisierungsskript bereitgestellte Konto tatsächlich der richtige Benutzer ist, der vom Active Directory Connector verwendet wird. Gehen Sie dazu wie folgt vor:
	- Öffnen Sie im Startmenü den **Synchronisierungsdienst**.
	- Öffnen Sie die Registerkarte **Connectors**.
	- Suchen Sie nach dem Connector mit dem Typ „Active Directory-Domänendienste“, und wählen Sie ihn aus.
	- Klicken Sie unter **Aktionen** auf **Eigenschaften**.
	- Navigieren Sie zu **Mit Active Directory-Gesamtstruktur verbinden**. Stellen Sie sicher, dass die Domäne und der Benutzername, die auf diesem Bildschirm angegeben sind, mit dem im Skript angegebenen Konto übereinstimmen. ![Connector-Konto](./media/active-directory-aadconnect-get-started-custom-device-writeback/connectoraccount.png)

Überprüfen der Konfiguration in Active Directory: - Überprüfen Sie, ob sich der Geräteregistrierungsdienst am unten angegebenen Ort (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) unter dem Konfigurationsnamenskontext befindet.

![Problembehandlung1](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot1.png)

- Stellen Sie sicher, dass nur ein Konfigurationsobjekt vorhanden ist, indem Sie den Konfigurationsnamespace durchsuchen. Löschen Sie das Duplikat, falls mehr als ein Objekt vorhanden ist.

![Problembehandlung2](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot2.png)

- Stellen Sie für das Geräteregistrierungsdienst-Objekt sicher, dass msDS-DeviceLocation für das Attribut vorhanden ist und über einen Wert verfügt. Suchen Sie nach diesem Ort, und vergewissern Sie sich, dass er vorhanden ist und über objectType msDS-DeviceContainer verfügt.

![Problembehandlung3](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot3.png)

![Problembehandlung4](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot4.png)

- Überprüfen Sie, ob das vom Active Directory Connector verwendete Konto über die erforderlichen Berechtigungen für den Container „Registrierte Geräte“ aus dem vorherigen Schritt verfügt. Dies sind die erwarteten Berechtigungen für diesen Container:

![Problembehandlung5](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot5.png)

- Stellen Sie sicher, dass das Active Directory-Konto über Berechtigungen für das Objekt „CN=Device Registration Configuration,CN=Services,CN=Configuration“ verfügt.

![Problembehandlung6](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot6.png)

## Zusätzliche Informationen
- [Verwalten von Risiken mit bedingtem Zugriff](active-directory-conditional-access.md)
- [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1223_2015-->