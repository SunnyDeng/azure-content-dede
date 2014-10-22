<properties linkid="mobile-services-javascript-backend-windows-universal-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Universal) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your universal Windows app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Erste Schritte mit Daten in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data][mobile-services-selector-get-started-data]]

Dieses Thema beschreibt den Einsatz von Azure Mobile Services für die Nutzung von Daten in universellen Windows-Apps. Universelle Windows-App-Lösungen beinhalten Projekte für Windows Store 8.1, Windows Phone Store 8.1-Apps und ein gemeinsames, geteiltes Projekt. Weitere Informationen finden Sie unter [Erstellen universeller Windows-Apps, die Windows und Windows Phone als Ziel verwenden][Erstellen universeller Windows-Apps, die Windows und Windows Phone als Ziel verwenden].

In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine universelle Windows-App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in die App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

> [WACOM.NOTE] In diesem Thema wird erläutert, wie Sie mithilfe der in Visual Studio Professional 2013 mit Update 3 enthaltenen Tools einen mobilen Dienst mit einer universellen Windows-App verbinden. Mit denselben Schritten können Sie einen mobilen Dienst mit einer Windows Store- oder einer Windows Phone Store 8.1-App verbinden. Informationen zum Verbinden eines mobilen Diensts mit einer Windows Phone 8.0- oder Windows Phone Silverlight 8.1-App finden Sie unter [Erste Schritte mit Daten für Windows Phone][Erste Schritte mit Daten für Windows Phone].

> Wenn Sie kein Upgrade auf Visual Studio Professional 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version][dieser Version] des Themas weitere Informationen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Windows Store-App-Projekts][Herunterladen des Windows Store-App-Projekts]
2.  [Erstellen des mobilen Diensts in Visual Studio][Erstellen des mobilen Diensts in Visual Studio]
3.  [Erstellen einer Datentabelle als Datenspeicher][Erstellen einer Datentabelle als Datenspeicher]
4.  [Aktualisieren der App für die Verwendung des mobilen Diensts][Aktualisieren der App für die Verwendung des mobilen Diensts]
5.  [Testen der App mit Mobile Services][Testen der App mit Mobile Services]
6.  [Anzeigen der hochgeladenen Daten im Azure-Verwaltungsportal][Anzeigen der hochgeladenen Daten im Azure-Verwaltungsportal]

Für dieses Lernprogramm benötigen Sie Folgendes:

-   Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].
-   [Visual Studio Express 2013 für Windows][Visual Studio Express 2013 für Windows] (Update 2 oder eine höhere Version).

## <a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-download-project][mobile-services-windows-universal-dotnet-download-project]]

## <a name="create-service"></a>Erstellen eines mobilen Diensts in Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][mobile-services-create-new-service-vs2013]]

1.  Öffnen Sie im Projektmappen-Explorer im Projektordner "GetStartedWithData.Shared" die Codedatei "App.xaml.cs". Im Block für die bedingte Kompilierung einer Windows Store-App wurde der **App**-Klasse ein neues statisches Feld hinzugefügt, das dem im folgenden Beispiel entspricht:

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über eine Instanz der [MobileServiceClient-Klasse][MobileServiceClient-Klasse] bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Dieses statische Feld steht allen Seiten in Ihrer App zur Verfügung.

2.  Klicken Sie mit der rechten Maustaste auf das Windows Phone-App-Projekt, klicken Sie auf **Hinzufügen** und dann auf **Verbundener Dienst...**. Wählen Sie anschließend den soeben erstellten mobilen Dienst aus, und klicken Sie auf **OK**.

    Derselbe Code wird der gemeinsam verwendeten Datei "App.xaml.cs" hinzugefügt, diesmal jedoch innerhalb eines Blocks für die bedingte Kompilierung einer Windows Phone-App.

Jetzt sind sowohl die Windows Store- als auch die Windows Phone Store-App mit dem neuen mobilen Dienst verbunden. Im nächsten Schritt wird eine neue TodoItem-Tabelle im mobilen Dienst erstellt.

## <a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][mobile-services-create-new-table-vs2013]]

> [WACOM.NOTE] Neue Tabellen werden mit den Spalten "Id", "\_\_createdAt", "\_\_updatedAt" und "\_\_version" erstellt. Wenn das dynamische Schema aktiviert ist, generieren Mobile Services automatisch neue Spalten auf Grundlage des JSON-Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema][Dynamisches Schema].

# <a name="update-app"></a>Aktualisieren der App für die Verwendung des mobilen Diensts

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app][mobile-services-windows-dotnet-update-data-app]]

## <a name="test-azure-hosted"></a>Testen des in Azure gehosteten mobilen Diensts

Nun können wir beide Versionen der universellen Windows-App mit dem in Azure gehosteten mobilen Dienst testen.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app][mobile-services-windows-universal-test-app]]

1.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

    <p>
2.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

    Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

![][]

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer universellen Windows-App für die Arbeit mit Daten in Mobile Services behandelt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Erstellen universeller Windows-Apps, die Windows und Windows Phone als Ziel verwenden]: http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn609832.aspx
  [Erste Schritte mit Daten für Windows Phone]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-data
  [dieser Version]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Herunterladen des Windows Store-App-Projekts]: #download-app
  [Erstellen des mobilen Diensts in Visual Studio]: #create-service
  [Erstellen einer Datentabelle als Datenspeicher]: #add-table
  [Aktualisieren der App für die Verwendung des mobilen Diensts]: #update-app
  [Testen der App mit Mobile Services]: #test-app
  [Anzeigen der hochgeladenen Daten im Azure-Verwaltungsportal]: #view-data
  [Kostenlose Azure-Testversion]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F
  [Visual Studio Express 2013 für Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-dotnet-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [MobileServiceClient-Klasse]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Dynamisches Schema]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx
  [mobile-services-windows-dotnet-update-data-app]: ../includes/mobile-services-windows-dotnet-update-data-app.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [Verwaltungsportal]: https://manage.windowsazure.com/
  []: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
  [Optimieren von Abfragen mittels Paging]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
