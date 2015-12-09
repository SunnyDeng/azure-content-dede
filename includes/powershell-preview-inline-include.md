Azure PowerShell ist derzeit in zwei Versionen verfügbar: 1.0 und 0.9.8. Wenn Sie bereits Skripts haben und diese nicht sofort ändern möchten, können Sie weiterhin Version 0.9.8 verwenden. Bei der Verwendung der Version 1.0 sollten Sie Ihre Skripts sorgfältig in Präproduktionsumgebungen testen, bevor Sie sie in der Produktionsumgebung einsetzen, um unerwartete Folgen zu vermeiden.

Cmdlet-Namen der Version 1.0 haben das Muster {Verb}-AzureRm{Nomen}, während Namen der Version 0.9.8 nicht **Rm** enthalten (z. B. „New-AzureRmResourceGroup“ statt „New-AzureResourceGroup“). Wenn Sie Azure PowerShell 0.9.8 verwenden, müssen Sie zunächst den Ressourcen-Manager-Modus aktivieren, indem Sie den Befehl **Switch-AzureMode AzureResourceManager** ausführen. Dieser Befehl ist in 1.0 nicht erforderlich.

Nur der Version 1.0 werden neue Features hinzugefügt. Informationen zur Version 1.0, z. B. zur Installation und Deinstallation, finden Sie unter [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

<!---HONumber=AcomDC_1203_2015-->