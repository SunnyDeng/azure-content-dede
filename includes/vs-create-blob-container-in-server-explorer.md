Sie können Azure Storage-Warteschlangen erstellen, indem Sie den **Server-Explorer** von Visual Studio verwenden.

![Server-Explorer-BLOBs][Image1]

1. Wählen Sie im Menü **Ansicht** den Eintrag **Server-Explorer** aus.
2. Erweitern Sie im Server-Explorer den Knoten **Azure** für Ihr Abonnement, erweitern Sie den Knoten **Speicher**und den Knoten für das Speicherkonto, das Sie in dem mit Azure Storage verbundenen Dienst angegeben haben.
3. Wählen Sie den Knoten **Warteschlangen** aus, und wählen Sie im Kontextmenü **Warteschlange erstellen** aus.
4. Geben Sie einen Namen für den Container ein, und wählen Sie **OK** aus.   

Standardmäßig ist der neue Container privat, und Sie müssen Ihren Speicherzugriffsschlüssel angeben, um Blobs aus diesem Container herunterzuladen. Wenn Sie die Dateien im Container öffentlich machen möchten, wählen Sie den Container im **Server-Explorer** aus, und drücken Sie `F4`, um das Fenster **Eigenschaften** anzuzeigen. Legen Sie für die Eigenschaft **Öffentlicher Lesezugriff** die Option **Blob** fest. Jede Person im Internet kann Blobs in einem öffentlichen Container anzeigen, Sie können sie jedoch nur bearbeiten oder löschen, wenn Sie über den entsprechenden Zugriffsschlüssel verfügen.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png

<!---HONumber=July15_HO5-->