

Ein *benutzerdefinierter* virtueller Computer ist einfach ein virtueller Computer, den Sie mit der Option **Aus Katalog** erstellen, da Sie durch diese Option mehr Konfigurationsmöglichkeiten erhalten als durch die Option **Schnellerfassung**. Zu diesen Möglichkeiten gehören:

- Verbinden des virtuellen Computers mit einem virtuellen Netzwerk.
- Installieren des virtuellen Azure-Computer-Agents und der -Erweiterungen, z. B. für Antischadsoftware.
- Hinzufügen des virtuellen Computers zu vorhandenen Clouddiensten.
- Hinzufügen des virtuellen Computers zu einem vorhandenen Speicherkonto.
- Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe.

> [AZURE.IMPORTANT] Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie sich direkt über den Hostnamen oder über eine lokal eingerichtete Verbindung mit ihm verbinden können, stellen Sie sicher, dass Sie das virtuelle Netzwerk schon dann angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Ausführliche Informationen über virtuelle Netzwerke erhalten Sie unter [Übersicht über Azure Virtual Network](virtual-networks-overview.md).


## So erstellen Sie den virtuellen Computer

<!---HONumber=AcomDC_0323_2016-->