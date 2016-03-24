<properties
	pageTitle="Erste Schritte mit Daten auf Android (JavaScript-Back-End) | Microsoft Azure"
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Android-Apps (JavaScript-Back-End)."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="ricksal"/>

# Hinzufügen von Mobile Services zu einer vorhandenen Android-App (JavaScript-Back-End)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Informationen für die entsprechende Mobile Apps-Version dieses Themas finden Sie unter [Verwenden der Android-Clientbibliothek für Mobile Apps](../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md).

## Zusammenfassung

Dieses Thema beschreibt die Nutzung von Azure Mobile Services zum Hinzufügen von persistenten Daten in einer Android-App. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App, sodass diese Daten in Azure Mobile Services anstatt lokal aktualisiert und gespeichert werden, und melden sich dann beim klassischen Azure-Portal an, um Datenänderungen beim Ausführen der App anzuzeigen.

In diesem Lernprogramm wird ausführlich darauf eingegangen, wie Azure Mobile Services zum Speichern und Abrufen von Daten aus einer Android-App verwendet wird. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für Mobile Services abgenommen werden. Falls Sie noch keine Erfahrung mit mobilen Diensten haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit mobilen Diensten](mobile-services-android-get-started.md) abschließen.

## Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

- ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357).


- das [Azure Mobile Services Android SDK];
- die [integrierte Entwicklungsumgebung Android Studio](https://developer.android.com/sdk/index.html), die das Android SDK enthält, sowie Android 4.2 oder eine höhere Version. Das heruntergeladene GetStartedWithData-Projekt benötigt Android 4.2 oder eine neuere Version. Für das Mobile Services SDK ist Android 2.2 oder eine höhere Version erforderlich.

## Beispielcode

Den vollständigen Quellcode finden Sie [hier](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio).

## Herunterladen des GetStartedWithData-Projekts

###Laden Sie den Beispielcode herunter

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### Prüfen und Ausführen des Beispielcodes

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## Erstellen neuer mobiler Dienste im klassischen Azure-Portal

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## Hinzufügen einer neuen Tabelle zum mobilen Dienst

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## Aktualisieren der App für den Datenzugriff über mobile Dienste

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## Testen der App mit dem neuen mobilen Dienst

Die App verwendet nun mobile Dienste als Back-End-Speicher, und Sie können sie entweder im Android-Emulator oder auf einem Android-Telefon gegen den mobilen Dienst testen.

1. Klicken Sie im Menü **Ausführen** auf **App ausführen**, um das Projekt zu starten.

	Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

5. Geben Sie wie zuvor einen lesbaren Text ein und klicken Sie auf **Hinzufügen**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [klassischen Azure-Portal] auf **Mobile Services** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]

   	Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit ist das Android-Lernprogramm **Erste Schritte mit Daten** abgeschlossen.

## Problembehandlung

### Prüfen der Version des Android-SDK

[AZURE.INCLUDE [Prüfen des SDK](../../includes/mobile-services-verify-android-sdk-version.md)]



## Nächste Schritte

Dieses Lernprogramm zeigt die Grundlagen für die Integration von Daten in mobilen Diensten in Android-Apps. Führen Sie die folgenden anderen Android-Lernprogramme aus:

* [Erste Schritte mit der Authentifizierung](mobile-services-android-get-started-users.md) <br/>Informationen zur Authentifizierung von Benutzern Ihrer App.

* [Erste Schritte mit Pushbenachrichtigungen](mobile-services-javascript-backend-android-get-started-push.md) <br/>Verschicken Sie mithilfe Ihres mobilen Dienstes eine einfache Pushbenachrichtigung an Ihre App.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->

[klassischen Azure-Portal]: https://manage.windowsazure.com/
[Azure Mobile Services Android SDK]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!---HONumber=AcomDC_0309_2016-->