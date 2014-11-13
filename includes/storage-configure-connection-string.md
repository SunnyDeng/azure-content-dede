Die Azure-Speicherclientbibliothek für .NET unterstützt die Verwendung einer Speicherverbindungszeichenfolge zum Konfigurieren von Endpunkten und Anmeldeinformationen für den Zugriff auf Speicherdienste. Es wird empfohlen, dass Sie Ihre Speicherverbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren. Zum Speichern der Verbindungszeichenfolge stehen Ihnen zwei Optionen zur Verfügung:

-   Wenn Ihre Anwendung in einem Azure-Clouddienst ausgeführt wird, speichern Sie die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (`*.csdef`- und `*.cscfg`-Dateien).
-   Wenn Ihre Anwendung auf virtuellen Azure-Computern ausgeführt wird oder Sie .NET-Anwendungen erstellen, die außerhalb von Azure ausgeführt werden, speichern Sie die Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems (z. B. `web.config`- oder `app.config`-Dateien).

Weiter unten in diesem Leitfaden erfahren Sie, wie Sie die Verbindungszeichenfolge aus Ihrem Code abrufen können.

### Konfigurieren der Verbindungszeichenfolge aus einem Azure-Clouddienst

Die Dienstkonfiguration ist in Azure-Clouddiensten eindeutig und ermöglicht es Ihnen, Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung erneut bereitzustellen.

So konfigurieren Sie die Verbindungszeichenfolge in der Azure-Dienstkonfiguration:

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio im Ordner **Rollen**
    des Azure-Bereitstellungsprojekts mit der rechten Maustaste auf Ihre
    Webrolle oder Workerrolle, und klicken Sie dann auf **Eigenschaften**.
    ![Eigenschaften für eine Clouddienstrolle in Visual Studio auswählen][Eigenschaften für eine Clouddienstrolle in Visual Studio auswählen]

2.  Klicken Sie auf die Registerkarte **Einstellungen**, und wählen Sie die Schaltfläche **Einstellung hinzufügen** aus.
    ![Clouddiensteinstellung in Visual Studio hinzufügen][Clouddiensteinstellung in Visual Studio hinzufügen]

    In der Einstellungstabelle wird daraufhin der neue Eintrag **Setting1** angezeigt.

3.  Wählen Sie in der Dropdownliste **Typ** des neuen Eintrags **Setting1** die Option
    **Verbindungszeichenfolge** aus.
    ![Verbindungszeichenfolgentyp festlegen][Verbindungszeichenfolgentyp festlegen]

4.  Klicken Sie auf die Schaltfläche **...** rechts neben dem Eintrag **Setting1**.
    Das Dialogfeld **Verbindungszeichenfolge für das Speicherkonto** wird geöffnet.

5.  Wählen Sie aus, ob das Ziel der Speicheremulator (simulierter Windows
    Azure-Speicher auf dem lokalen Computer) oder ein Speicherkonto
    in der Cloud sein soll. Der Code in diesem Leitfaden kann mit
    beiden Optionen verwendet werden. Geben Sie den Wert **Primärer Zugriffsschlüssel** ein, den Sie an
    früherer Stelle in diesem Lernprogramm kopiert haben, wenn das Ziel das
    Speicherkonto sein soll, das zuvor in Azure erstellt wurde.

    > [WACOM.NOTE] Sie können auf den Speichermulator zielen, um jegliche Kosten im Zusammenhang mit Windows Azure Storage zu vermeiden. Wenn jedoch ein Azure-Speicherkonto in der Cloud das Ziel sein soll, sind die Kosten für das Ausführen dieses Lernprogramms vernachlässigbar.

    ![Zielumgebung auswählen][Zielumgebung auswählen]

6.  Ändern Sie den Eintrag **Name** von **Setting1** in einen benutzerfreundlicheren
     Namen wie **StorageConnectionString**. Sie werden später in diesem Leitfaden auf diese
    Verbindungszeichenfolge verweisen.
    ![Namen der Verbindungszeichenfolge ändern][Namen der Verbindungszeichenfolge ändern]

### Konfigurieren der Verbindungszeichenfolge in der .NET-Konfiguration

Wenn Sie eine Anwendung schreiben, bei der es sich nicht um einen Azure-Clouddienst handelt (siehe vorheriger Abschnitt), empfiehlt es sich, das .NET-Konfigurationssystem zu verwenden (z. B. `web.config` oder `app.config`). Dies umfasst Azure-Websites oder virtuelle Azure-Computer sowie Anwendungen, die für die Ausführung außerhalb von Azure konzipiert sind. Speichern Sie die Verbindungszeichenfolge wie folgt mit dem Element `<appSettings>`. Ersetzen Sie `account-name` durch den Namen Ihres Speicherkontos und `account-key` durch den Zugriffsschlüssel des Kontos:

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

Weitere Informationen zu Speicherverbindungszeichenfolgen finden Sie unter [Konfigurieren von Verbindungszeichenfolgen][Konfigurieren von Verbindungszeichenfolgen].

Sie sind nun bereit für die Gewusst-wie-Aufgaben in diesem Leitfaden.

  [Eigenschaften für eine Clouddienstrolle in Visual Studio auswählen]: ./media/storage-configure-connection-string/connection-string1.png
  [Clouddiensteinstellung in Visual Studio hinzufügen]: ./media/storage-configure-connection-string/connection-string2.png
  [Verbindungszeichenfolgentyp festlegen]: ./media/storage-configure-connection-string/connection-string3.png
  [Zielumgebung auswählen]: ./media/storage-configure-connection-string/connection-string4.png
  [Namen der Verbindungszeichenfolge ändern]: ./media/storage-configure-connection-string/connection-string5.png
  [Konfigurieren von Verbindungszeichenfolgen]: http://msdn.microsoft.com/de-de/library/windowsazure/ee758697.aspx
