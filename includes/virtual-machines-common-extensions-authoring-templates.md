## Übersicht über Azure-Ressourcen-Manager-Vorlagen

Azure Resource Manager-Vorlagen ermöglichen es Ihnen, deklarativ die Azure IaaS-Infrastruktur in der Json-Sprache anzugeben, indem Sie die Abhängigkeiten zwischen Ressourcen definieren. Eine ausführliche Übersicht über Azure Resource Manager-Vorlagen finden Sie in den folgenden Artikeln:

[Übersicht über Ressourcengruppen](../resource-group-overview.md)

## Ausschnitt einer Beispielvorlage für Erweiterungen für virtuelle Computer.
Bereitstellen der Erweiterung für virtuelle Computer als Teil der Azure Resource Manager-Vorlage erfordert das deklarative Festlegen der Konfiguration für die Erweiterung in der Vorlage. Dies ist das Format für die Angabe der Konfiguration für die Erweiterung.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Wie Sie oben sehen können, enthält die Erweiterungsvorlage zwei Hauptabschnitte:

1. Name, Herausgeber und Version der Erweiterung
2. Konfiguration der Erweiterung.

## Identifizieren des Herausgebers, des Typs und des typeHandlerVersion-Elements für eine beliebige Erweiterung

Erweiterungen für virtuelle Azure-Computer werden von Microsoft und vertrauenswürdigen Drittanbietern veröffentlicht. Jede Erweiterung wird eindeutig durch Herausgeber, Typ und typeHandlerVersion-Element identifiziert. Diese können wie folgt bestimmt werden:

<!---HONumber=AcomDC_0323_2016-->