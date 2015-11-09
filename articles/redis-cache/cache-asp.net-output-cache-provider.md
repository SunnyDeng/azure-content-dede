<properties 
   pageTitle="Cache – ASP.NET-Ausgabecacheanbieter"
   description="Erfahren Sie, wie Sie die ASP.NET-Seitenausgabe mit Azure Redis Cache zwischenspeichern."
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="dwrede"
   editor="tysonn" />
<tags 
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="10/23/2015"
   ms.author="sdanie" />

# ASP.NET-Ausgabecacheanbieter für Azure Redis Cache

Der Redis-Ausgabecacheanbieter ist ein prozessunabhängiger Speichermechanismus für Ausgabecachedaten. Diese Daten sind für vollständige HTTP-Antworten bestimmt (Zwischenspeichern von Seitenausgaben). Der Anbieter wird zum neuen Erweiterungspunkt des Ausgabecacheanbieters hinzugefügt, der in ASP.NET 4 eingeführt wurde.

Zum Verwenden des Redis-Ausgabecacheanbieters konfigurieren Sie zunächst den Cache. Danach konfigurieren Sie Ihre ASP.NET-Anwendung mithilfe des Redis-Ausgabecacheanbieter-NuGet-Pakets. In diesem Thema wird erläutert, wie Sie Ihre Anwendung für die Verwendung des Redis-Ausgabecacheanbieters konfigurieren. Informationen über das Erstellen und Konfigurieren einer Azure Redis Cache-Instanz finden Sie unter [Erstellen eines Caches](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## Speichern der ASP.NET-Seitenausgabe im Cache

Um eine Clientanwendung in Visual Studio mithilfe des Redis-Ausgabecacheanbieter-NuGet-Pakets zu konfigurieren, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und wählen **NuGet-Pakete verwalten** aus.

![Azure Redis Cache – NuGet-Pakete verwalten](./media/cache-asp.net-output-cache-provider/IC729541.png)

Geben Sie **RedisOutputCacheProvider** in das Textfeld **Online-Suche** ein, wählen Sie in den Suchergebnissen das Paket aus, und klicken Sie auf **Installieren**.

![Azure Redis Cache – Ausgabecacheanbieter](./media/cache-asp.net-output-cache-provider/IC751727.jpg)

Das Redis-Ausgabecacheanbieter-NuGet-Paket steht in einer Abhängigkeitsbeziehung zum Paket „StackExchange.Redis.StrongName“. Wenn das Paket „StackExchange.Redis.StrongName“ nicht in Ihrem Projekt vorhanden ist, wird es installiert. Beachten Sie, dass es außer dem Paket „StackExchange.Redis.StrongName“ mit starkem Namen auch noch das Paket „StackExchange.Redis“ als Version ohne starken Namen gibt. Wenn in Ihrem Projekt die Version „StackExchange.Redis“ ohne starken Namen verwendet wird, müssen Sie diese vor oder nach dem Installieren des Redis-Ausgabecacheanbieter-NuGet-Pakets deinstallieren. Andernfalls treten Namenskonflikte in Ihrem Projekt auf. Weitere Informationen über diese Pakete finden Sie unter [Konfigurieren der .NET-Cacheclients](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Das NuGet-Paket wird heruntergeladen und fügt die erforderlichen Assemblyverweise sowie den folgenden Abschnitt zu Ihrer web.config-Datei hinzu. Dieser Abschnitt enthält die Konfiguration, die Ihre ASP.NET-Anwendung für die Verwendung des Redis-Ausgabecacheanbieters benötigt.

    <caching>
      <outputCachedefaultProvider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache"type="Microsoft.Web.Redis.RedisOutputCacheProvider"host="127.0.0.1"accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

Im kommentierten Bereich finden Sie Beispiele für die Attribute sowie Beispieleinstellungen für jedes Attribut.

Konfigurieren Sie die Attribute mit den Werten aus dem Blatt „Cache“ im Microsoft Azure-Portal, und konfigurieren Sie die restlichen Werte wie gewünscht. Anweisungen zum Zugreifen auf die Cacheeigenschaften finden Sie unter [Konfigurieren von Redis-Cacheeinstellungen](cache-configure.md#configure-redis-cache-settings).

-	**host**: Geben Sie den Cacheendpunkt an.
-	**port**: Verwenden Sie abhängig von Ihren SSL-Einstellungen entweder den Nicht-SSL-Port oder den SSL-Port.
-	**accessKey**: Verwenden Sie den primären oder sekundären Schlüssel für Ihren Cache.
-	**ssl**: Geben Sie „true“ an, wenn Sie die Kommunikation zwischen Cache und Client mit SSL absichern möchten, andernfalls „false“. Achten Sie darauf, dass Sie den richtigen Port angeben.
	-	Der Nicht-SSL-Port ist für neue Caches standardmäßig deaktiviert. Geben Sie für diese Einstellung „true“ an, wenn Sie den SSL-Port verwenden möchten. Weitere Informationen zum Aktivieren des Nicht-SSL-Ports finden Sie im Abschnitt [Zugriffsports](cache-configure.md#access-ports) des Themas [Konfigurieren eines Caches](cache-configure.md).
-	**databaseId**: Geben Sie an, welche Datenbank für die Cacheausgabedaten verwendet werden soll. Wenn Sie hier nichts angeben, wird der Standardwert 0 verwendet.
-	**applicationName**: Schlüssel werden in Redis als „<AppName>\_<SessionId>\_Data“ gespeichert. Dadurch wird es möglich, dass mehrere Anwendungen denselben Schlüssel gemeinsam nutzen. Dieser Parameter ist optional, und wenn Sie ihn nicht angeben, wird der Standardwert verwendet.
-	**connectionTimeoutInMilliseconds**: Diese Einstellung ermöglicht es Ihnen, die connectTimeout-Einstellung im StackExchange.Redis-Client zu überschreiben. Wenn Sie sie nicht angegeben, wird die connectTimeout-Standardeinstellung „5000“ verwendet. Weitere Informationen finden Sie unter [StackExchange.Redis-Konfigurationsmodell](http://go.microsoft.com/fwlink/?LinkId=398705).
-	**operationTimeoutInMilliseconds**: Diese Einstellung ermöglicht es Ihnen, die syncTimeout-Einstellung im StackExchange.Redis-Client zu überschreiben. Wenn Sie sie nicht angegeben, wird die syncTimeout-Standardeinstellung „1000“ verwendet. Weitere Informationen finden Sie unter [StackExchange.Redis-Konfigurationsmodell](http://go.microsoft.com/fwlink/?LinkId=398705).

Fügen Sie jeder Seite, für die Sie die Ausgabe zwischenspeichern möchten, eine OutputCache-Direktive hinzu.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

In diesem Beispiel verbleiben die zwischengespeicherten Daten 60 Sekunden lang im Cache. Für jede Parameterkombination wird eine andere Version der Seite zwischengespeichert. Weitere Informationen über die OutputCache-Direktive finden Sie unter [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Nach Abschluss dieser Schritte ist Ihre Anwendung für die Verwendung des Redis-Ausgabecacheanbieters konfiguriert.

## Nächste Schritte

Lesen Sie die Seite [ASP.NET-Sitzungszustandsanbieter für Azure Redis Cache](cache-asp.net-session-state-provider.md).

<!---HONumber=Nov15_HO1-->