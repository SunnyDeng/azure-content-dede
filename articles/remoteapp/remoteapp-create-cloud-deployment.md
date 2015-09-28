<properties 
	pageTitle="Erstellen einer Cloudsammlung von Azure RemoteApp | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Azure RemoteApp-Bereitstellung erstellen, mit der Daten in der Azure-Cloud gespeichert werden." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2015" 
	ms.author="elizapo"/>

# Erstellen einer Cloudsammlung von Azure RemoteApp

Es gibt zwei Arten von Azure RemoteApp-Sammlungen:

- Cloud: vollständige Speicherung in Azure. Sie können alle Daten in der Cloud speichern (also eine reine Cloudsammlung), oder Sie verbinden Ihre Sammlung mit einem VNET und speichern die Daten dort.   
- Hybrid: Umfasst ein virtuelles Netzwerk für den lokalen Zugriff – dies erfordert die Verwendung von Azure AD und einer lokalen Active Directory-Umgebung.

Dieses Lernprogramm beschreibt die Schritte zur Erstellung einer Cloudsammlung. Es sind vier Schritte notwendig:

1.	Erstellen einer RemoteApp-Sammlung.
2.	Optionale Konfiguration der Verzeichnissynchronisierung. Wenn Sie Azure AD und Active Directory verwenden, müssen Sie Benutzer, Kontakte und Kennwörter zwischen Ihrem lokalen Active Directory und dem Azure AD-Mandanten synchronisieren.
5.	Veröffentlichen von RemoteApp-Apps.
6.	Konfigurieren des Benutzerzugriffs.

**Hinweis** *Dieses Thema wird zurzeit überarbeitet. Ich habe die Schritte der neuen Benutzeroberfläche entsprechend aktualisiert, kann jedoch das gesamte Thema noch nicht erneut veröffentlichen. Es wird an verschiedenen neuen Artikeln gearbeitet, um Sie bei der Auswahl der für Sie geeigneten Authentifizierungs- und Sammlungsoptionen zu unterstützen. Wenn Sie also unsicher sind: Bald stehen bessere und ausführlichere Informationen für Sie bereit. Vielen Dank*

**Voraussetzungen**

Bevor Sie mit der Erstellung der Sammlung beginnen, benötigen Sie Folgendes:

- [Registrieren](http://azure.microsoft.com/services/remoteapp/) Sie sich für Azure RemoteApp. 
- Sammeln Sie Informationen zu den Benutzern, denen Sie Zugriff gewähren möchten. Dies können Informationen zu Microsoft-Konten oder Active Directory-Geschäftskonten für Benutzer sein.
- Voraussetzung ist, dass Sie entweder eins der im Rahmen Ihres Abonnements bereitgestellten Vorlagenimages verwenden oder das gewünschte Vorlagenimage bereits hochgeladen haben. Falls Sie eine andere Abbildvorlage hochladen möchten, können Sie dies auf der Seite für Abbildvorlagen tun. Klicken Sie auf **Abbildvorlage hochladen** und folgen Sie den Schritten im Assistenten. 
- Möchten Sie das Office 365 ProPlus-Image verwenden? Informationen dazu finden Sie [hier](remoteapp-officesubscription.md).
- Möchten Sie benutzerdefinierte Apps oder LOB-Programme bereitstellen? Erstellen Sie ein neues [Image](remoteapp-imageoptions.md), und verwenden Sie es in Ihrer Cloud-Sammlung.
- Ermitteln Sie, ob Sie eine Verbindung mit einem VNET herstellen müssen. Wenn Sie eine Verbindung mit einem VNET herstellen möchten, stellen Sie sicher, dass die [Richtlinien zum Festlegen der Größe](remoteapp-vnetsizing.md) eingehalten werden und dass eine [Verbindung mit RemoteApp](remoteapp-vnet.md) hergestellt werden kann. Weitere Informationen finden Sie im [Artikel zur VNET-Planung](remoteapp-planvpn.md).
- Wenn Sie ein VNET verwenden, können Sie entscheiden Sie, ob es Ihrer lokalen Active Directory-Domäne beitreten soll.

## Schritt 1: Erstellen einer Cloudsammlung Cloud – mit oder ohne VNET##


Führen Sie die folgenden Schritte aus, um **eine reine Cloudsammlung zu erstellen**:

1. Navigieren Sie im Verwaltungsportal zur Seite "RemoteApp".
2. Klicken Sie auf **Neu > Schnellerfassung**.
3. Geben Sie einen Namen für Ihre Sammlung ein, und wählen Sie Ihre Region aus.
4. Wählen Sie den Plan aus, den Sie verwenden möchten: "Standard" oder "Einfach".
5. Wählen Sie die Vorlage für diese Sammlung aus. 

	**Tipp:** Ihr RemoteApp-Abonnement enthält bereits [Vorlagenimages](remoteapp-images.md) mit Office 365- oder Office 2013-Programmen (zu Testzwecken), von denen einige bereits veröffentlicht (z. B. Word) und andere bereit für die Veröffentlichung sind. Sie können auch ein neues [Image](remoteapp-imageoptions.md) erstellen und es in der Cloud-Sammlung verwenden.


1. Klicken Sie auf **Create RemoteApp collection**.
	
	**Wichtig:** Die Bereitstellung Ihrer Sammlung kann bis zu 30 Minuten in Anspruch nehmen.

Nachdem Ihre RemoteApp-Sammlung erstellt wurde, doppelklicken Sie auf den Namen der Sammlung. Daraufhin wird die Seite **Schnellstart** geöffnet – hier können Sie die Konfiguration der Sammlung abschließen.

Führen Sie die folgenden Schritte aus, um **eine Cloud- und VNET-Sammlung zu erstellen**:

1. Navigieren Sie im Verwaltungsportal zur Seite "RemoteApp".
2. Klicken Sie auf **Neu** > **Mit VNET erstellen**.
3. Geben Sie einen Namen für die Sammlung ein.
4. Wählen Sie den Plan aus, den Sie verwenden möchten: "Standard" oder "Einfach".
5. Wählen Sie das bereits erstellte VNET aus. Sie wissen nicht, wie das geht? Momentan finden Sie die entsprechenden Schritte im Thema [Hybrid](remoteapp-create-hybrid-deployment.md).
6. Entscheiden Sie, ob Ihre Sammlung Ihrer Domäne beitreten soll. Wenn dies der Fall ist, müssen Sie AD Connect verwenden, um Azure AD in Ihrer Active Directory-Umgebung zu integrieren. Informationen dazu finden Sie im unten in **Schritt 2**.
6. Klicken Sie auf **Create RemoteApp collection**.


## Schritt 2: Konfigurieren der Active Directory-Verzeichnissynchronisierung (optional) ##

Falls Sie Active Directory verwenden möchten, benötigt RemoteApp eine Verzeichnissynchronisierung zwischen Azure Active Directory und Ihrem lokalen Active Directory, um Benutzer, Kontakte und Kennwörter mit Ihrem Active Directory-Mandanten in Azure zu synchronisieren. Informationen zur Planung finden Sie unter [Konfigurieren von Active Directory für Azure RemoteApp](remoteapp-ad.md). Informationen finden Sie auch direkt unter [AD Connect](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx).

## Schritt 3: Veröffentlichen von RemoteApp-Programmen ##

Eine RemoteApp-App ist die App oder das Programm, das Sie den Benutzern zur Verfügung stellen. Es befindet sich im Vorlagenimage, das Sie für die Sammlung hochgeladen haben. Wenn ein Benutzer auf eine RemoteApp-App zugreift, scheint diese App in der lokalen Umgebung des Benutzers zu laufen, wird tatsächlich aber in Azure ausgeführt.

Bevor ein Benutzer aber auf die Apps zugreifen kann, müssen Sie sie im Endbenutzer-Feed – einer Liste verfügbarer Apps, auf die der Benutzer über den Remotedesktopclient zugreift – veröffentlichen.
 
Sie können in Ihrer RemoteApp-Sammlung mehrere Apps veröffentlichen. Um ein Programm hinzuzufügen, klicken Sie auf der RemoteApp-Veröffentlichungsseite auf **Veröffentlichen**. Sie können die App entweder aus dem Startmenü des Vorlagenimages veröffentlichen oder den Pfad zur App im Vorlagenimage angeben. Wenn Sie die App aus dem Startmenü hinzufügen möchten, wählen Sie die zu veröffentlichende App aus. Wenn Sie den Pfad angeben möchten, geben Sie den Namen der App sowie den Pfad an, in dem die App auf dem Vorlagenimage installiert ist.

## Schritt 4: Konfigurieren des Benutzerzugriffs ##

Nach dem Erstellen der RemoteApp-Sammlung müssen Sie nun die Benutzer hinzufügen, die Zugriff auf die Remoteressourcen haben sollen. Falls Sie Active Directory verwenden, müssen die entsprechenden Benutzer in dem Active Directory-Mandanten vorhanden sein, der dem Abonnement zugeordnet ist, mit dem Sie diese RemoteApp-Sammlung erstellt haben.

1.	Klicken Sie auf der Seite "Schnellstart" auf **Benutzerzugriff konfigurieren**. 
2.	Geben Sie das Geschäftskonto (aus Active Directory) oder das Microsoft-Konto ein, dem Sie Zugriff gewähren möchten.

	**Hinweise:**

	Verwenden Sie unbedingt das Format "Benutzer@Domäne.com".

	Wenn Sie Office 365 ProPlus in der Sammlung verwenden, müssen Sie für Ihre Benutzer die Active Directory-Identitäten verwenden. Damit wird die Lizenzierung überprüft.

3.	Klicken Sie nach der Überprüfung der Benutzer auf **Speichern**.


## Nächste Schritte ##

Geschafft - Sie haben Ihre Cloudsammlung in RemoteApp erfolgreich erstellt und bereitgestellt. Als Nächstes müssen die Benutzer den Remotedesktopclient herunterladen und installieren. Die URL für den Client finden Sie auf der RemoteApp-Schnellstartseite. Die Benutzer müssen sich nun beim Client anmelden und können dann auf die veröffentlichten Apps zugreifen.

 

<!---HONumber=Sept15_HO3-->