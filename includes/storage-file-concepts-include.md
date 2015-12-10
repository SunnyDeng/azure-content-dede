## Was ist der Azure-Dateispeicher?

Der Dateispeicher bietet einen gemeinsam genutzten Speicher für Anwendungen und verwendet dabei das SMB 2.1- oder SMB 3.0-Protokoll. Virtuelle Microsoft Azure-Computer und Clouddienste können Dateidaten in verschiedenen Anwendungskomponenten über eingebundene Freigaben teilen, und lokale Anwendungen können über die Dateispeicher-API auf freigegebene Dateien zugreifen.

Anwendungen in virtuellen Azure-Computern oder Clouddiensten können eine Dateispeicher-Freigabe einbinden, um auf Dateidaten zuzugreifen, ebenso wie eine Desktopanwendung eine typische SMB-Freigabe einbinden würde. Die Dateispeicherfreigabe kann von einer beliebigen Anzahl von virtuellen Azure-Computern oder -Rollen gleichzeitig bereitgestellt und verwendet werden.

Da eine Dateispeicherfreigabe eine standardmäßige Dateifreigabe in Azure mit dem SMB-Protokoll ist, können in Azure ausgeführte Anwendungen über Datei-E/A-APIs auf Daten der Freigabe zugreifen. Entwickler können daher ihren vorhandenen Code und bereits erlernte Fertigkeiten für die Migration vorhandener Anwendungen verwenden. IT-Fachkräfte können PowerShell-Cmdlets verwenden, um Dateispeicher-Freigaben im Rahmen der Administration von Azure-Anwendungen zu erstellen, einzubinden und zu verwalten. Diese Anleitung zeigt jeweils Beispiele dazu.

Dateispeicher werden hauptsächlich für folgende Zwecke verwendet:

- Migration von lokalen Anwendungen, die Dateifreigaben aus virtuellen Azure-Computern oder Clouddiensten ohne teure Umschreibungen verwenden
- Speichern von gemeinsam genutzten Anwendungseinstellungen, z. B. in Konfigurationsdateien
- Speichern von Diagnosedaten wie Protokolle, Metriken und Absturzabbilder an einem freigegebenen Speicherort. 
- Speichern von Tools und Dienstprogrammen für das Entwickeln und Verwalten von virtuellen Azure-Computern oder Clouddiensten

## Dateispeicherkonzepte

Der Dateispeicher umfasst die folgenden Komponenten:

![files-concepts][files-concepts]

-   **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Weitere Informationen zur Kapazität der Speicherkonten finden Sie unter [Azure Storage Scalability and Performance Targets](http://msdn.microsoft.com/library/azure/dn249410.aspx) (Skalierbarkeits- und Leistungsziele für Microsoft Azure-Speicher, in englischer Sprache).

-   **Freigabe:** Eine Datenspeicherfreigabe ist eine SMB-Dateifreigabe in Azure. Alle Verzeichnisse und Dateien müssen in der übergeordneten Freigabe erstellt werden. Ein Konto kann eine unbegrenzte Anzahl von Freigaben enthalten, und eine Freigabe kann eine unbegrenzte Anzahl von Dateien speichern, bis die Kapazitätsgrenze von 5 TB für die Dateifreigabe erreicht ist.

-   **Verzeichnis:** Eine optionale Hierarchie von Verzeichnissen.

-	**Datei:** Eine Datei in der Freigabe. Die Datei kann bis zu 1 TB groß sein.

-   **URL-Format:** Dateien sind über das folgende URL-Format adressierbar: https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`
    
    Mit der folgenden Beispiel-URL kann eine der Dateien im Diagramm oben adressiert werden: `http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

Informationen zur Benennung von Freigaben, Verzeichnissen und Dateien finden Sie unter [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](http://msdn.microsoft.com/library/azure/dn167011.aspx).

[files-concepts]: ./media/storage-file-concepts-include/files-concepts.png

<!---HONumber=Oct15_HO3-->