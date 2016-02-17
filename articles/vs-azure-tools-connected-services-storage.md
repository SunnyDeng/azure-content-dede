<properties 
   pageTitle="Hinzufügen von Azure Storage mithilfe von verbundenen Diensten in Visual Studio | Microsoft Azure"
   description="Fügen Sie Ihrer App mithilfe des Dialogfelds ";Verbundene Dienste hinzufügen"; in Visual Studio Azure Storage hinzu"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="12/16/2015"
   ms.author="tarcher" />

# Hinzufügen von Azure-Speicher mithilfe von verbundenen Visual Studio-Diensten

## Übersicht

Mit Visual Studio 2015 können Sie beliebige C#-Clouddienste, mobile .NET-Back-End-Dienste, ASP.NET-Websites oder -Dienste, ASP.NET 5-Dienste oder Azure WebJob-Dienste über das Dialogfeld **Verbundene Dienste hinzufügen** mit Azure Storage verbinden. Mit der Funktion für verbundene Dienste werden die benötigten Verweise und der Verbindungscode hinzugefügt und Ihre Konfigurationsdateien entsprechend geändert. Das Dialogfeld leitet Sie auch zur Dokumentation weiter, in der Sie erfahren, was die nächsten Schritte sind, um mit der Verwendung von Blobspeichern, Warteschlangen und Tabellen zu beginnen.

## Unterstützte Projekttypen

Mit dem Dialogfeld "Verbundene Dienste" können Sie in den folgenden Projekttypen eine Verbindung mit Azure Storage herstellen.

- ASP.NET-Webprojekte

- ASP.NET 5-Projekte

- Webrollen- und Workerrollenprojekte für .NET-Clouddienste

- .NET Mobile Services-Projekte

- Azure WebJob-Projekte


## Herstellen einer Verbindung mit Azure Storage mithilfe des Dialogfelds "Verbundene Dienste"

1. Stellen Sie sicher, dass Sie über ein Azure-Konto verfügen. Wenn Sie nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose Testversion](http://go.microsoft.com/fwlink/?LinkId=518146) registrieren. Sobald Sie ein Azure-Konto haben, können Sie Speicherkonten und Mobile Services erstellen und Azure Active Directory konfigurieren.

1. Öffnen Sie das Projekt in Visual Studio, öffnen Sie das Kontextmenü für den Knoten **Verweise** im Projektmappen-Explorer, und wählen Sie dann **Verbundenen Dienst hinzufügen** aus.

    ![Hinzufügen eines verbundenen Diensts](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Wählen Sie im Dialogfeld **Verbundenen Dienst hinzufügen** die Option **Azure Storage** und dann die Schaltfläche **Konfigurieren** aus. Sie werden möglicherweise aufgefordert, sich bei Azure anzumelden, wenn Sie noch nicht angemeldet sind.

    ![Dialogfeld "Verbundenen Dienst hinzufügen" – Speicher](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. Wählen Sie im Dialogfeld **Azure Storage** ein vorhandenes Speicherkonto aus, und klicken Sie dann auf **Hinzufügen**.

    Wenn Sie ein neues Speicherkonto erstellen müssen, fahren Sie mit dem nächsten Schritt fort. Fahren Sie andernfalls mit Schritt 6 fort.

    ![Dialogfeld "Azure Storage"](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. So erstellen Sie ein neues Speicherkonto

    1. Klicken Sie unten im Dialogfeld "Azure Storage" auf **Neues Speicherkonto erstellen**.

    1. Füllen Sie das Dialogfeld **Speicherkonto erstellen** aus, und wählen Sie dann die Schaltfläche **Erstellen** aus.
    
        ![Dialogfeld "Azure Storage"](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        Wenn Sie wieder das Dialogfeld **Azure Storage** aufrufen, wird der neue Speicher in der Liste angezeigt.

    1. Wählen Sie den neuen Speicher in der Liste aus, und klicken Sie auf **Hinzufügen**.

1. Der verbundene Speicherdienst wird unter dem Knoten "Dienstverweise" des WebJob-Projekts angezeigt.

    ![Azure-Speicher im WebJobs-Projekt](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. Überprüfen Sie die Seite mit den ersten Schritten, die angezeigt wird, und finden Sie heraus, wie das Projekt geändert wurde. Eine Seite mit ersten Schritten wird im Browser angezeigt, sobald Sie einen verbundenen Dienst hinzufügen. Sie können die vorgeschlagenen nächsten Schritte und Codebeispiele prüfen oder zur Seite "Was ist passiert" wechseln, um herauszufinden, welche Verweise in Ihrem Projekt hinzugefügt wurden und wie Ihr Code und die Konfigurationsdateien geändert wurden.

## Änderungen am Projekt

Wenn Sie die Bearbeitung des Dialogfelds abschließen, fügt Visual Studio Verweise hinzu und ändert bestimmte Konfigurationsdateien. Die genauen Änderungen hängen vom Projekttyp ab.

 - Weitere Informationen zu ASP.NET-Projekten finden Sie unter [Was ist passiert – ASP.NET-Projekte](http://go.microsoft.com/fwlink/p/?LinkId=513126). 
 - Weitere Informationen zu ASP.NET 5-Projekten finden Sie unter [Was ist passiert – ASP.NET 5-Projekte](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
 - Weitere Informationen zu Clouddienstprojekten (Web- und Workerrollen) finden Sie unter [Was ist passiert – Clouddienstprojekte](http://go.microsoft.com/fwlink/p/?LinkId=516965). 
 - Informationen zu WebJob-Projekten finden Sie unter [Was ist passiert – WebJob-Projekte](vs-storage-webjobs-what-happened/).

## Nächste Schritte

1. Nutzen Sie die Codebeispiele für die ersten Schritte als Leitfaden, um den gewünschten Speichertyp zu erstellen. Beginnen Sie dann mit dem Schreiben von Code für den Zugriff auf Ihr Speicherkonto.

1. Stellen Sie Fragen, und holen Sie sich Hilfe
     - [MSDN Forum: Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Azure Storage-Teamblog](http://blogs.msdn.com/b/windowsazurestorage/)

     - [Speicher auf "azure.microsoft.com"](https://azure.microsoft.com/services/storage/)

     - [Speicherdokumentation auf "azure.microsoft.com"](https://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=AcomDC_0128_2016-->