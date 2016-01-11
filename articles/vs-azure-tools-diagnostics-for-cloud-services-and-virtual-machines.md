<properties
   pageTitle="Konfigurieren der Diagnose für Azure Cloud Services und Virtual Machines | Microsoft Azure"
   description="Beschreibt, wie Sie Diagnoseinformationen für das Debuggen von Azure-Clouddiensten und virtuellen Computern in Visual Studio konfigurieren müssen."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/19/2015"
   ms.author="tarcher" />

# Konfigurieren der Diagnose für Azure Cloud Services und Virtual Machines

Wenn Sie Probleme bei einem Azure-Clouddienst oder virtuellen Azure-Computer beheben müssen, können Sie mithilfe von Visual Studio Azure-Diagnose leichter konfigurieren. Azure-Diagnose erfasst Systemdaten und Protokollierungsdaten auf den virtuellen Computern und den virtuellen Computerinstanzen, auf denen der Clouddienst ausgeführt wird, und überträgt die Daten in ein Speicherkonto Ihrer Wahl. Weitere Informationen zur Diagnoseprotokollierung in Azure finden Sie unter [Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service](web-sites-enable-diagnostic-log.md).

In diesem Thema wird das Aktivieren und Konfigurieren von Azure-Diagnose in Visual Studio vor und nach der Bereitstellung sowie auf virtuellen Azure-Computern veranschaulicht. Sie erfahren auch, wie Sie die Arten von Diagnoseinformationen, die Sie erfassen möchten, auswählen und die Informationen nach der Erfassung anzeigen können.

Sie können Azure-Diagnose folgendermaßen konfigurieren:

- Sie können die Diagnosekonfigurationseinstellungen im Dialogfeld **Diagnosekonfiguration** in Visual Studio ändern. Die Einstellungen werden in einer Datei namens "diagnostics.wadcfgx" ("diagnostics.wadcfg" in Azure SDK 2.4 oder früher) gespeichert. Alternativ können Sie die Konfigurationsdatei direkt ändern. Wenn Sie die Datei manuell aktualisieren, werden die Änderungen bei der nächsten Bereitstellung des Clouddiensts in Azure oder bei der nächsten Ausführung des Diensts im Emulator wirksam.

- Verwenden Sie **Cloud Explorer** oder **Server-Explorer** in Visual Studio, um die Diagnoseeinstellungen für einen ausgeführten Clouddienst oder virtuellen Computer zu ändern.

## Änderungen an Azure 2.6-Diagnose

Für Azure SDK 2.6-Projekte in Visual Studio wurden die folgenden Änderungen vorgenommen. (Diese Änderungen gelten auch für spätere Versionen des Azure SDK.)

- Der lokale Emulator unterstützt jetzt die Diagnose. Dies bedeutet, dass Sie Diagnosedaten erfassen und sicherstellen können, dass Ihre Anwendung die richtigen Ablaufverfolgungen erstellt, während Sie Ihre Entwicklung und Tests in Visual Studio durchführen. Die Verbindungszeichenfolge `UseDevelopmentStorage=true` aktiviert das Sammeln von Diagnosedaten, während Sie Ihr Clouddienstprojekt in Visual Studio mithilfe des Azure-Speicheremulators ausführen. Alle Diagnosedaten werden im Speicherkonto (Development Storage) gesammelt.

- Die Verbindungszeichenfolge für das Diagnosespeicherkonto (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) wird erneut in der Dienstkonfigurationsdatei (.cscfg) gespeichert. In Azure SDK 2.5 wurde das Diagnosespeicherkonto in der Datei "diagnostics.wadcfgx" angegeben.

Es gibt einige wichtige Unterschiede zwischen der Funktion der Verbindungszeichenfolge in Azure SDK 2.4 und früher und in Azure SDK 2.6 und später.

- Bis Azure SDK 2.4 wurde die Verbindungszeichenfolge vom Diagnose-Plug-In als Laufzeit verwendet, um Informationen zum Speicherkonto für die Übertragung der Diagnoseprotokolle abzurufen.

- Ab Azure SDK 2.6 wird die Diagnoseverbindungszeichenfolge von Visual Studio während der Veröffentlichung zum Konfigurieren der Diagnoseerweiterung mit den entsprechenden Speicherkontoinformationen verwendet. Mit der Verbindungszeichenfolge können Sie die verschiedenen Speicherkonten für verschiedene Dienstkonfigurationen definieren, die Visual Studio beim Veröffentlichen verwendet. Da das Diagnose-Plug-In (nach Azure SDK 2.5) nicht mehr verfügbar ist, kann jedoch die CSCFG-Datei allein die Diagnoseerweiterung nicht aktivieren. Sie müssen die Erweiterung mithilfe von Tools wie Visual Studio oder PowerShell separat aktivieren.

- Um die Konfiguration der Diagnoseerweiterung mit PowerShell zu vereinfachen, enthält die Paketausgabe von Visual Studio auch die öffentliche Konfigurations-XML für die Diagnoseerweiterung jeder Rolle. Visual Studio verwendet die Diagnoseverbindungszeichenfolge zum Ausfüllen der Speicherkontoinformationen, die in der öffentlichen Konfiguration vorhanden sind. Die öffentlichen Konfigurationsdateien werden im Extensions-Ordner erstellt und haben das folgende Namensgebungsmuster: "PaaSDiagnostics.<RoleName>. PubConfig.xml". Dieses Muster kann von allen PowerShell-basierten Bereitstellungen verwendet werden, um die einzelnen Konfigurationen einer Rolle zuzuordnen.

- Die Verbindungszeichenfolge in der CSCFG-Datei wird auch im Azure-Portal verwendet, um auf die Diagnosedaten zuzugreifen, sodass sie in der Registerkarte **Überwachung** angezeigt werden kann. Die Verbindungszeichenfolge ist erforderlich, um den Dienst so zu konfigurieren, dass ausführliche Überwachungsdaten im Portal angezeigt werden.

## Migrieren von Projekten zu Azure SDK 2.6 und höher

Wenn beim Migrieren von Azure SDK 2.5 zu Azure SDK 2.6 oder höher in der WADCFGX-Datei ein Diagnosespeicherkonto angegeben ist, dann bleibt der Speicherort unverändert. Um von der Flexibilität zu profitieren, verschiedene Speicherkonten für unterschiedliche Speicherkonfigurationen verwenden zu können, müssen Sie die Verbindungszeichenfolge manuell dem Projekt hinzufügen. Wenn Sie ein Projekt von Azure SDK 2.4 oder früher zu Azure SDK 2.6 migrieren, werden die Diagnoseverbindungszeichenfolgen beibehalten. Beachten Sie jedoch die Änderungen im Hinblick auf die Handhabung von Verbindungszeichenfolgen in Azure SDK 2.6, wie im vorherigen Abschnitt beschrieben.

### Bestimmung des Diagnosespeicherkontos durch Visual Studio

- Wenn eine Diagnoseverbindungszeichenfolge in der CSCFG-Datei angegeben ist, wird es von Visual Studio beim Veröffentlichen und beim Generieren der öffentlichen XML-Konfigurationsdateien beim Verpacken zur Konfiguration der Diagnoseerweiterung verwendet.

- Wenn keine Diagnoseverbindungszeichenfolge in der CSCFG-Datei angegeben ist, verwendet Visual Studio beim Veröffentlichen und beim Generieren der öffentlichen XML-Konfigurationsdateien beim Verpacken wieder das in der WADCFGX-Datei angegebene Speicherkonto zur Konfiguration der Diagnoseerweiterung.

- Die Diagnoseverbindungszeichenfolge in der CSCFG-Datei hat Vorrang vor dem in der WADCFGSX-Datei angegebenen Speicherkonto. Wenn eine Diagnoseverbindungszeichenfolge in der CSCFG-Datei angegeben ist, verwendet Visual Studio diese und ignoriert das Speicherkonto in der WADCFGX-Datei.

### Wozu dient das Kontrollkästchen "Verbindungszeichenfolgen für Entwicklungsspeicher...aktualisieren"?

Das Kontrollkästchen **Verbindungszeichenfolgen für Entwicklungsspeicher zur Diagnose und zum Zwischenspeichern beim Veröffentlichen in Microsoft Azure mit Anmeldeinformationen für Microsoft Azure-Speicherkonto aktualisieren** ist eine bequeme Möglichkeit, um alle Verbindungszeichenfolgen für ein Entwicklungsspeicherkonto mit dem Azure-Speicherkonto zu aktualisieren, das während der Veröffentlichung angegeben wird.

Beispiel: Sie aktivieren dieses Kontrollkästchen, und die Diagnoseverbindungszeichenfolge gibt `UseDevelopmentStorage=true` an. Wenn Sie das Projekt in Azure veröffentlichen, aktualisiert Visual Studio automatisch die Diagnoseverbindungszeichenfolge mit dem Speicherkonto, das Sie im Webpublishing-Assistenten angegeben haben. Wenn jedoch ein echtes Speicherkonto als Diagnoseverbindungszeichenfolge angegeben wurde, wird stattdessen dieses Konto verwendet.

## Unterschiede zwischen Diagnosefunktionen in Azure SDK 2.4 und früher und Azure SDK 2.5 und später

Wenn Sie für Ihr Projekt ein Upgrade von Azure SDK 2.4 auf Azure SDK 2.5 oder höher durchführen, sollten Sie die folgenden Diagnosefunktionsunterschiede beachten.

- **Konfigurations-APIs sind veraltet** – In Azure SDK 2.4 oder früheren Versionen ist eine programmgesteuerte Konfiguration der Diagnose verfügbar; diese Funktion ist jedoch in Azure SDK 2.5 und höher veraltet. Wenn die Diagnosekonfiguration derzeit im Code definiert ist, müssen Sie diese Einstellungen im Projekt von Grund auf neu konfigurieren, damit die Diagnose weiterhin funktioniert. Die Diagnosekonfigurationsdatei für Azure SDK 2.4 ist "diagnostics.wadcfg"; in Azure SDK 2.5 oder höher hat sie den Namen "diagnostics.wadcfgx".

- **Diagnose für Clouddienstanwendungen kann nur auf Rollenebene, nicht auf der Instanzebene konfiguriert werden.**

- **Jedes Mal, wenn Sie Ihre App bereitstellen, wird die Diagnosekonfiguration aktualisiert** – Dies kann Paritätsprobleme verursachen, wenn Sie die Diagnosekonfiguration im Server-Explorer ändern und anschließend die App erneut bereitstellen.

- **In Azure SDK 2.5 und höher werden Absturzabbilder in der Diagnosekonfigurationsdatei und nicht im Code konfiguriert** – Wenn Sie Absturzabbilder im Code konfiguriert haben, müssen Sie die Konfiguration manuell vom Code in die Konfigurationsdatei übertragen, da die Absturzabbilder während der Migration zu Azure SDK 2.6 nicht übertragen werden.

## Aktivieren der Diagnose in Clouddienstprojekten vor der Bereitstellung

In Visual Studio können Sie Diagnosedaten für Rollen erfassen, die in Azure ausgeführt werden, wenn Sie den Dienst im Emulator ausführen, bevor Sie ihn bereitstellen. Alle Änderungen an den Diagnoseeinstellungen in Visual Studio werden in der Konfigurationsdatei "diagnostics.wadcfgx" gespeichert. Diese Konfigurationseinstellungen geben das Speicherkonto an, in dem die Diagnosedaten bei der Bereitstellung des Clouddiensts gespeichert werden.

### So aktivieren Sie die Diagnose in Visual Studio vor der Bereitstellung

1. Wählen Sie im Kontextmenü für die Rolle, die Sie konfigurieren möchten, die Option **Eigenschaften**, und wählen Sie dann die Registerkarte **Konfiguration** im Fenster **Eigenschaften** der Rolle aus.

1. Stellen Sie im Abschnitt **Diagnose** sicher, dass das Kontrollkästchen **Diagnose aktivieren** aktiviert ist.

    ![Zugreifen auf die Option "Diagnose aktivieren"](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)

1. Wählen Sie die Schaltfläche mit dem Auslassungszeichen (...), um das Speicherkonto anzugeben, in dem die Diagnosedaten gespeichert werden sollen. Das ausgewählte Speicherkonto ist der Speicherort, an dem die Diagnosedaten gespeichert werden.

    ![Angeben des zu verwendenden Speicherkontos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)

1. Geben Sie im Dialogfeld **Verbindungszeichenfolge für den Speicher erstellen** an, ob Sie die Verbindung mit dem Azure-Speicheremulator, einem Azure-Abonnement oder mit manuell eingegebenen Anmeldeinformationen herstellen möchten.

    ![Dialogfeld "Speicherkonto"](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)

  - Bei Auswahl der Option "Microsoft Azure-Speicheremulator" wird die Verbindungszeichenfolge auf "UseDevelopmentStorage = True" festgelegt.

  - Wenn Sie die Option "Eigenes Abonnement" wählen, können Sie das gewünschte Azure-Abonnement und den Kontonamen auswählen. Sie können auf die Schaltfläche "Konten verwalten" klicken, um Ihre Azure-Abonnements zu verwalten.

  - Wenn Sie die Option "Manuell eingegebene Anmeldeinformationen" auswählen, werden Sie aufgefordert, den Namen und Schlüssel des gewünschten Azure-Kontos einzugeben.

1. Wählen Sie die Schaltfläche **Konfigurieren** aus, um das Dialogfeld **Diagnosekonfiguration** anzuzeigen. Jede Registerkarte (mit Ausnahme von **Allgemein** und **Protokollverzeichnisse**) stellt eine Quelle für Diagnosedaten dar, die Sie sammeln können. Die Standardregisterkarte **Allgemein** enthält die folgenden Optionen für die Diagnosedatensammlung: **Nur Fehler**, **Alle Informationen** und **Benutzerdefinierter Plan**. Die Standardoption **Nur Fehler** benötigt am wenigsten Speicherplatz, da keine Warnungen oder Ablaufverfolgungsmeldungen übertragen werden. Die Option "Alle Informationen" überträgt die meisten Informationen und ist daher die teuerste Option in Bezug auf Speicher.

    ![Aktivieren der Azure-Diagnose und Konfiguration](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

1. Wählen Sie für dieses Beispiel die Option **Benutzerdefinierter Plan**, um die gesammelten Daten anzupassen.

1. Über das Feld **Datenträgerkontingent in MB** können Sie festlegen, wie viel Speicherplatz Sie in Ihrem Speicherkonto für Diagnosedaten zuordnen möchten. Sie können den Standardwert bei Bedarf ändern.

1. Wählen Sie auf jeder Registerkarte der erfassten Diagnosedaten das Kontrollkästchen **Übertragung von <log type> aktivieren** aus. Wählen Sie beispielsweise das Kontrollkästchen **Übertragung von Anwendungsprotokollen aktivieren** auf der Registerkarte **Anwendungsprotokolle** aus, wenn Sie die Anwendungsprotokolle erfassen möchten. Geben Sie auch alle andere Informationen an, die für die einzelnen Diagnosedatentypen erforderlich sind. Die Konfigurationsangaben für jede Registerkarte finden Sie nachfolgend im Abschnitt **Konfigurieren von Diagnosedatenquellen**.

1. Nachdem Sie die Sammlung aller gewünschten Diagnosedaten aktiviert haben, wählen Sie die Schaltfläche **OK** aus.

1. Führen Sie das Azure-Clouddienstprojekt wie gewohnt in Visual Studio aus. Beim Verwenden der Anwendung werden die Protokollinformationen, die Sie aktiviert haben, im angegebenen Azure-Speicherkonto gespeichert.

## Aktivieren der Diagnose auf virtuellen Azure-Computern

In Visual Studio können Sie festlegen, dass Diagnosedaten für virtuelle Azure-Computer gesammelt werden.

### So aktivieren Sie die Diagnose auf virtuellen Azure-Computern

1. Wählen Sie im **Server-Explorer** den Azure-Knoten, und stellen Sie dann eine Verbindung mit Ihrem Azure-Abonnement her, wenn noch keine Verbindung vorliegt.

1. Erweitern Sie den Knoten **Virtuelle Computer**. Sie können einen neuen virtuellen Computer erstellen oder einen bereits vorhandenen auswählen.

1. Wählen Sie im Kontextmenü für den gewünschten virtuellen Computer die Option **Konfigurieren** aus. Daraufhin wird das Dialogfeld zur Konfiguration des virtuellen Computers angezeigt.

    ![Konfigurieren eines virtuellen Azure-Computers](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)

1. Fügen Sie die Erweiterung "Microsoft Monitoring Agent-Diagnose" hinzu, wenn sie noch nicht installiert wurde. Mit dieser Erweiterung können Sie Diagnosedaten für virtuelle Azure-Computer erfassen. Wählen Sie in der Liste "Installierte Erweiterung" das Dropdownmenü "Verfügbare Erweiterung auswählen" und dann "Microsoft Monitoring Agent-Diagnose" aus.

    ![Installieren einer virtuellen Azure-Computererweiterung](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)

    >[AZURE.NOTE]Für Ihre virtuellen Computer sind noch andere Diagnoseerweiterungen verfügbar. Weitere Informationen finden Sie unter "Azure VM-Erweiterungen".

1. Wählen Sie die Schaltfläche **Hinzufügen**, um die Erweiterung hinzuzufügen und das Dialogfeld **Diagnosekonfiguration** anzuzeigen.

1. Wählen Sie die Schaltfläche **Konfigurieren**, um ein Speicherkonto anzugeben, und klicken Sie dann auf **OK**.

    Jede Registerkarte (mit Ausnahme von **Allgemein** und **Protokollverzeichnisse**) stellt eine Diagnosedatenquelle dar, die Sie erfassen können.

    ![Aktivieren der Azure-Diagnose und Konfiguration](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

    Die Standardregisterkarte **Allgemein** enthält die folgenden Optionen für die Diagnosedatensammlung: **Nur Fehler**, **Alle Informationen** und **Benutzerdefinierter Plan**. Die Standardoption **Nur Fehler** benötigt am wenigsten Speicherplatz, da keine Warnungen oder Ablaufverfolgungsmeldungen übertragen werden. Die Option **Alle Informationen** überträgt die meisten Informationen und ist daher die teuerste Option in Bezug auf Speicher.

1. Wählen Sie für dieses Beispiel die Option **Benutzerdefinierter Plan**, um die gesammelten Daten anzupassen.

1. Über das Feld **Datenträgerkontingent in MB** können Sie festlegen, wie viel Speicherplatz Sie in Ihrem Speicherkonto für Diagnosedaten zuordnen möchten. Sie können den Standardwert bei Bedarf ändern.

1. Wählen Sie auf jeder Registerkarte für Diagnosedaten, die Sie sammeln möchten, das Kontrollkästchen **Übertragung von <log type> aktivieren** aus.

    Wählen Sie beispielsweise die Kontrollkästchen **Übertragung von Anwendungsprotokollen aktivieren** auf der Registerkarte **Anwendungsprotokolle** aus, wenn Sie Anwendungsprotokolle erfassen möchten. Geben Sie auch alle andere Informationen an, die für die einzelnen Diagnosedatentypen erforderlich sind. Die Konfigurationsangaben für jede Registerkarte finden Sie nachfolgend im Abschnitt **Konfigurieren von Diagnosedatenquellen**.

1. Nachdem Sie die Sammlung aller gewünschten Diagnosedaten aktiviert haben, wählen Sie die Schaltfläche **OK** aus.

1. Speichern Sie das aktualisierte Projekt.

    Es wird eine Meldung im Fenster **Microsoft Azure-Aktivitätsprotokoll** angezeigt, dass der virtuelle Computer aktualisiert wurde.

## Konfigurieren der Diagnosedatenquellen

Nachdem Sie die Sammlung von Diagnosedaten aktiviert haben, können Sie genau auswählen, welche Datenquellen Sie sammeln möchten und welche Informationen erfasst werden. Im Folgenden finden Sie eine Liste der Registerkarten im Dialogfeld **Diagnosekonfiguration** sowie eine Beschreibung der einzelnen Konfigurationsoptionen.

### Anwendungsprotokolle

**Anwendungsprotokolle** enthalten Diagnoseinformationen, die von einer Webanwendung erstellt wurden. Wählen Sie das Kontrollkästchen **Übertragung von Anwendungsprotokollen aktivieren** aus, wenn Sie Anwendungsprotokolle erfassen möchten. Sie können die Anzahl der Minuten erhöhen oder verringern, in denen die Anwendungsprotokolle an Ihr Speicherkonto übertragen werden, indem Sie den Wert für **Übertragungszeitraum (Min.)** ändern. Sie können auch die Menge an Informationen ändern, die im Protokoll aufgezeichnet wird, indem Sie den Wert "Protokollierungsebene" festlegen. Sie können z. B. **Ausführlich** auswählen, um mehr Informationen zu erhalten, oder wählen Sie **Kritisch**, um nur kritische Fehler zu erfassen. Wenn Sie einen bestimmten Diagnoseanbieter haben, der Anwendungsprotokolle ausgibt, können sie diese erfassen, indem Sie die Anbieter-GUID in das Feld **Anbieter GUID** Feld einfügen.

  ![Anwendungsprotokolle](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  Weitere Informationen zu Anwendungsprotokollen finden Sie unter [Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service](web-sites-enable-diagnostic-log.md).

### Windows-Ereignisprotokolle

Wählen Sie das Kontrollkästchen **Übertragung von Windows-Ereignisprotokollen aktivieren** aus, wenn Sie Windows-Ereignisprotokolle erfassen möchten. Sie können die Anzahl der Minuten erhöhen oder verringern, in denen die Ereignisprotokolle an Ihr Speicherkonto übertragen werden, indem Sie den Wert für **Übertragungszeitraum (Min.)** ändern. Wählen Sie die Kontrollkästchen für die Ereignistypen aus, die Sie nachverfolgen möchten.

  ![Ereignisprotokolle](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Wenn Sie Azure SDK 2.6 oder höher verwenden und eine benutzerdefinierte Datenquelle angeben möchten, geben Sie sie in das Textfeld **<Data source name>** ein, und wählen Sie dann die Schaltfläche**Hinzufügen** daneben aus. Die Datenquelle wird der Datei "diagnostics.cfcfg" hinzugefügt.

Wenn Sie Azure SDK 2.5 verwenden und eine benutzerdefinierte Datenquelle angeben möchten, können Sie sie dem Abschnitt "`WindowsEventLog`" in der Datei "diagnostics.wadcfgx" hinzufügen, wie im folgenden Beispiel gezeigt.

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### Leistungsindikatoren

Anhand von Leistungsindikatorinformationen können Sie nach Engpässen im System suchen und die System- und Anwendungsleistung optimieren. Weitere Informationen finden Sie unter [Erstellen und Verwenden von Leistungsindikatoren in einer Azure-Anwendung](https://msdn.microsoft.com/library/azure/hh411542.aspx). Wenn Sie Leistungsindikatoren erfassen möchten, wählen Sie das Kontrollkästchen **Übertragung der Leistungsindikatoren aktivieren** aus. Sie können die Anzahl der Minuten erhöhen oder verringern, in denen die Ereignisprotokolle an Ihr Speicherkonto übertragen werden, indem Sie den Wert für **Übertragungszeitraum (Min.)** ändern. Wählen Sie die Kontrollkästchen für die Leistungsindikatoren aus, die Sie nachverfolgen möchten.

  ![Leistungsindikatoren](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Um einen Leistungsindikator nachzuverfolgen, der nicht aufgelistet ist, geben Sie ihn mit der vorgeschlagenen Syntax ein, und wählen Sie dann die Schaltfläche **Hinzufügen** aus. Das Betriebssystem auf dem virtuellen Computer bestimmt, welche Leistungsindikatoren Sie nachverfolgen können. Weitere Informationen zur Syntax finden Sie unter [Angeben eines Indikatorpfads](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### Infrastrukturprotokolle

Wenn Sie Infrastrukturprotokolle erfassen möchten, die Informationen zur Azure-Diagnoseinfrastruktur, zum RemoteAccess-Modul und zum RemoteForwarder-Modul enthalten, wählen Sie das Kontrollkästchen **Übertragung von Infrastrukturprotokollen aktivieren** aus. Sie können die Anzahl der Minuten erhöhen oder verringern, in denen die Protokolle an Ihr Speicherkonto übertragen werden, indem Sie den Wert für "Übertragungszeitraum (Min.)" ändern.

  ![Diagnoseinfrastrukturprotokolle](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  Weitere Informationen finden Sie unter [Sammeln von Protokollierungsdaten mit der Azure-Diagnose](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### Protokollverzeichnisse

Wenn Sie Protokollverzeichnisse erfassen möchten, die Daten aus Protokollverzeichnissen für Internet Information Services (IIS)-Anforderungen, fehlgeschlagene Anforderungen oder von Ihnen ausgewählten Ordnern enthalten, wählen Sie das Kontrollkästchen **Übertragung von Protokollverzeichnissen aktivieren** aus. Sie können die Anzahl der Minuten erhöhen oder verringern, in denen die Protokolle an Ihr Speicherkonto übertragen werden, indem Sie den Wert für **Übertragungszeitraum (Min.)** ändern.

Sie können die Kästchen der Protokolle, die Sie erfassen möchten, auswählen, z. B. **IIS-Protokolle** und **Protokolle zu fehlerhaften Anforderungen**. Es werden standardmäßige Speichercontainernamen angegeben; Sie können die Namen jedoch auf Wunsch hin ändern.

Darüber hinaus können Sie die Protokolle aus einem beliebigen Ordner erfassen. Geben Sie nur den Pfad im Abschnitt **Protokoll aus absolutem Verzeichnis** an, und wählen Sie dann die Schaltfläche **Verzeichnis hinzufügen** aus. Die Protokolle werden in den angegebenen Containern erfasst.

  ![Protokollverzeichnisse](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### ETW-Protokolle

Wenn Sie [Ereignisablaufverfolgung für Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803(v=vs.85).aspx) (ETW) verwenden und ETW-Protokolle erfassen möchten, wählen Sie das Kontrollkästchen **Übertragung von ETW-Protokollen aktivieren** aus. Sie können die Anzahl der Minuten erhöhen oder verringern, in denen die Protokolle an Ihr Speicherkonto übertragen werden, indem Sie den Wert für **Übertragungszeitraum (Min.)** ändern.

Die Ereignisse werden von den Ereignisquellen und Ereignismanifesten erfasst, die Sie angeben. Um eine Ereignisquelle anzugeben, geben Sie einen Namen im Abschnitt **Ereignisquellen** ein, und wählen Sie dann die Schaltfläche **Ereignisquelle hinzufügen** aus. Gleichermaßen können Sie ein Ereignismanifest im Abschnitt **Ereignismanifeste** festlegen und dann die Schaltfläche **Ereignismanifest hinzufügen** auswählen.

  ![ETW-Protokolle](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  Das ETW-Framework wird in ASP.NET durch Klassen im [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)-Namespace unterstützt. Der Microsoft.WindowsAzure.Diagnostics-Namespace, der von [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)-Standardklassen erbt und diese erweitert, ermöglicht die Verwendung von [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) als Protokollierungsframework in der Azure-Umgebung. Weitere Informationen finden Sie unter [Protokollierung und Ablaufverfolgung in Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) und [Aktivieren der Diagnose in Azure Cloud Services und Virtual Machines](cloud-services-dotnet-diagnostics.md).

### Absturzabbilder

Wenn Sie Informationen zum Absturz einer Rolleninstanz erfassen möchten, wählen Sie das Kontrollkästchen **Übertragung von Absturzabbildern aktivieren** aus. (Da die meisten Ausnahmen von ASP.NET verarbeitet werden, ist dies im Allgemeinen nur für Workerrollen von nutzen.) Sie können den Prozentsatz des Speicherplatzes erhöhen oder verringern, der für die Absturzabbilder reserviert ist, indem Sie den Wert ** Verzeichniskontingent (%)** ändern. Sie können den Speichercontainer ändern, in dem die Absturzabbilder gespeichert werden, und Sie können angeben, ob Sie ein **vollständiges Abbild** oder ein **Miniabbild** erfassen möchten.

Die derzeit nachverfolgten Prozesse werden aufgelistet. Wählen Sie die Kontrollkästchen für die Prozesse aus, die Sie erfassen möchten. Um der Liste einen anderen Prozess hinzuzufügen, geben Sie den Namen des Prozesses ein, und wählen Sie dann die Schaltfläche **Prozess hinzufügen** aus.

  ![Absturzabbilder](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  Weitere Informationen finden Sie unter [Protokollierung und Ablaufverfolgung in Windows Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) und [Microsoft Azure-Diagnose Teil 4: Benutzerdefinierte Protokollierungskomponenten und Änderungen an Azure-Diagnose 1.3](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## Anzeigen von Diagnosedaten

Nachdem Sie die Diagnosedaten für einen Clouddienst oder einen virtuellen Computer gesammelt haben, können Sie sie anzeigen.

### So zeigen Sie die Clouddienst-Diagnoseinformationen an

1. Stellen Sie Ihren Clouddienst wie gewohnt bereit, und führen Sie ihn dann aus.

1. Sie können die Diagnosedaten in einem Bericht, der von Visual Studio generiert wird, oder in Tabellen in Ihrem Speicherkonto anzeigen. Öffnen Sie zum Anzeigen der Daten in einem Bericht **Cloud Explorer** oder **Server-Explorer**, rufen Sie dann das Kontextmenü des Knotens für die gewünschte Rolle auf, und wählen Sie **Diagnosedaten anzeigen** aus.

    ![Anzeigen von Diagnosedaten](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)

    Es wird ein Bericht mit den verfügbaren Daten angezeigt.

    ![Microsoft Azure-Diagnosebericht in Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)

    Wenn die neuesten Daten nicht angezeigt werden, müssen Sie möglicherweise warten, bis der Übertragungszeitraum abgelaufen ist.

    Wählen Sie den Link **Aktualisieren** aus, um die Daten sofort zu aktualisieren, oder wählen Sie ein Intervall in dem Dropdownlistenfeld **Automatische Aktualisierung** aus, um die Daten automatisch zu aktualisieren. Wählen Sie zum Exportieren der Fehlerdaten die Schaltfläche **Nach CSV exportieren** aus, um eine CSV-Datei zu erstellen, die Sie in einer Kalkulationstabelle öffnen können.

    Öffnen Sie in **Cloud Explorer** oder **Server-Explorer** das Speicherkonto, das der Bereitstellung zugeordnet ist.

1. Öffnen Sie die Diagnosetabellen im Tabellenviewer, und überprüfen Sie die erfassten Daten. Für IIS-Protokolle und benutzerdefinierte Protokolle können Sie einen Blobcontainer öffnen. Mithilfe der folgenden Tabelle finden Sie die Tabelle oder den Blobcontainer, die bzw. der die gewünschten Daten enthält. Zusätzlich zu den Daten für diese Protokolldatei enthalten die Tabelleneinträge "EventTickCount", "DeploymentId", "Role" und "RoleInstance", mit denen Sie identifizieren können, welcher virtuelle Computer und welche Rolle die Daten generiert hat und wann.

    |Diagnosedaten|Beschreibung|Standort|
    |---|---|---|
    |Anwendungsprotokolle|Protokolle, die Ihr Code durch den Aufruf von Methoden der System.Diagnostics.Trace-Klasse generiert.|WADLogsTable|
    |Ereignisprotokolle|Diese Daten stammen aus den Windows-Ereignisprotokollen auf den virtuellen Computern. Windows speichert Informationen in diesen Protokollen, aber Anwendungen und Dienste verwenden sie, um Fehler zu melden oder Informationen zu protokollieren.|WADWindowsEventLogsTable|
    |Leistungsindikatoren|Sie können Daten zu jedem Leistungsindikator erfassen, der auf dem virtuellen Computer verfügbar ist. Das Betriebssystem bietet Leistungsindikatoren, darunter viele Statistiken, wie z. B. Speichernutzung und Prozessorzeit.|WADPerformanceCountersTable|
    |Infrastrukturprotokolle|Diese Protokolle werden von der Diagnoseinfrastruktur selbst generiert.|WADDiagnosticInfrastructureLogsTable|
    |IIS-Protokolle|Diese Protokolle zeichnen Webanforderungen auf. Wenn in Ihrem Clouddienst eine beträchtliche Menge an Datenverkehr stattfindet, können diese Protokolle ziemlich umfassend sein. Daher sollten Sie diese Daten nur bei Bedarf erfassen und speichern.|Protokolle mit fehlgeschlagenen Anforderungen finden Sie im Blobcontainer unter "wad-iis-failedreqlogs" unter einem Pfad für diese Bereitstellung, Rolle und Instanz. Die vollständigen Protokolle finden Sie unter "wad-iis-logfiles". Einträge für jede Datei werden in der WADDirectories-Tabelle vorgenommen.|
    |Absturzabbilder|Diese Informationen umfassen binäre Abbilder des Clouddienstprozesses (in der Regel eine Workerrolle).|wad-crush-dumps-Blobcontainer|
    |Benutzerdefinierte Protokolldateien|Protokolle der Daten, die Sie vordefiniert haben.|Sie können im Code den Speicherort der benutzerdefinierten Protokolldateien in Ihrem Speicherkonto angeben. Sie können beispielsweise einen benutzerdefinierten Blobcontainer angeben.|

1. Wenn Daten eines beliebigen Typs abgeschnitten sind, können Sie versuchen, den Puffer für diesen Datentyp zu vergrößern oder das Intervall zwischen Datenübertragungen vom virtuellen Computer an Ihr Speicherkonto zu verkürzen.

1. (Optional) Löschen Sie gelegentlich Daten aus dem Speicherkonto, um die Gesamtspeicherkosten zu verringern.

1. Bei einer vollständigen Bereitstellung wird die Datei "diagnostics.cscfg" (bzw. die WADCFGX-Datei für Azure SDK 2.5) in Azure aktualisiert, und der Clouddienst erkennt alle Änderungen an Ihrer Diagnosekonfiguration. Wenn Sie stattdessen eine vorhandene Bereitstellung aktualisieren, wird die CSCFG-Datei in Azure nicht aktualisiert. Sie können die Diagnoseeinstellungen jedoch weiterhin ändern, indem Sie den Schritten im nächsten Abschnitt folgen. Weitere Informationen zum Durchführen einer vollständigen Bereitstellung und zum Aktualisieren einer vorhandenen Bereitstellung finden Sie unter [Assistent zur Veröffentlichung einer Azure-Anwendung](vs-azure-tools-publish-azure-application-wizard.md).

### So zeigen Sie Diagnosedaten für den virtuellen Computer an

1. Wählen Sie im Kontextmenü für den virtuellen Computer **Diagnosedaten anzeigen** aus.

    ![Anzeigen von Diagnosedaten auf virtuellen Azure-Computern](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)

    Daraufhin wird das Fenster **Diagnosezusammenfassung** angezeigt.

    ![Diagnosezusammenfassung von virtuellen Azure-Computern](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)

    Wenn die neuesten Daten nicht angezeigt werden, müssen Sie möglicherweise warten, bis der Übertragungszeitraum abgelaufen ist.

    Wählen Sie den Link **Aktualisieren** aus, um die Daten sofort zu aktualisieren, oder wählen Sie ein Intervall in dem Dropdownlistenfeld **Automatische Aktualisierung** aus, um die Daten automatisch zu aktualisieren. Wählen Sie zum Exportieren der Fehlerdaten die Schaltfläche **Nach CSV exportieren** aus, um eine CSV-Datei zu erstellen, die Sie in einer Kalkulationstabelle öffnen können.

## Konfigurieren der Clouddienstdiagnose nach der Bereitstellung

Wenn Sie ein Problem bei einem Clouddienst untersuchen, der bereits ausgeführt wird, möchten Sie womöglich Daten erfassen, die Sie vor der Bereitstellung der Rolle nicht angegeben haben. In diesem Fall können Sie mithilfe der Einstellungen im Server-Explorer mit der Sammlung dieser Daten beginnen. Sie können die Diagnose für eine einzelne Instanz oder für alle Instanzen in einer Rolle konfigurieren, je nachdem, ob Sie das Dialogfeld "Diagnosekonfiguration" über das Kontextmenü für die Instanz oder die Rolle öffnen. Wenn Sie den Rollenknoten konfigurieren, gelten alle Änderungen für alle Instanzen. Wenn Sie den Instanzknoten konfigurieren, gelten alle Änderungen nur für diese Instanz.

### So konfigurieren Sie die Diagnose für einen ausgeführten Clouddienst

1. Erweitern Sie im Server-Explorer den Knoten **Clouddienste**, und erweitern Sie dann die Knoten, um die Rolle oder Instanz bzw. beides anzuzeigen, die Sie untersuchen möchten.

    ![Konfigurieren der Diagnose](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)

1. Wählen Sie im Kontextmenü für einen Instanzknoten oder einen Rollenknoten **Diagnoseeinstellungen aktualisieren** aus, und wählen Sie dann die Diagnoseeinstellungen aus, die Sie sammeln möchten.

    Informationen zu den Konfigurationseinstellungen finden Sie unter **Konfigurieren von Diagnosedatenquellen** in diesem Thema. Informationen über das Anzeigen von Diagnosedaten finden Sie unter **Anzeigen von Diagnosedaten** in diesem Thema.

    Wenn Sie die Datensammlung in **Server-Explorer** ändern, bleiben diese Änderungen wirksam, bis Sie den Clouddienst vollständig erneut bereitstellen. Wenn Sie die Standardeinstellungen zur Veröffentlichung verwenden, werden die Änderungen nicht überschrieben, da die Standardeinstellung für die Veröffentlichung die vorhandene Bereitstellung aktualisieren soll, anstatt eine vollständige erneute Bereitstellung durchzuführen. Um sicherzustellen, dass die Einstellungen zum Zeitpunkt der Bereitstellung gelöscht werden, gehen Sie zur Registerkarte **Erweiterte Einstellungen** im Webpublishing-Assistenten, und deaktivieren Sie das Kontrollkästchen **Bereitstellungsaktualisierung**. Wenn Sie bei deaktiviertem Kontrollkästchen eine erneute Bereitstellung durchführen, werden die Einstellungen wieder auf die der WADCFGX-Datei (oder WADCFG) zurückgesetzt, die mit dem Eigenschafteneditor für die Rolle festgelegt wurden. Wenn Sie Ihre Bereitstellung aktualisieren, behält Azure die alten Einstellungen bei.

## Problembehandlung für Azure-Clouddienste

Bei Problemen mit Ihren Clouddienstprojekten auftreten, z. B. eine Rolle, die dauerhaft den Status "Beschäftigt" hat, wiederholte Neustarts oder Ausgabe eines internen Serverfehlers, stehen Ihnen Tools und Techniken zur Verfügung, mit denen Sie diese Probleme diagnostizieren und beheben können. Spezielle Beispiele für häufige Probleme und Lösungen sowie einen Überblick über die Konzepte und Tools zur Diagnose und zur Behebung dieser Fehler finden Sie unter [Windows Azure PaaS Compute Diagnostics Data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## Fragen und Antworten

**Wie groß ist der Puffer, und wie groß sollte er sein?**

Für jede virtuelle Computerinstanz gelten Kontingente, die festlegen, wie viele Diagnosedaten auf dem lokalen Dateisystem gespeichert werden können. Darüber hinaus geben Sie eine Puffergröße für jeden verfügbaren Diagnosedatentyp an. Diese Puffergröße fungiert als individuelles Kontingent für diesen Datentyp. Das Gesamtkontingent und der verbleibende Arbeitsspeicher werden im unteren Bereich des Dialogfelds angezeigt. Wenn Sie größere Puffer oder mehr Datentypen angeben, nähern Sie sich dem Gesamtkontingent. Sie können das Gesamtkontingent ändern, indem Sie die Konfigurationsdatei "diagnostics.wadcfg" bzw. "diagnostics.wadcfgx" ändern. Die Diagnosedaten werden in demselben Dateisystem wie Ihre Anwendungsdaten gespeichert. Wenn die Anwendung viel Speicherplatz verwendet, sollten Sie das Gesamtdiagnosekontingent nicht erhöhen.

**Was ist der Übertragungszeitraum, und wie lange sollte er sein?**

Der Übertragungszeitraum ist der Zeitraum zwischen Datenerfassungen. Nach jedem Übertragungszeitraum werden Daten vom lokalen Dateisystem auf einem virtuellen Computer in die Tabellen in Ihrem Speicherkonto verschoben. Wenn die Menge an gesammelten Daten das Kontingent vor dem Ende eines Übertragungszeitraums übersteigt, werden ältere Daten verworfen. Bei Datenverlust empfiehlt es sich womöglich, den Übertragungszeitraum zu verkleinern, da Ihre Daten die Puffergröße oder das gesamte Kontingent überschreiten.

**Welche Zeitzone gilt für die Zeitstempel?**

Die Zeitstempel haben dieselbe Zeitzone wie das lokale Rechenzentrum, in dem der Clouddienst gehostet wird. Es werden die folgenden drei Zeitstempelspalten in den Protokolltabellen verwendet.

  - **PreciseTimeStamp** ist der ETW-Zeitstempel des Ereignisses. Das heißt die Zeit, zu der das Ereignis vom Client protokolliert wird.

  - **TIMESTAMP** ist der auf die Uploadhäufigkeitsgrenze abgerundete PreciseTimeStamp. Wenn die Uploadhäufigkeit 5 Minuten beträgt und die Ereigniszeit 00:17:12 ist, lautet der TIMESTAMP 00:15:00.

  - **Timestamp** ist der Zeitstempel, zu der die Entität in der Azure-Tabelle erstellt wurde.

**Wie verwalte ich Kosten beim Sammeln von Diagnoseinformationen?**

Die Standardeinstellungen (**Protokollebene** festgelegt auf **Fehler** und **Übertragungszeit** festgelegt auf **1 Minute**) dienen der Minimierung von Kosten. Ihre Serverkosten steigen, wenn Sie weitere Diagnosedaten sammeln oder den Übertragungszeitraum verkürzen. Sammeln Sie nicht mehr Daten als nötig, und vergessen Sie nicht, die Datensammlung zu deaktivieren, wenn Sie sie nicht mehr benötigen. Sie können sie auch zur Laufzeit stets wieder aktivieren, wie im vorherigen Abschnitt gezeigt.

**Wie sammle ich Protokolle zu fehlgeschlagenen Anforderungen von IIS?**

Standardmäßig erfasst IIS keine Protokolle zu fehlgeschlagenen Anforderungen. Sie können IIS so konfigurieren, dass solche Protokolle erfasst werden, indem Sie die Datei "web.config" für Ihre Webrolle bearbeiten.

**Ich erhalte keine Nachverfolgungsinformationen von RoleEntryPoint-Methoden wie OnStart. Woran könnte das liegen?**

Die Methoden von RoleEntryPoint werden im Kontext von WAIISHost.exe und nicht von IIS aufgerufen. Daher gelten die Konfigurationsinformationen in der Datei "web.config" nicht, mit denen die Nachverfolgung normalerweise aktiviert wird. Fügen Sie Ihrem Webrollenprojekt eine CONFIG-Datei hinzu, um dieses Problem zu beheben, und geben Sie der Datei einen Namen, der mit der Ausgabeassembly übereinstimmt, die den RoleEntryPoint-Code enthält. Im standardmäßigen Webrollenprojekt wäre der Name der CONFIG-Datei "WAIISHost.exe.config". Fügen Sie dieser Datei die folgenden Zeilen hinzu:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Legen Sie im Fenster **Eigenschaften** die Eigenschaft **In Ausgabeverzeichnis kopieren** auf **Immer kopieren** fest.

## Nächste Schritte

Weitere Informationen zur Diagnoseprotokollierung in Azure finden Sie unter [Aktivieren der Diagnose in Azure Cloud Services und Virtual Machines](cloud-services-dotnet-diagnostics.md) und [Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service](web-sites-enable-diagnostic-log.md).

<!---HONumber=AcomDC_1223_2015-->