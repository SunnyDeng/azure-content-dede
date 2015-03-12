<properties 
	pageTitle="Erstellen einer Hybridsammlung für RemoteApp" 
	description="Erfahren Sie, wie Sie eine Bereitstellung von RemoteApp erstellen, die eine Verbindung mit Ihrem internen Netzwerk herstellt." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/17/2015" 
	ms.author="elizapo"/>

#Erstellen einer Hybridbereitstellung von RemoteApp

Es existieren zwei Bereitstellungsarten für RemoteApps: 

- Cloud: Die Bereitstellung erfolgt vollständig in der Cloud mithilfe der Option **Schnellerfassung** im Azure-Verwaltungsportal.  
- Hybrid: 	Verwendet ein virtuelles Netzwerk für lokalen Zugriff und wird mithilfe der Option **Mit VPN erstellen** im Verwaltungsportal erstellt.

Dieses Lernprogramm führt Sie durch den Prozess der Erstellung einer Hybridbereitstellung. Es besteht aus sieben Schritten: 

1.	Erstellen Sie ein [benutzerdefiniertes Vorlagenimage für RemoteApp](http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/).
2.	Erstellen eines RemoteApp-Dienstes.
2.	Verbinden mit einem virtuellen Netzwerk.
3.	Verbinden mit einem Vorlagenimage.
4.	Konfigurieren Sie die Verzeichnissynchronisierung. Für RemoteApp müssen Sie eine Integration in Azure Active Directory durchführen, indem Sie entweder 1) DirSync mit der Kennwortsynchronisierungsoption konfigurieren oder 2) DirSync ohne die Kennwortsynchronisierungsoption, aber unter Verwendung einer Domäne konfigurieren, die mit AD FS verbunden ist.
5.	Veröffentlichen von RemoteApp-Programmen.
6.	Konfigurieren des Benutzerzugriffs.

**Voraussetzungen**

Bevor Sie mit der Erstellung des Dienstes beginnen, führen Sie Folgendes aus:

- Registrieren Sie sich für die Vorschau von RemoteApp. Sie können sich unter [http://azure.microsoft.com/services/remoteapp/](http://azure.microsoft.com/services/remoteapp/) registrieren.
- Erstellen Sie ein Benutzerkonto in Active Directory, das als Konto für den RemoteApp-Dienst dient. Beschränken Sie die Berechtigungen für dieses Konto, sodass es nur Computer in die Domäne einbinden kann.
- Sammeln Sie folgende Informationen über das lokale Netzwerk: IP-Adresse und Informationen zum VPN-Gerät.
- Installieren Sie das [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)-Modul.
- Sammeln Sie Informationen über die Benutzer und Gruppen, denen Sie Zugriff gewähren möchten. Dies können Informationen zu Microsoft-Konten oder Active Directory-Geschäftskonten für Benutzer oder Gruppen sein.
- Erstellen Sie Ihr Vorlagenimage. Ein RemoteApp-Vorlagenimage enthält die Apps und Programme, die Sie für Ihre Benutzer veröffentlichen möchten. Die ausführliche Vorgehensweise finden Sie unter [Erstellen eines benutzerdefinierten Vorlagenimage für RemoteApp](http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/). 






## **Schritt 1: Erstellen eines RemoteApp-Dienstes** ##



1. Gehen Sie im [Azure-Verwaltungsportal](http://manage.windowsazure.com) zur Seite "RemoteApp".
2. Klicken Sie auf **Neu > Mit VPN erstellen**.
3. Geben Sie einen Namen für den Dienst ein und klicken Sie auf **RemoteApp-Dienst erstellen**.

Gehen Sie nach dem Erstellen des RemoteApp-Dienstes zur RemoteApp-Seite **Schnellstart**, um mit der Einrichtung fortzufahren.

## **Schritt 2: Verbinden mit einem virtuellen Netzwerk** ##

Ein virtuelles Netzwerk ermöglicht es den Benutzern, auf Daten auf Ihrem lokalen Netzwerk über RemoteApp-Remoteressourcen zuzugreifen. Die Konfiguration der Verbindung mit dem virtuellen Netzwerk besteht aus vier Schritten:

1. Klicken Sie auf der Seite "Schnellstart" auf **Virtuelles RemoteApp-Netzwerk verbinden**. 
2. Bestimmen Sie, ob ein neues virtuelles Netzwerk erstellt oder ein vorhandenes verwendet werden soll. In diesem Lernprogramm erstellen wir ein neues Netzwerk.
3. Geben Sie die folgenden Informationen an:  
      - Name
      - Adressraum des virtuellen Netzwerks
      - lokaler Adressraum
      - IP-Adresse des DNS-Servers
      - IP-Adresse des VPN

	Weitere Informationen erhalten Sie unter[Konfigurieren eines standortübergreifenden VPN im Verwaltungsportal](http://msdn.microsoft.com/library/azure/dn133795.aspx).

4. Klicken Sie auf der Seite "Schnellstart" auf **Skript herunterladen**, um ein Skript zur Konfiguration des VPN-Geräts herunterzuladen, damit dieses eine Verbindung mit dem gerade erstellten virtuellen Netzwerk herstellt. Sie benötigen folgende Informationen über das VPN-Gerät: 
	- Hersteller
	- Plattform
	- Betriebssystem

Speichern Sie das Skript und führen Sie es auf dem VPN-Gerät aus. 

	**Hinweis:** Der Status des virtuellen Netzwerks ändert sich nach dem Ausführen dieses Skripts in "Bereit". Dies kann 5 bis 7 Minuten dauern. Sie können das Netzwerk erst verwenden, wenn es fertiggestellt ist.

5. 	Klicken Sie abschließend auf der Seite "Schnellstart" auf **lokaler Domäne beitreten**. Fügen Sie das Konto des RemoteApp-Dienstes zur lokalen Active Directory-Domäne hinzu. Dazu benötigen Sie den Domänennamen, die Organisationseinheit, den Benutzernamen des Kontos und das Kennwort.


## **Schritt 3: 	Verbinden mit einem RemoteApp-Vorlagenimage** ##

Ein RemoteApp-Vorlagenimage enthält die Programme, die Sie an die Benutzer freigeben möchten. Sie können entweder das neue Vorlagenimage, das Sie (anhand der Anweisungen unter [Erstellen eines benutzerdefinierten Vorlagenimage für RemoteApp](http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/)) erstellt haben, hochladen, oder eine Verbindung zu einem vorhandenen Image (das bereits in Azure hochgeladen wurde) herstellen.

Wenn Sie ein neues Abbild hochladen, müssen Sie den Namen des Abbilds eingeben und den Speicherort wählen. Auf der nächsten Seite des Assistenten wird Ihnen eine Gruppe von PowerShell-Cmdlets angezeigt. Kopieren Sie diese Cmdlets und führen Sie sie von einer Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten aus, um das Abbild hochzuladen.

Wenn Sie eine Verbindung zu einem vorhandenen Abbild herstellen, geben Sie einfach den Namen und den Speicherort des Abbilds sowie das dazugehörige Azure-Abonnement an.



## **Schritt 4: Konfigurieren der Active Directory-Verzeichnissynchronisierung** ##

Für RemoteApp müssen Sie eine Integration in Azure Active Directory durchführen, indem Sie entweder 1) DirSync mit der Kennwortsynchronisierungsoption konfigurieren oder 2) DirSync ohne die Kennwortsynchronisierungsoption, aber unter Verwendung einer Domäne konfigurieren, die mit AD FS verbunden ist. Informationen zur Planung und eine detaillierte Anleitung finden Sie unter [Fahrplan zur Verzeichnissynchronisierung](http://msdn.microsoft.com//library/azure/hh967642.aspx).

## **Schritt 5: Veröffentlichen von RemoteApp-Programmen** ##

Ein RemoteApp-Programm ist die App oder das Programm, das Sie den Benutzern zur Verfügung stellen. Es befindet sich im Vorlagenimage, das Sie für den Dienst hochgeladen haben. Wenn ein Benutzer auf das RemoteApp-Programm zugreift, scheint dieses Programm in der lokalen Umgebung des Benutzers zu laufen, tatsächlich wird es aber in Azure ausgeführt. 

Bevor ein Benutzer aber auf das RemoteApp-Programm zugreifen kann, muss es im Endbenutzer-Feed - einer Liste verfügbarer Programme, auf die der Benutzer über das Azure-Portal zugreift - veröffentlicht werden.
 
Sie können in Ihrem RemoteApp-Dienst mehrere Programme veröffentlichen. Um ein Programm hinzuzufügen, klicken Sie auf der Seite mit den RemoteApp-Programmen auf **Veröffentlichen**. Sie können das Programm vom Startmenü des Vorlagenimage aus veröffentlichen oder den Pfad zum Programm im Vorlagenimage angeben. Wenn Sie das Programm vom Startmenü aus veröffentlichen möchten, wählen Sie das zu veröffentlichende Programm aus. Wenn Sie den Pfad angeben möchten, geben Sie den Namen des Programms sowie den Pfad an, unter dem das Programm im Vorlagenimage installiert ist.

## **Schritt 6: Konfigurieren des Benutzerzugriffs** ##

Nach dem Erstellen des RemoteApp-Dienstes müssen Sie nun die Benutzer und Gruppen hinzufügen, die Zugriff auf die Remoteressourcen haben sollen. Die Benutzer und Gruppen müssen in dem Active Directory-Mandanten vorhanden sein, der zu dem Abonnement gehört, das Sie für die Erstellung dieses RemoteApp-Dienstes verwendet haben.

1.	Klicken Sie auf der Seite "Schnellstart" auf **Benutzerzugriff konfigurieren**. 
2.	Geben Sie das Geschäftskonto, den Gruppennamen (aus Active Directory) oder das Microsoft-Konto ein, dem Sie den Zugriff erteilen möchten.

	Verwenden Sie für Benutzer das Format "user@domain.com". Für Gruppen geben Sie den Gruppennamen ein.

3.	Sobald die Benutzer oder Gruppen überprüft wurden, klicken Sie auf **Speichern**.


## Nächste Schritte
Sie haben die RemoteApp-Hybridbereitstellung nun erfolgreich erstellt und bereitgestellt. Als Nächstes müssen die Benutzer den Remotedesktopclient herunterladen und installieren. Die URL für den Client finden Sie auf der RemoteApp-Schnellstartseite. Die Benutzer müssen sich nun beim Client anmelden und auf die veröffentlichten RemoteApp-Programme zugreifen.



<!--HONumber=46--> 
