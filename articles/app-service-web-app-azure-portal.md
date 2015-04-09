<properties
	pageTitle="Referenz zur Navigation im Azure-Portal"
	description="Informationen zu den unterschiedlichen Benutzerumgebungen für App Service Web Apps zwischen dem Verwaltungsportal und Azure-Portal"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="jaime-espinosa"/>

# Referenz zur Navigation im Azure-Portal

Azure Websites heißt nun [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Wir aktualisieren unsere gesamte Dokumentation entsprechend dieser Namensänderung und bieten Anleitungen für das neue Azure-Portal. Bis dieser Prozess abgeschlossen ist, können Sie dieses Dokument als Leitfaden für die Arbeit mit Web-Apps im neuen Azure-Portal verwenden.
 
## Die Zukunft des alten Portals

Sie werden die Brandingänderungen am alten Portal schon bemerkt haben. Dieses Portal wird gerade durch das neue Azure-Portal ersetzt. Da das alte Portal ein Auslaufmodell ist, wird der Schwerpunkt für neue Entwicklung auf das neue Portal verlagert. Alle anstehenden neuen Features für Web-Apps fließen in das neue Portal. Beginnen Sie mit dem Nutzen des neuen Portals, um in den Genuss der neuesten und besten Features zu kommen, die Web-Apps zu bieten haben.

## Unterschiede beim Layout zwischen dem alten Portal und dem Azure-Portal

Im alten Portal sind alle Azure-Dienste auf der linken Seite aufgeführt. Die Navigation im alten Portal folgt einer Baumstruktur, bei der Sie beim Dienst starten und in jedes einzelne Element navigieren. Diese Struktur funktioniert gut, wenn Sie unabhängige Komponenten verwalten. Auf Azure basierende Anwendungen sind jedoch eine Sammlung miteinander verbundener Dienste, weshalb diese Struktur für die Arbeit mit Sammlungen von Diensten nicht ideal ist. 

Das neue Portal erleichtert das Entwickeln von Anwendungen von A bis Z mit Komponenten aus mehreren Diensten. Das neue Azure-Portal ist organisiert als  *journeys*. Eine  *journey* ist eine Folge von  *blades*, die Container für die verschiedenen Komponenten sind. Das Einrichten der automatischen Skalierung für eine Web-App ist z. B. eine  *journey*, bei der Sie, wie im folgenden Beispiel gezeigt, zu mehreren Blättern gelangen: zum Blatt **Website** (der Blatttitel wurde noch nicht mit der neuen Terminologie aktualisiert), zum Blatt **Einstellungen** und zum Blatt **Skalieren**. Bei diesem Beispiel ist die automatische Skalierung so eingerichtet, dass sie von der CPU-Auslastung abhängig ist. Daher gibt es auch ein Blatt **CPU-Prozentsatz**. Die Komponenten in den  *blades* heißen  *parts*, die wie Kacheln aussehen. 

![](./media/app-service-web-app-preview-portal/AutoScaling.png)

## Navigationsbeispiel: Erstellen einer Web-App

Das Erstellen neuer Web-Apps ist immer noch kinderleicht. Die folgende Abbildung zeigt das alte Portal und das neue Portal nebeneinander, um zu zeigen, dass sich die Anzahl der Schritte, die zum Erstellen einer neuen Web-App und Aufnehmen ihres Betriebs erforderlich sind, nicht groß geändert hat. 

![](./media/app-service-web-app-preview-portal/CreateWebApp.png)

Im neuen Portal können Sie aus den am häufigsten verwendeten Typen von Webanwendungen wählen, wozu auch beliebte Kataloganwendungen wie WordPress zählen. Eine vollständige Liste der verfügbaren Anwendungen finden Sie im [Azure Marketplace].

Wenn Sie eine Webanwendung erstellen, geben Sie im neuen Portal die URL, den App Service-Plan und Speicherort wie im alten Portal an. 

![](./media/app-service-web-app-preview-portal/CreateWebAppSettings.png)

Darüber hinaus können Sie im neuen Portal weitere allgemeine Einstellungen festlegen. Beispielsweise vereinfachen [Ressourcengruppen] (azure-preview-portal-using-resource-groups.md) das Anzeigen und Verwalten zugehöriger Azure-Ressourcen. 

## Navigationsbeispiel: Einstellungen und Features

Alle Einstellungen und Features sind nun logisch in einem einzelnen Blatt gruppiert, von wo aus Sie navigieren können.

![](./media/app-service-web-app-preview-portal/WebAppSettings.png)

Beispielsweise können Sie benutzerdefinierte Domänen erstellen, indem Sie im Blatt **Einstellungen** auf **Benutzerdefinierte Domänen und SSL** klicken.

![](./media/app-service-web-app-preview-portal/ConfigureWebApp.png)

Um eine Überwachungswarnung einzurichten, klicken Sie auf **Anforderungen und Fehler** und dann auf **Warnung hinzufügen**.

![](./media/app-service-web-app-preview-portal/Monitoring.png)

Um die Diagnose zu aktivieren, klicken Sie auf dem Blatt **Einstellungen** auf **Diagnoseprotokolle**.

![](./media/app-service-web-app-preview-portal/Diagnostics.png)
 
Zum Konfigurieren von Anwendungseinstellungen klicken Sie im Blatt **Einstellungen** auf **Anwendungseinstellungen**. 

![](./media/app-service-web-app-preview-portal/AppSettingsPreview.png)

Außer dem Markennamen wurden ein paar Dinge im Portal umbenannt oder anders gruppiert, damit Sie sie einfacher finden können. Nachstehend sehen Sie beispielsweise ein Bildschirmfoto der entsprechenden Seite für App-Einstellungen (**Konfigurieren**) im alten Portal.

![](./media/app-service-web-app-preview-portal/AppSettings.png)

## Weitere Ressourcen

[Die Azure-Cloudanwendungsplattform](app-service-cloud-app-platform.md)

[Neues Portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

>[AZURE.NOTE] Wenn Sie in Azure App Service einsteigen möchten, ohne sich zuvor für ein Azure-Konto zu registrieren, wechseln Sie zur Website [App Service ausprobieren](http://go.microsoft.com/fwlink/?LinkId=523751), auf der Sie eine Web-App mit kurzer Gültigkeitsdauer für Einsteiger in App Service erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Was sich geändert hat
* Informationen über die Änderung von Websites zu App Service finden Sie unter: [Azure App Service und seine Auswirkungen auf vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714)
* Informationen über die Änderung vom alten Portal zum neuen Portal finden Sie unter: [Referenz zur Navigation im neuen Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->