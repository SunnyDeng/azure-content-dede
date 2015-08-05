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
	ms.date="06/24/2015" 
	ms.author="sdanie"/>

# So implementieren Sie die Notfallwiederherstellung mit Sichern und Wiederherstellen von Diensten in Azure API Management

Indem Sie Ihre APIs über Azure API Management veröffentlichen und verwalten, profitieren Sie von vielen Fehlertoleranz- und Infrastrukturfunktionen, die Sie sonst erst entwickeln, implementieren und verwalten müssten. Die Azure-Plattform minimiert einen Großteil der potenziellen Ausfälle zu einem Bruchteil der Kosten.

Bei Verfügbarkeitsproblemen in der Region, in der Ihr API Management-Dienst gehostet wird, sollten Sie jederzeit dazu in der Lage sein, Ihren Dienst in einer anderen Region wiederherzustellen. Je nach Verfügbarkeitsziel und Wiederherstellungszeitfenster möchten Sie möglicherweise einen Backup-Dienst in einer oder mehreren Regionen vorhalten, dessen Konfiguration und Inhalt möglichst mit dem aktiven Dienst synchron sind. Die Funktion zum Sichern und Wiederherstellen von Diensten ist grundlegend für die Implementierung Ihrer Strategie zur Notfallwiederherstellung.

In dieser Anleitung wird erläutert, wie Anforderungen des Azure-Ressourcen-Managers authentifiziert und API Management-Dienstinstanzen gesichert und wiederhergestellt werden.

>[AZURE.NOTE]Der Vorgang zum Sichern und Wiederherstellen einer API Management-Dienstinstanz für die Notfallwiederherstellung kann auch zum Replizieren von API Management-Dienstinstanzen für Szenarios wie z. B. das Staging verwendet werden.

## Authentifizieren von Anforderungen des Azure-Ressourcen-Managers

>[AZURE.IMPORTANT]Die REST-API für die Sicherung und Wiederherstellung verwendet den Azure-Ressourcen-Manager und weist eine andere Authentifizierungsmethode als die REST-APIs für die Verwaltung von API Management-Entitäten auf. In den Schritten in diesem Abschnitt wird beschrieben, wie Anforderungen des Azure-Ressourcen-Managers authentifiziert werden. Weitere Informationen finden Sie unter [Authentifizieren von Anforderungen des Azure-Ressourcen-Managers](http://msdn.microsoft.com/library/dn790557.aspx).

Alle Aufgaben, die Sie mithilfe des Azure-Ressourcen-Managers für Ressourcen ausführen, müssen entsprechend den folgenden Schritten mit Azure Active Directory authentifiziert werden.

-	Fügen Sie dem Azure Active Directory-Mandanten eine Anwendung hinzu.
-	Legen Sie Berechtigungen für die hinzugefügte Anwendung fest.
-	Rufen Sie das Token zum Authentifizieren von Anforderungen an den Azure-Ressourcen-Manager ab.

Im ersten Schritt wird eine Azure Active Directory-Anwendung erstellt. Melden Sie sich mit dem Abonnement, das Ihre API Management-Dienstinstanz enthält, beim [Verwaltungsportal](http://manage.windowsazure.com/) an, und navigieren Sie zu der Registerkarte **Anwendungen** für Ihr Azure Active Directory-Standardverzeichnis.

>[AZURE.NOTE]Wenn das Azure Active Directory-Standardverzeichnis in Ihrem Konto nicht angezeigt wird, bitten Sie den Administrator des Azure-Abonnements, die erforderlichen Berechtigungen für das Konto zu erteilen. Informationen zur Suche des Standardverzeichnisses finden Sie unter [Suchen des Standardverzeichnisses im Azure-Verwaltungsportal](../virtual-machines/resource-group-create-work-id-from-persona.md/#locate-your-default-directory-in-the-azure-portal).

![Erstellen der Azure Active Directory-Anwendung][api-management-add-aad-application]

Klicken Sie auf **Hinzufügen**, anschließend auf **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**, und wählen Sie dann **Systemeigene Clientanwendung** aus. Geben Sie einen beschreibenden Namen ein, und klicken Sie auf den Pfeil für "Weiter". Geben Sie eine Platzhalter-URL wie z. B. `http://resources` für den **Umleitungs-URI** ein, da es sich um ein Pflichtfeld handelt. Der Wert wird jedoch später nicht verwendet. Klicken Sie auf das Kontrollkästchen, um die Anwendung zu speichern.

Klicken Sie nach dem Speichern der Anwendung auf **Konfigurieren**, scrollen Sie nach unten zum Bereich **Berechtigungen für andere Anwendungen**, und klicken Sie auf **Anwendung hinzufügen**.

![Hinzufügen von Berechtigungen][api-management-aad-permissions-add]

Wählen Sie **Windows** **Azure-Dienstverwaltungs-API** aus, und klicken Sie auf das Kontrollkästchen, um die Anwendung hinzuzufügen.

![Hinzufügen von Berechtigungen][api-management-aad-permissions]

Klicken Sie auf **Delegierte Berechtigungen** neben der neu hinzugefügten Anwendung **Windows** **Azure-Dienstverwaltungs-API**, aktivieren Sie das Kontrollkästchen für **Access Azure Service Management (preview)**, und klicken Sie dann auf **Speichern**.

![Hinzufügen von Berechtigungen][api-management-aad-delegated-permissions]

Vor dem Aufrufen der APIs, die die Sicherung erstellen und wiederherstellen, muss ein Token abgerufen werden. Im folgenden Beispiel wird das Token mit dem NuGet-Paket [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) abgerufen.

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System;

	namespace GetTokenResourceManagerRequests
	{
        class Program
	    {
	        static void Main(string[] args)
	        {
	            var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
	            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

	            if (result == null) {
	                throw new InvalidOperationException("Failed to obtain the JWT token");
	            }

	            Console.WriteLine(result.AccessToken);

	            Console.ReadLine();
	        }
    	}
	}

Ersetzen Sie `{tentand id}`, `{application id}` und `{redirect uri}` entsprechend den folgenden Anweisungen.

Ersetzen Sie `{tenant id}` durch die Mandanten-ID der Azure Active Directory-Anwendung, die Sie gerade erstellt haben. Auf die ID können Sie durch Klicken auf **Endpunkte anzeigen** zugreifen.

![Endpunkte][api-management-aad-default-directory]

![Endpunkte][api-management-endpoint]

Ersetzen Sie `{application id}` und `{redirect uri}` durch die **Client-ID** und die URL aus dem Bereich **Umleitungs-URIs** auf der Registerkarte **Konfigurieren** der Azure Active Directory-Anwendung.

![Ressourcen][api-management-aad-resources]

Wenn die Werte angegeben wurden, sollte im Codebeispiel ein Token ähnlich dem folgenden Beispiel zurückgegeben werden.

![Tokenverschlüsselung][api-management-arm-token]

Legen Sie vor dem Aufrufen der in den folgenden Abschnitten beschriebenen Vorgänge zur Sicherung und Wiederherstellung den Autorisierungsanforderungsheader für den REST-Aufruf fest.

	request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

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
	    storageAccount : {storage account name for the backup},  
	    accessKey : {access key for the account},  
	    containerName : {backup container name},  
	    backupName : {backup blob name}  
	}'

Legen Sie für den `Content-Type`-Anforderungsheader den Wert `application/json` fest.

Die Sicherung ist ein länger anhaltender Vorgang, der bis zum Abschluss mehrere Minuten dauern kann. Falls die Anforderung erfolgreich war und der Sicherungsvorgang eingeleitet wurde, erhalten Sie den Antwortstatuscode `202 Accepted`, zusammen mit einem `Location`-Header. Senden Sie GET-Anforderungen der URL im `Location`-Header, um den Status des Vorgangs zu ermitteln. Während der Sicherung erhalten Sie weiterhin den Statuscode '202 Accepted‘. Mit dem Antwortcode `200 OK` wird der erfolgreiche Abschluss des Sicherungsvorgangs angezeigt.

**Hinweis**:

- Der im Hauptteil der Anforderung angegebene **Container** **muss vorhanden sein**.
* **Versuchen Sie nicht, bei laufender Sicherung Dienstverwaltungsvorgänge durchzuführen**, z. B. SKU-Upgrade oder -Downgrade, Wechseln des Domänennamens usw. 
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
	    storageAccount : {storage account name for the backup},  
	    accessKey : {access key for the account},  
	    containerName : {backup container name},  
	    backupName : {backup blob name}  
	}'

Legen Sie für den `Content-Type`-Anforderungsheader den Wert `application/json` fest.

Die Wiederherstellung ist ein länger anhaltender Vorgang, der bis zum Abschluss bis zu 30 Minuten dauern kann. Falls die Anforderung erfolgreich war und der Wiederherstellungsvorgang eingeleitet wurde, erhalten Sie den Antwortstatuscode `202 Accepted`, zusammen mit einem `Location`-Header. Senden Sie GET-Anforderungen der URL im `Location`-Header, um den Status des Vorgangs zu ermitteln. Während der Wiederherstellung erhalten Sie weiterhin den Statuscode '202 Accepted‘. Mit dem Antwortcode `200 OK` wird der erfolgreiche Abschluss des Wiederherstellungsvorgangs angezeigt.

>[AZURE.IMPORTANT]**Die SKU** des wiederherzustellenden Diensts **muss übereinstimmen** mit der SKU des gesicherten Diensts.
>
>**Änderungen** an der Dienstkonfiguration (z. B. APIs, Richtlinien, Erscheinungsbild des Entwicklerportals), die während des Wiederherstellungsvorgangs vorgenommen werden, **könnten überschrieben werden**.

## Nächste Schritte
Sehen Sie sich die folgenden Microsoft-Blogs für zwei verschiedene Vorgehensweisen des Sicherungs- und Wiederherstellungsvorgangs an.

-	[Replicate Azure API Management Accounts](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) (in englischer Sprache) 
	-	Vielen Dank an Gisela für ihren Beitrag zu diesem Artikel.
-	[Azure API Management: Backing Up and Restoring Configuration](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx) (in englischer Sprache)
	-	Der Ansatz von Stuart entspricht nicht der offiziellen Anleitung, ist aber sehr interessant.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 

<!---HONumber=July15_HO4-->