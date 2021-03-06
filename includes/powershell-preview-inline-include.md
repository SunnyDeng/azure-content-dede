Dieses Thema enthält Beispiele, bei denen Azure PowerShell-Cmdlets verwendet werden. Azure PowerShell ist derzeit in zwei Versionen verfügbar – 1.0 Preview und 0.9.8. Wenn Sie bereits Skripts haben und diese nicht sofort ändern möchten, können Sie weiterhin Version 0.9.8 verwenden. Bei der Verwendung der Version 1.0 Preview sollten Sie Ihre Skripts sorgfältig in Präproduktionsumgebungen testen, bevor Sie sie in der Produktionsumgebung einsetzen, um unerwartete Folgen zu vermeiden.

In den meisten Fällen ist der einzige Unterschied zwischen den beiden Versionen, dass die Cmdlet-Namen der Version 1.0 Preview das Muster {Verb}-AzureRm{Nomen} haben, während der Name der Version 0.9.8 nicht **Rm** enthält (z. B. „New-AzureRmResourceGroup“ statt „New-AzureResourceGroup“). Wenn der Unterschied zwischen den Versionen größer ist, werden in diesem Thema Beispiele für beide Versionen aufgeführt.

Wenn Sie Azure PowerShell 0.9.8 verwenden, müssen Sie zunächst den Ressourcen-Manager-Modus aktivieren, indem Sie den Befehl **Switch-AzureMode AzureResourceManager** ausführen. Dieser Befehl ist in der Vorschau für 1.0 nicht erforderlich.

Informationen zur Version 1.0 Preview, z. B. zur Installation und Deinstallation der Version, finden Sie unter [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/). Informationen zu wichtigen Änderungen bei den Ressourcen-Manager-Befehlen finden Sie unter [Änderungen der PowerShell-Cmdlets für die Verwaltung in Azure-Ressourcen-Manager](../articles/powershell-preview-resource-manager-changes.md).

<!---HONumber=Oct15_HO4-->