<properties 
	pageTitle="Verwenden von CDN | Microsoft Azure" 
	description="Erfahren Sie, wie das Azure Content Delivery Network (CDN) für die Übermittlung breitbandiger Inhalte eingesetzt wird, indem Blobs und statische Inhalte zwischengespeichert werden." 
	services="cdn" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# Verwenden von CDN für Azure

Azure Content Delivery Network (CDN) ist der grundlegende Baustein für die Skalierung beliebiger HTTP-Anwendungen in Azure. Es bietet Azure-Kunden eine globale Lösung für das Zwischenspeichern und Bereitstellen von Inhalten in geografischer Nähe zu den Endbenutzern. So müssen Anforderungen der Benutzer nicht jedes Mal an den ursprünglichen Inhaltsspeicherort gesendet werden – nachfolgend als „Ursprung“ bezeichnet –, sondern die Anforderungen werden intelligent an den CDN-Edge-POP mit der besten Leistung geroutet. Dies führt zu einer erheblichen Verbesserung von Leistung und Benutzerfreundlichkeit. Eine aktuelle Liste von CDN-Knotenstandorten finden Sie unter [POP-Standorte von Azure Content Delivery Network (CDN)](cdn-pop-locations.md).

Die Verwendung von CDN zum Zwischenspeichern von Azure-Daten bietet folgende Vorteile:

- Bessere Leistung und Benutzerfreundlichkeit für Endbenutzer, die von einer Inhaltsquelle weit entfernt sind und Anwendungen verwenden, bei denen viele HTTP-Anforderungen zum Laden der Inhalte benötigt werden.
- Starke verteilte Skalierung, um kurzfristige hohe Lasten verarbeiten zu können, z. B. zu Beginn eines Ereignisses wie einer Produkteinführung.
- Durch das Verteilen der Benutzeranforderungen und die Bereitstellung von Inhalten über globale Edge-POPs entfällt weniger Datenverkehr auf den Ursprung der Inhalte.

>[AZURE.TIP]Mit Azure CDN können Inhalte aus einer ganzen Reihe von Ursprüngen verteilt werden. In Azure integriert sind bereits Ursprünge wie App Service, Cloud Services, Blob Storage und Media Service. Außerdem können Sie jede öffentlich zugängliche Webadresse verwenden, um einen benutzerdefinierten Ursprung zu definieren.

##Aktivieren von CDN

1. Erstellen eines CDN-Profils mit Endpunkten, die auf den Ursprung Ihrer Inhalte verweisen

	Ein CDN-Profil ist eine Sammlung von CDN-Endpunkten. Jedes Profil enthält mindestens einen CDN-Endpunkt. Nachdem Sie ein CDN-Profil erstellt haben, können Sie anhand des ausgewählten Ursprungs einen neuen CDN-Endpunkt erstellen.
	
	>[AZURE.NOTE]Ein einzelnes Azure-Abonnement ist auf vier CDN Profile beschränkt. Jedes CDN-Profil ist auf vier CDN-Endpunkte beschränkt.
	>
	> Die Preise für Azure CDN gelten auf der Ebene von CDN-Profilen. Wenn Sie die Features von Standard-CDN und Premium-CDN kombinieren möchten, benötigen Sie mehrere CDN-Profile.
	
	Ein ausführliches Tutorial zum Erstellen von CDN-Profilen und Endpunkten finden Sie unter [Aktivieren des Content Delivery Network für Azure](cdn-create-new-endpoint.md).
	
2. Einrichten der CDN-Konfiguration

	Sie können verschiedene Features für Ihren CDN-Endpunkt aktivieren, z. B. eine [Richtlinie für die Zwischenspeicherung](cdn-caching-policy.md), das [Zwischenspeichern von Abfragezeichenfolgen](cdn-query-string.md), ein [Regelmodul](cdn-rules-engine.md) und anderes. Weitere Informationen erhalten Sie, wenn Sie links auf den Menüeintrag **Verwalten** klicken.

3. Zugreifen auf CDN-Inhalte

	Verwenden Sie für den Zugriff auf die im CDN zwischengespeicherten Inhalte den im Portal bereitgestellten CDN-URL. Beispielsweise hat die Adresse eines zwischengespeicherten Blobs etwa folgendes Format: `http://<identifier>.azureedge.net/<myPublicContainer>/<BlobName>`

## Zwischenspeichern von Inhalten aus Azure-Speicher

Sobald das CDN für ein Azure-Speicherkonto aktiviert wurde, werden alle Blobs in öffentlichen Containern, die für den anonymen Zugriff zur Verfügung stehen, über das CDN zwischengespeichert. Nur Blobs, die öffentlich verfügbar sind, können mit dem Azure CDN zwischengespeichert werden. Um ein Blob öffentlich für den anonymen Zugriff verfügbar zu machen, müssen Sie den Container als öffentlich kennzeichnen. Nachdem dies erfolgt ist, sind alle Blobs in diesem Container für anonymen Lesezugriff verfügbar. Sie können auch Containerdaten öffentlich verfügbar machen oder den Zugriff auf die enthaltenen Blobs beschränken. Unter [Verwalten des Zugriffs auf Azure-Speicherressourcen](../storage/storage-manage-access-to-resources.md) finden Sie weitere Informationen zum Verwalten der Zugriffssteuerung für Container und Blobs.

Für eine optimale Leistung verwenden Sie die CDN-Edge-Zwischenspeicherung für die Übermittlung von Blobs kleiner als 10 GB.

Wenn Sie den CDN-Zugriff für ein Speicherkonto aktivieren, stellt Ihnen das Verwaltungsportal einen CDN-Domänennamen im folgenden Format zur Verfügung: `http://<identifier>.azureedge.net/`. Dieser Domänenname kann verwendet werden, um auf Blobs in einem öffentlichen Container zuzugreifen. Beispielsweise können Benutzer bei einem öffentlichen Container namens "music" in einem Speicherkonto namens "myaccount" über eine der beiden folgenden URLs auf die Blobs in diesem Container zugreifen:

- **Azure-Blobdienst-URL**: `http://myAccount.blob.core.windows.net/music/` 
- **Azure CDN-URL**: `http://<identifier>.azureedge.net/music/` 

## Zwischenspeichern von Inhalten von Azure-Websites

Sie können das CDN auf Ihren Websites zum Zwischenspeichern Ihrer Web-Inhalte wie Bilder, Skripts und Stylesheets aktivieren. Siehe [Integrieren einer Azure-Website in Azure CDN](../app-service-web/cdn-websites-with-cdn.md).

Wenn Sie den CDN-Zugriff für eine Website aktivieren, stellt Ihnen das Verwaltungsportal einen CDN-Domänennamen im folgenden Format zur Verfügung: `http://<identifier>.azureedge.net/`. Dieser Domänenname kann verwendet werden, um Objekte von einer Website abzurufen. Beispielsweise können Benutzer bei einem öffentlichen Container namens "cdn" und einer Bilddatei namens "music.png" auf das Objekt über eine der beiden folgenden URLs zugreifen:

- **Azure-Website-URL**: `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **Azure CDN-URL**: `http://<identifier>.azureedge.net/cdn/music.png`
 
## Zwischenspeichern von Inhalten aus Azure-Clouddiensten

Sie können Objekte im CDN zwischenspeichern, die von einem Azure-Clouddienst bereitgestellt werden.

Das Zwischenspeichern für Clouddienste unterliegt folgenden Einschränkungen:


- Das CDN darf nur verwendet werden, um statische Inhalte zwischenzuspeichern.

	>[AZURE.WARNING]Das Zwischenspeichern von überaus veränderlichen oder wirklich dynamischen Inhalten kann sich negativ auf die Leistung auswirken oder zu Problemen mit Inhalten führen, was alles höhere Kosten verursacht.
- Ihr Clouddienst muss in einer Produktionsumgebung bereitgestellt werden.
- Ihr Clouddienst muss das Objekt an Port 80 über HTTP bereitstellen.
- Der Clouddienst muss den Inhalt, der zwischengespeichert oder übermittelt werden soll, im Ordner "/cdn" für den Clouddienst platzieren.

Wenn Sie den CDN-Zugriff für einen Clouddienst aktivieren, stellt Ihnen das Verwaltungsportal einen CDN-Domänennamen im folgenden Format zur Verfügung: `http://<identifier>.azureedge.net/`. Dieser Domänenname kann verwendet werden, um Objekte aus einem Clouddienst abzurufen. Beispielsweise können Benutzer bei einem Clouddienst namens "myHostedService" und einer ASP.NET-Webseite namens "music.aspx", die Inhalte übermittelt, auf das Objekt über eine der beiden folgenden URLs zugreifen:


- **Azure-Clouddienst-URL**: `http://myHostedService.cloudapp.net/music.aspx` 
- **Azure CDN-URL**: `http://<identifier>.azureedge.net/music.aspx` 

## Zwischenspeichern von Inhalten aus benutzerdefinierten Ursprüngen

Sie können im CDN Objekte zwischenspeichern, die von beliebigen öffentlich zugänglichen Webanwendungen bereitgestellt werden.

Das Zwischenspeichern für benutzerdefinierte Ursprünge unterliegt folgenden Einschränkungen:

- Das CDN darf nur verwendet werden, um statische Inhalte zwischenzuspeichern.

	>[AZURE.WARNING]Das Zwischenspeichern von überaus veränderlichen oder wirklich dynamischen Inhalten kann sich negativ auf die Leistung auswirken oder zu Problemen mit Inhalten führen, was alles höhere Kosten verursacht.
- Der Inhalt des benutzerdefinierten Ursprungs muss auf einem Server mit einer öffentlichen IP-Adresse gehostet sein. CDN-Edge-Knoten können keine Ressourcen von Intranet-Servern abrufen, die durch eine Firewall geschützt sind.

Wenn Sie den CDN-Zugriff für einen benutzerdefinierten Ursprung aktivieren, stellt Ihnen das Azure-Portal einen CDN-Domänennamen im folgenden Format zur Verfügung: `http://<identifier>.azureedge.net/`. Dieser Domänenname kann verwendet werden, um Objekte aus dem benutzerdefinierten Ursprung abzurufen. Beispielsweise können Benutzer bei einer Website wie www.contoso.com mit einer ASP.NET-Webseite namens "music.aspx", die Inhalte übermittelt, auf das Objekt über eine der beiden folgenden URLs zugreifen:


- **URL des benutzerdefinierten Ursprungs**: `http://www.contoso.com/music.aspx` 
- **Azure CDN-URL**: `http://<identifier>.azureedge.net/music.aspx` 

## Zwischenspeichern bestimmter Inhalte mit Abfragezeichenfolgen

Sie können Abfragezeichenfolgen verwenden, um die aus einem Ursprung abgerufenen Objekte zu unterscheiden. Wenn der Ursprung z. B. ein Diagramm anzeigt, das variieren kann, können Sie eine Abfragezeichenfolge übergeben, um das jeweils erforderliche Diagramm abzurufen. Beispiel:

`http://<identifier>.azureedge.net/chart.aspx?item=1`

Abfragezeichenfolgen werden als Zeichenfolgenliterale übergeben. Wenn Sie über einen Dienst verfügen, der zwei Parameter wie z. B. `?area=2&item=1` verwendet und nachfolgend den Ursprung mithilfe von `?item=1&area=2` aufruft, werden zwei Kopien desselben Objekts zwischengespeichert.

Weitere Informationen zum Zwischenspeichern mit Abfragezeichenfolgen finden Sie unter [Steuern des Zwischenspeicherverhaltens von CDN-Anforderungen mit Abfragezeichenfolgen](cdn-query-string.md).

## Zugreifen auf zwischengespeicherte Inhalte über HTTPS

Azure ermöglicht das Abrufen von Inhalten aus dem CDN mithilfe von HTTPS-Aufrufen. Dadurch können Sie im CDN zwischengespeicherte Inhalte in sichere Webseiten integrieren, ohne Warnungen zu Inhaltstypen mit gemischten Sicherheitsmodi zu erhalten.

Der Zugriff auf CDN-Inhalte über HTTPS weist folgende Einschränkungen auf:


- Sie müssen das vom CDN bereitgestellte Zertifikat verwenden. Zertifikate von Drittanbietern werden nicht unterstützt.
- Sie müssen die CDN-Domäne verwenden, um auf Inhalte zugreifen. HTTPS-Unterstützung ist für benutzerdefinierte Domänennamen (CNAMEs) nicht verfügbar, da das CDN derzeit keine benutzerdefinierten Zertifikate unterstützt.

Weitere Informationen zum Aktivieren von HTTPS für CDN-Inhalte finden Sie unter [Aktivieren des Content Delivery Network (CDN) für Azure](cdn-create-new-endpoint.md).


## Zugreifen auf zwischengespeicherte Inhalte mit benutzerdefinierten Domänen

Sie können den CDN-HTTP-Endpunkt einem benutzerdefinierten Domänennamen zuordnen und diesen Namen zum Anfordern von Objekten aus dem CDN verwenden.

Weitere Informationen zum Zuordnen einer benutzerdefinierten Domäne finden Sie unter [Zuordnen von CDN-Inhalt (Content Delivery Network, Netzwerk für die Inhaltsübermittlung) zu einer benutzerdefinierten Domäne](cdn-map-content-to-custom-domain.md).

## Programmgesteuertes Verwalten von CDN

Microsoft Azure CDN kann mithilfe der [CDN-Ressourcenanbieter-REST-API](https://msdn.microsoft.com/library/mt634456.aspx) programmgesteuert verwaltet werden.


## Weitere Informationen

- [Aktivieren des Content Delivery Network für Azure](cdn-create-new-endpoint.md)
- [Übersicht über das Azure Content Delivery Network (CDN)](cdn-overview.md)
- [Löschen eines Azure CDN-Endpunkts](cdn-purge-endpoint.md)
- [CDN-Ressourcenanbieter-REST-API](https://msdn.microsoft.com/library/mt634456.aspx)

<!---HONumber=AcomDC_1217_2015-->