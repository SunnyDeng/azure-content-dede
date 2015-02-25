<properties 
	pageTitle="Speichern von Serverskripts in der Quellcodeverwaltung - Azure Mobile Services" 
	description="Erfahren Sie, wie das New Relic-Add-On verwenden, um Ihren mobilen Dienst zu überwachen." 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="stepsic"/>

# Verwenden von New Relic zum Überwachen von Mobile Services

Dieses Thema demonstriert das Konfigurieren des Drittanbieter-Add-ons New Relic für die Verwendung mit Azure Mobile Services, um eine erweiterte Überwachung Ihres mobilen Diensts bereitzustellen. 

Dieses Lernprogramm führt Sie durch die folgenden Schritte:

1. [Anmelden bei New Relic über den Azure Store]
2. [Installieren des New Relic-Moduls].
3. [Aktivieren der New Relic-Entwickleranalyse für den mobilen Dienst]
4. [Überwachen des mobilen Diensts im New Relic-Dashboard]

Dieses Lernprogramm setzt voraus, dass Sie bereits einen mobilen Dienst aus einem der Lernprogramme [Erste Schritte mit Mobile Services] bzw. [Erste Schritte mit Daten] erstellt haben.

##<a name="sign-up"></a>Anmelden bei New Relic über den Azure Store

Der erste Schritt besteht im Kauf des New Relic-Diensts. Diese Lernprogramm demonstriert den Kauf dieses Diensts im Azure Store. Mobile Services unterstützt New Relic-Abonnements, die außerhalb des Azure Store erworben wurden.

1. Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.

2. Klicken Sie im unteren Bereich des Verwaltungsportal auf **Neu**.

3. Klicken Sie auf **Store**.

4. Wählen Sie im Dialogfeld **Add-On auswählen** die Option **New Relic** aus, und klicken Sie auf **Weiter**.

5. Wählen Sie im Dialogfeld **Add-On personalisieren** den gewünschten New Relic-Plan aus.

7. Geben Sie den Namen ein, mit dem der New Relic-Dienst in Ihren Azure-Einstellungen angezeigt werden soll, oder wählen Sie den Standardwert **NewRelic** aus. Dieser Name muss in der Liste der abonnierten Azure Store-Elemente eindeutig sein.

8. Wählen Sie einen Wert für die Region aus, z. B. **West US**.

9. Klicken Sie auf **Weiter**.

10. Prüfen Sie den Plan, die Preisinformationen sowie die rechtlichen Bedingungen im Dialogfeld **Kauf überprüfen**. Wenn Sie den Bedingungen zustimmen, klicken Sie auf **Kaufen**.

11. Nachdem Sie auf **Kaufen** geklickt haben, wird mit der Erstellung Ihres neuen New Relic-Kontos begonnen. Sie können den Status im Azure-Verwaltungsportal überwachen.

##<a name="install-module"></a>Installieren des New Relic-Moduls

Nachdem Sie sich für den New Relic-Dienst angemeldet haben, müssen Sie das New Relic-Modul "Node.js" in Ihrem mobilen Dienst installieren. Um dieses Modul hochladen zu können, muss die Quellcodeverwaltung für Ihren mobilen Dienst aktiviert sein.

1. Folgen Sie, falls noch nicht geschehen, den Schritten im Lernprogramm [Speichern von Serverskripts in der Quellcodeverwaltung], um die Quellcodeverwaltung für Ihren mobilen Dienst zu erstellen, das Repository zu klonen und den <a href="http://nodejs.org/" target="_blank">Node Package Manager (NPM)</a> zu installieren.

2. Navigieren Sie zum Ordner `.\service` in Ihrem lokalen Git-Repository, und führen Sie an der Eingabeaufforderung den folgenden Befehl aus:

		npm install newrelic

	NPM installiert das [New Relic-Modul][newrelic] im Unterverzeichnis `\newrelic`. 

3. Öffnen Sie ein Git-Befehlszeilentool, zum Beispiel **GitBash** (Windows) oder **Bash** (Unix Shell), und geben Sie den folgenden Befehl an der Git-Eingabeaufforderung ein: 

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	Auf diese Weise wird das neue `newrelic`-Modul in Ihren mobilen Dienst hochgeladen. 

Als Nächstes aktivieren Sie die New Relic-Überwachung Ihres mobilen Diensts in der [Verwaltungsportal][Azure-Verwaltungsportal]. 

##<a name="enable-service"></a>Aktivieren der New Relic-Entwickleranalyse für den mobilen Dienst

1. Wählen Sie im [Verwaltungsportal][Azure-Verwaltungsportal] Ihren mobilen Dienst aus. Klicken Sie dann auf die Registerkarte **Konfigurieren**.

	![][0]

2. Führen Sie einen Bildlauf nach unten zu **Developer analytics** durch, und führen Sie eine der folgenden Aktionen aus, je nachdem, wie Sie Ihr New Relic-Abonnement erworben haben:

	+ Kauf im Azure Store:

		Klicken Sie auf **Add-on**, wählen Sie das New Relic-Add-on in **Add-on auswählen** aus, und klicken Sie dann auf **Speichern**.

		![][1]

	+ Kauf direkt von New Relic: 

		Klicken Sie auf **Benutzerdefiniert**, wählen Sie das New Relic-Add-on unter **Anbieter** aus, geben Sie Ihren Schlüssel ein, und klicken Sie dann auf **Speichern**.

		![][2]

		Den Schlüssel erhalten Sie im New Relic-Dashboard.

3. Nach Abschluss der Registrierung wird ein neuer Wert unter **App-Einstellungen** angezeigt:

	![][3] 

##<a name="monitor"></a>Überwachen des mobilen Diensts im New Relic-Dashboard

1. Führen Sie die Client-App aus, um Lese-, Erstellen-, Aktualisieren- und Löschanforderungen an Ihren mobilen Dienst zu erzeugen.

2. Warten Sie einige Minuten, bis die Daten verarbeitet wurden, und wechseln Sie dann zum New Relic-Dashboard.

	Wenn Ihr New Relic-Abonnement als Add-On erworben wurde, wählen Sie es im [Verwaltungsportal][Azure-Verwaltungsportal] aus. Klicken Sie dann auf **Verwalten**.

3. Klicken Sie in New Relic auf **Applications** und dann auf Ihren mobilen Dienst.

	![][4]

4. Klicken Sie auf **Web Transactions**, um Ihre aktuellen Anforderungen an den mobilen Dienst anzuzeigen:

	![][5]

##<a name="next-steps"> </a>Nächste Schritte

+ Informationen zum Optimieren der Leistung der mobilen **iOS**- bzw. **Android**-App finden Sie unter [New Relic Mobile].
+ Preisinformationen finden Sie auf der [New Relic-Seite im Azure Store].
+ Weitere Informationen zum Verwenden von New Relic erhalten Sie unter [Applications Overview] in der New Relic-Dokumentation. 

<!-- Anchors. -->
[Anmelden bei New Relic über den Azure Store]: #sign-up
[Installieren des New Relic-Moduls]: #install-module
[Aktivieren der New Relic-Entwickleranalyse für den mobilen Dienst]: #enable-service
[Überwachen des mobilen Diensts im New Relic-Dashboard]: #monitor
[Nächste Schritte]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[Quellcodeverwaltung]: http://msdn.microsoft.com/de-de/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Arbeiten mit Serverskripts in Mobile Services]: /de-de/develop/mobile/how-to-guides/work-with-server-scripts.md

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Node.js API-Dokumentation: Module]: http://nodejs.org/api/modules.html
[Speichern von Serverskripts in der Quellcodeverwaltung]: /de-de/develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[New Relic-Seite im Azure Store]: /de-de/gallery/store/new-relic/new-relic/
[Applications Overview]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet
[New Relic Mobile]: http://newrelic.com/mobile-monitoring





<!--HONumber=42-->
