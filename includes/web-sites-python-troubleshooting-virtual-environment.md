Das Bereitstellungsskript überspringt die Erstellung der virtuellen Umgebung auf Azure, wenn es erkennt, dass bereits eine kompatible virtuelle Umgebung vorhanden ist. Dadurch können Sie die Bereitstellung erheblich beschleunigen. Pakete, die bereits installiert sind, werden von Pip übersprungen.

In bestimmten Situationen müssen Sie eventuell das Löschen dieser virtuellen Umgebung erzwingen. Sie sollten dies tun, wenn eine virtuelle Umgebung als Teil des Repositorys enthalten soll. Sie sollten dies auch tun, wenn Sie bestimmte Pakete entfernen möchten oder Änderungen an requirements.txt testen müssen.

Es gibt mehrere Möglichkeiten, die vorhandene virtuelle Umgebung auf Azure zu verwalten:

### Option 1: Verwenden von FTP

Stellen Sie mit einem FTP-Client eine Verbindung mit dem Server her, sodass Sie den Ordner "env" löschen können. Beachten Sie, dass einige FTP-Clients (z. B. Webbrowser) möglicherweise nur Lesezugriff haben und das Löschen von Ordnern nicht erlauben, daher sollten Sie sicherstellen, dass Sie einen FTP-Client mit dieser Funktion verwenden. Der FTP-Hostname und Benutzer werden auf dem Blatt Ihrer Web-App im [Azure-Portal](https://portal.azure.com) angezeigt.

### Option 2: Ein-/Ausschalten von Runtime

Hier ist eine Alternative, welche die Tatsache nutzt, dass das Bereitstellungsskript den env-Ordner löscht, wenn die gewünschte Version von Python nicht übereinstimmt. Dadurch wird effektiv die vorhandene Umgebung gelöscht und eine neue erstellt.

1. Wechseln Sie zu einer anderen Version von Python (über "runtime.txt" oder das Blatt **Anwendungseinstellungen** im Azure-Portal)
1. Übertragen Sie einige Änderungen durch Git-Push (mögliche pip-Installationsfehler ignorieren, falls vorhanden)
1. Wechseln Sie zurück zur ersten Version von Python
1. Übertragen Sie erneut einige Änderungen durch Git-Push

### Option 3: Anpassen des Bereitstellungsskripts

Wenn Sie das Bereitstellungsskript angepasst haben, können Sie den Code in "deploy.cmd" ändern, um das Löschen des Ordners "env" zu erzwingen.

<!---HONumber=August15_HO6-->