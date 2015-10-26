<properties 
   pageTitle="Zugreifen auf private Azure-Clouds mit Visual Studio"
   description="Erfahren Sie, wie Sie mithilfe von Visual Studio auf private Cloudressourcen zugreifen."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="kempb" />

# Zugreifen auf private Azure-Clouds mit Visual Studio

##Übersicht

Visual Studio unterstützt standardmäßig REST-Endpunkte der öffentlichen Azure-Cloud. Dies kann jedoch ein Problem sein, wenn Sie Visual Studio mit einer privaten Azure-Cloud verwenden. Sie können Zertifikate verwenden, um Visual Studio für den Zugriff auf REST-Endpunkte der privaten Azure-Cloud zu konfigurieren. Sie erhalten diese Zertifikate über Ihre Datei mit den Azure-Veröffentlichungseinstellungen.

## So greifen Sie auf eine private Azure-Cloud in Visual Studio zu

1. Laden Sie im Verwaltungsportal für die private Cloud die Datei mit den Veröffentlichungseinstellungen herunter, oder wenden Sie sich an Ihren Administrator, um eine Datei mit Veröffentlichungseinstellungen zu erhalten. Für die öffentliche Version von Azure ist dies der Link zum Herunterladen: [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (Die herunterzuladende Datei muss die Erweiterung ".publishsettings" aufweisen.)

1. Wählen Sie in Visual Studio im **Server-Explorer** den Knoten **Azure** und dann im Kontextmenü den Befehl **Abonnements verwalten** aus.

    ![Befehl "Abonnement verwalten"](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Klicken Sie im Dialogfeld **Microsoft Azure-Abonnements verwalten** auf die Registerkarte **Zertifikate** und dann auf die Schaltfläche **Importieren**.

    ![Importieren von Azure-Zertifikaten](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Navigieren Sie im Dialogfeld **Microsoft Azure-Abonnements importieren** zum Ordner, in dem Sie die Datei mit den Veröffentlichungseinstellungen gespeichert haben, und klicken Sie dann auf die Schaltfläche **Importieren**. Daraufhin werden die Zertifikate in der Datei mit den Veröffentlichungseinstellungen in Visual Studio importiert. Sie sollten jetzt in der Lage sein, mit privaten Cloudressourcen zu interagieren.

    ![Importieren von Veröffentlichungseinstellungen](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## Nächste Schritte

[Veröffentlichen eines Clouddiensts mit Azure Tools](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Vorgehensweise: Herunterladen und Importieren von Veröffentlichungseinstellungen und Abonnementinformationen] (https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

<!---HONumber=Oct15_HO3-->