<properties 
	pageTitle="Erstellen einer Cloud-Sammlung von RemoteApp" 
	description="Erfahren Sie, wie Sie eine Bereitstellung einer RemoteApp erstellen, mit der Daten in der Azure-Cloud gespeichert werden." 
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
	ms.date="04/08/2015" 
	ms.author="elizapo"/>

# Erstellen einer Cloud-Sammlung von RemoteApp

Es gibt zwei Arten von RemoteApp-Sammlungen:

- Cloud: befindet sich vollständig in Azure und wird mithilfe der Option **Schnellerfassung** im Azure-Verwaltungsportal erstellt.  
- Hybrid: enthält ein virtuelles Netzwerk für den lokalen Zugriff und wird mithilfe der Option **Mit VPN erstellen** im Verwaltungsportal erstellt.

Dieses Lernprogramm beschreibt die Schritte zur Erstellung einer Cloud-Sammlung. Es sind vier Schritte notwendig:

1.	Erstellen einer RemoteApp-Sammlung.
2.	Optionale Konfiguration der Verzeichnissynchronisierung. RemoteApp benötigt diese für die Synchronisierung von Benutzern, Kontakten und Kennwörtern zwischen lokalem Active Directory und Azure Active Directory-Mandant.
5.	Veröffentlichen von RemoteApp-Apps.
6.	Konfigurieren des Benutzerzugriffs.

**Voraussetzungen**

Bevor Sie mit der Erstellung der Sammlung beginnen, benötigen Sie Folgendes:

- [Melden](http://azure.microsoft.com/services/remoteapp/) Sie sich für RemoteApp an. 
- Sammeln Sie Informationen zu den Benutzern, denen Sie Zugriff gewähren möchten. Dies können Informationen zu Microsoft-Konten oder Active Directory-Geschäftskonten für Benutzer sein.
- Voraussetzung ist, dass Sie entweder eins der im Rahmen Ihres Abonnements bereitgestellten Vorlagenimages verwenden oder das gewünschte Vorlagenimage bereits hochgeladen haben. Falls Sie eine andere Abbildvorlage hochladen möchten, können Sie dies auf der Seite für Abbildvorlagen tun. Klicken Sie auf **Abbildvorlage hochladen** und folgen Sie den Schritten im Assistenten. 
- Möchten Sie benutzerdefinierte Apps oder LOB-Programme bereitstellen? Erstellen Sie ein neues [Image](remoteapp-imageoptions.md), und verwenden Sie es in Ihrer Cloud-Sammlung.

## Schritt 1: Erstellen einer RemoteApp-Sammlung ##



1. Navigieren Sie im Verwaltungsportal zur Seite "RemoteApp".
2. Klicken Sie auf **Neu > Schnellerfassung**.
3. Geben Sie einen Namen für Ihre Sammlung ein, und wählen Sie Ihre Region aus.
4. Wählen Sie den Plan aus, den Sie verwenden möchten: "Standard" oder "Einfach".
5. Wählen Sie die Vorlage für diese Sammlung aus. 

	**Tipp:** Ihr RemoteApp-Abonnement enthält bereits [Vorlagenimages](remoteapp-images.md) mit Office 365- oder Office 2013-Programmen (zu Testzwecken), von denen einige bereits veröffentlicht (z. B. Word) und andere bereit für die Veröffentlichung sind. Sie können auch ein neues [Image](remoteapp-imageoptions.md) erstellen und es in der Cloud-Sammlung verwenden.


1. Klicken Sie auf **Create RemoteApp collection**.
	
	**Wichtig:** Die Bereitstellung Ihrer Sammlung kann bis zu 30 Minuten in Anspruch nehmen.

Gehen Sie nach dem Erstellen der RemoteApp-Sammlung zur RemoteApp-Seite **Schnellstart**, um mit der Einrichtung fortzufahren.


## Schritt 2: Konfigurieren der Active Directory-Verzeichnissynchronisierung (optional) ##

Falls Sie Active Directory verwenden möchten, benötigt RemoteApp eine Verzeichnissynchronisierung zwischen Azure Active Directory und Ihrem lokalen Active Directory, um Benutzer, Kontakte und Kennwörter mit Ihrem Active Directory-Mandanten in Azure zu synchronisieren. Informationen zur Planung finden Sie unter [Konfigurieren von Active Directory für Azure RemoteApp](remoteapp-ad.md).

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

Geschafft - Sie haben Ihre Cloud-Sammlung in RemoteApp erfolgreich erstellt und bereitgestellt. Als Nächstes müssen die Benutzer den Remotedesktopclient herunterladen und installieren. Die URL für den Client finden Sie auf der RemoteApp-Schnellstartseite. Die Benutzer müssen sich nun beim Client anmelden und können dann auf die veröffentlichten Apps zugreifen.


<!--HONumber=54--> 