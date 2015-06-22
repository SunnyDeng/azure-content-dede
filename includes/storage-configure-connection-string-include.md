## Einrichten einer Speicherverbindungszeichenfolge

Die Azure-Speicherclientbibliothek für .NET unterstützt die Verwendung einer Speicherverbindungszeichenfolge zum Konfigurieren von Endpunkten und Anmeldeinformationen für den Zugriff auf Speicherdienste. Es wird empfohlen, dass Sie Ihre Speicherverbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren. Zum Speichern der Verbindungszeichenfolge stehen Ihnen zwei Optionen zur Verfügung:

- Wenn Ihre Anwendung in einem Azure-Clouddienst ausgeführt wird, speichern Sie die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (`*.csdef`- und `*.cscfg`-Dateien).
- Wenn Ihre Anwendung auf virtuellen Azure-Computern ausgeführt wird oder Sie .NET-Anwendungen erstellen, die außerhalb von Azure ausgeführt werden, speichern Sie die Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems (z. B.  `web.config`- oder  `app.config`-Dateien).

Weiter unten in diesem Leitfaden erfahren Sie, wie Sie die Verbindungszeichenfolge aus Ihrem Code abrufen können.

### Konfigurieren der Verbindungszeichenfolge aus einem Azure-Clouddienst

Die Dienstkonfiguration ist in Azure-Clouddiensten eindeutig und ermöglicht es Ihnen, Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung erneut bereitzustellen.

So konfigurieren Sie die Verbindungszeichenfolge in der Azure-Dienstkonfiguration:

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio im Ordner **Rollen**
    des Azure-Bereitstellungsprojekts mit der rechten Maustaste auf die
    Webrolle oder Workerrolle, und klicken Sie dann auf **Eigenschaften**.  
    ![Auswählen von Eigenschaften für eine Clouddienstrolle in Visual Studio][connection-string1]

2.  Klicken Sie auf die Registerkarte **Settings** und anschließend auf die Schaltfläche **Add Setting**.  
    ![Hinzufügen von Clouddiensteinstellungen in Visual Studio][connection-string2]

    In der Einstellungstabelle wird daraufhin der neue Eintrag **Setting1** angezeigt.

3.  Wählen Sie in der Dropdownliste **Type** des neuen Eintrags **Setting1** die Option
    **Connection String** aus.  
    ![Festlegen des Typs der Verbindungszeichenfolge][connection-string3]

4.  Klicken Sie auf die Schaltfläche **...** rechts neben dem Eintrag **Setting1**.
    Daraufhin öffnet sich der Dialogfeld **Storage Account Connection String**.

5.  Wählen Sie aus, ob als Ziel der Speicheremulator (Windows
    Azure-Speicher, simuliert auf Ihrem lokalen Computer) oder ein Speicherkonto
    in der Cloud verwendet werden soll. Der Code in diesem Leitfaden kann mit beiden Optionen verwendet
    werden. 

	> [AZURE.NOTE] Sie können auf den Speicheremulator zielen, um jegliche Kosten im Zusammenhang mit Windows Azure Storage zu vermeiden. Wenn jedoch ein Azure-Speicherkonto in der Cloud das Ziel sein soll, sind die Kosten für das Ausführen dieses Lernprogramms vernachlässigbar.

	Wenn Sie ein Speicherkonto in der Cloud als Ziel verwenden, geben Sie den primären Zugriffsschlüssel für dieses Speicherkonto ein. Informationen zum Kopieren des Primärschlüssels über das Azure-Verwaltungsportal finden Sie unter 	
	[Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).
	
    ![Auswählen der Zielumgebung][connection-string4]

6.  Ändern Sie den Eintrag **Name** von **Setting1** in einen benutzerfreundlicheren Namen
    wie **StorageConnectionString**. Sie werden später
    in diesem Leitfaden auf diese Verbindungszeichenfolge verweisen.  
    ![Ändern des Namens der Verbindungszeichenfolge][connection-string5]
	
### Konfigurieren der Verbindungszeichenfolge in der .NET-Konfiguration

Wenn Sie eine Anwendung schreiben, bei der es sich nicht um einen Azure-Clouddienst handelt (siehe vorheriger Abschnitt), empfiehlt es sich, das .NET-Konfigurationssystem zu verwenden (z. B. `web.config` oder `app.config`). Dies umfasst Azure-Websites oder virtuelle Azure-Computer sowie Anwendungen, die für die Ausführung außerhalb von Azure konzipiert sind. Sie speichern die Verbindungszeichenfolge mit dem "<appSettings>"-Element wie folgt. Ersetzen Sie `account-name` durch den Namen Ihres Speicherkontos und `account-key` durch den Zugriffsschlüssel des Kontos:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

Die Konfigurationseinstellung in Ihrer Konfigurationsdatei könnte beispielsweise folgendermaßen aussehen:

	<configuration>
    	<appSettings>
      		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
    	</appSettings>
	</configuration>

Sie sind nun bereit für die Gewusst-wie-Aufgaben in diesem Leitfaden.

[connection-string1]: ./media/storage-configure-connection-string-include/connection-string1.png
[connection-string2]: ./media/storage-configure-connection-string-include/connection-string2.png
[connection-string3]: ./media/storage-configure-connection-string-include/connection-string3.png
[connection-string4]: ./media/storage-configure-connection-string-include/connection-string4.png
[connection-string5]: ./media/storage-configure-connection-string-include/connection-string5.png

[Konfigurieren von Verbindungszeichenfolgen]: http://msdn.microsoft.com/library/azure/ee758697.aspx

<!--HONumber=49-->