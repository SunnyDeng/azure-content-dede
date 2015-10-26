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
	ms.date="09/15/2015"
	ms.author="billmath"/>

# Aktivieren des Geräterückschreibens in Azure AD Connect

Die folgende Dokumentation enthält Informationen zum Aktivieren des Features "Geräterückschreiben" in Azure AD Connect. Das Geräterückschreiben wird in den folgenden Szenarien verwendet:

Aktivieren des bedingten Zugriffs basierend auf Geräten auf von ADFS (2012 R2 oder höher) geschützte Anwendungen (Vertrauensstellungen für vertrauende Seiten).

Dies bietet zusätzliche Sicherheit und die Gewissheit, dass nur vertrauenswürdige Geräte auf die Anwendung zugreifen können. Weitere Informationen zum bedingten Zugriff finden Sie unter [Verwalten von Risiken mit bedingtem Zugriff](active-directory-conditional-access.md) und [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.Note]Wenn Geräte verwendet werden, die über den Azure Active Directory-Geräteregistrierungsdienst registriert und mit Richtlinien für den bedingten Zugriff konfiguriert sind, wird ein Office 365-Abonnement oder eine Azure AD Premium-Lizenz benötigt. Hierzu gehören auch Richtlinien, die von den Active Directory-Verbunddiensten (AD FS) für lokale Ressourcen erzwungen werden.

## Teil 1: Vorbereiten von Azure AD Connect
Gehen Sie folgendermaßen vor, um die Verwendung des Geräterückschreibens vorzubereiten.

1.	Starten Sie auf dem Computer, auf dem Azure AD Connect installiert ist, PowerShell im erweiterten Modus.

2.	Wenn das Active Directory-PowerShell-Modul NICHT installiert ist. Installieren Sie es mithilfe des folgenden Befehls:

	Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools

3.	Führen Sie mit Enterprise-Administratoranmeldeinformationen die folgenden Befehle aus, und beenden Sie dann PowerShell.

	Import-Module ‘C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1’

	Initialize-ADSyncDeviceWriteback –DomainName <name> -AdConnectorAccount <account>

Beschreibung:



- Falls nicht vorhanden werden die neuen Container und Objekte unter CN = Konfiguration der Geräteregistrierung, CN = Dienste, CN = Konfiguration, <forest-dn> erstellt und konfiguriert.



- Falls nicht vorhanden werden die neuen Container und Objekte unter CN = "RegisteredDevices", <domain-dn> erstellt und konfiguriert. Geräteobjekte werden in diesem Container erstellt.



- Legt die erforderlichen Berechtigungen für das Azure AD Connector-Konto fest, um Geräte in Active Directory zu verwalten.



- Muss nur in einer Gesamtstruktur ausgeführt werden, auch wenn Azure AD Connect in mehreren Gesamtstrukturen installiert ist.

Parameter:


- DomainName: Active Directory-Domäne, in der Geräteobjekte erstellt werden. Hinweis: Alle Geräte für eine bestimmte Active Directory-Gesamtstruktur werden in einer einzelnen Domäne erstellt.


- AdConnectorAccount: Active Directory-Konto, das von Azure AD Connect zum Verwalten von Objekten im Verzeichnis verwendet wird.

## Teil 2: Aktivieren des Geräterückschreibens
Verwenden Sie das folgende Verfahren, um das Geräterückschreiben in Azure AD Connect zu aktivieren.

1.	Führen Sie den AAD Connect-Assistenten aus. Wenn Sie den Assistenten zum ersten Mal verwenden, führen Sie eine benutzerdefinierte Installation durch, indem Sie "Anpassen" auf dem Bildschirm mit Express-Einstellungen auswählen. ![Benutzerdefinierte Installation](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback1.png)
2.	Wenn dies nicht das erste Mal ist, wählen Sie "Synchronisierungsoptionen anpassen" auf der Seite "Weitere Aufgaben" aus, und klicken Sie auf "Weiter". ![Benutzerdefinierte Installation](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
3.	Auf der Seite "Optionale Features" wird das Geräterückschreiben nicht mehr abgeblendet dargestellt. Beachten Sie, dass auf der Seite "Optionale Features" das Geräterückschreiben abgeblendet dargestellt wird, wenn vorbereitende Azure AD Connect-Schritte nicht abgeschlossenen sind. Aktivieren Sie das Kontrollkästchen für das Geräterückschreiben, und klicken Sie auf "Weiter". ![Geräterückschreiben](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
4.	Auf der Seite "Rückschreiben" sehen Sie die angegebene Domäne als die standardmäßige Gesamtstruktur für das Geräterückschreiben. ![Benutzerdefinierte Installation](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
5.	Schließen Sie die Installation des Assistenten ohne zusätzliche Konfigurationsänderungen ab. Bei Bedarf finden Sie weitere Informationen unter [Benutzerdefinierte Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md).



## Aktivieren des bedingten Zugriffs
Ausführliche Informationen zum Aktivieren dieses Szenarios finden Sie unter [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## Überprüfen, ob die Geräte mit Active Directory synchronisiert werden
Das Geräterückschreiben sollte jetzt ordnungsgemäß ausgeführt werden. Bedenken Sie, dass es bis zu drei Stunden dauern kann, bis Geräteobjekte in Active Directory zurückgeschrieben werden. Um sicherzustellen, dass Ihre Geräte ordnungsgemäß synchronisiert werden, gehen Sie nach Abschluss der Synchronisierungsregeln wie folgt vor:

1.	Starten Sie das Active Directory-Verwaltungscenter.
2.	Erweitern Sie "RegisteredDevices" innerhalb der Verbunddomäne. ![Benutzerdefinierte Installation](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	Die gegenwärtig registrierten Geräte werden hier aufgeführt.

![Benutzerdefinierte Installation](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Zusätzliche Informationen
- [Verwalten von Risiken mit bedingtem Zugriff](active-directory-conditional-access.md)
- [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## Nächste Schritte
Weitere Informationen zum [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->