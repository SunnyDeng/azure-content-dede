
<properties 
    pageTitle="Konfigurieren von Active Directory für Azure RemoteApp" 
    description="Erfahren Sie, wie Sie Active Directory für Azure RemoteApp einrichten können." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/28/2015" 
    ms.author="elizapo" />



# Konfigurieren von Active Directory für Azure RemoteApp


Bei einer Hybrid-Sammlung von RemoteApp müssen Sie eine lokale Active Directory-Domäneninfrastruktur und einen Azure Active Directory-Mandanten mit Verzeichnisintegration (und optional einmaliges Anmelden) einrichten. Darüber hinaus müssen Sie einige Active Directory-Objekte im lokalen Verzeichnis erstellen. Konfigurieren Sie das lokale Active Directory und Azure Active Directory anhand der folgenden Informationen, und integrieren Sie dann die beiden Dienste.

## Konfigurieren des lokalen Active Directory
Beginnen Sie mit der Konfiguration des lokalen Active Directory. Sie müssen das zu verwendende UPN-Domänensuffix definieren und dann die Active Directory-Objekte für RemoteApp erstellen.

Haben Sie Ihrer Windows Server 2012 R2-Umgebung noch keine Active Directory-Domänendienste hinzugefügt? Lesen Sie [diese Anweisungen](https://technet.microsoft.com/library/cc731053.aspx), um Informationen hierzu zu erhalten.
### Überprüfen und Konfigurieren des UPN-Domänensuffix
Wenn die Gesamtstruktur nicht mit einem UPN-Suffix konfiguriert ist, das der für RemoteApp zu verwendenden Azure AD-Domäne entspricht, müssen Sie das Suffix hinzufügen. Ermitteln Sie, ob das erforderliche Suffix konfiguriert ist:


1. Starten Sie "Active Directory-Benutzer und -Computer".
2.	Erweitern Sie den Domänennamen, und klicken Sie dann auf **Benutzer**.
3.	Klicken Sie mit der rechten Maustaste auf **Administrator**, und klicken Sie dann auf **Eigenschaften**.
4.	Überprüfen Sie auf der Registerkarte **Konto** im Feld **Benutzeranmeldename** die für diese Domäne konfigurierten UPN-Namen.
5.	Wenn kein Suffix angezeigt wird, das dem Domänennamen entspricht, den Sie für die RemoteApp-Sammlung verwenden möchten, führen Sie folgende Schritte aus:
	1.	Starten Sie "Active Directory-Domänen und -Vertrauensstellungen".
	2.	Klicken Sie mit der rechten Maustaste auf **Active Directory-Domänen und -Vertrauensstellungen**, und klicken Sie dann auf **Eigenschaften**.
	3.	Überprüfen Sie die Suffix-Liste.
	4.	Geben Sie für den Domänennamen im Feld den vollqualifizierten Domänennamen ein, und klicken Sie auf **Hinzufügen** und dann auf **OK**. Beispiel: "contoso.com". 

		Beziehen Sie "@" NICHT in das Suffix ein.

Wenn Sie neue Benutzer erstellen, können Sie von nun an das neue Suffix als Benutzeranmeldename auswählen. Darüber hinaus können Sie in den Eigenschaften der Benutzer auf der Registerkarte "Konto" das Suffix für vorhandene Benutzer ändern.

Weitere Informationen finden Sie unter [Hinzufügen von Benutzerprinzipalnamen-Suffixen](http://technet.microsoft.com/library/cc772007.aspx).

### Erstellen von Objekten für RemoteApp in Active Directory
RemoteApp benötigt zwei Objekte im lokalen Active Directory:


- Ein Dienstkonto für den Zugriff auf Domänenressourcen für RemoteApp-Programme durch Verknüpfen von RDSH-Endpunkten mit der lokalen Domäne.
- Eine Organisationseinheit (OE), die RemoteApp-Computerobjekte enthält. Die Verwendung einer Organisationseinheit ist empfehlenswert (jedoch nicht unbedingt erforderlich), um die Konten und Richtlinien, die Sie mit RemoteApp verwenden, zu isolieren.

Erstellen Sie die einzelnen Objekte anhand der folgenden Informationen.

#### Erstellen des Dienstkontos:


1. Starten Sie "Active Directory-Benutzer und -Computer".
2.	Klicken Sie mit der rechten Maustaste auf **Benutzer**, und klicken Sie dann auf **Neu > Benutzer**.
3.	Geben Sie einen Benutzernamen und ein Kennwort für das RemoteApp-Dienstkonto ein.

	**Hinweis**: Sie benötigen diese Kontoinformationen bei der Erstellung der RemoteApp-Sammlung.

#### Erstellen einer neuen Organisationseinheit (OE)


1. Klicken Sie in "Active Directory-Benutzer und -Computer" mit der rechten Maustaste auf Ihre Domäne. Klicken Sie auf **Neu > Organisationseinheit**.
2. Fügen Sie das Dienstkonto, das Sie für RemoteApp erstellt haben, dieser neuen Organisationseinheit hinzu.

	Suchen Sie hierzu das Dienstkonto, das Sie erstellt haben. Klicken Sie mit der rechten Maustaste darauf, und klicken Sie dann auf **Verschieben**. Wählen Sie die neue Organisationseinheit aus, und klicken Sie dann auf **OK**.


1. Gewähren Sie dem RemoteApp-Dienstkonto die Berechtigung zum Hinzufügen und Löschen von Computern für diese Organisationseinheit:
	1. Klicken Sie im Snap-in "Active Directory-Benutzer und -Computer" auf **Ansicht > Erweiterte Funktionen**. Dadurch wird den Eigenschafteninformationen die Registerkarte **Sicherheit** hinzugefügt.
	2. Klicken Sie mit der rechten Maustaste auf die Organisationseinheit des RemoteApp-Diensts, und klicken Sie dann auf **Eigenschaften**.
	3. Klicken Sie auf der Registerkarte **Sicherheit** auf **Hinzufügen**. Wählen Sie das Benutzerobjekt des RemoteApp-Dienstkontos aus, und klicken Sie dann auf **OK**.
	4. Klicken Sie auf **Erweitert**.
	5. Wählen Sie auf der Registerkarte **Berechtigungen** das RemoteApp-Dienstkonto aus, und klicken Sie dann auf **Bearbeiten**.
	6. Wählen Sie **Dieses Objekt und alle untergeordneten Objekte** im Feld **Gilt für** aus.
	7. Aktivieren Sie im Feld **Berechtigungen** neben "Computerobjekte erstellen" und "Computerobjekte löschen" die Option **Zulassen** aus, und klicken Sie dann auf **OK**. 
	8. Klicken Sie in den beiden folgenden Fenstern auf **OK**.


## Konfigurieren von Azure Active Directory
Nachdem Sie das lokale Active Directory eingerichtet haben, fahren Sie mit Azure Active Directory fort. Sie müssen eine benutzerdefinierte Domäne erstellen, die dem UPN-Domänensuffix Ihrer lokalen Domäne entspricht, und die Verzeichnisintegration einrichten. Die Hybrid-Sammlung unterstützt nur Azure Active Directory-Konten, die von einer Windows Server Active Directory-Bereitstellung synchronisiert wurden (mithilfe eines Tools wie "DirSync"); hier besonders Konten, die entweder mit der Option "Kennwortsynchronisierung" oder mit Active Directory Federation Services (AD FS) in einer Verbundkonfiguration synchronisiert wurden.

Konfigurieren Sie Azure Active Directory anhand der folgenden Informationen.


- [Hinzufügen Ihrer Domäne](http://technet.microsoft.com/library/hh969247.aspx) – verwenden Sie diese Informationen zum Hinzufügen einer Domäne, die dem UPN-Domänensuffix der lokalen Active Directory-Domäne entspricht.
- [Verzeichnisintegration](http://technet.microsoft.com/library/jj573653.aspx) – verwenden Sie diese Informationen zum Auswählen einer Option für die Verzeichnisintegration: [DirSync mit Kennwortsynchronisierung](http://technet.microsoft.com/library/dn441214.aspx) oder [DirSync mit Verbund](http://technet.microsoft.com/library/dn441213.aspx).

Sie können auch die [Multi-Factor Authentication (MFA)](http://technet.microsoft.com/library/dn249466.aspx) konfigurieren.

## Haben Sie Probleme mit der Konfiguration der Verzeichnissynchronisierung?

Wenn beim Konfigurieren der Verzeichnissynchronisierung Probleme auftreten, überprüfen Sie Folgendes:

- Verwenden Sie die neueste Version des Azure-Verzeichnissynchronisierungstools 
-	Im Verwaltungsportal wurde unter **Active Directory->Standardverzeichnis->Domänen** bereits Ihre benutzerdefinierte Domäne (z. B. mydomain.com) hinzugefügt und als primäre Domäne festgelegt.
-	Unter **Active Directory->Standardverzeichnis->Benutzer** fügen Sie unter dieser Domäne einen neuen Benutzer hinzu (z. B. myAzureSyncUser@mydomain.com)).
-	Sie haben in Ihrer Domäne in Active Directory einen neuen Domänenbenutzer hinzugefügt und ihn zu einem Mitglied der Gruppe Organisations-Admins gemacht (z. B. myDomainSyncUser@mydomain.com)).

Starten Sie nun das Azure-Verzeichnissynchronisierungstool, und verwenden Sie die Anmeldeinformationen **myAzureSyncUser@mydomain.com** für die erste Eingabeaufforderung (Microsoft Azure Active Directory-Administrator-Anmeldeinformationen) und **myDomainSyncUser@mydomain.com** für die zweite Aufforderung.
 

<!---HONumber=62-->