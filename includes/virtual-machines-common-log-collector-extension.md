
Für die Diagnose von Problemen mit einem Microsoft Azure-Clouddienst müssen die Protokolldateien des Diensts auf virtuellen Computern gesammelt werden, während die Probleme auftreten. Sie können die AzureLogCollector-Erweiterung bedarfsgesteuert verwenden, um eine einmalige Sammlung von Protokollen von einer oder mehreren Clouddienst-VMs (von Web- und Workerrollen aus) durchzuführen und die gesammelten Dateien an ein Azure-Speicherkonto zu übertragen – alles ohne Remoteanmeldung bei den virtuellen Computern.
> [AZURE.NOTE]Beschreibungen für den Großteil der protokollierten Informationen finden Sie unter http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.

Abhängig von den Typen der zu sammelnden Dateien gibt es zwei Modi für die Sammlung.
- Nur Protokolle von Azure-Gast-Agents (GA). Dieser Sammlungsmodus umfasst alle Protokolle im Zusammenhang mit Azure-Gast-Agents und anderen Azure-Komponenten.
- Alle Protokolle (vollständig). Dieser Sammlungsmodus sammelt alle Dateien im GA-Modus und zusätzlich:

  - System- und Anwendungsereignisprotokolle
  
  - HTTP-Fehlerprotokolle
  
  - IIS-Protokolle
  
  - Setupprotokolle
  
  - Sonstige Systemprotokolle

In beiden Sammlungsmodi können zusätzliche Ordner für die Datensammlung angegeben werden, indem eine Sammlung mit der folgenden Struktur verwendet wird:

- **Name**: Der Name der Sammlung, der als Name des Unterordners in der zu erfassenden ZIP-Datei verwendet wird.

- **Speicherort**: Der Pfad des Ordners auf dem virtuellen Computer, in dem die Datei gesammelt wird.

- **Suchmuster**: Das Muster der Namen der zu sammelnden Dateien. Der Standardwert ist „*“.

- **Rekursiv**: Angabe, ob die Dateien im Ordner rekursiv gesammelt werden.

<!---HONumber=AcomDC_0323_2016-->