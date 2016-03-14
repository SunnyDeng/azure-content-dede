<properties 
	pageTitle="Application Insights-Datenmodell" 
	description="Beschreibt die Eigenschaften, die aus dem fortlaufenden Export in JSON exportiert und als Filter verwendet wurden." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="awills"/>

# Application Insights-Exportdatenmodell

In dieser Tabelle sind die Eigenschaften der Telemetriedaten aufgelistet, die von [Application Insights](app-insights-overview.md) SDKs an das Portal gesendet werden. Sie sehen diese Eigenschaften in der Datenausgabe von [Fortlaufender Export](app-insights-export-telemetry.md). Sie werden darüber hinaus in den Eigenschaftsfiltern im [Metrik-Explorer](app-insights-metrics-explorer.md) und in der [Diagnosesuche](app-insights-diagnostic-search.md) angezeigt.

Es gibt mehrere [Beispiele](app-insights-export-telemetry.md#code-samples), die ihre Verwendung veranschaulichen.

„&lt;telemetryType&gt;“ im ersten Abschnitt ist ein Platzhalter für einen beliebigen Telemetrietypnamen: Ansicht, Anforderung usw.


## &lt;telemetryType&gt;

**<measurement>**

    KVPs <string, double> <telemetryType>.measurement      Max: 100
* 
    Ein Eigenschaftenbehälter für Schlüssel-Wert-Paare, der AppInsights-Telemetrieelementen das Hinzufügen benutzerdefinierter Metriken ermöglicht. 

    *Ableitung:* Messungsnamen haben die maximale Größe 100.

    *Standard:* Bei vorhandenem Schlüssel und fehlendem Wert gilt: Anzahl = 1, Wert = 0, Min/Max = 0.

**<property>**

    KVPs <string, string> <telemetryType>.properties      Max: 100
* 
    Ein Eigenschaftenbehälter für Schlüssel-Wert-Paare, der AppInsights-Telemetrieelementen das Hinzufügen benutzerdefinierter Eigenschaften ermöglicht. Der Entwickler kann eine Schlüssel-Wert-Paarliste bereitstellen, die einem Telemetrieelement zugeordnet ist. Jeder Schlüssel wird nachverfolgt. Maximal 200 eindeutige Schlüssel können pro AppInsights Ikey (App) bereitgestellt werden. Ein Schlüssel kann maximal 100 Zeichen lang sein. Alle Werte werden als "string" behandelt, und eine maximale Größe von 1000 Zeichen kann angegeben werden. Jede Eigenschaft wird anfänglich als Dimension klassifiziert, wodurch Segmentierungsfeatures basierend auf dem festgelegten Wert jeder Eigenschaft ermöglicht werden. Jeder festgelegte Wert wird pro Eigenschaftsschlüssel auf seine Kardinalität nachverfolgt. Wenn die Kardinalität eines Schlüssels 100 eindeutige Werte überschreitet, wird die Eigenschaft als Attribut klassifiziert. Ein Attribut kann durchsucht werden, jedoch nicht das Ziel der Segmentierung sein (Aggregation oder "Group by"). 

    *Ableitung:* Eigenschaftsnamen haben die maximale Größe 100, Eigenschaftswerte die maximale Größe 1024.

    *Standard:* Bei vorhandenem Schlüssel und fehlendem Wert ist der Wert NULL.

**count**

    long <telemetryType>.count      
* 
    Die Anzahl des Telemetrieelements.   

    *Ableitung:* Im Fall von NULL ist die Anzahl = 1.

**duration**

    simpleMetric <telemetryType>.duration   ticks   
* 
    Die Dauer des Telemetrieelements. Bei Anforderungen ist dies die Ausführungszeit der Anforderung. 

    *Standard:* R1: Dieses Feld ist für die Ansicht optional.

**message**

    string <telemetrytype>.message      Max: 10240
* 
    Eine Textnachricht zum Telemetrietyp. Diese Zeichenfolge ist für die Volltextsuche verfügbar. 

**name**

    string <telemetrytype>.name      Max: 250
* 
    Der Name des Telemetrieelements. Dieser Name ist bei mehreren Instanzen nicht eindeutig und stellt eine Gruppierung von Telemetrietypen dar. Für Ansichten ist dies standardmäßig auf "URLData.base" festgelegt. Für Ereignisse ist dies eine vom Entwickler bereitgestellte Bezeichnung. Für Anforderungen ist dies eine lesbare Form der Anforderung, z. B. Controller\\Aktion. 

    *Beispiele*<br/> Ansichtsnamen:<br/>70-486 Prüfungsfrage 1<br/>Info – Meine ASP.NET-Anwendung<br/><br/>Beispielanforderungsnamen:<br/>POST /Components/WebHandlers/ItemCompare.ashx<br/>GET /signalr/poll<br/>GET /signalr/negotiate

**severity**

    string <telemetryType>.severity      Max: 100
* 
    Der Schweregrad des Telemetrieelements. Dies gilt für die Telemetrieelemente "Ablaufverfolgung" und "Ausnahme". 

**url**

    string <telemetrytype>.url      Max: 2048
* 
    Die URL des Seitenaufrufs, Ereignisses, der Anforderung oder RDD. Die vollständige URL wird für Volltextsuche und Export unterstützt. Dieses Feld kann eine sehr hohe Kardinalität haben und ist ein Attribut. Es wird in eine Gruppe von "urlData"-Datenelementen analysiert, die für Aggregationen im Metrik-Explorer verwendet werden können. 

    *Standard:* R2: Für „remotedepencyType“ ist dieses Feld erforderlich, falls „dependencyType = HTTP“.<br/> Für „clientperformanceType“ ist dieses Feld erforderlich.

    *Beispiele*<br/> https://icecream.contoso.com/main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>http://fabrikam-oats.azurewebsites.net/index.htm

**urlData.base**

    string <telemetrytype>.urldata.base      Max: 2048
* 
    Ein Teil des URL-Datenelements ohne Host- und Abfrageparameter. Dies ist der Stamm-URI. Dieser Wert kann für Segmentierung/Aggregation verwendet werden. 

    *Ableitung:* Siehe Anhang zur URL-Transformation

    *Beispiele*<br/> /main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>/default.aspx<br/>/Patients/Search/<br/>

**urldata.hashTag**

    string <telemetrytype>.urldata.hashtag      Max: 100
* 
    Der Text des Hashtags des URL-Datenelements. 

    *Ableitung:* Siehe Anhang zur URL-Transformation

**urlData.host**

    string <telemetrytype>.urldata.host      Max: 200
* 
    Der Host des URL-Datenelements. Wenn das URL-Datenelement ein lokaler URI ist, wird es leer dargestellt. 

    *Ableitung:* Siehe Anhang zur URL-Transformation

    *Beispiele*<br/> www.fabrikam.com<br/>www.contoso.com<br/>bretwpc711.azurewebsites.net<br/>



## Verfügbarkeit

**availability**

    simpleMetric availability.availability      
* 
    Ein Indikator für den Erfolg des Verfügbarkeitstests. 

**dataSize**

    simpleMetric availability.datasize      
* 
    Die Größe der Textnachricht "&lt;telemetry&gt;.message". 

**result**

    enum (pass/fail) availability.result      
* 
    Ein Zeiger (HTTP-Aufruf) zum Abrufen der detaillierten Informationen zum Verfügbarkeitwebtest. 

**runLocation**

    string availability.runlocation      Max: 100
* 
    Der Ausführungsort des Verfügbarkeitstests. 

**testName**

    string availability.testname      Max: 1024
* 
    Der Name des Verfügbarkeitstests. 

**testRunId**

    string availability.testrunid      Max: 100
* 
    Eine eindeutige ID der Instanz der Ausführung des Verfügbarkeitstests. 

**testTimeStamp**

    datetime availability.testtimestamp      
* 
    Der Zeitstempel des Anfangs der Instanz der Ausführung des Verfügbarkeitstests. 


## basicexception

**assembly**

    string basicexception.assembly      Max: 100
**exceptionGroup**

    string basicException.exceptionGroup      
**exceptionType**

    string basicexception.exceptiontype      Max: 100
**failedUserCodeAssembly**

    string basicException.failedUserCodeAssembly      
**failedUserCodeMethod**

    string basicException.failedUserCodeMethod      
**handledAt**

    string basicexception.handledat      Max: 100
**innerMostExceptionMessage**

    string basicException.innermostExceptionMessage      
**innerMostExceptionThrownAtAssembly**

    string basicException.innermostExceptionThrownAtAssembly      
**innerMostExceptionThrownAtMethod**

    string basicException.innermostExceptionThrownAtMethod      
**innerMostExceptionType**

    string basicException.innermostExceptionType      
**method**

    string basicexception.method      Max: 100
**outerMostExceptionMessage**

    string basicException.outerExceptionMessage      
**outerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtAssembly      
**outerMostExceptionTrownAtMethod**

    string basicException.outerExceptionThrownAtMethod      
**outerMostExceptionType**

    string basicException.outerExceptionType      
**problemid**

    string basicexception.problemid      Max: 100
* 
    *Ableitung:* Siehe Anhang zur Aufruflistenanalyse 

**Exceptions.Assembly**

    string basicexception.exceptions.assembly      Max: 100
**Exceptions.fileName**

    string basicexception.exceptions.filename      Max: 100
**Exceptions.hasFullStack**

    boolean basicexception.exceptions.hasfullstack      
**Exceptions.Level**

    string basicexception.exceptions.level      Max: 100
**Exceptions.Line**

    long basicexception.exceptions.line      
**exceptions.message**

    string basicexception.exceptions.message      Max: 10240
**Exceptions.Method**

    string basicexception.exceptions.method      Max: 100
**Exceptions.outerId**

    long basicexception.exceptions.outerid      
**Exceptions.parsedStack**

    List<StackFrame> basicexception.exceptions.parsedstack      
**Exceptions.Stack**

    string basicexception.exceptions.stack      Max: 10240
**Exceptions.typeName**

    string basicexception.exceptions.typename      Max: 100
**parsedStackAssembly**

    string basicException.parsedStack.assembly      
**parsedStackFilename**

    string basicException.parsedStack.fileName      
**parsedStackLevel**

    string basicException.parsedStack.level      
**parsedStackLine**

    string basicException.parsedStack.line      
**paseStackMethod**

    string basicException.parsedStack.method      

## clientperformance

**domProcessing**

    simpleMetric clientperformance.domprocessing   ms   
* 
    Ein Teil der "perTotal"-Zeit. Dieser Teilt stellt die Zeit dar, die die Anwendung benötigt hat, um die Antwort zu verarbeiten. Für einen Webclient ist dies die DOM-Verarbeitungszeit (Document Objekt Model). Diese Angabe wird mithilfe der "perfTiming"-API in modernen Browsern erfasst. 

**networkConnect**

    simpleMetric clientperformance.networkconnect   ms   
* 
    Ein Teil der "perTotal"-Zeit. Dieser Teilt stellt die Zeit dar, die die Anwendung benötigt hat, um die Netzwerkverbindung herzustellen. Diese Angabe wird mithilfe der "perfTiming"-API in modernen Browsern erfasst. 

**perfTotal**

    simpleMetric clientperformance.perftotal   ms   
* 
    Die Gesamtzeit zum Laden der Ansicht. Bei Webclients entspricht dies der "Seitenladezeit". Diese Angabe wird mithilfe der "perfTiming"-API in modernen Browsern erfasst. 

**receiveResponse**

    simpleMetric clientperformance.receiveresponse   ms   
* 
    Ein Teil der "perTotal"-Zeit. Dieser Teil stellt die Zeit dar, die die Anwendung benötigt hat, um die Anforderungsantwort zu empfangen. Diese Angabe wird mithilfe der "perfTiming"-API in modernen Browsern erfasst. 

**sendRequest**

    simpleMetric clientperformance.sendrequest   ms   
* 
    Ein Teil der "perTotal"-Zeit. Dieser Teil stellt die Zeit dar, die die Anwendung benötigt hat, um die Anforderung zum Server zu senden. Diese Angabe wird mithilfe der "perfTiming"-API in modernen Browsern erfasst. 


## context

**applicationBuild**

    string context.application.build      Max: 100
* 
    Die Anweldungsbuildnummer der App. 

**applicationVersion**

    string context.application.version      Max: 100
* 
    Die Anwendungsversion der Clientanwendung. Nicht verfügbar, wenn auf "Unbekannt" festgelegt. 

    *Beispiele*<br/> 2015.5.21.3<br/>NokiaMailBye\_CD\_20150227.4

**telemetryType**

    string context.billing.telemetrytype      Max: 100
* 
    Dies stellt den Telemetrietyp für einen Abrechnungsbeleg dar und wird intern zur Segmentierung abrechenbarer Telemetrieelemente für eine App verwendet. 

**dataId**

    string context.data.id      Max: 100
* 
    Der eindeutige Bezeichner des Telemetrieelements. Wird dem Endpunkt der Datenerfassung zugeordnet. 

    *Ableitung:* Generierte UUID4

    *Beispiele*<br/> edc6eaf3-3459-46a0-bb81-bedc24913864

**eventTime**

    datetime context.data.eventtime      
* 
    Die Uhrzeit des aufgezeichneten Telemetrieereignisses in UTC. Diese wird in der Regel auf dem Client aufgefüllt. Wenn dieses Feld nicht vorhanden ist, wird es am Endpunkt der Datenerfassung aufgefüllt. Das Format des Felds ist JJJJ-MM-DDTHH:MM:SS.sssZ.    

    *Beispiele*<br/> 2015-05-20T04:00:46.8338283Z

**samplingRate**

    string context.data.samplerate      Max: 100
* 
    Die Stichprobenrate des Datengenerators (SDK). Ein anderer Wert als 1 gibt an, dass die Metriken, die diesem Telemetrieelement zugeordnet sind, Stichprobenwerte darstellen. Daher ergibt eine Stichprobenrate von 0,05 ein Telemetrieelement, das die Anzahl 20 darstellt. 

**browser**

    string context.device.browser      Max: 100
* 
    Der Browser des Clients. 

    *Standard:* Falls NULL, erfolgt die Festlegung basierend auf der Benutzer-Agent-Verarbeitung. Siehe den Anhang zur Benutzer-Agent-Analyse.

    *Beispiele*<br/> Opera<br/>Mobile Safari<br/>Ovi Browser<br/>Chrome<br/>Firefox<br/>Internet Explorer

**browserVersion**

    string context.device.browserversion      Max: 100
* 
    Die Browserversion des Clients. 

    *Standard:* Falls NULL, erfolgt die Festlegung basierend auf der Benutzer-Agent-Verarbeitung. Siehe den Anhang zur Benutzer-Agent-Analyse.

    *Beispiele*<br/> Opera 12.17<br/>Mobile Safari 8.0<br/>Ovi Browser 5.5<br/>Chrome 37.0<br/>Firefox 21.0<br/>Internet Explorer 7.0

**deploymentId**

    string context.device.deploymentid      Max: 100
* 
    Die Bereitstellungs-ID des Servers. 


**deviceName**

    string context.device.name      Max: 100
* 
    Der Name des Geräts, auf dem die App ausgeführt wird. 

**locale**

    string context.device.locale      Max: 100
* 
    Das Gebietsschema der App auf dem Client. Falls nicht explizit im Telemetrieelement enthalten, wird sie durch die Verarbeitung des Benutzer-Agent-Felds bestimmt. 

    *Beispiele*<br/> ru<br/>de-DE<br/>de-DE<br/>unbekannt

**machineName**

    string context.device.vmname      Max: 100
* 
    Der Computername des Servers. Für virtualisierte Computer entspricht dieses Datenelement dem zugrunde liegenden Host. Für physische Computer ist dies der Name des Computers. 


**operatingSystem**

    string context.device.os      Max: 100
* 
    Das Betriebssystem des Clients. 

    *Standard:* Falls NULL, erfolgt die Festlegung basierend auf der Benutzer-Agent-Verarbeitung. Siehe den Anhang zur Benutzer-Agent-Analyse.

    *Beispiele*<br/> Windows<br/>iOS iPad<br/>Nokia

**operatingSystemVersion**

    string context.device.osversion      Max: 100
* 
    Die Betriebssystemversion des Clients. 

    *Standard:* Falls NULL, erfolgt die Festlegung basierend auf der Benutzer-Agent-Verarbeitung. Siehe den Anhang zur Benutzer-Agent-Analyse.

    *Beispiele*<br/> Windows XP<br/>iOS 8.3<br/>Nokia Series 40<br/>Windows 7<br/>Windows 8

**roleInstance**

    string context.device.roleinstance      Max: 100
* 
    Die Servercompute-Instanz. In einem Cloud-/virtualisierten Szenario ist dies der virtuelle Name der Compute-Instanz. Bei einer physischen Compute-Instanz ist dies der Name des Computers. Für Azure Cloud-Dienste muss dies standardmäßig der Instanzname der PaaS-Rolle oder der Name des virtuellen IaaS-Computers sein.  

**roleName**

    string context.device.rolename      Max: 100
* 
    Ein logischer Namespace zum Gruppieren von Servercompute-Instanzen. Für in Azure gehostete Dienste müssen PaaS-Rollen standardmäßig auf den PaaS-Rollennamen festgelegt sein. IaaS-Rollen müssen standardmäßig auf den Namen des virtuellen Computers festgelegt sein.  

**screenHeight**

    string context.device.screenresolution.height      Max: 100
* 
    Die Bildschirmhöhe der App auf der Clienthardware zum Zeitpunkt der Aufzeichnung des Telemetrieelements. Falls nicht explizit angegeben, wird der Wert durch eine Transformation des Datenelements "screenresolution" ermittelt. 

    *Ableitung:* Aus Analyse von „context.device.screenresolution“, sofern vorhanden.

    *Beispiele*<br/> 360<br/>1280<br/>1920

**screenResolution**

    string context.device.screenresolution      Max: 100
* 
    Die Auflösung des Bildschirms zum Zeitpunkt, an dem das Telemetrieelement von der App aufgezeichnet wurde. Diese kann im Verlauf einer Sitzung zwischen Hochformat und Querformat wechseln. Wenn dieses Attribut auf Sitzungsebene genutzt wird, ist dies die erste Bildschirmauflösung, die erfasst wurde, um die vollständige Sitzung darzustellen. 

    *Beispiele*<br/> Bildschirmauflösung Höhe Breite<br/>360 x 640<br/>1280 x 800<br/>1920 x 1080

**screenWidth**

    string context.device.screenresolution.width      Max: 100
* 
    Die Bildschirmbreite der App auf der Clienthardware zum Zeitpunkt der Aufzeichnung des Telemetrieelements. Falls nicht explizit angegeben, wird der Wert durch eine Transformation des Datenelements "screenresolution" ermittelt. 

    *Ableitung:* Aus Analyse von „context.device.screenresolution“, sofern vorhanden.

    *Beispiele*<br/> 640<br/>800<br/>1080


**aiAgentVersion**

    string context.internal.agentversion      Max: 100
* 
    Das interne Datenelement, das die SDK-Agent-Version darstellt, die das Telemetrieelement erzeugt hat. 

**city**

    string context.location.city      Max: 100
* 
    Der Ort der App-Sitzung. Kann direkt für das Telemetrieelement angegeben werden. Falls nicht vorhanden, wird es basierend auf der IPv4-Adresse für das Telemetrieelement aufgefüllt. Ist keine IPv4-Adresse angegeben, bleibt das Feld leer. 

    *Beispiele*<br/> Minsk<br/>Haarlem

**clientIpAddress**

    ipv4 context.location.clientip      
* 
    Die IPv4-Adresse des Clients im Format xxx.xxx.xxx.xxx.

     Das letzte Oktett wird aus Datenschutzgründen immer auf 0 festgelegt.

    *Standard:* Falls NULL, erfolgt die Festlegung auf die HTTP-IP-Adresse, die am Endpunkt der Datenerfassung erfasst wurde.

    *Beispiele:*<br/> 186.123.63.0<br/>123.203.131.0

**continent**

    string context.location.continent      Max: 100
* 
    Der Kontinent der App-Sitzung. Kann direkt für das Telemetrieelement angegeben werden. Falls nicht vorhanden, wird es basierend auf der IPv4-Adresse für das Telemetrieelement aufgefüllt. Ist keine IPv4-Adresse angegeben, bleibt das Feld leer. 

    *Beispiele*<br/> Europa<br/>Nordamerika

**country**

    string context.location.country      Max: 100
* 
    Das Land der App-Sitzung. Kann direkt für das Telemetrieelement angegeben werden. Falls nicht vorhanden, wird es basierend auf der IPv4-Adresse für das Telemetrieelement aufgefüllt. Ist keine IPv4-Adresse angegeben, bleibt das Feld leer. 

    *Beispiele*<br/> Belarus<br/>Niederlande<br/>Deutschland


**state**

    string context.location.province      Max: 100
* 
    Bundesland/Kanton der App-Sitzung. Kann direkt für das Telemetrieelement angegeben werden. Falls nicht vorhanden, wird es basierend auf der IPv4-Adresse für das Telemetrieelement aufgefüllt. Ist keine IPv4-Adresse angegeben, bleibt das Feld leer. 

    *Beispiele*<br/> Minsk<br/>Oregon<br/>Zentralserbien<br/>Provincia di Oristano

**operationId**

    string context.operation.id      Max: 100
* 
    Die "operation.id" ist eine Korrelations-ID, die für verschiedene Telemetrieelemente verwendet werden kann. Beispielsweise kann eine Anforderungs-ID in der "operation.id" mit allen zugehörigen Telemetrieelementen aufgefüllt werden, wodurch eine Korrelation für Telemetrieelemente ermöglicht wird, wie z. B. RDD, Ausnahme, Ereignisse usw.  

**operationName**

    string context.operation.name      Max: 100
* 
    Ein für Menschen lesbarer Vorgangsname. Siehe "operation.Id". Dies ermöglicht eine Gruppierung ähnlicher Vorgangs-IDs wie z. B. "Einkauf abgeschlossen".   

**operationParentId**

     context.operation.parentid      
* 
    Eine übergeordnete ID von "operation.id", die eine Schachtelung von IDs für die Korrelation von Telemetriedaten ermöglicht.   

**syntheticSource**

    string context.data.syntheticsource      Max: 100
* 
    Falls "issynthetic = true", stellt dieses Datenelement die Quelle synthetischer Daten dar. 

    *Standard:* Falls NULL, wird der Benutzer-Agent auf bekannte synthetische Datenquellen (Webcrawler usw.) überprüft. Auf dieser Grundlage kann die Quelle festgelegt werden.

**syntheticTransaction**

    boolean context.data.issynthetic      
* 
    Ein Indikator, dass das Telemetrieelement mittels synthetischer Tests und nicht durch reale Benutzeraktivität generiert wurde. 

    *Standard:* Falls NULL, wird der Benutzer-Agent mit einer Liste synthetischer Agents abgeglichen. Wird eine Übereinstimmung gefunden, wird der Wert auf „true“ festgelegt.<br/>Wenn der Benutzer-Agent NULL ist, wird der Wert auf „false“ festgelegt.

**session.Id**

    String context.session.id      Max: 100
* 
    Ein eindeutiger Bezeichner für reale Benutzerinteraktion mit einer Anwendung. Diese Interaktion ist eine "Sitzung". Alle Telemetriedaten, die von der Anwendung unter dem gleichen iKey generiert werden, müssen diesen eindeutigen Bezeichner enthalten. <br/><br/>Eine Sitzung wird durch aufeinanderfolgende Ereignisse innerhalb der gleichen Benutzerinteraktion definiert. Ein Zeitraum von mehr als 30 Minuten ohne Telemetrieereignis signalisiert das Ende einer Sitzung.   

    *Standard:* Ungültig für „MetricType“ und „BillingType“.

    *Beispiele*<br/> CFFC8B21-9828-4F56-AD7C-B6B5AC26B133

**accountAcquisitionDate**

    datetime context.user.accountAcquisitionDate  
    
**anonUserId**

    string context.user.anonId      Max: 100
* 
    Ein eindeutiger Bezeichner, der einen anonymen Benutzer innerhalb der App definiert. Wenn ein SDK verwendet wird, wird diese Angabe automatisch generiert und dauerhaft auf dem Client gespeichert. Es erfolgt allerdings keine Unterscheidung zwischen realen Benutzern, wenn ein Gerät unter der gleichen Anmeldung gemeinsam genutzt wird. Es wird zwischen realen Benutzern unterschieden, wenn verschiedene Benutzernamen verwendet werden und das Betriebssystem Profile unterstützt. Dies ist der beste Proxy für eindeutige Benutzer, wenn keine authentifizierte Umgebung vorliegt. 

    *Beispiele*<br/> f23854a1b01c4b1fa79fa2d9a5768526

**anonymousUserAcquisitionDate**

    datetime context.user.anonAcquisitionDate      

**authenticatedUserAcquisitionDate**

    datetime context.user.authAcquisitionDate     
 
**authUserId**

    string context.user.authId      Max: 100
* 
    Ein eindeutiger Bezeichner, der einen authentifizierten Benutzer innerhalb der App definiert. Wird vom Entwickler angegeben. Der Vorteil eines authentifizierten Benutzers ist, dass der Bezeichner bei Verwenden mehrerer Geräte innerhalb der App weiterhin seine Eindeutigkeit behält. 

**storeRegion**

    string context.user.storeregion      Max: 100
* 
    Die Region des Stores, in dem die App gekauft wurde. 

**userAcountId**

    string context.user.accountId      Max: 100
* 
    Ein eindeutiger Bezeichner, der ein Konto innerhalb der App definiert. Wird vom Entwickler angegeben. 

### Benutzerdefinierte Metriken

    context.custom.metrics.<metric-name>

      double value
      double count
      double min
      double max
      double stdDev
      double sampledValue
      double sum


## remotedependency

**async**

    boolean remotedependency.async      
* 
    Ein Indikator, wenn der Remoteabhängigkeitsaufruf asynchron war. 

**commandName**

    string remotedependency.commandname      Max: 2048
* 
    Der SQL-Befehlsname der Remoteabhängigkeit, wenn die Remoteabhängigkeit "SQL" ist. 

**dependencyTypeName**

    string remotedependency.dependencytypename      Max: 2048
* 
    Der Typname der Remoteabhängigkeit. 

**remoteDependencyName**

    string remotedependency.remotedependencyname      Max: 2048
* 
    Der Name der Remoteabhängigkeit. 

    *Ableitung*: Standardisierung zu "&lt;telemetryType.name&gt;"

**type**

    string remotedependency.remotedependencytype      Max: 100
* 
    Der Typ der Remoteabhängigkeit. Die unterstützten Typen sind SQL, AZURE-Ressourcen (Speicher, Warteschlangen usw.) und HTTP. 

**success**

    boolean remotedependency.success      
* 
    Ein Indikator, wenn der Remoteabhängigkeitsaufruf erfolgreich bzw. nicht erfolgreich war. 


## request

**hasdetaileddata**

    boolean request.hasdetaileddata      
* 
    Ein Indikator, der angibt, ob basierend auf "operation.id" verwandte Telemetrieelemente vorhanden sind. 

**httpMethod**

    string request.httpmethod      Max: 100
* 
    Die für die Anforderung verwendete HTTP-Methode.   

**id**

    string request.id      Max: 100
* 
    Ein eindeutiger Bezeichner einer Anforderung, der vom SDK generiert wird. Diese ID kann von der "operation.id"-Eigenschaft weiter aufgefüllt werden, um Telemetrieelemente zu korrelieren, die aus der gleichen Anforderung resultieren. 

**responseCode**

    long request.responsecode      
* 
    Der Antwortcode einer Anforderung. 

**success**

    boolean request.success      
* 
    Ein Indikator, ob die Anforderung erfolgreich ist. Ein Antwortcode in den 200-ern wird als Erfolg gewertet. 

    *Standard*: Bei NULL auf "true" festgelegt.


## sessionmetric

**anonymousUserDurationSinceLastVisit**

    Long sessionmetric.anonymoususerdurationsincelastvisit      
* 
    Die Zeit seit dem letzten Besuch dieses Bezeichners für anonyme Benutzer. Beim ersten Besuch ist dieser Wert leer. Bei jedem nachfolgenden Besuch ist dies die Zeit zwischen Besuchen in Tagen. Der Wert 3 bedeutet, dass zwischen der vorherigen Sitzungsinstanz und dieser Sitzungsinstanz drei Tagen liegen. 

**anonymousUserSessionCount**

    Long sessionmetric.anonymoususersessioncount      
* 
    Die Anzahl der Besuche des anonymen Benutzers. Dies ist ein zunehmender Zähler für den gesamten Verlauf von Sitzungen für diesen eindeutigen Bezeichner für anonyme Benutzer. Jede Sitzung mit diesem Bezeichner erhöht den Zähler. Dieser Zähler wird gelöscht, wenn die Benutzer-ID innerhalb von 30 Tagen nicht vorkommt. Der Zähler wird zurückgesetzt, und beim nächsten Besuch des Benutzerbezeichners wird dieser als neuer Benutzer eingestuft. 

**authenticatedAccountDurationSinceLastVisit**

    Long sessionmetric.authenticatedaccountdurationsincelastvisit      
* 
    Die Zeit seit dem letzten Besuch dieses Kontobezeichners. Beim ersten Besuch ist dieser Wert leer. Bei jedem nachfolgenden Besuch ist dies die Zeit zwischen Besuchen in Tagen. Der Wert 3 bedeutet, dass zwischen der vorherigen Sitzungsinstanz und dieser Sitzungsinstanz drei Tagen liegen. 

**authenticatedAccountSessionCount**

    Long sessionmetric.authenticatedaccountsessioncount      
* 
    Die Anzahl der Besuch des authentifizierten Kontobezeichners. Dies ist ein zunehmender Zähler für den gesamten Verlauf von Sitzungen für diesen eindeutigen Kontobezeichner. Jede Sitzung mit diesem Bezeichner erhöht den Zähler. Dieser Zähler wird gelöscht, wenn die Benutzer-ID innerhalb von 30 Tagen nicht vorkommt. Der Zähler wird zurückgesetzt, und beim nächsten Besuch des Benutzerbezeichners wird dieser als neuer Benutzer eingestuft. 

**authenticatedUserDurationSinceLastVisit**

    Long sessionmetric.authenticateduserdurationsincelastvisit      
* 
    Die Zeit seit dem letzten Besuch dieses Bezeichners für authentifizierte Benutzer. Beim ersten Besuch ist dieser Wert leer. Bei jedem nachfolgenden Besuch ist dies die Zeit zwischen Besuchen in Tagen. Der Wert 3 bedeutet, dass zwischen der vorherigen Sitzungsinstanz und dieser Sitzungsinstanz drei Tagen liegen. 

**authenticatedUserSessionCount**

    Long sessionmetric.authenticatedusersessioncount      
* 
    Die Anzahl der Besuch des authentifizierten Benutzerbezeichners. Dies ist ein zunehmender Zähler für den gesamten Verlauf von Sitzungen für diesen eindeutigen Bezeichner für authentifizierte Benutzer. Jede Sitzung mit diesem Bezeichner erhöht den Zähler. Dieser Zähler wird gelöscht, wenn die Benutzer-ID innerhalb von 30 Tagen nicht vorkommt. Der Zähler wird zurückgesetzt, und beim nächsten Besuch des Benutzerbezeichners wird dieser als neuer Benutzer eingestuft. 

**crashCount**

    Long sessionmetric.crashcount      
* 
    Die Anzahl der Abstürze, die während dieser Sitzungsinstanz aufgetreten sind. 

**duration**

    timespan sessionmetric.duration      
* 
    Die Dauer einer Sitzungsinstanz. 

**entryEvent**

    string sessionmetric.entryevent      Max: 200
* 
    Das erste Ereignis der Sitzung. Dieses wird aus "event.name" erstellt und ist für die Segmentierung/Aggregation für "sessionMetric"-Metriken verfügbar. 

    *Ableitung*: Erstellt aus "event.name".

**entryUrl**

    string sessionmetric.entryurl      Max: 2048
* 
    Die erste URL der Sitzung. Diese wird aus "urlData.base" erstellt und ist als Segmentierung/Aggregation für "sessionMetric"-Metriken verfügbar. 

    *Ableitung*: Erstellt aus "&lt;telemetryType&gt;.Url"

**eventCount**

    Long sessionmetric.eventcount      
* 
    Die Anzahl der Ereignisse, die während dieser Sitzungsinstanz aufgetreten sind. 

**exceptionCount**

    Long sessionmetric.exceptioncount      
* 
    Die Anzahl der Ausnahmen, die während dieser Sitzungsinstanz aufgetreten sind. 

**exitEvent**

    string sessionmetric.exitevent      Max: 200
* 
    Das letzte Ereignis der Sitzung. Dieses wird aus "event.name" erstellt und ist für die Segmentierung/Aggregation für "sessionMetric"-Metriken verfügbar. 

    *Ableitung*: Erstellt aus "event.name".

**exitUrl**

    string sessionmetric.exiturl      Max: 2048
* 
    Das letzte URL der Sitzung. Diese wird aus "urlData.base" erstellt und ist als Segmentierung/Aggregation für "sessionMetric"-Metriken verfügbar. 

    *Ableitung*: Erstellt aus "&lt;telemetryType&gt;.Url"

**pageBounceCount**

    boolean sessionmetric.pagebouncecount      
* 
    Die Anzahl der Bounce-Sitzungen, die dieses "sessionMetric"-Telemetrieelement darstellt. Eine Bounce-Sitzung ist eine Sitzung, die basierend auf einem Telemetrieelement mit einzelner Ansicht erstellt wird. 

    *Ableitung*: Wenn "sessionMetric.viewCount + sessionMetric.requestCount = 1", dann 1, andernfalls 0.

**pageCount**

    Long sessionmetric.pagecount      
* 
    Die Anzahl der Seitenaufrufe, die während dieser Sitzungsinstanz aufgetreten sind. 

**requestCount**

    Long sessionmetric.requestcount      
* 
    Die Anzahl der Anforderungen, die während dieser Sitzungsinstanz aufgetreten sind. 

**sessionCount**

    Long sessionmetric.sessioncount      
* 
    Die Anzahl der Sitzungen, die dieses "sessionMetric"-Telemetrieelement darstellt. 


## Ablaufverfolgung

**context**

    string trace.context      Max: 100
* 
    Der Kontext der App zum Zeitpunkt der Ablaufverfolgung/Ausnahme. 

**exception**

    string trace.exception      Max: 10240
* 
    Die Ausnahme, die der Ablaufverfolgung des Telemetrieselements zugeordnet ist. 

**excerpt**

    string trace.excerpt      Max: 100
* 
    Eine kurze Textnachricht zur Ablaufverfolgung des Telemetrieselements. 

**formatMessage**

    string trace.formatmessage      Max: 100
* 
    Die Formatnachricht der Ablaufverfolgung des Telemetrieselements. 

**formatProvider**

    string trace.formatprovider      Max: 100
* 
    Der Formatanbieter der Ablaufverfolgung des Telemetrieselements. 

**hasStackTrace**

    boolean trace.hasstacktrace      
* 
    Der Indikator, wenn die Ablaufverfolgung des Telemetrieelements eine Stapelüberwachung enthält. 

**level**

    string trace.level      Max: 100
* 
    Die Ebene der Ablaufverfolgungsnachricht. 

**loggerName**

    string trace.loggername      Max: 100
* 
    Der Name des Protokollierungstools der Ablaufverfolgung. 

**loggerShortName**

    string trace.loggershortname      Max: 100
* 
    Der Kurzname des Protokollierungstools. 

**message**

    string trace.message      Max: 10240
* 
    Die vollständige Textnachricht zur Ablaufverfolgung des Telemetrieelements. 

**messageId**

    string trace.messageId      Max: 100
* 
    Der eindeutige Bezeichner der Ablaufverfolgung des Telemetrieelements. 

**parameters**

    string trace.parameters      Max: 100
* 
    Dies sind die Parameter, die für die Ablaufverfolgungsaufzeichnung des Telemetrieelements angegeben werden. 

**processId**

    string trace.processId      Max: 100
* 
    Die Prozess-ID der App bei der Aufzeichnung des Telemetrieelements. 

**sourceType**

    string trace.sourceType      Max: 100
* 
    Der Quelltyp der Ablaufverfolgung des Telemetrieelements. 

**sqquenceId**

    string trace.sequenceid      Max: 100
* 
    Ein Sequenzbezeichner, den ein Ablaufverfolgungsanbieter verwenden kann, um die Sequenz der Ablaufverfolgung des Telemetrieelements aufzuzeichnen. 

**stacktrace**

    string trace.stacktrace      Max: 100
* 
    Eine Stapelüberwachung für die Ablaufverfolgung des Telemetrieelements. 

**threadId**

    string trace.threadId      Max: 100
* 
    Die Thread-ID der Anwendung zum Zeitpunkt der Aufzeichnung des Telemetrieelements. 

**userStackframe**

    string trace.userstackframe      Max: 100
* 
    Der Benutzerstapelrahmen für die Ablaufverfolgung des Telemetrieelements. 

**userStackframNum**

    string trace.userstackframenum      Max: 100
* 
    Die Nummer des Benutzerstapelrahmens für die Ablaufverfolgung des Telemetrieelements. 


## Ansicht

**referrerDataUrl**

    string view.referralurl      Max: 2048
* 
    Die Verweis-URL des Seitenaufrufs. Die vollständige URL wird für Volltextsuche und Export unterstützt. Dieses Feld kann eine sehr hohe Kardinalität haben und ist ein Attribut. Es wird in einen Satz von "referralData"-Datenelementen analysiert, die für Aggregationen im Metrik-Explorer verwendet werden können. 

**referrerData.base**

    string view.referrerdata.base      Max: 2048
* 
    Ein Teil der Verweis-URL ohne Host- und Abfrageparameter. Dies ist der Stamm-URI. Dieser Wert kann für Segmentierung/Aggregation verwendet werden. 

    *Ableitung*: Siehe Anhang zur URL-Transformation

**referrerData.hashTag**

    string view.referrerdata.hashtag      Max: 100
* 
    Der Text des Hashtags der Verweis-URL. 

    *Ableitung*: Siehe Anhang zur URL-Transformation

**referrerData.host**

    string view.referrerdata.host      Max: 200
* 
    Der Host der Verweis-URL. Wenn die URL ein lokaler URI ist, wird dieser leer dargestellt. 

    *Ableitung*: Siehe Anhang zur URL-Transformation

**referrerData.port**

    string view.referrerdata.port      Max: 100
* 
    Der Port der Verweis-URL, wenn die vollständige URL angezeigt wird. Andernfalls ist er leer. 

    *Ableitung*: Siehe Anhang zur URL-Transformation

**referrerData.protocol**

    string view.referrerdata.protocol      Max: 100
* 
    Das Protokoll (HTTP, FTP usw.) der Verweis-URL. 

    *Ableitung*: Siehe Anhang zur URL-Transformation

    *Beispiele*<br/> http<br/>https

**referrerData.queryParameters.parameter**

    string view.referrerdata.queryparameters.parameter      Max: 100
* 
    Ein Array von Abfrageparameternamen der Verweis-URL. 

    *Ableitung*: Siehe Anhang zur URL-Transformation

**referrerData.queryParameters.value**

    string view.referrerdata.queryparameters.value      Max: 100
* 
    Ein Array von Abfrageparameterwerten als Ergebnis der Analyse der Verweis-URL. 

    *Ableitung*: Siehe Anhang zur URL-Transformation



## Weitere Informationen

* [Application Insights](app-insights-overview.md) 
* [Fortlaufender Export](app-insights-export-telemetry.md)
* [Codebeispiele](app-insights-export-telemetry.md#code-samples)

<!---HONumber=AcomDC_0302_2016-->