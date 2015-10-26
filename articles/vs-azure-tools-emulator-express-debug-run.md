<properties
   pageTitle="Verwenden von Emulator Express zum Ausführen und Debuggen eines Clouddiensts auf einem lokalen Computer | Microsoft Azure"
   description="Verwenden von Emulator Express zum Ausführen und Debuggen eines Clouddiensts auf einem lokalen Computer"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="patshea" />


# Verwenden von Emulator Express zum Ausführen und Debuggen eines Clouddiensts auf einem lokalen Computer

Mithilfe von Emulator Express können Sie einen Clouddienst testen und debuggen, ohne Visual Studio als Administrator auszuführen. Abhängig von den Anforderungen Ihres Clouddiensts können Sie die Projekteinstellungen für die Verwendung von Emulator Express oder des vollständigen Emulators festlegen. Weitere Informationen zum vollständigen Emulator finden Sie unter [Ausführen einer Azure-Anwendung im Serveremulator](../storage/storage-use-emulator.md). Emulator Express war erstmals in Azure SDK 2.1 enthalten. Ab Azure SDK 2.3 handelt es sich um den Standardemulator.

## Verwenden von Emulator Express in der Visual Studio IDE

Wenn Sie ein neues Projekt in Azure SDK 2.3 oder höher erstellen, ist Emulator Express bereits ausgewählt. Für vorhandene Projekte, die mit einer früheren Version des SDKs erstellt wurden, führen Sie die folgenden Schritte aus, um Emulator Express auszuwählen.

### So konfigurieren Sie ein Projekt für die Verwendung von Emulator Express

1. Öffnen Sie das Kontextmenü für das Azure-Projekt, wählen Sie **Eigenschaften** und dann die Registerkarte **Web** aus.

1. Wählen Sie unter **Lokaler Bereitstellungsserver** die Schaltfläche **IIS Express verwenden** aus. Emulator Express ist nicht mit dem IIS-Webserver kompatibel.

1. Wählen Sie unter **Emulator** das Optionsfeld **Emulator Express verwenden** aus.

    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## Starten von Emulator Express über eine Eingabeaufforderung

Mithilfe der /useemulatorexpress-Option können Sie die Express-Version des Azure-Compute-Emulators (csrun.exe) über eine Eingabeaufforderung starten.

## Einschränkungen

Bevor Sie Emulator Express verwenden, sollten Sie sich einige Einschränkungen vergegenwärtigen:

- Emulator Express ist nicht mit dem IIS-Webserver kompatibel.

- Der Clouddienst kann mehrere Rollen enthalten, dabei ist jedoch jede Rolle auf eine Instanz beschränkt.

- Der Zugriff auf Portnummern unterhalb von 1000 ist nicht möglich. Wenn Sie beispielsweise einen Authentifizierungsanbieter verwenden, der üblicherweise einen Port unter 1000 verwendet, müssen Sie diesen Wert u. U. in eine Portnummer über 1000 ändern.

- Einschränkungen, die auf den Azure-Compute-Emulator zutreffen, gelten auch für Emulator Express. Sie können z. B. nicht mehr als 50 Rolleninstanzen pro Bereitstellung verwenden. Siehe [Ausführen einer Azure-Anwendung im Serveremulator](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## Nächste Schritte

[Debuggen von Cloud-Diensten.](https://msdn.microsoft.com/library/azure/ee405479.aspx)

<!---HONumber=Oct15_HO3-->