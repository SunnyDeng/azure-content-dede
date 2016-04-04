

Seit der Einführung der benutzerdefinierten Skripterweiterung wird diese häufig verwendet, um Workloads auf virtuellen Windows und Linux-Computern zu konfigurieren. Seit der Einführung von Azure-Ressourcen-Manager-Vorlagen können Benutzer nun eine einzelne Vorlage erstellen, die nicht nur den virtuellen Computer bereitstellt, sondern auch den Workload auf ihm konfiguriert.

## Informationen zu Azure-Ressourcen-Manager-Vorlagen

Eine Azure-Ressourcen-Manager-Vorlage ermöglicht es Ihnen, deklarativ die Azure IaaS-Infrastruktur in der Json-Sprache anzugeben, indem Sie die Abhängigkeiten zwischen Ressourcen definieren. Eine ausführliche Übersicht über Azure-Ressourcen-Manager-Vorlagen finden Sie in den folgenden Artikeln:

- [Übersicht über Ressourcengruppen](../resource-group-overview.md)
- [Bereitstellen von Vorlagen mit der Azure-Befehlszeilenschnittstelle](virtual-machines-linux-cli-manage.md)
- [Bereitstellen von Vorlagen mit Azure PowerShell](virtual-machines-windows-ps-manage.md)

### Voraussetzungen

1. Laden Sie die Azure-Befehlszeilentools für Ihr Betriebssystem [hier](https://azure.microsoft.com/downloads/) herunter.
2. Wenn die Skripts auf einem vorhandenen virtuellen Computer ausgeführt werden, stellen Sie sicher, dass der VM-Agent auf dem virtuellen Computer aktiviert ist. Führen Sie ansonsten die Schritte im Leitfaden [Linux](virtual-machines-linux-classic-manage extensions.md) oder [Windows](virtual-machines-windows-classic-manage extensions.md) aus, um einen zu installieren.
3. Laden Sie die Skripts, die Sie auf der VM ausführen möchten, nach Azure Storage hoch. Die Skripts können aus einem einzelnen oder mehreren Speichercontainern stammen.
4. Alternativ können die Skripts in ein GitHub-Konto hochgeladen werden.
5. Das Skript sollte so geschrieben sein, dass das Eingangsskript, das durch die Erweiterung gestartet wird, weitere Skripts startet.

## Verwenden der benutzerdefinierten Skripterweiterung

Zum Bereitstellen mit Vorlagen wird genau die Version der CustomScript-Erweiterung verwendet, die für Azure-Service-Verwaltungs-APIs verfügbar ist. Die Erweiterung unterstützt dieselben Parameter und Szenarios, etwa Hochladen von Dateien in ein Azure Storage-Konto oder in einen Github-Speicherort. Der wesentliche Unterschied bei einer Verwendung von Vorlagen besteht darin, dass die genaue Version der Erweiterung angegeben werden muss im Gegensatz zur Angabe der Version im Format Hauptversion.*.

<!---HONumber=AcomDC_0323_2016-->