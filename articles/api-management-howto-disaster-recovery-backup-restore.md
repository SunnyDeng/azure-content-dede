<properties pageTitle="How to implement disaster recovery using service backup and restore in Azure API Management" metaKeywords="" description="Learn how to use backup and restore to perform disaster recovery in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to implement disaster recovery using service backup and restore in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# So implementieren Sie die Notfallwiederherstellung mit Sichern und Wiederherstellen von Diensten in Azure API Management

Indem Sie Ihre APIs über Azure API Management veröffentlichen und verwalten, profitieren Sie von vielen Fehlertoleranz- und Infrastrukturfunktionen, die Sie sonst erst entwickeln, implementieren und verwalten müssten. Die Azure-Plattform minimiert einen Großteil der potenziellen Ausfälle zu einem Bruchteil der Kosten.

Bei Verfügbarkeitsproblemen in der Region, in der Ihr API-Verwaltungsdienst gehostet wird, sollten Sie jederzeit dazu in der Lage sein, Ihren Dienst in einer anderen Region wiederherzustellen. Je nach Verfügbarkeitsziel und Wiederherstellungszeitfenster möchten Sie möglicherweise einen Backup-Dienst in einer oder mehreren Regionen vorhalten, dessen Konfiguration und Inhalt möglichst mit dem aktiven Dienst synchron sind. Die Funktion zum Sichern und Wiederherstellen von Diensten ist grundlegend für die Implementierung Ihrer Strategie zur Notfallwiederherstellung.

Die Funktion zum Sichern und Wiederherstellen von Diensten ist über die Service Management REST API verfügbar. Eine Anleitung, wie Sie Zugriff auf die API erhalten, finden Sie unter [Authenticating Azure Resource Manager requests][Authenticating Azure Resource Manager requests].

## In diesem Thema

-   [Sichern eines API-Verwaltungsdiensts][Sichern eines API-Verwaltungsdiensts]
-   [Wiederherstellen eines API-Verwaltungsdiensts][Wiederherstellen eines API-Verwaltungsdiensts]

## <a name="step1"></a>Sichern eines API-Verwaltungsdiensts

Zum Sichern eines API-Verwaltungsdiensts stellen Sie die folgende HTTP-Anforderung:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

Hinweis:

-   `subscriptionId` – Abonnement-ID des API-Verwaltungsdiensts, den Sie sichern möchten
-   `resourceGroupName` – eine Zeichenfolge in der Form 'Api-Default-{service-region}', wobei `service-region` die Azure-Region bezeichnet, in welcher der zu sichernde API-Verwaltungsdienst gehostet wird, z. B. `North-Central-US`
-   `serviceName` – der Name des zu sichernden API-Verwaltungsdiensts zum Zeitpunkt seiner Erstellung
-   `api-version` – Ersetzen durch `2014-02-14`

Geben Sie im Hauptteil der Anforderung das Azure-Speicherkonto, den Zugriffsschlüssel, den Blob-Containernamen und den Sicherungsnamen an:

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

Legen Sie für den `Content-Type`-Anforderungsheader den Wert `application\json` fest.

Die Sicherung ist ein länger anhaltender Vorgang, der bis zum Abschluss mehrere Minuten dauern kann. Falls die Anforderung erfolgreich war und der Sicherungsvorgang eingeleitet wurde, erhalten Sie den Antwortstatuscode `202 Accepted`, zusammen mit einem `Location`-Header. Stellen Sie GET-Anforderungen der URL im `Location`-Header, um den Status des Vorgangs zu ermitteln. Während der Sicherung erhalten Sie weiterhin den Statuscode '202 Accepted‘. Mit dem Antwortcode `200 OK` wird der erfolgreiche Abschluss des Sicherungsvorgangs angezeigt.

**Hinweis**:

-   Der im Hauptteil der Anforderung angegebene **Container** **muss vorhanden sein**.
-   **Versuchen Sie nicht, bei laufender Sicherung Dienstverwaltungsvorgänge durchzuführen** wie Ebenen-Upgrade oder -Downgrade, Wechseln des Domänennamens usw.
-   Die Wiederherstellung einer Sicherung nach ihrer Erstellung **wird nur 7 Tage lange garantiert**.
-   **Nutzungsdaten** zum Erstellen von Analyseberichten sind in der Sicherung **nicht enthalten**. Verwenden Sie [Azure API Management REST API][Azure API Management REST API], um regelmäßig Analyseberichte zur Aufbewahrung abzurufen.
-   Die Häufigkeit, mit der Sie Dienstsicherungen durchführen, wirkt sich auf das Ziel Ihres Wiederherstellungspunkts aus. Um die Auswirkungen zu minimieren, empfehlen wir, regelmäßige Sicherungen zu implementieren und bedarfsweise Sicherungen durchzuführen, wenn Sie bedeutende Änderungen an Ihrem API-Verwaltungsdienst vorgenommen haben.
-   **Änderungen** an der Dienstkonfiguration (z. B. APIs, Richtlinien, Erscheinungsbild des Entwicklerportals), die während des Sicherungsvorgangs vorgenommen werden, sind ggf. **nicht in der Sicherung enthalten und gehen somit verloren**.

## <a name="step2"> </a>Wiederherstellen eines API-Verwaltungsdiensts

Zum Widerherstellen eines API-Verwaltungsdiensts aus einer zuvor erstellten Sicherung stellen Sie die folgende HTTP-Anforderung:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

Hinweis:

-   `subscriptionId` – Abonnement-ID des API-Verwaltungsdiensts, den Sie sichern möchten
-   `resourceGroupName` – eine Zeichenfolge in der Form 'Api-Default-{service-region}', wobei `service-region` die Azure-Region bezeichnet, in welcher der wiederherzustellende API-Verwaltungsdienst gehostet wird, z. B. `North-Central-US`
-   `serviceName` – der Name des wiederherzustellenden API-Verwaltungsdiensts zum Zeitpunkt seiner Erstellung
-   `api-version` – Ersetzen durch `2014-02-14`

Geben Sie im Hauptteil der Anforderung den Speicherort der Sicherungsdatei an, d. h. das Azure-Speicherkonto, den Zugriffsschlüssel, den Blob-Containernamen und den Sicherungsnamen:

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

Legen Sie für den `Content-Type`-Anforderungsheader den Wert `application\json` fest.

Die Wiederherstellung ist ein länger anhaltender Vorgang, der bis zum Abschluss bis zu 30 Minuten dauern kann. Falls die Anforderung erfolgreich war und der Wiederherstellungsvorgang eingeleitet wurde, erhalten Sie den Antwortstatuscode `202 Accepted`, zusammen mit einem `Location`-Header. Stellen Sie GET-Anforderungen der URL im `Location`-Header, um den Status des Vorgangs zu ermitteln. Während der Wiederherstellung erhalten Sie weiterhin den Statuscode '202 Accepted‘. Mit dem Antwortcode `200 OK` wird der erfolgreiche Abschluss des Wiederherstellungsvorgangs angezeigt.

**Hinweis**:

-   **Die Ebene** des wiederherzustellenden Diensts **muss der Ebene des gesicherten Diensts entsprechen**.
-   **Änderungen** an der Dienstkonfiguration (z. B. APIs, Richtlinien, Erscheinungsbild des Entwicklerportals), die während des Wiederherstellungsvorgangs vorgenommen werden, **könnten überschrieben werden**.

  [Authenticating Azure Resource Manager requests]: http://msdn.microsoft.com/en-us/library/dn790557.aspx
  [Sichern eines API-Verwaltungsdiensts]: #step1
  [Wiederherstellen eines API-Verwaltungsdiensts]: #step2
  [Azure API Management REST API]: http://msdn.microsoft.com/en-us/library/azure/dn781421.aspx
