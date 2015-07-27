Jeder Blob im Azure-Speicher muss sich in einem Container befinden. Der Container ist Teil des Blob-Namens. Beispiel: `mycontainer` ist der Name des Containers in diesen Beispiel-Blob-URIs:

	https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
	https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
 
> [AZURE.IMPORTANT]Beachten Sie, dass der Name eines Containers immer aus Kleinbuchstaben bestehen muss. Wenn der Containername einen Großbuchstaben enthält oder anderweitig gegen die Benennungsregeln für Container verstößt, wird möglicherweise der Fehlercode 400 (Ungültige Anforderung) zurückgegeben. Informationen zu den Benennungsregeln für Container finden Sie unter [Benennen von Containern, Blobs und Metadaten und Verweisen auf diese](https://msdn.microsoft.com/library/azure/dd135715.aspx).

<!---HONumber=July15_HO3-->