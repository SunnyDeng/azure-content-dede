<properties
	pageTitle="Problembehandlung: Die Web-App ist aufgrund eines HTTP-Fehlers 502/503 nicht verfügbar"
	description="In diesem Artikel wird beschrieben, wie Sie die HTTP-Fehler 502/503 in Ihrer in Azure App Service gehosteten Web-App beheben."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="cephalin"/>

# Problembehandlung: Die Web-App ist aufgrund eines HTTP-Fehlers 502/503 nicht verfügbar

In diesem Artikel wird beschrieben, wie Sie die HTTP-Fehler 502/503 in Ihrer in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) gehosteten Web-App beheben.

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**.

## Symptom

Beim Aufrufen der Web-App wird ein HTTP-Fehler 502 (ungültiges Gateway) oder 503 (Dienst nicht verfügbar) zurückgegeben.

## Ursache

Diese Fehler sind häufig auf Probleme auf der Anwendungsebene zurückzuführen, z. B.:

-	Anforderungen, die sehr lange dauern
-	Eine Anwendung mit hoher Speicher-/CPU-Auslastung
-	Eine Anwendung, die aufgrund einer Ausnahme abstürzt

## Schritte zur Problembehandlung

Die Problembehandlung lässt sich in diesem Fall in drei unterschiedliche Aufgaben unterteilen, die nacheinander ausgeführt werden:

1.	[Beobachten und Überwachen des Anwendungsverhaltens](#observe)
2.	[Sammeln von Daten](#collect)
3.	[Minimieren der Auswirkungen des Problems](#mitigate)

Das Feature [App Service-Web-Apps](/services/app-service/web/) bietet Ihnen bei jedem Schritt verschiedene Optionen.

<a name="observe" />
### 1\. Beobachten und Überwachen des Anwendungsverhaltens

####	Nachverfolgen der Dienstintegrität

Microsoft Azure informiert jeweils aktuell über Dienstunterbrechungen oder Leistungsbeeinträchtigungen. Sie können die Integrität des Diensts im [Azure-Portal](https://portal.azure.com/) nachverfolgen. Weitere Informationen finden Sie unter [Nachverfolgen der Dienstintegrität](insights-service-health.md).

####	Überwachen Ihrer Web-App

Durch das Überwachen der Web-App können Sie herausfinden, ob in Ihrer Anwendung Probleme vorliegen. Klicken Sie auf dem Blatt Ihrer Web-App auf die Kachel **Anforderungen und Fehler**. Auf dem Blatt **Metrik** werden alle Metriken angezeigt, die Sie hinzufügen können.

Folgende Metriken können Sie z. B. für Ihre Web-App überwachen:

-	Durchschnittlicher Arbeitssatz für Arbeitsspeicher
-	Average response time
-	CPU-Zeit
-	Arbeitssatz für Arbeitsspeicher
-	Requests

![](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Weitere Informationen finden Sie unter:

-	[Überwachen von Web-Apps in Azure App Service](web-sites-monitor.md)
-	[Empfangen von Warnbenachrichtigungen](insights-receive-alert-notifications.md)

<a name="collect" />
### 2\. Sammeln von Daten

####	Verwenden des Azure App Service-Support-Portals

Web-Apps bietet Ihnen die Möglichkeit, Probleme im Zusammenhang mit Ihrer Web-App anhand von HTTP-Protokollen, Ereignisprotokollen, Prozesssicherungen usw. zu behandeln. Auf all diese Informationen können Sie in unserem Support-Portal unter **http://&lt;your App-Name>.scm.azurewebsites.net/Support** zugreifen.

Im Azure App Service-Support-Portal stehen Ihnen drei separate Registerkarten für die drei Schritte eines typischen Problembehandlungsszenarios zur Verfügung:

1.	Aktuelles Verhalten beobachten
2.	Diagnoseinformationen sammeln und integrierte Analysen ausführen, um das Problem zu analysieren
3.	Auswirkungen des Problems minimieren

Wenn das Problem gerade auftritt, klicken Sie auf **Analysieren** > **Diagnose** > **Jetzt diagnostizieren**, um eine Diagnosesitzung zu erstellen, die HTTP-Protokolle, Protokolle der Ereignisanzeige, Speicherabbilddateien, PHP-Fehlerprotokolle und einen PHP-Prozessbericht sammelt.

Nachdem die Daten gesammelt wurden, wird auch eine Analyse der Daten ausgeführt, und es wird ein HTML-Bericht angezeigt.

Falls Sie die Daten herunterladen, werden diese standardmäßig im Ordner „D:\\home\\data\\DaaS“ gespeichert.

Weitere Informationen zum Azure App Service-Support-Portal finden Sie unter [Neue Updates zur Unterstützung von Websiteerweiterungen für Azure-Websites](/blog/new-updates-to-support-site-extension-for-azure-websites).

####	Verwenden der Kudu-Debugkonsole

Web-Apps enthält eine Debugkonsole, die Sie zum Debuggen, Untersuchen und Hochladen von Dateien verwenden können, sowie JSON-Endpunkte zum Abrufen von Informationen über Ihre Umgebung. Dieses Feature wird als die _Kudu-Konsole_ oder das _SCM-Dashboard_ für Ihre Web-App bezeichnet.

Sie können über den Link **https://&lt;Your App-Name>.scm.azurewebsites.net/** auf das Dashboard zugreifen.

Die Kudu-Konsole bietet z. B. folgende Funktionen:

-	Umgebungseinstellungen für Ihre Anwendung
-	Protokolldatenstrom
-	Speicherabbild zu Diagnosezwecken
-	Debugkonsole zum Ausführen von Powershell-Cmdlets und grundlegenden DOS-Befehlen


Eine weitere nützliche Funktion von Kudu ist die Möglichkeit, mithilfe von Kudu und dem SysInternals-Tool „Procdump“ Speicherabbilddateien zu erstellen, wenn Ihre Anwendung Ausnahmen (erste Chance) auslöst. Bei diesen Speicherabbilddateien handelt es sich um Momentaufnahmen des Prozesses, die beim Beheben komplizierterer Probleme mit Ihrer Web-App hilfreich sein können.

Weitere Informationen zu den verfügbaren Funktionen in Kudu finden Sie unter [Online-Tools für Azure-Websites, die Sie kennen sollten](/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />
### 3\. Minimieren der Auswirkungen des Problems

####	Skalieren der Web-App

In Azure App Service können Sie zum Steigern der Leistung und des Durchsatzes die Skalierung anpassen, mit der Sie Ihre Anwendung ausführen. Zum Hochskalieren einer Web-App müssen Sie zwei zusammenhängende Schritte ausführen: Für den App Service-Plan muss ein höherer Tarif verwendet und nach dem Tarifwechsel müssen bestimmte Einstellungen konfiguriert werden.

Weitere Informationen zur Skalierung finden Sie unter [Skalieren einer Web-App in Azure App Service](web-sites-scale.md).

Außerdem haben Sie die Möglichkeit, Ihre Anwendung auf mehreren Instanzen auszuführen. Dies erhöht nicht nur die Verarbeitungskapazität, sondern bietet auch ein gewisses Maß an Fehlertoleranz. Wenn der Prozess in einer Instanz ausfällt, kann die andere Instanz weiterhin Anfragen verarbeiten.

Sie können die Skalierung auf „Manuell“ oder „Automatisch“ festlegen.

####	Verwenden von „AutoHeal“

„AutoHeal“ startet den Arbeitsprozess für Ihre App basierend auf von Ihnen ausgewählten Einstellungen neu (z. B. Konfigurationsänderungen, Anforderungen, speicherbasierte Grenzwerte oder zum Ausführen einer Anforderung benötigte Zeit). In den meisten Fällen ist das Neustarten des Prozesses die schnellste Methode zum Beheben eines Problems. Sie können die Web-App zwar immer direkt im Azure-Portal neu starten, „AutoHeal“ führt diesen Schritt jedoch automatisch für Sie aus. Sie müssen lediglich einige Trigger in der Stammdatei „web.config“ Ihrer Web-App hinzufügen. Beachten Sie, dass diese Einstellungen immer auf die gleiche Weise angewendet werden, auch dann, wenn es sich bei Ihrer Anwendung nicht um eine .Net-Anwendung handelt.

Weitere Informationen finden Sie unter [Automatische Reparatur von Azure-Websites](/blog/auto-healing-windows-azure-web-sites/).


####	Neustarten der Web-App

Dies ist oft die einfachste Methode zum Beheben einmaliger Probleme. Im [Azure-Portal](https://portal.azure.com) stehen auf dem Blatt Ihrer Web-App Optionen zum Beenden oder Neustarten der App zur Verfügung.

 ![](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Sie können Ihre Web-App auch mit Azure PowerShell verwalten. Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](powershell-azure-resource-manager.md).

<!---HONumber=AcomDC_1203_2015-->