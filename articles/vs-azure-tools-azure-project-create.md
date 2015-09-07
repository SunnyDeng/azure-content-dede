<properties 
   pageTitle="Erstellen eines Azure-Projekts mit Visual Studio"
	description="Erstellen eines Azure-Projekts mit Visual Studio"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee"/>
<tags 
   ms.service="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/24/2015"
	ms.author="kempb"/>

# Erstellen eines Azure-Projekts mit Visual Studio

Die Azure Tools für Visual Studio stellen eine Vorlage zur Verfügung, mit der Sie einen Clouddienst für Azure erstellen können. Die Tools unterstützen Sie außerdem beim Konfigurieren, Debuggen und Bereitstellen des Clouddiensts in Azure.

Eine Azure-Clouddienstlösung enthält die folgenden Projekttypen:

- **Azure-Projekt** 
    
    Das Azure-Projekt weist Zuordnungen zu den Rollenprojekten in der Projektmappe auf. Es schließt auch die Dienstdefinition und die Dienstkonfigurationsdateien ein. Die Dienstdefinitionsdatei definiert die Laufzeiteinstellungen für Ihre Anwendung, u. a. erforderliche Rollen, Endpunkte und Größe des virtuellen Computers. Die Dienstkonfigurationsdatei konfiguriert, wie viele Instanzen einer Rolle ausgeführt werden, und die Werte der für eine Rolle definierten Einstellungen. Weitere Informationen zu diesen Einstellungen finden Sie unter [Vorgehensweise: Konfigurieren der Rollen für einen Azure-Clouddienst mit Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx).

- **Webrollenprojekt**
 
    Eine Workerrolle führt die Hintergrundverarbeitung aus. Eine Workerrolle kann mit Speicherdiensten und anderen internetbasierten Diensten kommunizieren. Eine Workerrolle kann eine beliebige Anzahl von HTTP-, HTTPS- oder TCP-Endpunkten aufweisen.

    - **ASP.NET-Webrolle** zum Erstellen einer ASP.NET-Anwendung mit einem Web-Front-End
    - **ASP.NET MVC5-Webrolle**
    - **ASP.NET MVC4-Webrolle**
    - **ASP.NET MVC3-Webrolle**
    - **Webrolle für WCF-Dienst** zum Erstellen eines WCF-Diensts
    - **Webrolle für Silverlight Business Application** (erfordert Visual Studio 2012)

- **Cacheworkerrolle**

    Eine Rolle, die einen dedizierten Cache für Ihre Anwendung bereitstellt.

- **Workerrolle mit Service Bus-Warteschlange**

    Eine Service Bus-Warteschlange bietet Message Queueing-Funktionen für die Kommunikation mit dem Arbeitsprozess. Weitere Informationen finden Sie unter [Verwenden von Service Bus-Warteschlangen](http://go.microsoft.com/fwlink/?LinkId=260560).

## So erstellen Sie ein Azure-Clouddienstprojekt in Visual Studio

1. Starten Sie Microsoft Visual Studio als Administrator.

1. Wählen Sie auf der Menüleiste **Datei**, **Neu**, **Projekt** aus.

1. Wählen Sie im Bereich **Projekttypen** in den Projektvorlagenknoten "Visual C#" oder "Visual Basic" **Cloud** aus.

1. Wählen Sie im Bereich **Vorlagen** die Option **Azure-Clouddienst** aus.

1. Geben Sie an, mit welcher Version von .NET Framework Sie das Projekt entwickeln möchten.

1. Geben Sie einen Namen und Speicherort für das Projekt und einen Namen für die Projektmappe ein. Klicken Sie auf die Schaltfläche **OK**.

1. Wählen Sie im Dialogfeld **Neues Azure-Projekt** die Rollen aus, die Sie hinzufügen möchten, und klicken Sie auf die Schaltfläche mit dem nach rechts zeigenden Pfeil, um sie der Projektmappe hinzuzufügen. Sie können so viele Rollen wie benötigt hinzufügen.

1. Um eine Rolle umzubenennen, die dem Projekt hinzugefügt wurde, zeigen Sie im Dialogfeld **Neues Azure-Projekt** mit dem Mauszeiger auf die Rolle und klicken rechts neben der Rolle auf das Symbol **Umbenennen**. Sie können auch eine Rolle innerhalb der Projektmappe umbenennen, nachdem sie hinzugefügt wurde.

<!---HONumber=August15_HO9-->