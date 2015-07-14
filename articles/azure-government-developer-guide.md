<properties 
	pageTitle="Entwicklerhandbuch für Azure Government" 
	description="Dies bietet einen Vergleich der Features und Richtlinien zum Entwickeln von Anwendungen für Azure Government" 
	services="" 
	documentationCenter="" 
	authors="Joharve2" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="01/21/2014" 
	ms.author="jharve"/>


#  Microsoft Azure Government-Entwicklerhandbuch 

<p> Microsoft Azure Government ist eine physisch sowie im Netzwerk isolierte Instanz von Microsoft Azure. Dieses Entwicklerhandbuch bietet Informationen zu den Unterschieden, die für Entwickler und Administratoren erforderlich sind, um mit diesen unterschiedlichen Bereichen von Azure interagieren und arbeiten zu können.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## In diesem Thema


+ [Übersicht](#Overview)
+ [Anleitung für Entwickler](#Guidance)
+ [Derzeit in Microsoft Azure Government verfügbare Funktionen](#Features)
+ [Endpunktzuordnung](#Endpoint)
+ [Nächste Schritte](#next)


## <a name="Overview"></a>Übersicht

Microsoft Azure Government ist eine separate Instanz des Microsoft Azure Service, die auf die Anforderungen an die Sicherheit und Kompatibilität von Bundes-, Landes- und kommunalen Behörden sowie deren Lösungsanbietern ausgerichtet ist. Azure Government bietet die physische Isolation und Netzwerkisolation von nicht behördlichen Bereitstellungen sowie überprüftes US-Personal.

Microsoft bietet eine Reihe von Tools zum Erstellen und Bereitstellen von Cloud-Anwendungen für den globalen Microsoft Azure Service ("globaler Dienst") und Microsoft Azure Government.

Beim Erstellen und Bereitstellen von Anwendungen für Azure Government-Dienste müssen die Entwickler anders als beim globalen Dienst die wichtigsten Unterschiede zwischen den beiden Diensten kennen. Dies gilt insbesondere bei der Einrichtung und Konfiguration der Programmierumgebung, beim Konfigurieren der Endpunkte, beim Erstellen von Anwendungen sowie bei deren Bereitstellung als Dienste für Azure Government.

Die Informationen in diesem Dokument fassen diese Unterschiede zusammen und ergänzen die Informationen, die auf der [Azure Government](http://www.azure.com/gov "Azure Government")-Website und in der [Technischen Bibliothek für Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") auf der MSDN-Website verfügbar sind. Möglicherweise sind auch an vielen anderen Orten wie dem [Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center"), [Azure-Dokumentationscenter](http://azure.microsoft.com/documentation/) und in [Azure-Blogs](http://azure.microsoft.com/blog/ "Azure-Blogs") offizielle Informationen verfügbar.

Dieser Inhalt ist für Partner und Entwickler für Bereitstellungen mit Microsoft Azure Government konzipiert.



## <a name="Guidance"></a>Anleitung für Entwickler
Da die meisten technischen Inhalte, die derzeit verfügbar sind, davon ausgehen, dass Anwendungen für den globalen Dienst und nicht für Microsoft Azure Government entwickelt werden, müssen Sie sicherstellen, dass Entwickler die wichtigsten Unterschiede für Anwendungen beachten, die in Azure Government gehostet werden.

- Zunächst gibt es Unterschiede bei den Diensten und Funktionen. Daher sind bestimmte Funktionen, die in bestimmten Regionen des globalen Diensts verfügbar sind, in Azure Government möglicherweise nicht enthalten.

- Für Funktionen in Azure Government gibt es zudem hinsichtlich der Konfiguration Unterschiede zum globalen Dienst. Aus diesem Grund sollten Sie Ihren Beispielcode, Ihre Konfigurationen und Schritte überprüfen, um sicherzustellen, dass Sie innerhalb der Azure Government Cloud Services-Umgebung erstellen und ausführen.


## <a name="Features"></a> Derzeit in Microsoft Azure Government verfügbare Funktionen
Azure Government verfügt derzeit in den Regionen US GOV IOWA und US GOV VIRGINIA über die folgenden Dienste:

- Virtuelle Computer
- Cloud Services
- Storage
- Active Directory
- Scheduler
- Virtuelle Netzwerke
- SQL-Datenbank

Es sind auch noch andere Dienste verfügbar und es werden zudem kontinuierlich weitere Dienste hinzugefügt. Die aktuelle Liste der Dienste finden Sie in der [Regionsübersicht](http://azure.microsoft.com/regions/#services), in der die einzelnen verfügbaren Regionen und ihre Dienste hervorgehoben sind.

Derzeit wird Azure Government von den Rechenzentren von US GOV Iowa und US GOV Virginia unterstützt. Informationen zu aktuell verfügbaren Rechenzentren und Diensten finden Sie in der obigen Regionsübersicht.

## <a name="Endpoint"></a>Endpunktzuordnung

Verwenden Sie die folgende Tabelle als Anleitung für die Zuordnung von öffentlichen Microsoft Azure- und SQL-Datenbank-Endpunkten zu Azure Government-spezifischen Endpunkten.

<table>
<tr style='font-weight:bold'><td>
Dienstart</td><td>	Azure – Öffentlich</td><td>	Azure Government
</td></tr><tr><td>
Azure Government-Startseite</td><td>	windowsazure.com	</td><td>microsoftazure.us
</td></tr><tr><td>
Verwaltungsportal</td><td>	manage.windowsazure.com</td><td>	manage.windowsazure.us
</td></tr><tr><td>
Allgemein</td><td>	*.windows.net	</td><td>*.usgovcloudapi.net
</td></tr><tr><td>
Core	</td><td>*.core.windows.net	</td><td>*.core.usgovcloudapi.net
</td></tr><tr><td>
Compute	</td><td>*.cloudapp.net	</td><td>*.usgovcloudapp.net
</td></tr><tr><td>
Blob-Speicher</td><td>	*.blob.core.windows.net</td><td>	*.blob.core.usgovcloudapi.net
</td></tr><tr><td>
Warteschlangenspeicher	</td><td>*.queue.core.windows.net</td><td>	*.queue.core.usgovcloudapi.net
</td></tr><tr><td>
Tabellenspeicher</td><td>	*.table.core.windows.net	</td><td>*.table.core.usgovcloudapi.net
</td></tr><tr><td>
Dienstverwaltung</td><td>	management.core.windows.net</td><td>	management.core.usgovcloudapi.net

</td></tr>
<tr><td>SQL-Datenbank</td><td>	*.database.windows.net	</td><td>*.database.usgovcloudapi.net</td></tr>
</table>

## <a name="next"></a>Nächste Schritte
Weitere Informationen zu Azure Government und zur Qualifikation von Unternehmen für den Zugriff finden Sie unter <A href="http://azure.com/gov">http://www.azure.com/gov</a>.

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=July15_HO1-->