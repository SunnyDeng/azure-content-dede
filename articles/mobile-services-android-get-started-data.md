<properties 
	pageTitle="Erste Schritte mit Daten (Android) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer Android-App." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/16/2014" 
	ms.author="ricksal,glenga"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Dieses Thema beschreibt die Nutzung von mobilen Azure-Diensten für die Nutzung von Daten in einer Android-App. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm anschauen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a><span class="time">15:32</span></div>
</div>

> [AZURE.NOTE] Dieses Lernprogramm vermittelt ein besseres Verständnis davon, wie Sie mit mobilen Diensten in Azure Daten aus einer Android-App speichern und abrufen können. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started-android). abschließen.
> 
> Den Quellcode der fertiggestellten App finden Sie [hier](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData).


In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Android-App-Projekts]
2. [Erstellen des mobilen Diensts]
3. [Hinzufügen einer Datentabelle als Speicher]
4. [Aktualisieren der App zur Verwendung von Mobile Services]
5. [Testen der App mit Mobile Services]

> [AZURE.IMPORTANT] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357"%20target="_blank). 

Für dieses Lernprogramm benötigen Sie das [Android-SDK für mobile Dienste]. Dieses <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Android-SDK</a> enthält die integrierte Eclipse-Entwicklungsumgebung (IDE) und das Android Developer Tools (ADT)-Plugin und Android 4.2 oder eine neuere Version. 

> [AZURE.NOTE] Dieses Lernprogramm enthält Anleitungen für die Installation des Android-SDK und des Android-SDK für mobile Dienste. Das heruntergeladene GetStartedWithData-Projekt benötigt Android 4.2 oder eine neuere Version. Das SDK für mobile Dienste benötigt dagegen nur Android 2.2 oder eine neuere Version.

>[AZURE.NOTE] Dieses Lernprogramm verwendet die neueste Version des Mobile Services SDK. Eine frühere Version des SDK aus Gründen der Abwärtskompatibilität finden Sie <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126"> hier</a>; der in diesen Lernprogrammen enthaltene Code funktioniert damit jedoch nicht.

<h2><a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts</h2>

###Laden Sie den Beispielcode herunter

[AZURE.INCLUDE [download-android-sample-code](../includes/download-android-sample-code.md)]

###Prüfen der Version des Android-SDK

[AZURE.INCLUDE [Prüfen des SDK](../includes/mobile-services-verify-android-sdk-version.md)]


###Prüfen und Ausführen des Beispielcodes

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

<h2><a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]


<h2><a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst</h2>

Die App verwendet nun mobile Dienste als Back-End-Speicher, und Sie können sie entweder im Android-Emulator oder auf einem Android-Telefon gegen den mobilen Dienst testen.

1. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt zu starten.

	Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

5. Geben Sie wie zuvor einen sinnvollen Text ein, und klicken Sie auf **Hinzufügen**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Services** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
  
   	Beachten Sie, dass die **TodoItem**-Tabelle nun Daten enthält, bei denen einige Werte von Mobile Services generiert wurden, und dass der Tabelle automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt wurden.

Damit ist das Android-Lernprogramm **Erste Schritte mit Daten** abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

Dieses Lernprogramm zeigt die Grundlagen für die Integration von Daten in mobilen Diensten in Android-Apps. 

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Nach Abschluss der Daten-Lernprogramme können Sie diese anderen Android-Lernprogramme ausprobieren:

* [Erste Schritte mit der Authentifizierung]
	<br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Erfahren Sie, wie Sie eine ganz einfache Pushbenachrichtigung mit Mobile Services an Ihre App senden können.

<!-- Anchors. -->
[Herunterladen des Android-App-Projekts]: #download-app
[Erstellen des mobilen Diensts]: #create-service
[Hinzufügen einer Datentabelle als Speicher]: #add-table
[Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
[Testen der App mit Mobile Services]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-android
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-android
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-android
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-android
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-android

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services Android SDK]: http://aka.ms/Iajk6q
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125


<!--HONumber=42-->
