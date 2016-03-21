<properties
   pageTitle="Übersicht über den lokalen Cache von Azure App Service"
   description="In diesem Artikel wird beschrieben, wie Sie den lokalen Cache von Azure App Service aktivieren, dessen Größe ändern und seinen Status abrufen."
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords="Nur zur Verwendung durch SEO Champs. Begriffe müssen durch Kommas getrennt werden. Wenden Sie sich an Ihren SEO Champ, bevor Sie Inhalte dieses Artikels ändern, die diese Begriffe enthalten."/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# Übersicht über den lokalen Cache von Azure App Service

Der Inhalt von Azure-Webanwendungen wird in Azure Storage gespeichert und dauerhaft als Inhaltsfreigabe bereitgestellt. Dieses Design ist auf die Zusammenarbeit mit einer Vielzahl von Anwendungen ausgelegt und weist die folgenden Merkmale auf:

* Der Inhalt wird über mehrere VM-Instanzen der Webanwendung freigegeben. 
* Der Inhalt wird dauerhaft bereitgestellt und kann durch ausgeführte Webanwendungen geändert werden. 
* Protokolldateien und Diagnosedatendateien stehen unterhalb desselben freigegebenen Inhaltsordners zur Verfügung. 
* Durch das direkte Veröffentlichen neuer Inhalte wird der Inhaltsordner aktualisiert, der dann über die SCM-Website und die ausgeführte Web-App sofort angezeigt werden kann (typischerweise wird durch Technologien wie ASP.NET bei einigen Dateiänderungen ein Neustart der Webanwendung ausgelöst, um den neuesten Inhalt abzurufen). 

Während viele Webanwendungen einzelne oder alle dieser Features nutzen, benötigen einige Webanwendungen lediglich einen hoch leistungsfähigen schreibgeschützten Inhaltsspeicher, aus dem sie mit hoher Verfügbarkeit ausgeführt werden können. Diese Anwendungen können von einer Kopie des Inhalts einer bestimmten VM-Instanz profitieren. Dieser Inhalt wird nachfolgend als „lokaler Cache“ bezeichnet. Der _lokale Cache_ stellt eine Webrollenansicht Ihres Inhalts dar. Es handelt sich um einen Cache Ihres Speicherinhalts mit „write but discard“-Prinzip, der beim Sitestart asynchron erstellt wird. Wenn der Cache bereit ist, wird die Site zur Ausführung mit dem zwischengespeicherten Inhalt umgeschaltet. Webanwendungen, die mit einem lokalen Cache ausgeführt werden, profitieren von den folgenden Vorteilen:

* Sie werden nicht durch Latenzen beeinträchtigt, die beim Zugriff auf Inhalt in Azure Storage auftreten. 
* Sie sind unbeeinflusst von geplanten Upgrades oder ungeplanten Downtimes der Server, die die Inhaltsfreigabe verwalten, und sie werden nicht durch andere Unterbrechungen bei Azure Storage beeinträchtigt. 
* Es sind weniger App-Neustarts aufgrund von Änderungen an der Speicherfreigabe erforderlich. 

## Auswirkung des lokalen Caches auf das Verhalten von App Service

* Der lokale Cache ist eine Kopie der Ordner „/site“ und „/siteextensions“ der Webanwendung. Er wird beim Start der Webanwendung auf der lokalen VM-Instanz erstellt. Die Größe des lokalen Caches ist pro Webanwendung standardmäßig auf 300 MB beschränkt, kann aber auf bis zu 1 GB erhöht werden. 
* Der lokale Cache ermöglicht Lese- und Schreibzugriff, aber Änderungen werden verworfen, wenn die Webanwendung zwischen virtuellen Computern verschoben oder neu gestartet wird. Der lokale Cache sollte nicht für Anwendungen verwendet werden, die unternehmenskritische Daten dauerhaft im Inhaltsspeicher speichern. 
* Webanwendungen können wie bisher Protokolldateien und Diagnosedaten schreiben. Protokolldateien und -daten werden jedoch lokal auf dem virtuellen Computer gespeichert und dann in regelmäßigen Abständen in den freigegebenen Inhaltsspeicher kopiert. Der Kopiervorgang in den freigegebenen Inhaltsspeicher erfolgt nach dem „Best Case“-Prinzip, und zurückgeschriebene Daten können bei einem plötzlichen Absturz einer VM-Instanz verloren gehen. 
* Für Web-Apps, die den lokalen Cache verwenden, ergibt sich eine Änderung in Bezug auf die Ordnerstruktur für die LogFiles- und Data-Ordner. Es sind jetzt Unterordner in den Speicherordnern „LogFiles“ und „Data“ vorhanden, die das Namensmuster „eindeutiger Bezeichner“ + Zeitstempel verwenden. Jeder der Unterordner entspricht einer VM-Instanz, auf der die Webanwendung ausgeführt wird oder wurde.  
* Das Veröffentlichen von Änderungen an der Webanwendung über einen der Veröffentlichungsmechanismen führt zu einer Veröffentlichung im freigegebenen Inhaltsspeicher. Dies ist ein Designkonzept, weil der veröffentlichte Inhalt dauerhaft bereitgestellt werden soll. Zum Aktualisieren des lokalen Caches der Webanwendung ist ein Neustart erforderlich. Dies erscheint überzogen? Siehe nachstehende Informationen für einen nahtlosen Lebenszyklus.
* „D:\\Home“ zeigt auf den lokalen Cache. „D:\\local“ zeigt weiterhin auf den temporären VM-spezifischen Speicher. 
* Die standardmäßige Inhaltsansicht der SCM-Site ist weiterhin die des freigegebenen Inhaltsspeichers. Um zu sehen, wie Ihr lokaler Cacheordner aussieht, können Sie zu https://[site-name].scm.azurewebsites.net/VFS/LocalSiteRoot/LocalCache navigieren.

## Aktivieren des lokalen Caches in Azure App Service

Der lokale Cache wird mithilfe einer Kombination aus reservierten App-Einstellungen konfiguriert. Diese App-Einstellungen können mithilfe der folgenden Methoden konfiguriert werden:

* [Azure-Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### Gewusst wie: Konfigurieren des lokalen Caches mit dem Azure-Portal
<a name="Configure-Local-Cache-Portal"></a>

Der lokale Cache wird mithilfe einer AppSetting pro Webanwendung aktiviert. `WEBSITE_LOCAL_CACHE_OPTION` = `Always`

![App-Einstellungen im Azure-Portal: lokaler Cache](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### Gewusst wie: Konfigurieren des lokalen Caches mit Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```
...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],
    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300" 
    }
}

...
```

## Wie wird die Größe des lokalen Caches von App Service geändert?

Standardmäßig ist der lokale Cache **300 MB** groß. Dies schließt die Ordner „Site“ und „SiteExtensions“ ein, die aus dem Inhaltspeicher kopiert werden, sowie alle lokal erstellten Protokoll- und Datenordner. Um dieses Limit zu erhöhen, verwenden Sie die AppSetting `WEBSITE_LOCAL_CACHE_SIZEINMB`. Die Größe kann auf bis zu **1 GB** (1.000 MB) pro Webanwendung erhöht werden.

## Best Practices für die Verwendung des lokalen Caches von App Service

Es wird empfohlen, den lokalen Cache gemeinsam mit dem Feature [Stagingumgebungen](../app-service-web/web-sites-staged-publishing.md) zu verwenden.

* Fügen Sie eine _persistente_ AppSetting `WEBSITE_LOCAL_CACHE_OPTION` mit dem Wert `Always` zu Ihrem **Produktionsslot** hinzu. Wenn Sie `WEBSITE_LOCAL_CACHE_SIZEINMB` verwenden, fügen Sie sie ebenfalls als persistente Einstellung zu Ihrem Produktionsslot hinzu. 
* Erstellen Sie einen Stagingslot, und führen Sie eine Veröffentlichung in Ihrem Stagingslot durch. Der Stagingslot verwendet üblicherweise nicht den lokalen Cache, um einen nahtlosen Lebenszyklus für das Erstellen/Bereitstellen/Testen für das Staging zu ermöglichen, während die Vorteile des lokalen Caches für den Produktionsslot genutzt werden. 
*	Testen Sie Ihre Site mit dem Stagingslot.  
*	Wenn Sie bereit dazu sind, wechseln Sie über einen [swap-Vorgang](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots) zwischen Ihren **Stagingslots** und den **Produktionsslots**.  
*	Persistente Einstellungen gelten nach Name und dauerhaft für einen Slot. Wenn also der Stagingslot auf den Produktionsslot umgeschaltet wird, erbt dieser die App-Einstellungen für den lokalen Cache. Der neue Produktionsslot wird nach ein paar Minuten mit dem lokalen Cache ausgeführt und im Rahmen der Slotaufwärmung nach dem Wechsel aufgewärmt. Wenn der Slotwechsel abgeschlossen ist, wird Ihr Produktionsslot mit dem lokalen Cache ausgeführt.

## Häufig gestellte Fragen

### Ist der lokale Cache für meine Webanwendung geeignet? 

Wenn Ihre Webanwendung einen hoch leistungsfähigen, zuverlässigen Inhaltsspeicher benötigt, den Inhaltsspeicher nicht zum Schreiben von unternehmenskritischen Daten zur Laufzeit nutzt und eine Gesamtgröße von < 1 GB aufweist, dann lautet die Antwort: Ja, Ihre Anwendung ist für den lokalen Cache geeignet! Um die Gesamtgröße der Ordner „site“ und „siteextensions“ zu ermitteln, können Sie die Siteerweiterung „Azure Web Apps Disk Usage“ verwenden.
 
### Wie aktiviere ich den lokalen Cache? 

Lesen Sie den obigen Abschnitt zu den Best Practices bei Verwendung des lokalen Caches.
 
### Woher weiß ich, dass meine Site auf die Verwendung des lokalen Caches umgeschaltet hat? 

Wenn Sie das Feature für den lokalen Cache mit Stagingumgebungen verwenden, findet der Wechsel erst dann statt, wenn der lokale Cache aufgewärmt wurde. Um zu prüfen, ob Ihre Site mit dem lokalen Cache ausgeführt wird, können Sie die Workerprozess-Umgebungsvariable `WEBSITE_LOCALCACHE_READY` überprüfen. Verwenden Sie die hier bereitgestellten Anweisungen, um auf mehreren Instanzen auf die Workerprozess-Umgebungsvariable zuzugreifen.
 
### Ich habe soeben neue Änderungen veröffentlicht, aber meine Webanwendung scheint nicht über diese zu verfügen. Warum? 
Wenn Ihre Webanwendung den lokalen Cache verwendet, müssen Sie Ihre Site neu starten, um die letzten Änderungen abzurufen. Sie möchten für eine Produktionssite keinen Neustart durchführen? Informationen zu den Slotoptionen finden Sie oben.
 
### Wo sind meine Protokolle? 

Bei Verwendung des lokalen Caches sehen die Protokoll- und Datenordner etwas anders aus. Die Struktur Ihrer Unterordner bleibt jedoch erhalten, mit der Ausnahme, dass sie unterhalb eines Unterordners mit dem Format „eindeutiger VM-Bezeichner“ + Zeitstempel geschachtelt werden.
 
### Ich habe den lokalen Cache aktiviert, aber meine Webanwendung wird weiterhin neu gestartet. Warum? Ich dachte, durch den lokalen Cache werden App-Neustarts verringert. 

Der lokale Cache trägt dazu bei, Webanwendungsneustarts in Bezug auf den Speicher zu vermeiden. Ihre Webanwendung kann jedoch aufgrund von geplanten Upgrades der VM-Infrastruktur weiterhin neu gestartet werden. Die App sollte bei aktiviertem lokalen Cache insgesamt weniger oft neu gestartet werden.

<!---HONumber=AcomDC_0309_2016-->