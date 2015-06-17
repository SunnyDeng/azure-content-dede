<properties 
	pageTitle="So implementieren Sie die Notfallwiederherstellung mit Sichern und Wiederherstellen von Diensten in Azure API Management" 
	description="Erfahren Sie, wie Sie Sichern und Wiederherstellen zur Notfallwiederherstellung in Azure API Management verwenden." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="sdanie"/>

# So implementieren Sie die Notfallwiederherstellung mit Sichern und Wiederherstellen von Diensten in Azure API Management
Indem Sie Ihre APIs über Azure API Management veröffentlichen und verwalten, profitieren Sie von vielen Fehlertoleranz- und Infrastrukturfunktionen, die Sie sonst erst entwickeln, implementieren und verwalten müssten. Die Azure-Plattform minimiert einen Großteil der potenziellen Ausfälle zu einem Bruchteil der Kosten.

Bei Verfügbarkeitsproblemen in der Region, in der Ihr API Management-Dienst gehostet wird, sollten Sie jederzeit dazu in der Lage sein, Ihren Dienst in einer anderen Region wiederherzustellen. Je nach Verfügbarkeitsziel und Wiederherstellungszeitfenster möchten Sie möglicherweise einen Backup-Dienst in einer oder mehreren Regionen vorhalten, dessen Konfiguration und Inhalt möglichst mit dem aktiven Dienst synchron sind. Die Funktion zum Sichern und Wiederherstellen von Diensten ist grundlegend für die Implementierung Ihrer Strategie zur Notfallwiederherstellung.

Die Funktion zum Sichern und Wiederherstellen von Diensten ist über die Service Management REST API verfügbar. Eine Anleitung dazu, wie Sie Zugriff auf die API erhalten, finden Sie unter [Authentifizieren von Azure-Ressourcen-Manager-Anforderungen][].

## <a name="step1"> </a>Sichern eines API Management-Diensts
Zum Sichern eines API Management-Diensts führen Sie die folgende HTTP-Anforderung aus:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

Hinweis:

* `subscriptionId` – Abonnement-ID des API Management-Diensts, den Sie sichern möchten
* `resourceGroupName` – Eine Zeichenfolge in der Form "Api-Default-{service-region}", wobei `service-region` die Azure-Region angibt, in welcher der zu sichernde API Management-Dienst gehostet wird, z. B. `North-Central-US`
* `serviceName` – Der Name des zu sichernden API Management-Diensts zum Zeitpunkt seiner Erstellung
* `api-version` – Ersetzen Sie diesen Wert durch `2014-02-14`

Geben Sie im Hauptteil der Anforderung das Azure-Zielspeicherkonto, den Zugriffsschlüssel, den Blobcontainernamen und den Sicherungsnamen an:

	'{  
	    storageAccount : "{storage account name for the backup}",  
	    accessKey : "{access key for the account}",  
	    containerName : "{backup container name}",  
	    backupName : "{backup blob name}"  
	}'

Legen Sie für den `Content-Type`-Anforderungsheader den Wert `application/json` fest.

Die Sicherung ist ein länger anhaltender Vorgang, der bis zum Abschluss mehrere Minuten dauern kann. Falls die Anforderung erfolgreich war und der Sicherungsvorgang eingeleitet wurde, erhalten Sie den Antwortstatuscode `202 Accepted`, zusammen mit einem `Location`-Header. Senden Sie GET-Anforderungen der URL im `Location`-Header, um den Status des Vorgangs zu ermitteln. Während der Sicherung erhalten Sie weiterhin den Statuscode '202 Accepted‘. Mit dem Antwortcode `200 OK` wird der erfolgreiche Abschluss des Sicherungsvorgangs angezeigt.

**Hinweis**:

- Der im Hauptteil der Anforderung angegebene **Container** **muss vorhanden sein**.
* **Versuchen Sie nicht, bei laufender Sicherung Dienstverwaltungsvorgänge durchzuführen** wie Ebenen-Upgrade oder -Downgrade, Wechseln des Domänennamens usw. 
* Die Wiederherstellung einer Sicherung nach ihrer Erstellung **wird nur 7 Tage lange garantiert**. 
* **Nutzungsdaten** zum Erstellen von Analyseberichten sind in der Sicherung **nicht enthalten**. Verwenden Sie [Azure API Management REST API][], um regelmäßig Analyseberichte zur Aufbewahrung abzurufen.
* Die Häufigkeit, mit der Sie Dienstsicherungen durchführen, wirkt sich auf das Ziel Ihres Wiederherstellungspunkts aus. Um die Auswirkungen zu minimieren, empfehlen wir, regelmäßige Sicherungen zu implementieren und bei Bedarf Sicherungen durchzuführen, wenn Sie bedeutende Änderungen an Ihrem API Management-Dienst vorgenommen haben.
* **Änderungen** an der Dienstkonfiguration (z. B. APIs, Richtlinien, Erscheinungsbild des Entwicklerportals), die während des Sicherungsvorgangs vorgenommen werden, sind ggf. **nicht in der Sicherung enthalten und gehen somit verloren**.

## <a name="step2"> </a>Wiederherstellen eines API Management-Diensts
Zum Wiederherstellen eines API Management-Diensts aus einer zuvor erstellten Sicherung führen Sie die folgende HTTP-Anforderung aus:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

Hinweis:

* `subscriptionId`– Abonnement-ID des API Management-Diensts, den Sie sichern möchten
* `resourceGroupName` – Eine Zeichenfolge in der Form "Api-Default-{service-region}", wobei `service-region` die Azure-Region angibt, in welcher der zu wiederherzustellende API Management-Dienst gehostet wird, z. B. `North-Central-US`
* `serviceName` – Der Name des wiederherzustellenden API Management-Diensts zum Zeitpunkt seiner Erstellung
* `api-version` – Ersetzen Sie diesen Wert durch `2014-02-14`

Geben Sie im Hauptteil der Anforderung den Speicherort der Sicherungsdatei an, d. h. das Azure-Zielspeicherkonto, den Zugriffsschlüssel, den Blobcontainernamen und den Sicherungsnamen:

	'{  
	    storageAccount : "{storage account name for the backup}",  
	    accessKey : "{access key for the account}",  
	    containerName : "{backup container name}",  
	    backupName : "{backup blob name}"  
	}'

Legen Sie für den `Content-Type`-Anforderungsheader den Wert `application\json` fest.

Die Wiederherstellung ist ein länger anhaltender Vorgang, der bis zum Abschluss bis zu 30 Minuten dauern kann. Falls die Anforderung erfolgreich war und der Wiederherstellungsvorgang eingeleitet wurde, erhalten Sie den Antwortstatuscode `202 Accepted`, zusammen mit einem `Location`-Header. Senden Sie GET-Anforderungen der URL im `Location`-Header, um den Status des Vorgangs zu ermitteln. Während der Wiederherstellung erhalten Sie weiterhin den Statuscode '202 Accepted‘. Mit dem Antwortcode `200 OK` wird der erfolgreiche Abschluss des Wiederherstellungsvorgangs angezeigt.

**Hinweis**:

- **Die Ebene** des wiederherzustellenden Diensts **muss der Ebene des gesicherten Diensts entsprechen**.
- **Änderungen** an der Dienstkonfiguration (z. B. APIs, Richtlinien, Erscheinungsbild des Entwicklerportals), die während des Wiederherstellungsvorgangs vorgenommen werden, **könnten überschrieben werden**.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2

[Authentifizieren von Azure-Ressourcen-Manager-Anforderungen]: http://msdn.microsoft.com/library/dn790557.aspx
[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->