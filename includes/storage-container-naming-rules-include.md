Jeder Blob im Azure-Speicher muss sich in einem Container befinden. Der Container ist Teil des Blob-Namens. Beispiel: `mycontainer` ist der Name des Containers in diesen Beispiel-Blob-URIs:

	https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
	https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

Ein Containername muss ein gültiger DNS-Name sein und den folgenden Benennungsregeln entsprechen:

1. Containernamen müssen mit einem Buchstaben oder einer Zahl beginnen und dürfen nur Buchstaben, Zahlen und Bindestriche (-) enthalten.
1. Jedem Bindestrich (-) muss unmittelbar ein Buchstabe oder eine Zahl vorangehen und folgen von einem Buchstaben oder einer Zahl; zudem dürfen nicht mehrere Bindestriche direkt aufeinander folgen.
1. Der Containername darf ausschließlich Kleinbuchstaben enthalten.
1. Containernamen müssen zwischen 3 und 63 Zeichen lang sein.

> [AZURE.IMPORTANT]Beachten Sie, dass der Name eines Containers immer aus Kleinbuchstaben bestehen muss. Wenn der Containername einen Großbuchstaben enthält oder anderweitig gegen die Benennungsregeln für Container verstößt, wird möglicherweise der Fehlercode 400 (Ungültige Anforderung) zurückgegeben.

<!---HONumber=Oct15_HO3-->