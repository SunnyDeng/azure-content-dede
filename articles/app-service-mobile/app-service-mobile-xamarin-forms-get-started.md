<properties
	pageTitle="Erste Schritte mit Mobile Apps unter Verwendung von Xamarin.Forms"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Apps für die Xamarin.Forms-Entwicklung."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="11/23/2015"
	ms.author="normesta"/>

#Erstellen einer Xamarin.Forms-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Übersicht

Dieses Lernprogramm zeigt Ihnen, wie Sie einen cloudbasierten Back-End-Dienst mithilfe eines mobilen Azure-App-Back-Ends zu einer mobilen Xamarin.Forms-App hinzufügen. Sie erstellen sowohl ein neues mobiles App-Back-End und eine einfache Xamarin.Forms-_Todo list_-App, die App-Daten in Azure speichert.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Apps-Lernprogramme für Xamarin.Forms.

##Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).
 
* [Visual Studio Community 2013] oder höher. Wenn Sie Visual Studio Community 2013 installieren, dann installieren Sie [Xamarin] separat. Sie können die Xamarin-Tools bei der Installation von Visual Studio 2015 installieren.

* Ein Mac, auf dem [Xcode] Version 7.0 oder höher und [Xamarin Studio] installiert ist. Wenn Sie beabsichtigen, Ihre App mit Visual Studio auf einem Windows-Computer zu erstellen, benötigen Sie weiterhin Zugriff auf einen vernetzten Mac.
 
>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](https://tryappservice.azure.com/?appServiceName=mobile) sofort kostenlos eine kurzlebige mobile Starter-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen eines neuen Azure Mobile App-Back-Ends

Führen Sie die folgenden Schritte aus, um ein neues mobiles App-Back-End zu erstellen.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Sie haben nun ein Azure-Back-End für mobile Apps bereitgestellt, das von Ihren mobilen Clientanwendungen verwendet werden kann. Als Nächstes laden Sie ein Serverprojekt für ein einfaches "Aufgabenlisten"-Back-End herunter und veröffentlichen es in Azure.

## Konfigurieren des Serverprojekts

Befolgen Sie die nachstehenden Anweisungen zum Konfigurieren des Serverprojekts für die Verwendung des Node.js- oder .NET-Back-Ends.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

 
## (Optional) Lokales Testen Ihres Back-End-Projekts

Wenn Sie zuvor eine .NET-Back-End-Konfiguration gewählt haben, können Sie das Back-End optional lokal testen.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]


##Herunterladen und Ausführen der Xamarin.Forms-Lösung

Hier stehen Ihnen einige Optionen zur Verfügung. Sie können die Lösung auf einem Mac herunterladen und in Xamarin Studio öffnen, oder Sie können die Lösung auf einem Windows-Computer herunterladen und in Visual Studio öffnen. Sie können auch beide Umgebungen verwenden und zwischen diesen hin- und herwechseln. Beachten Sie Folgendes:

* Es ist einfacher, das iOS-Projekt der Lösung auf einem Mac auszuführen. Sie können Visual Studio bei Bedarf auf Ihrem Windows-Computer verwenden. Allerdings ist dies etwas kompliziert, da Sie eine Verbindung zu einem vernetzten Mac benötigen. Wenn Sie daran interessiert sind, finden Sie Informationen dazu unter [Installation von Xamarin.iOS auf Windows].
* Sie können Android-Projekte auf Ihrem Mac oder Ihrem Windows-Computer ausführen.
* Sie können Windows-Projekte dagegen nur mithilfe von Visual Studio auf einem Windows-Computer ausführen.

Vor diesem Hintergrund fahren wir fort.

 1. Öffnen Sie auf Ihrem Mac oder Ihrem Windows-Computer das [Azure-Portal] in einem Browserfenster.
 2. Klicken Sie auf dem Blatt „Einstellungen“ für Ihre mobile App auf **Erste Schritte** (unter „Mobil“) > **Xamarin.Forms**. Klicken Sie unter Schritt 3 auf **Neue App erstellen**, falls noch nicht ausgewählt. Klicken Sie dann auf die Schaltfläche **Herunterladen**.

    Dadurch wird ein Projekt heruntergeladen, das eine Clientanwendung enthält, die mit der mobilen App verbunden ist. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

 3. Extrahieren Sie das heruntergeladene Projekt, und öffnen Sie es dann in Xamarin Studio oder Visual Studio.

	![][9]

	![][8]

##(Optional) Ausführen des iOS-Projekts

Dieser Abschnitt bezieht sich auf das Ausführen des Xamarin iOS-Projekts für iOS-Geräte. Wenn Sie nicht mit iOS-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

####In Xamarin Studio

1. Klicken Sie mit der rechten Maustaste auf das iOS-Projekt, und klicken Sie dann auf **Als Startprojekt festlegen**.
2. Klicken Sie im Menü **Ausführen** auf **Debuggen starten**, um das Projekt zu erstellen und die App im iPhone-Simulator zu starten.

####In Visual Studio
1. Klicken Sie mit der rechten Maustaste auf das iOS-Projekt, und klicken Sie dann auf **Als Startprojekt festlegen**.
2. Klicken Sie im Menü **Build** auf **Konfigurations-Manager**. 
3. Aktivieren Sie im Dialogfeld **Konfigurationsmanager** die Kontrollkästchen **Erstellen** und **Bereitstellen** des iOS-Projekts.
4. Drücken Sie **F5**, um das Projekt zu erstellen und die App im iPhone-Simulator zu starten.

Geben Sie in der App sinnvollen Text ein, z. B. _Xamarin kennenlernen_, und klicken Sie dann auf die Schaltfläche **+**.

![][10]

Dadurch wird eine POST-Anforderung an das neue in Azure gehostete mobile App-Back-End gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen App-Back-End zurückgegeben, und die Daten werden in der Liste angezeigt.

> [AZURE.NOTE]Sie finden den Code, der auf Ihr mobiles App-Back-End zugreift, in der C#-Datei „TodoItemManager.cs“ des Portable Class Library-Projekts Ihrer Projektmappe.

##(Optional) Ausführen des Android-Projekts

Dieser Abschnitt bezieht sich auf das Ausführen des Xamarin Android-Projekts für Android. Wenn Sie nicht mit Android-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

####In Xamarin Studio

1. Klicken Sie mit der rechten Maustaste auf das Android-Projekt, und klicken Sie dann auf **Als Startprojekt festlegen**.
2. Klicken Sie im Menü **Ausführen** auf **Debuggen starten**, um das Projekt zu erstellen und die App im Android-Emulator zu starten.

####In Visual Studio
1. Klicken Sie mit der rechten Maustaste auf das Android-Projekt, und klicken Sie dann auf **Als Startprojekt festlegen**.
4. Klicken Sie im Menü **Build** auf **Konfigurations-Manager**. 
5. Aktivieren Sie im Dialogfeld **Konfigurationsmanager** die Kontrollkästchen **Erstellen** und **Bereitstellen** des Android-Projekts.
6. Drücken Sie **F5**, um das Projekt zu erstellen und die App in einem Android-Emulator zu starten.

Geben Sie in der App sinnvollen Text ein, z. B. _Xamarin kennenlernen_, und klicken Sie dann auf die Schaltfläche **+**.

![][11]

Dadurch wird eine POST-Anforderung an das neue in Azure gehostete mobile App-Back-End gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen App-Back-End zurückgegeben, und die Daten werden in der Liste angezeigt.

> [AZURE.NOTE]Sie finden den Code, der auf Ihr mobiles App-Back-End zugreift, in der C#-Datei „TodoItemManager.cs“ des Portable Class Library-Projekts Ihrer Projektmappe.


##(Optional) Ausführen des Windows-Projekts


Dieser Abschnitt bezieht sich auf das Ausführen des Xamarin WinApp-Projekts für Windows-Geräte. Wenn Sie nicht mit Windows-Geräten arbeiten, können Sie diesen Abschnitt überspringen.


####In Visual Studio
1. Klicken Sie mit der rechten Maustaste auf eines der Windows-Projekte, und klicken Sie dann auf **Als Startprojekt festlegen**.
4. Klicken Sie im Menü **Build** auf **Konfigurations-Manager**. 
5. Aktivieren Sie im Dialogfeld **Konfigurationsmanager** die Kontrollkästchen **Erstellen** und **Bereitstellen** des ausgewählten Windows-Projekts.
6. Drücken Sie **F5**, um das Projekt zu erstellen und die App im Windows-Emulator zu starten.

Geben Sie in der App sinnvollen Text ein, z. B. _Xamarin kennenlernen_, und klicken Sie dann auf die Schaltfläche **+**.
	
Dadurch wird eine POST-Anforderung an das neue in Azure gehostete mobile App-Back-End gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden vom mobilen App-Back-End zurückgegeben, und die Daten werden in der Liste angezeigt.

![][12]
	
> [AZURE.NOTE]Sie finden den Code, der auf Ihr mobiles App-Back-End zugreift, in der C#-Datei „TodoItemManager.cs“ des Portable Class Library-Projekts Ihrer Projektmappe.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure-Portal]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installation von Xamarin.iOS auf Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
 

<!---HONumber=AcomDC_1203_2015--->