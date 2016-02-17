<properties 
	pageTitle="Erstellen einer Hybridsammlung für Azure RemoteApp | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Bereitstellung von RemoteApp erstellen, die eine Verbindung mit Ihrem internen Netzwerk herstellt." 
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
	ms.date="11/04/2015" 
	ms.author="elizapo"/>

# Erstellen einer Hybridsammlung für Azure RemoteApp

Es gibt zwei Arten von Azure RemoteApp-Sammlungen:

- Cloud: vollständige Speicherung in Azure. Sie können alle Daten in der Cloud speichern (also eine reine Cloudsammlung), oder Sie verbinden Ihre Sammlung mit einem VNET und speichern die Daten dort.   
- Hybrid: Umfasst ein virtuelles Netzwerk für den lokalen Zugriff – dies erfordert die Verwendung von Azure AD und einer lokalen Active Directory-Umgebung.

Sie wissen nicht, was Sie benötigen? Lesen Sie [Welche Art von Sammlung benötigen Sie für Azure RemoteApp?](remoteapp-collections.md)

Dieses Tutorial führt Sie durch den Prozess für das Erstellen einer Hybridsammlung. Der Vorgang umfasst acht Schritte:

1.	Entscheiden Sie, welches [Image](remoteapp-imageoptions.md) für Ihre Sammlung verwendet werden soll. Sie können ein benutzerdefiniertes Image erstellen oder ein Microsoft-Image erstellen, das in Ihrem Abonnement enthalten ist.
2. Richten Sie Ihr virtuelles Netzwerk ein. Sehen Sie sich die Informationen [VNET-Planung](remoteapp-planvpn.md) und [-Größe](remoteapp-vnetsizing.md) an.
2.	Erstellen Sie eine Sammlung.
2.	Fügen Sie Ihre Sammlung Ihrer lokalen Domäne hinzu.
3.	Fügen Sie Ihrer Sammlung ein Vorlagenimage hinzu.
4.	Konfigurieren der Verzeichnissynchronisierung Für Azure RemoteApp müssen Sie eine Integration in Azure Active Directory durchführen, indem Sie entweder 1) Azure Active Directory Sync mit der Kennwortsynchronisierungsoption konfigurieren oder 2) Azure Active Directory Sync ohne die Kennwortsynchronisierungsoption, aber unter Verwendung einer Domäne konfigurieren, die mit AD FS verbunden ist. Beachten Sie die [Konfigurationsinformationen für Active Directory mit RemoteApp](remoteapp-ad.md).
5.	Veröffentlichen von RemoteApp-Apps.
6.	Konfigurieren des Benutzerzugriffs.

**Voraussetzungen**

Bevor Sie mit der Erstellung der Sammlung beginnen, benötigen Sie Folgendes:

- [Registrieren](https://azure.microsoft.com/services/remoteapp/) Sie sich für Azure RemoteApp. 
- Erstellen Sie ein Benutzerkonto in Active Directory, das als Konto für den Azure RemoteApp-Dienst verwendet werden soll. Beschränken Sie die Berechtigungen für dieses Konto, sodass es nur Computer in die Domäne einbinden kann.
- Sammeln Sie Informationen zu Ihrem lokalen Netzwerk: IP-Adressdaten und Details zum VPN-Gerät.
- Installieren Sie das [Azure PowerShell](../install-configure-powershell.md)-Modul.
- Sammeln Sie Informationen zu den Benutzern, denen Sie Zugriff gewähren möchten. Sie benötigen den Azure Active Directory-Benutzerprinzipalnamen (z. B. name@contoso.com) für jeden Benutzer. Stellen Sie sicher, dass der Benutzerprinzipalname von Azure AD und Active Directory übereinstimmt.
- Wählen Sie Ihr Vorlagenimage aus. Ein Azure RemoteApp-Vorlagenimage enthält die Apps und Programme, die Sie für Ihre Benutzer veröffentlichen möchten. Weitere Informationen finden Sie unter [Azure RemoteApp-Imageoptionen](remoteapp-imageoptions.md). 
- Möchten Sie das Office 365 ProPlus-Image verwenden? Dieses steht [hier](remoteapp-officesubscription.md) zur Verfügung.
- [Konfigurieren von Active Directory für RemoteApp](remoteapp-ad.md)



## Schritt 1: Einrichten des virtuellen Netzwerks
Sie können eine Hybridsammlung bereitstellen, die ein vorhandenes virtuelles Azure-Netzwerk nutzt, oder Sie können ein neues virtuelles Netzwerk erstellen. Ein virtuelles Netzwerk ermöglicht es den Benutzern, auf Daten auf Ihrem lokalen Netzwerk über RemoteApp-Remoteressourcen zuzugreifen. Mit einem virtuellen Azure-Netzwerk verfügt Ihre Sammlung über direkten Zugriff auf andere Azure-Dienste und virtuelle Computer in diesem virtuellen Netzwerk.

Überprüfen Sie die Informationen zur [VNET-Planung](remoteapp-planvnet.md) und [VNET-Größe](remoteapp-vnetsizing.md), bevor Sie Ihr VNET erstellen.

### Erstellen eines Azure-VNET und Verknüpfen mit der Active Directory-Bereitstellung

Erstellen Sie zunächst ein [virtuelles Netzwerk](../virtual-network/virtual-networks-create-vnet.md). Dies erfolgt auf der Registerkarte **Netzwerk** im Azure-Verwaltungsportal. Sie müssen Ihr virtuelles Netzwerk mit der Active Directory-Bereitstellung verbinden, die mit Ihrem Azure Active Directory-Mandanten synchronisiert ist.

Weitere Informationen finden Sie unter [Informationen zu virtuellen Netzwerkeinstellungen im Verwaltungsportal](../virtual-network/virtual-networks-settings.md).

### Sicherstellen, dass das virtuelle Netzwerk für Azure RemoteApp bereit ist
Bevor Sie Ihre Sammlung erstellen, müssen Sie sicherstellen, dass Ihr neues virtuelles Netzwerk bereit ist. Sie können dies wie folgt überprüfen:

1. Erstellen Sie einen virtuellen Azure-Computer in dem Subnetz des virtuellen Netzwerks, das Sie gerade RemoteApp erstellt haben.
2. Stellen Sie über Remotedesktop eine Verbindung zum virtuellen Computer her. (Klicken Sie auf **Verbinden**.)
3. Verknüpfen Sie ihn mit der gleichen Active Directory-Bereitstellung, die auch für RemoteApp verwendet werden soll.

Hat dies funktioniert? Ihr virtuelles Netzwerk und das Subnetz sind für Azure RemoteApp bereit.

[Hier](https://msdn.microsoft.com/library/azure/jj156003.aspx) finden Sie weitere Informationen zum Erstellen von virtuellen Azure-Computern und zum Verbinden mit Remotedesktop.

## Schritt 2: Erstellen einer Azure RemoteApp-Sammlung ##



1. Wechseln Sie im [Azure-Portal](http://manage.windowsazure.com) zur Seite „Azure RemoteApp“.
2. Klicken Sie auf **Neu > Create with VNET**.
3. Geben Sie einen Namen für die Sammlung ein.
4. Wählen Sie den Plan aus, den Sie verwenden möchten: "Standard" oder "Einfach".
5. Wählen Sie Ihr VNET aus der Dropdownliste aus, und geben Sie dann Ihr Subnetz an.
6. Wählen Sie einen Beitritt zu Ihrer Domäne.
5. Klicken Sie auf **Create RemoteApp collection**.

Nachdem Ihre Azure RemoteApp-Sammlung erstellt wurde, doppelklicken Sie auf den Namen der Sammlung. Daraufhin wird die Seite **Schnellstart** geöffnet – hier können Sie die Konfiguration der Sammlung abschließen.

Ist etwas schiefgegangen? Sehen Sie sich die [Informationen zur Problembehandlung bei Hybridsammlungen](remoteapp-hybridtrouble.md) an.

## Schritt 3: Verknüpfen der Sammlung mit der lokalen Domäne ##

 
1. Klicken Sie auf der Seite **Schnellstart** auf **Lokaler Domäne beitreten**.
2. Fügen Sie das Konto des Azure RemoteApp-Diensts zur lokalen Active Directory-Domäne hinzu. Dazu benötigen Sie den Domänennamen, die Organisationseinheit, den Benutzernamen des Kontos und das Kennwort. 

	Dies sind die Informationen, die Sie im Rahmen der Schritte unter [Konfigurieren von Active Directory für Azure RemoteApp](remoteapp-ad.md) erfasst haben.


## Schritt 4: Verbinden mit einem Azure RemoteApp-Image ##

Ein Azure RemoteApp-Vorlagenimage enthält die Programme, die Sie für Benutzer freigeben möchten. Sie können entweder ein neues [Vorlagenimage](remoteapp-imageoptions.md) erstellen oder eine Verbindung zu einem vorhandenen Image (das bereits in Azure hochgeladen oder importiert wurde) herstellen. Sie können außerdem eine Verbindung mit einem der [Vorlagenimages](remoteapp-images.md) für Azure RemoteApp herstellen, die Office 365- oder Office 2013-Programme (für die Testnutzung) enthalten.

Wenn Sie ein neues Abbild hochladen, müssen Sie den Namen des Abbilds eingeben und den Speicherort wählen. Auf der nächsten Seite des Assistenten wird Ihnen eine Gruppe von PowerShell-Cmdlets angezeigt. Kopieren Sie diese Cmdlets und führen Sie sie von einer Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten aus, um das Abbild hochzuladen.

Wenn Sie eine Verbindung zu einem vorhandenen Abbild herstellen, geben Sie einfach den Namen und den Speicherort des Abbilds sowie das dazugehörige Azure-Abonnement an.



## Schritt 5: Konfigurieren der Active Directory-Verzeichnissynchronisierung ##

Für Azure RemoteApp müssen Sie eine Integration in Azure Active Directory durchführen, indem Sie entweder 1) Azure Active Directory Sync mit der Kennwortsynchronisierungsoption konfigurieren oder 2) Azure Active Directory Sync ohne die Kennwortsynchronisierungsoption, aber unter Verwendung einer Domäne konfigurieren, die mit AD FS verbunden ist.

Lesen Sie den Artikel [AD Connect](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx) – hier erfahren Sie, wie Sie die Verzeichnisintegration in 4 Schritten einrichten.

Informationen zur Planung und eine detaillierte Anleitung finden Sie unter [Fahrplan zur Verzeichnissynchronisierung](http://msdn.microsoft.com//library/azure/hh967642.aspx).

## Schritt 6: Veröffentlichen von Apps ##

Eine Azure RemoteApp-App ist die App oder das Programm, die bzw. das Sie den Benutzern zur Verfügung stellen. Es befindet sich im Vorlagenimage, das Sie für die Sammlung hochgeladen haben. Wenn ein Benutzer auf das RemoteApp-Programm zugreift, scheint dieses Programm in der lokalen Umgebung des Benutzers zu laufen, tatsächlich wird es aber in Azure ausgeführt.

Bevor ein Benutzer aber auf die Apps zugreifen kann, müssen Sie sie im Endbenutzer-Feed – einer Liste verfügbarer Apps, auf die der Benutzer über den Remotedesktopclient zugreift – veröffentlichen.
 
Sie können in Ihrer Sammlung mehrere Apps veröffentlichen. Um eine App hinzuzufügen, klicken Sie auf der Veröffentlichungsseite auf **Veröffentlichen**. Sie können die App entweder aus dem **Startmenü** des Vorlagenimages veröffentlichen oder den Pfad zur App im Vorlagenimage angeben. Wenn Sie das Programm vom **Startmenü** aus veröffentlichen möchten, wählen Sie das hinzuzufügende Programm aus. Wenn Sie den Pfad angeben möchten, geben Sie den Namen der App sowie den Pfad an, in dem die App auf dem Vorlagenimage installiert ist.

## Schritt 7: Konfigurieren des Benutzerzugriffs ##

Nach dem Erstellen der Sammlung müssen Sie nun die Benutzer hinzufügen, die Zugriff auf die Remoteressourcen haben sollen. Die Benutzer, denen Zugriff gewährt werden soll, müssen in dem Active Directory-Mandanten vorhanden sein, der dem zum Erstellen dieser Azure RemoteApp-Sammlung verwendeten Abonnement zugeordnet ist.

1.	Klicken Sie auf der Seite "Schnellstart" auf **Benutzerzugriff konfigurieren**. 
2.	Geben Sie das Geschäftskonto (aus Active Directory) oder das Microsoft-Konto ein, dem Sie Zugriff gewähren möchten.

	**Hinweise:**

	Verwenden Sie unbedingt das Format "Benutzer@Domäne.com".

	Wenn Sie Office 365 ProPlus in der Sammlung verwenden, müssen Sie für Ihre Benutzer die Active Directory-Identitäten verwenden. Damit wird die Lizenzierung überprüft.


3.	Klicken Sie nach der Überprüfung der Benutzer auf **Speichern**.


## Nächste Schritte ##
Geschafft – Sie haben die Azure RemoteApp-Hybridsammlung erfolgreich erstellt und bereitgestellt. Als Nächstes müssen die Benutzer den Remotedesktopclient herunterladen und installieren. Die URL für den Client finden Sie auf der Azure RemoteApp-Schnellstartseite. Die Benutzer müssen sich nun beim Client anmelden und können dann auf die veröffentlichten Apps zugreifen.


 
### Helfen Sie uns, Ihnen zu helfen 
Wussten Sie schon, dass Sie diesen Artikel im Bereich unten nicht nur bewerten und kommentieren, sondern ihn auch selbst ändern können? Fehlt etwas? Ist etwas nicht ganz richtig? Habe ich etwas geschrieben, das eher verwirrend ist? Scrollen Sie nach oben, und klicken Sie auf **In GitHub bearbeiten**, um die gewünschten Änderungen vorzunehmen. Ihr Vorschlag wird uns vorgelegt, und wenn wir ihn bestätigt haben, werden Ihre Änderungen und Verbesserungen hier angezeigt.

<!---HONumber=AcomDC_0128_2016-->