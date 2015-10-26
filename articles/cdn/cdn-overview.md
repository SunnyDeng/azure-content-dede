<properties 
	pageTitle="Übersicht über das Azure CDN" 
	description="Erfahren Sie, was das Azure Content Delivery Network (CDN, Netzwerk für die Inhaltsübermittlung) ist und wie es für die Übermittlung breitbandiger Inhalte eingesetzt wird, indem Blobs und statische Inhalte zwischengespeichert werden." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/01/2015" 
	ms.author="mazha"/>

#Übersicht über das Azure Content Delivery Network (CDN)

Das Azure CDN (Content Delivery Network, Netzwerk für die Inhaltsübermittlung) speichert von Clouddiensten verwendete Azure-Blobs und statische Inhalte an strategisch platzierten Standorten zwischen, um für das Bereitstellen von Inhalten für Benutzer maximale Bandbreite zu bieten.

Wenn Sie bereits CDN-Kunde sind, können Sie jetzt Ihre CDN-Endpunkte über das [Microsoft Azure-Verwaltungsportal](https://manage.windowsazure.com) verwalten.


>[AZURE.NOTE]Azure CDN verfügte über einen von Azure Storage und Azure Cloud Services getrennten [Abrechnungsplan](http://www.microsoft.com/windowsazure/pricing/).
 

Das CDN bietet Entwicklern eine globale Lösung für die Übermittlung von Inhalten mit hoher Bandbreite durch Zwischenspeichern der Inhalte auf physischen Knoten auf der ganzen Welt. Eine aktuelle Liste von CDN-Knotenstandorten finden Sie unter [POP-Standorte von Azure Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/gg680302.aspx).

Die Verwendung des CDN zum Zwischenspeichern von Azure-Daten bietet folgende Vorteile:

- Bessere Leistung und Benutzerfreundlichkeit für Endbenutzer, die von einer Inhaltsquelle weit entfernt sind und Anwendungen verwenden, die viele Verbindungen mit dem Internet benötigen, um Inhalte zu laden.
- Große verteilte Skalierung, um hohe Lasten etwa zu Beginn eines Ereignisses, z. B. bei einer Produkteinführung, besser verarbeiten zu können 


>[AZURE.IMPORTANT]Wenn Sie einen CDN-Endpunkt erstellen oder aktivieren, dauert es bis zu 60 Minuten, bis dieser weltweit verfügbar ist.
 
Wenn eine Anforderung für ein Objekt zuerst an das CDN gerichtet wird, wird das Objekt direkt aus dem Blobdienst oder dem Clouddienst abgerufen. Bei einer Anforderung mit der CDN-Syntax wird die Anforderung zum CDN-Endpunkt umgeleitet, der dem Standort am nächsten ist, von dem die Anforderung gestellt wurde, um auf das Objekt zuzugreifen. Wenn das Objekt an diesem Endpunkt nicht gefunden wird, wird es aus dem Dienst abgerufen und am Endpunkt zwischengespeichert, wobei eine Einstellung für Gültigkeitsdauer für das zwischengespeicherte Objekt verwaltet wird.
 
##Zwischenspeichern von Inhalten aus Azure-Speicher

Sobald das CDN für ein Azure-Speicherkonto aktiviert wurde, werden alle Blobs in öffentlichen Containern, die für den anonymen Zugriff zur Verfügung stehen, über das CDN zwischengespeichert. Nur Blobs, die öffentlich verfügbar sind, können mit dem Azure CDN zwischengespeichert werden. Um ein Blob öffentlich für den anonymen Zugriff verfügbar zu machen, müssen Sie den Container als öffentlich kennzeichnen. Nachdem dies erfolgt ist, sind alle Blobs in diesem Container für anonymen Lesezugriff verfügbar. Sie können auch Containerdaten öffentlich verfügbar machen oder den Zugriff auf die enthaltenen Blobs beschränken. Unter [Beschränken des Zugriffs auf Container und Blobs](http://msdn.microsoft.com/library/azure/dd179354.aspx) finden Sie weitere Informationen zur Verwaltung der Zugriffssteuerung für Container und Blobs.

Für eine optimale Leistung verwenden Sie die CDN-Edge-Zwischenspeicherung für die Übermittlung von Blobs kleiner als 10 GB.

Wenn Sie den CDN-Zugriff für ein Speicherkonto aktivieren, stellt Ihnen das Verwaltungsportal einen CDN-Domänennamen im folgenden Format zur Verfügung: http://<identifier>.vo.msecnd.net/. Dieser Domänenname kann verwendet werden, um auf Blobs in einem öffentlichen Container zuzugreifen. Beispielsweise können Benutzer bei einem öffentlichen Container namens "music" in einem Speicherkonto namens "myaccount" über eine der beiden folgenden URLs auf die Blobs in diesem Container zugreifen:

- **Azure-Blobdienst-URL**: `http://myAccount.blob.core.windows.net/music/` 
- **Azure CDN-URL**: `http://<identifier>.vo.msecnd.net/music/` 

##Zwischenspeichern von Inhalten von Azure-Websites

Sie können das CDN auf Ihren Websites zum Zwischenspeichern Ihrer Web-Inhalte wie Bilder, Skripts und Stylesheets aktivieren. Siehe [Integrieren einer Azure-Website in Azure CDN](../cdn-websites-with-cdn.md).

Wenn Sie den CDN-Zugriff für eine Website aktivieren, stellt Ihnen das Verwaltungsportal einen CDN-Domänennamen im folgenden Format zur Verfügung: http://<identifier>.vo.msecnd.net/. Dieser Domänenname kann verwendet werden, um Objekte von einer Website abzurufen. Beispielsweise können Benutzer bei einem öffentlichen Container namens "cdn" und einer Bilddatei namens "music.png" auf das Objekt über eine der beiden folgenden URLs zugreifen:

- **Azure-Website-URL**: `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **Azure CDN-URL**: `http://<identifier>.vo.msecnd.net/cdn/music.png`
 
##Zwischenspeichern von Inhalten aus Azure-Clouddiensten

Sie können Objekte im CDN zwischenspeichern, die von einem Azure-Clouddienst bereitgestellt werden.

Das Zwischenspeichern für Clouddienste unterliegt folgenden Einschränkungen:


- Das CDN darf nur verwendet werden, um statische Inhalte zwischenzuspeichern.

	>[AZURE.WARNING]Das Zwischenspeichern von überaus veränderlichen oder wirklich dynamischen Inhalten kann sich negativ auf die Leistung auswirken oder zu Problemen mit Inhalten führen, was alles höhere Kosten verursacht.
- Ihr Clouddienst muss in einer Produktionsumgebung bereitgestellt werden.
- Ihr Clouddienst muss das Objekt an Port 80 über HTTP bereitstellen.
- Der Clouddienst muss den Inhalt, der zwischengespeichert oder übermittelt werden soll, im Ordner "/cdn" für den Clouddienst platzieren.

Wenn Sie den CDN-Zugriff für einen Clouddienst aktivieren, stellt Ihnen das Verwaltungsportal einen CDN-Domänennamen im folgenden Format zur Verfügung: http://<identifier>.vo.msecnd.net/. Dieser Domänenname kann verwendet werden, um Objekte aus einem Clouddienst abzurufen. Beispielsweise können Benutzer bei einem Clouddienst namens "myHostedService" und einer ASP.NET-Website namens "music.aspx", die Inhalte übermittelt, auf das Objekt über eine der beiden folgenden URLs zugreifen:


- **Azure-Clouddienst-URL**: `http://myHostedService.cloudapp.net/cdn/music.aspx` 
- **Azure CDN-URL**: `http://<identifier>.vo.msecnd.net/music.aspx` 


###Zwischenspeichern bestimmter Inhalte mit Abfragezeichenfolgen

Sie können Abfragezeichenfolgen verwenden, um aus einem Clouddienst abgerufene Objekte zu unterscheiden. Wenn der Clouddienst beispielsweise ein Diagramm angezeigt, das variieren kann, können Sie eine Abfragezeichenfolge übergeben, um das jeweils erforderliche Diagramm abzurufen. Beispiel:

`http://<identifier>.vo.msecnd.net/chart.aspx?item=1`

Abfragezeichenfolgen werden als Zeichenfolgenliterale übergeben. Wenn Sie über einen Dienst verfügen, der zwei Parameter wie z. B. `?area=2&item=1` verwendet und nachfolgend den Dienst mithilfe von `?item=1&area=2` aufruft, werden zwei Kopien desselben Objekts zwischengespeichert.
 

##Zugreifen auf zwischengespeicherte Inhalte über HTTPS


Azure ermöglicht das Abrufen von Inhalten aus dem CDN mithilfe von HTTPS-Aufrufen. Dadurch können Sie im CDN zwischengespeicherte Inhalte in sichere Webseiten integrieren, ohne Warnungen zu Inhaltstypen mit gemischten Sicherheitsmodi zu erhalten.

Der Zugriff auf CDN-Inhalte über HTTPS weist folgende Einschränkungen auf:


- Sie müssen das vom CDN bereitgestellte Zertifikat verwenden. Zertifikate von Drittanbietern werden nicht unterstützt.
- Sie müssen die CDN-Domäne verwenden, um auf Inhalte zugreifen. HTTPS-Unterstützung ist für benutzerdefinierte Domänennamen (CNAMEs) nicht verfügbar, da das CDN derzeit keine benutzerdefinierten Zertifikate unterstützt.



Auch wenn HTTPS aktiviert ist, kann der Inhalt aus dem CDN über sowohl HTTP als auch HTTPS abgerufen werden.

Weitere Informationen zum Aktivieren von HTTPS für CDN-Inhalte finden Sie unter [Aktivieren des Content Delivery Network (CDN) für Azure](http://msdn.microsoft.com/library/azure/gg680301.aspx).


##Zugreifen auf zwischengespeicherte Inhalte mit benutzerdefinierten Domänen

Sie können den CDN-HTTP-Endpunkt einem benutzerdefinierten Domänennamen zuordnen und diesen Namen zum Anfordern von Objekten aus dem CDN verwenden.

Weitere Informationen zum Zuordnen einer benutzerdefinierten Domäne finden Sie unter [Zuordnen von CDN-Inhalt (Content Delivery Network, Netzwerk für die Inhaltsübermittlung) zu einer benutzerdefinierten Domäne](http://msdn.microsoft.com/library/azure/gg680307.aspx).

 

<!---HONumber=Oct15_HO3-->