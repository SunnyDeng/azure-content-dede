<properties
	pageTitle="Referenz zur Navigation im Azure-Portal"
	description="Informationen zu den Unterschieden in Bezug auf App Service-Web-Apps zwischen dem Verwaltungsportal und dem Azure-Portal"
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

Azure Websites heißt ab sofort [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Wir aktualisieren unsere gesamte Dokumentation, um dieser Namensänderung Rechnung zu tragen und bieten Anleitungen für das neue Azure-Portal. Bis dieser Prozess abgeschlossen ist, können Sie dieses Dokument als Leitfaden für die Arbeit mit Web-Apps im neuen Azure-Portal verwenden.
 
## Die Zukunft des alten Portals

Sie werden die Brandingänderungen am alten Portal schon bemerkt haben. Dieses Portal wird gerade durch das neue Azure-Portal ersetzt. Da das alte Portal schrittweise außer Betrieb genommen wird, werden die neuen Entwicklungen auf das neue Portal verlagert. Alle anstehenden neuen Features für Web-Apps fließen in das neue Portal ein. Nutzen Sie das neue Portal, um von den neuesten und besten Features zu profitieren, die Web-Apps zu bieten haben.

## Unterschiede beim Layout zwischen dem alten Portal und dem Azure-Portal

Im alten Portal sind alle Azure-Dienste auf der linken Seite aufgeführt. Die Navigation im alten Portal erfolgt in einer Strukturansicht, bei der Sie bei einem Dienst starten und zu den einzelnen Elementen navigieren. Diese Struktur funktioniert gut, wenn Sie unabhängige Komponenten verwalten. Auf Azure basierende Anwendungen sind jedoch eine Sammlung miteinander verbundener Dienste, weshalb diese Struktur für die Arbeit mit Sammlungen von Diensten nicht ideal ist.

Das neue Portal vereinfacht die Entwicklung von Anwendungen mit Komponenten aus mehreren Diensten in allen Aspekten. Das neue Azure-Portal ist in Form von *Abläufen* strukturiert. Ein *Ablauf* umfasst eine Folge von *Blättern*, die als Container für die verschiedenen Komponenten dienen. Das Einrichten der automatischen Skalierung für eine Web-App ist z. B. ein *Ablauf*, bei dem Sie, wie im folgenden Beispiel gezeigt, zu mehreren Blättern gelangen: zum Blatt **Website**, zum Blatt **Einstellungen** und zum Blatt **Skalieren**. Bei diesem Beispiel ist die automatische Skalierung so eingerichtet, dass sie von der CPU-Auslastung abhängig ist. Daher gibt es auch ein Blatt **CPU-Prozentsatz**. Die Komponenten in den *Blättern* heißen *Teile* und sehen wie Kacheln aus.

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## Navigationsbeispiel: Erstellen einer Web-App

Das Erstellen neuer Web-Apps ist immer noch kinderleicht. Die folgende Abbildung zeigt das alte Portal und das neue Portal nebeneinander. Sie können sehen, dass sich die Anzahl der Schritte, die für Erstellung und Inbetriebnahme einer neuen Web-App erforderlich sind, nur unwesentlich geändert hat.

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

Im neuen Portal können Sie aus den gängigsten Typen von Web-Apps wählen, darunter z. B. auch beliebte Kataloganwendungen wie WordPress. Eine vollständige Liste der verfügbaren Anwendungen finden Sie im [Azure Marketplace].

Wenn Sie eine Web-App erstellen, geben Sie im neuen Portal die URL, den App Service-Plan und Speicherort wie im alten Portal an.

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

Darüber hinaus können Sie im neuen Portal weitere allgemeine Einstellungen festlegen. Beispielsweise vereinfachen [Ressourcengruppen](../resource-group-overview.md) das Anzeigen und Verwalten zugehöriger Azure-Ressourcen.

## Navigationsbeispiel: Einstellungen und Features

Alle Einstellungen und Features sind nun logisch in einem einzelnen Blatt gruppiert, von wo aus Sie navigieren können.

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

Beispielsweise können Sie benutzerdefinierte Domänen erstellen, indem Sie im Blatt **Einstellungen** auf **Benutzerdefinierte Domänen und SSL** klicken.

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

Um eine Überwachungswarnung einzurichten, klicken Sie auf **Anforderungen und Fehler** und anschließend auf **Warnung hinzufügen**.

![](./media/app-service-web-app-azure-portal/Monitoring.png)

Um die Diagnose zu aktivieren, klicken Sie im Blatt **Einstellungen** auf **Diagnoseprotokolle**.

![](./media/app-service-web-app-azure-portal/Diagnostics.png)
 
Zum Konfigurieren von Anwendungseinstellungen klicken Sie im Blatt **Einstellungen** auf **Anwendungseinstellungen**.

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

Neben dem Markennamen wurden einige Elemente im Portal umbenannt oder anders gruppiert, um ihr Auffinden zu erleichtern. Nachstehend sehen Sie beispielsweise einen Screenshot der entsprechenden Seite für App-Einstellungen (Konfigurieren) im alten Portal.

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## Weitere Ressourcen

[Die Azure-Cloudanwendungsplattform](../app-service-cloud-app-platform.md)

[New portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Änderungen von Websites zu App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=62-->