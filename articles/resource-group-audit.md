<properties 
	pageTitle="Überwachen von Vorgängen mit dem Ressourcen-Manager | Microsoft Azure" 
	description="Verwenden Sie das Überwachungsprotokoll im Ressourcen-Manager, um Benutzeraktionen und Fehler zu überprüfen. Zeigt PowerShell, Azure CLI und REST." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015" 
	ms.author="tomfitz"/>

# Überwachen von Vorgängen mit dem Ressourcen-Manager

Wenn während der Bereitstellung oder des Betriebs Ihrer Lösung ein Problem auftritt, müssen Sie den Grund dafür finden. Der Ressourcen-Manager bietet zwei Möglichkeiten, um herauszufinden, was passiert ist und warum es passiert ist. Sie können Bereitstellungsbefehle verwenden, um Informationen zu bestimmten Bereitstellungen und Vorgängen abzurufen. Alternativ dazu können Sie Überwachungsprotokolle verwenden, um Informationen zu Bereitstellungen und anderen Aktionen abzurufen, die während der Lebensdauer der Lösung durchgeführt wurden. In diesem Thema geht es um die Überwachungsprotokolle.

Ein Überwachungsprotokoll enthält alle Aktionen, die mit Ihren Ressourcen durchgeführt wurden. Wenn also ein Benutzer Ihrer Organisation eine Ressource ändert, können Sie die Aktion, das Datum und die Uhrzeit sowie den Benutzer ermitteln.

Sie können Informationen aus den Überwachungsprotokollen über Azure PowerShell, die Azure-Befehlszeilenschnittstelle, eine REST-API oder das Azure-Vorschauportal abrufen.

## PowerShell

Führen Sie zum Abrufen von Protokolleinträgen den Befehl **Get-AzureRmLog** aus (oder **Get-AzureResourceGroupLog** für PowerShell-Versionen vor 1.0 Preview). Wenn Sie die Liste der Einträge filtern möchten, können Sie dem Befehl Parameter hinzufügen.

Das folgende Beispiel zeigt, wie Sie mithilfe des Überwachungsprotokolls nach Aktionen suchen können, die während der Lebensdauer der Lösung durchgeführt wurden. Sie können sehen, wann die Aktion durchgeführt wurde und wer sie angefordert hat.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00

Je nachdem, welches Startdatum und welche Startzeit Sie angeben, kann der oben stehende Befehl eine sehr lange Liste mit Aktionen für diese Ressourcengruppe zurückgeben. Sie können die Ergebnisse filtern, indem Sie Suchkriterien eingeben. Wenn Sie beispielsweise herausfinden möchten, wie eine Web-App angehalten wurde, können Sie folgenden Befehl ausführen, und Sie werden feststellen, dass die Aktion durch someone@example.com durchgeführt wurde.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 | Where-Object OperationName -eq Microsoft.Web/sites/stop/action

    Authorization     :
                        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
                        Action    : Microsoft.Web/sites/stop/action
                        Role      : Subscription Admin
                        Condition :
    Caller            : someone@example.com
    CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
    EventSource       : Administrative
    EventTimestamp    : 8/28/2015 4:08:18 PM
    OperationName     : Microsoft.Web/sites/stop/action
    ResourceGroupName : ExampleGroup
    ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
    Status            : Succeeded
    SubscriptionId    : xxxxx
    SubStatus         : OK

Im nächsten Beispiel suchen wir nach Aktionen, bei denen nach der angegebenen Startzeit ein Fehler aufgetreten ist. Hier wird auch der Parameter **DetailedOutput** einbezogen, damit die Fehlermeldungen angezeigt werden.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-27T12:00 -Status Failed –DetailedOutput
    
Wenn dieser Befehl zu viele Einträge und Eigenschaften zurückgibt, können Sie die Überwachung genauer fokussieren, indem Sie die **properties**-Eigenschaft abrufen.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties

    Content
    -------
    {}
    {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
    {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

Und Sie können die Suchergebnisse weiter eingrenzen, indem Sie die Statusmeldung anzeigen lassen.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :


## Azure-Befehlszeilenschnittstelle

Um Protokolleinträge abzurufen, führen Sie den Befehl **azure group log show** aus.

    azure group log show ExampleGroup

Sie können die Ergebnisse mit einem JSON-Hilfsprogramm wie z. B. [jq](http://stedolan.github.io/jq/download/) filtern. Das folgende Beispiel zeigt, wie Sie nach Vorgängen zur Aktualisierung einer Webkonfigurationsdatei suchen.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

Sie können den Parameter **–-last-deployment** hinzufügen, um die zurückgegebenen Einträge auf die Vorgänge seit der letzten Bereitstellung einzugrenzen.

    azure group log show ExampleGroup --last-deployment

Wenn die Liste der Vorgänge seit der letzten Bereitstellung zu lang ist, können Sie die Ergebnisse filtern, sodass nur fehlerhafte Vorgänge angezeigt werden.

    azure group log show tfCopyGroup --last-deployment --json | jq ".[] | select(.status.value == "Failed")"

                                   /Microsoft.Web/Sites/ExampleSite
    data:    SubscriptionId:       <guid>
    data:    EventTimestamp (UTC): Thu Aug 27 2015 13:03:27 GMT-0700 (Pacific Daylight Time)
    data:    OperationName:        Update website
    data:    OperationId:          cb772193-b52c-4134-9013-673afe6a5604
    data:    Status:               Failed
    data:    SubStatus:            Conflict (HTTP Status Code: 409)
    data:    Caller:               someone@example.com
    data:    CorrelationId:        a8c7a2b4-5678-4b1b-a50a-c17230427b1e
    data:    Description:
    data:    HttpRequest:          clientRequestId: <guid>
                                   clientIpAddress: 000.000.000.000
                                   method:          PUT

    data:    Level:                Error
    data:    ResourceGroup:        ExampleGroup
    data:    ResourceProvider:     Azure Web Sites
    data:    EventSource:          Administrative
    data:    Properties:           statusCode:       Conflict
                                   serviceRequestId:
                                   statusMessage:    {"Code":"Conflict","Message":"Website with given name
                                   ExampleSite already exists.","Target":null,"
                                   Details
                                   ":[{"Message":"Website with given name ExampleSite already exists."},
                                   {"Code":"Conflict"},
                                   {"ErrorEntity":{"Code":"Conflict","Message":"Website with given
                                   name ExampleSite already exists.","ExtendedCode
                                   ":"
                                   54001","MessageTemplate":"Website with given name {0} already exists.",
                                   "Parameters":["ExampleSite"],"
                                   InnerErrors":null}}],"Innererror":null}



## REST-API

Die REST-Vorgänge für die Arbeit mit Überwachungsprotokollen gehören zur [Insights-REST-API](https://msdn.microsoft.com/library/azure/dn931943.aspx). Informationen zum Abrufen von Überwachungsprotokollereignissen finden Sie unter [Auflisten der Verwaltungsereignisse in einem Abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Vorschauportal

Sie können protokollierte Vorgänge auch über das Vorschauportal anzeigen. Wählen Sie einfach das Blatt mit den Überwachungsprotokollen aus.

![Überwachungsprotokolle auswählen](./media/resource-group-audit/select-audit.png)

Zeigen Sie die Liste der letzten Vorgänge an.

![Aktionen anzeigen](./media/resource-group-audit/show-actions.png)

Sie können jeden Vorgang auswählen, um weitere Details anzuzeigen.

## Nächste Schritte

- Informationen zum Festlegen von Sicherheitsrichtlinien finden Sie unter [Verwalten des Zugriffs auf Ressourcen](./azure-portal/resource-group-rbac.md).
- Informationen zum Gewähren des Zugriffs für einen Dienstprinzipal finden Sie unter [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](resource-group-authenticate-service-principal.md).
- Informationen zum Beschränken von Aktionen für eine Ressource für alle Benutzer finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](resource-group-lock-resources.md).

<!---HONumber=Oct15_HO2-->