Es gibt verschiedene Möglichkeiten, die Module zu installieren: den PowerShell-Katalog und den Webplattform-Installer. Das Endergebnis ist weitgehend identisch, obwohl die Installationsmöglichkeit, die Sie auswählen, bestimmt, ob die Module standardmäßig auf dem Computer installiert werden.

Bei der Installation über den PowerShell-Katalog befinden sich die Dateien standardmäßig in *%ProgramFiles%\\WindowsPowerShell\\Modules*. Wenn Sie über den Webplattform-Installer installieren, befinden sich die Dateien standardmäßig in *%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell *. Aus diesem Grund sollten Sie bei einer Methode bleiben, um Fehler zu vermeiden, wenn Sie die Cmdlets zukünftig aktualisieren. Der Webplattform-Installer empfängt monatlich aktualisierte Cmdlets. Der Katalog empfängt aktualisierte Versionen der Cmdlets zum Zeitpunkt der Veröffentlichung. Aus diesem Grund bevorzugen manche den Katalog.

Weitere Informationen zum Installieren von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../articles/powershell-install-configure.md).

**So installieren Sie Module aus dem PowerShell-Katalog**

1. Um das Ressourcen-Manager-Modul direkt aus dem Katalog zu installieren, öffnen Sie Windows PowerShell als Administrator, und geben Sie Folgendes ein:

		Install-Module AzureRM
		Install-AzureRM

2. Nachdem Sie die Module installiert haben, müssen Sie diese importieren, um sie zu verwenden:

		Import-AzureRM

**So installieren Sie Module mit dem Webplattform-Installer**

- Sie können Module mit dem [Webplattform-Installer installieren](http://aka.ms/webpi-azps). Wenn Sie auf den Link klicken, wird das Installationsprogramm gestartet.

- Wenn Sie bei Verwendung des Webplattform-Installers Fehlermeldungen erhalten, liegt das möglicherweise daran, dass Sie bereits eine frühere Version der Cmdlets mit dem Katalog installiert haben. In diesem [Blogbeitrag](https://azure.microsoft.com/blog/azps-1-0/) erfahren Sie, wie Sie ältere Versionen der Module entfernen und die Einsatzbereitschaft wiederherstellen können. Wenn Sie den Webplattform-Installer verwendet haben und zum Katalog wechseln, bzw. umgekehrt, treten typische Fehler auf. Durch Entfernen der Module, die zuvor installiert wurden, lösen Sie dieses Problem, und anschließend können Sie vom neuen Speicherort aus installieren.

<!---HONumber=AcomDC_0218_2016-->