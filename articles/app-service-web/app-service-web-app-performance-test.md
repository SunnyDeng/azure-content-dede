<properties
   pageTitle="Testen der Leistung einer Azure-Web-App | Microsoft Azure"
   description="Führen Sie Azure-Web-App-Leistungstests aus, um zu überprüfen, wie Ihre App Benutzerlasten verarbeitet. Messen Sie die Antwortzeit, und finden Sie Fehler, die auf Probleme hinweisen können."
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/11/2015"
   ms.author="estfan; manasma"/>

# Leistungstest der Azure-Web-App unter Last

Testen Sie die Leistung Ihrer Web-App, bevor Sie sie starten oder Updates für die Produktion bereitstellen. Auf diese Weise können Sie besser beurteilen, ob Ihre App zur Veröffentlichung bereit ist. Und Sie können sicher sein, dass Ihre App den Datenverkehr zu Spitzenlastzeiten oder während Ihrer nächsten Marketingkampagne verarbeiten kann.

Während der öffentlichen Vorschau können Sie Ihre App kostenlos im Azure-Portal testen. Diese Tests simulieren über einen bestimmten Zeitraum eine bestimmte Benutzerlast für Ihre App und messen die Antwortzeit der App. Die Testergebnisse zeigen beispielsweise, wie schnell Ihre App bei einer bestimmten Anzahl von Benutzern reagiert. Sie zeigen auch, bei wie vielen Anforderungen Fehler aufgetreten sind, was auf Probleme mit der App hinweisen kann.

![Leistungsprobleme in Ihrer Web-App finden][TestOverview]

## Vorbereitung

*	Sie benötigen ein [Azure-Abonnement][AzureSubscription], falls Sie noch keins besitzen. Erfahren Sie, wie Sie [kostenlos ein Azure-Konto anlegen][AzureFreeTrial].

*	Sie benötigen ein Konto für [Visual Studio Team Services (VSTS)][WhatIsVSTS], um den Verlauf der Leistungstests zu speichern. Wenn Sie den Leistungstest einrichten, erstellen Sie ein neues Konto, oder verwenden Sie ein vorhandenes Konto, wenn Sie der Kontobesitzer sind. [Welche Möglichkeiten stehen mir mit einem Visual Studio Team Services-Konto noch zur Verfügung?](#VSTSAccount)

*	Sie können Ihre App zum Testen in einer nicht produktiven Umgebung bereitstellen. Ihre App kann einen anderen App Service-Plan als in der Produktion verwenden. So wirken sich Tests nicht auf vorhandene Kunden aus oder verlangsamen Ihre App in der Produktion.

## Einrichten und Ausführen des Leistungstests

0.	Melden Sie sich beim [Azure-Portal][AzurePortal] an. Um ein Visual Studio Team Services-Konto zu verwenden, dessen Besitzer Sie sind, melden Sie sich als Kontobesitzer an.

0.	Wechseln Sie zu Ihrer Web-App.

	![Zu "Alles durchsuchen" > "Web-Apps" > "Ihre App" wechseln][WebApp]

0.	Wechseln Sie zur **Leistungstest**.

	![Zu "Tools" > "Leistungstest" wechseln][ExpandedTools]
 
0.	Erstellen Sie jetzt eine Verknüpfung auf ein [Visual Studio Team Services (VSTS)][WhatIsVSTS]-Konto, um den Verlauf der Leistungstests zu speichern.

	Wenn Sie über ein verwendbares VSTS-Konto verfügen, wählen Sie dieses Konto aus. Andernfalls erstellen Sie ein neues Konto.

	![Vorhandenes VSTS-Konto auswählen oder neues Konto erstellen][ExistingNewVSTSAccount]

0.	Erstellen Sie den Leistungstest. Geben Sie die Details an, und führen Sie den Test aus. Sie können die Ergebnisse während der Testausführung in Echtzeit anzeigen.

	Angenommen, Sie hätten eine App, die im Weihnachtsgeschäft des letzten Jahres Coupons verschenkt hat. Das Ereignis dauerte 15 Minuten, und zu Spitzenzeiten waren 100 Kunden gleichzeitig anwesend. Dieses Jahr möchten Sie die Kundenzahl verdoppeln. Sie möchten auch die Kundenzufriedenheit verbessern, indem Sie die Seitenladezeit von 5 auf 2 Sekunden verkürzen. Sie testen also die Leistung Ihrer App über einen Zeitraum von 15 Minuten mit 250 Benutzern.

	Sie simulieren die Last Ihrer App, indem Sie virtuelle Benutzer (Kunden) erzeugen, die Ihre Website zur gleichen Zeit besuchen. So erfahren Sie, bei wie vielen Anforderungen Fehler auftreten oder die Reaktion zu langsam ist.

	![Leistungstest erstellen, einrichten und ausführen][NewTest]

	 *	Die Standard-URL Ihrer Web-App wird automatisch hinzugefügt. Sie können die URL ändern, um andere Seiten zu testen (nur HTTP GET-Anforderungen).

	 *	Um die lokalen Bedingungen zu simulieren und die Latenz zu verringern, wählen Sie einen Ort, der sich möglichst in der Nähe der Benutzer befindet, die die Last generieren.

	Hier sehen Sie den Testfortschritt. Während der ersten Minute lädt die Seite langsamer als Ihnen lieb sein kann.

	![Leistungstest während der Ausführung mit Echtzeitdaten][TestRunning]

	Nach Abschluss des Tests ist klar, dass die Seite nach der ersten Minute viel schneller lädt. So können Sie herausfinden, wo Sie mit der Behebung des Problems beginnen sollten.

	![Abgeschlossener Leistungstests mit Ergebnissen, einschließlich Anforderungen mit Fehlern][TestDone]
	
Wir freuen uns auf Ihr Feedback. Bei Fragen oder Problemen wenden Sie sich unter <vsoloadtest@microsoft.com> an uns.

##	Fragen und Antworten

#### F: Gibt es eine Beschränkung, wie lange ich einen Test ausführen kann? 

A: Ja. Sie können den Test bis zu einer Stunde lang im Azure-Portal ausführen.

#### F: Wie viel Zeit habe ich zum Ausführen von Leistungstests? 

A: Nach der öffentlichen Vorschau erhalten Sie jeden Monat 20.000 virtuelle Benutzerminuten (Virtual User Minute, VUM) kostenlos mit Ihrem Visual Studio Team Services-Konto. Bei einer VUM handelt es sich um die Anzahl an Benutzern multipliziert mit der Anzahl an Minuten in Ihrem Test. Wenn Ihre Anforderungen über dieses kostenlose Kontingent hinausgehen, können Sie mehr Zeit erwerben und bezahlen dabei nur die Zeit, die Sie wirklich verwenden.

#### F: Wo kann ich prüfen, wie viele virtuelle Benutzerminuten ich bereits verwendet habe?

A: Sie können die Anzahl im Azure-Portal prüfen.

![Zum VSTS-Konto wechseln][VSTSAccount]

![Verwendete VUM überprüfen][CheckTestTime]

<a name="VSOAccount"></a>
#### F: Welche anderen Möglichkeiten habe ich mit einem Visual Studio Team Services-Konto?

A: Sie finden Ihr neues Konto hier: ```https://{accountname}.visualstudio.com```. Code freigeben, Software erstellen, testen, nachverfolgen und ausliefern – all dies in der Cloud, mit jedem Tool und in jeder Sprache. Erfahren Sie, wie die Funktionen und Dienste von [Visual Studio Team Services][WhatIsVSTS] Ihr Team dabei unterstützen, noch einfacher zusammenzuarbeiten und kontinuierlich neue Apps bereitzustellen.

<!--Image references-->
[WebApp]: ./media/app-service-web-app-performance-test/azure-np-web-apps.png
[TestOverview]: ./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png
[ExpandedTools]: ./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png
[ExistingNewVSTSAccount]: ./media/app-service-web-app-performance-test/azure-np-no-vso-account.png
[NewTest]: ./media/app-service-web-app-performance-test/azure-np-new-performance-test.png
[TestRunning]: ./media/app-service-web-app-performance-test/azure-np-running-perf-test.png
[TestDone]: ./media/app-service-web-app-performance-test/azure-np-perf-test-done.png
[VSTSAccount]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts.png
[CheckTestTime]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png

<!--Reference links -->
[AzurePortal]: https://portal.azure.com
[AzureSubscription]: https://account.windowsazure.com/subscriptions
[AzureFreeTrial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[WhatIsVSTS]: https://www.visualstudio.com/products/what-is-visual-studio-online-vs

<!---HONumber=AcomDC_1203_2015-->