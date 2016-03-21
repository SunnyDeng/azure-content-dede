<properties 
   pageTitle="Cache – ASP.NET-Sitzungszustandsanbieter"
   description="Erfahren Sie, wie Sie den ASP.NET-Sitzungszustand mit Azure Redis Cache speichern."
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="erikre"
   editor="tysonn" />
<tags 
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="03/04/2016"
   ms.author="sdanie" />

# ASP.NET-Sitzungszustandsanbieter für Azure Redis Cache

Azure Redis Cache stellt einen Sitzungszustandsanbieter bereit, mit dem Sie den Sitzungszustand in einem Cache anstatt im Arbeitsspeicher oder in einer SQL Server-Datenbank speichern können. Um den Sitzungszustandsanbieter zu verwenden, konfigurieren Sie zuerst den Cache, und konfigurieren Sie danach Ihre ASP.NET-Anwendung für den Cache mithilfe des Redis Cache-Sitzungszustands-NuGet-Pakets.

Im praktischen Einsatz von Cloud-Apps lässt sich das Speichern von Zustandsinformationen für eine Benutzersitzung oft nicht vermeiden. Allerdings beeinträchtigen einige Herangehensweisen die Leistung und Skalierbarkeit stärker als andere. Wenn Sie einen Sitzungszustand speichern müssen, ist es am besten, die Menge der Zustandsinformationen niedrig zu halten und sie in Cookies zu speichern. Wenn das nicht umsetzbar ist, besteht die nächstbeste Lösung darin, den ASP.NET-Sitzungszustand mit einem Anbieter für verteilten In-Memory-Cache zu verwenden. Die schlechteste Lösung im Hinblick auf Leistung und Skalierbarkeit wäre es, einen datenbankbasierten Sitzungszustandsanbieter zu verwenden. Dieses Thema bietet einen Leitfaden zum Verwenden des ASP.NET-Sitzungszustandsanbieters für Azure Redis Cache. Informationen zu anderen Optionen für den Sitzungszustand finden Sie unter [Optionen für den ASP.NET-Sitzungszustand](#aspnet-session-state-options).

## Speichern des ASP.NET-Sitzungszustands im Cache

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und wählen Sie **NuGet-Pakete verwalten** aus, um eine Clientanwendung in Visual Studio mithilfe des Redis Cache Sitzungszustands-NuGet-Pakets zu konfigurieren.

![Azure Redis Cache – NuGet-Pakete verwalten](./media/cache-asp.net-session-state-provider/IC729541.png)

Geben Sie **RedisSessionStateProvider** in das Textfeld „Online-Suche“ ein, wählen Sie in den Suchergebnissen das Paket aus, und klicken Sie auf „Installieren“.

>[AZURE.IMPORTANT] Wenn Sie das Clusteringfeature aus dem Premium-Tarif verwenden, müssen Sie [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 oder höher verwenden. Andernfalls wird eine Ausnahme ausgelöst. Dies ist eine unterbrechende Änderung. Weitere Informationen finden Sie unter [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details) (in englischer Sprache).

![Azure Redis Cache – Sitzungszustandsanbieter](./media/cache-asp.net-session-state-provider/IC751730.png)

Das Redis-Sitzungszustandsanbieter-NuGet-Paket steht in einer Abhängigkeitsbeziehung zum Paket „StackExchange.Redis.StrongName“. Wenn das Paket „StackExchange.Redis.StrongName“ nicht in Ihrem Projekt vorhanden ist, wird es installiert. Beachten Sie, dass es außer dem Paket „StackExchange.Redis.StrongName“ mit starkem Namen auch noch das Paket „StackExchange.Redis“ als Version ohne starken Namen gibt. Wenn in Ihrem Projekt die Version „StackExchange.Redis“ ohne starken Namen verwendet wird, müssen Sie diese vor oder nach dem Installieren des Redis-Sitzungszustandsanbieter-NuGet-Pakets deinstallieren. Andernfalls treten Namenskonflikte in Ihrem Projekt auf. Weitere Informationen über diese Pakete finden Sie unter [Konfigurieren der .NET-Cacheclients](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Das NuGet-Paket wird heruntergeladen und fügt die benötigten Assemblyverweise sowie den folgenden Abschnitt zu Ihrer Web.config-Datei hinzu. Dieser Abschnitt enthält die Konfiguration, die Ihre ASP.NET-Anwendung für die Verwendung des Cache-Sitzungszustandsanbieters braucht.

    <sessionStatemode="Custom" customProvider="MySessionStateStore">
        <providers>
        <!--
		<add name="MySessionStateStore"
     	  	host = "127.0.0.1" [String]
    		port = "" [number]
    		accessKey = "" [String]
    		ssl = "false" [true|false]
    		throwOnError = "true" [true|false]
    		retryTimeoutInMilliseconds = "0" [number]
    		databaseId = "0" [number]
    		applicationName = "" [String]
    		connectionTimeoutInMilliseconds = "5000" [number]
    		operationTimeoutInMilliseconds = "5000" [number]
		/>
        -->
		<add name="MySessionStateStore"type="Microsoft.Web.Redis.RedisSessionStateProvider"host="127.0.0.1"accessKey="" ssl="false"/>
        </providers>
    </sessionState>

Im kommentierten Bereich finden Sie Beispiele der Attribute sowie Beispieleinstellungen für jedes Attribut.

Konfigurieren Sie die Attribute mit den Werten aus dem Blatt „Cache“ im Microsoft Azure-Portal, und konfigurieren Sie die restlichen Werte wie gewünscht. Anweisungen zum Zugreifen auf die Cacheeigenschaften finden Sie unter [Konfigurieren von Redis-Cacheeinstellungen](cache-configure.md#configure-redis-cache-settings).

-	**host**: Geben Sie den Cacheendpunkt an.
-	**port**: Verwenden Sie abhängig von Ihren SSL-Einstellungen entweder den Nicht-SSL-Port oder den SSL-Port.
-	**accessKey**: Verwenden Sie den primären oder sekundären Schlüssel für Ihren Cache.
-	**ssl**: Geben Sie „true“ an, wenn Sie die Kommunikation zwischen Cache und Client mit SSL absichern möchten, andernfalls „false“. Achten Sie darauf, dass Sie den richtigen Port angeben.
	-	Der Nicht-SSL-Port ist für neue Caches standardmäßig deaktiviert. Geben Sie für diese Einstellung „true“ an, wenn Sie den SSL-Port verwenden möchten. Weitere Informationen zum Aktivieren des Nicht-SSL-Ports finden Sie im Abschnitt [Zugriffsports](cache-configure.md#access-ports) des Themas [Konfigurieren eines Caches](cache-configure.md).
-	**throwOnError**: Geben Sie „true“ an, wenn bei einem Fehler eine Ausnahme ausgelöst werden soll, oder „false“, wenn bei einem nicht ausgeführten Vorgang keine Meldung erfolgen soll. Sie können das Vorliegen von Fehlern feststellen, indem Sie die statische Eigenschaft „Microsoft.Web.Redis.RedisSessionStateProvider.LastException“ überprüfen. Der Standardwert ist „true“.
-	**retryTimeoutInMilliseconds**: Bei Fehlern werden Vorgänge für diese Zeitspanne wiederholt (angegeben in Millisekunden). Die erste Wiederholung erfolgt nach 20 Millisekunden. Danach wird die Wiederholung jede Sekunde durchgeführt, bis die in „retryTimeoutInMilliseconds“ angegebene Zeitspanne abgelaufen ist. Unmittelbar nach dieser Zeitspanne wird der Vorgang ein letztes Mal wiederholt. Wenn dann immer noch ein Fehler bei dem Vorgang auftritt, wird der aufrufenden Funktion eine Ausnahme zurückgegeben, abhängig von der Einstellung für „throwOnError“. Der Standardwert lautet 0. Das heißt: keine Wiederholungen.
-	**databaseId**: Gibt an, welche Datenbank für die Cacheausgabedaten verwendet werden soll. Wenn Sie hier nichts angeben, wird der Standardwert 0 verwendet.
-	**applicationName**: Schlüssel werden in Redis als `{<Application Name>_<Session ID>}_Data` gespeichert. Dadurch wird es möglich, dass mehrere Anwendungen denselben Schlüssel gemeinsam nutzen. Dieser Parameter ist optional, und wenn Sie ihn nicht angeben, wird der Standardwert verwendet.
-	**connectionTimeoutInMilliseconds**: Diese Einstellung ermöglicht Ihnen, die connectTimeout-Einstellung im StackExchange.Redis-Client zu überschreiben. Wenn Sie sie nicht angegeben, wird die connectTimeout-Standardeinstellung „5000“ verwendet. Weitere Informationen finden Sie unter [StackExchange.Redis-Konfigurationsmodell](http://go.microsoft.com/fwlink/?LinkId=398705).
-	**operationTimeoutInMilliseconds**: Diese Einstellung ermöglicht Ihnen, die syncTimeout-Einstellung im StackExchange.Redis-Client zu überschreiben. Wenn Sie sie nicht angegeben, wird die syncTimeout-Standardeinstellung „1000“ verwendet. Weitere Informationen finden Sie unter [StackExchange.Redis-Konfigurationsmodell](http://go.microsoft.com/fwlink/?LinkId=398705).
							
Weitere Informationen über diese Eigenschaften finden Sie im Blogbeitrag mit der ursprünglichen Ankündigung: [Announcing ASP.NET Session State Provider for Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (in englischer Sprache).

Vergessen Sie nicht, den Abschnitt mit dem als Standard verwendeten InProc-Sitzungszustandsanbieter in Ihrer web.config-Datei auszukommentieren.

    <!-- <sessionState mode="InProc" 
         customProvider="DefaultSessionProvider">
         <providers>
            <add name="DefaultSessionProvider" 
                  type="System.Web.Providers.DefaultSessionStateProvider, 
                        System.Web.Providers, Version=1.0.0.0, Culture=neutral, 
                        PublicKeyToken=31bf3856ad364e35" 
                  connectionStringName="DefaultConnection" />
          </providers>
    </sessionState> -->

Nach Abschluss dieser Schritte ist Ihre Anwendung für die Verwendung des Redis Cache-Sitzungszustandsanbieters konfiguriert. Wenn Sie den Sitzungszustand in Ihrer Anwendung verwenden, wird er in einer Azure Redis Cache-Instanz gespeichert.

>[AZURE.NOTE] Beachten Sie, dass die im Cache gespeicherten Daten serialisierbar sein müssen. Sie unterscheiden sich darin von den Daten, die im standardmäßigen In-Memory-ASP.NET-Sitzungszustandsanbieter gespeichert werden können. Wenn der Sitzungszustandsanbieter für Redis verwendet wird, müssen Sie darauf achten, dass die im Sitzungszustand gespeicherten Datentypen serialisierbar sind.

## Optionen für den ASP.NET-Sitzungszustand

- In-Memory-Sitzungszustandsanbieter: Dieser Anbieter speichert den Sitzungszustand im Arbeitsspeicher. Der Vorteil bei diesem Anbieter ist, dass er einfach und schnell ist. Wenn Sie einen In-Memory-Anbieter verwenden, können Sie jedoch Ihre Web-Apps nicht skalieren, denn dieser Anbieter ist nicht verteilt.

- SQL Server-Sitzungszustandsanbieter: Dieser Anbieter speichert den Sitzungszustand in SQL Server. Sie sollten diesen Anbieter verwenden, wenn der Sitzungszustand in einem permanenten Speicher beibehalten werden soll. Sie können Ihre Web-App skalieren, aber die Verwendung von SQL Server für den Sitzungszustand hat Auswirkungen auf die Leistung Ihrer Web-App.

- Verteilter In-Memory-Sitzungszustandsanbieter wie z. B. Redis Cache-Sitzungszustandsanbieter: Dieser Anbieter bietet Ihnen das Beste aus beiden Welten. Ihre Web-App kann einen einfachen, schnellen und skalierbaren Sitzungszustandsanbieter erhalten. Sie müssen dabei jedoch beachten, dass dieser Anbieter den Sitzungszustand in einem Cache speichert. Ihre App muss daher so ausgelegt sein, dass die besonderen Merkmale bei der Kommunikation mit einem verteilten In-Memory-Cache berücksichtigt werden, z. B. kurze Netzwerkausfälle. Bewährte Verfahren zum Verwenden des Caches finden Sie unter [Caching Guidance](https://github.com/mspnp/azure-guidance/blob/master/Caching.md) (in englischer Sprache, aus Microsoft Patterns & Practices, [Azure Cloud Application Design and Implementation Guidance](https://github.com/mspnp/azure-guidance)).

Weitere Information über das Thema Sitzungszustand und andere bewährte Verfahren finden Sie unter [Web Development Best Practices (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices) (in englischer Sprache).

## Nächste Schritte

Lesen Sie [ASP.NET-Ausgabecacheanbieter für Azure Redis Cache](cache-asp.net-output-cache-provider.md).

<!---HONumber=AcomDC_0309_2016-->