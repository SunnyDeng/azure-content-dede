<a name="tellmecs"></a>
## Informationen zu Cloud Services

Cloud Services ist ein Beispiel für Platform-as-a-Service (PaaS). Diese Technologie unterstützt wie [App Services](app-service-web-overview.md) skalierbare und zuverlässige Anwendungen mit niedrigen Betriebskosten. Genau wie ein [App Service](app-service-web-overview.md) auf VMs gehostet werden, werden das auch die Cloud Services, Sie haben jedoch mehr Kontrolle über die virtuellen Computer. Sie können Ihre eigene Software auf Clouddienst-VMs installieren und per Remotezugriff auf diese zugreifen.

![cs_diagramcs_diagram](./media/cloud-services-choose-me-content/diagram.png)

Mehr Kontrolle bedeutet auch weniger Anwenderfreundlichkeit; wenn Sie die zusätzlichen Verwaltungsoptionen nicht benötigen, ist es i. d. R. im Vergleich zu Cloud Services schneller und einfacher Webanwendungen in Web-Apps in App Service zum Laufen zu bringen.

Diese Technologie bietet zwei leicht abweichende Optionen für VM: Auf Instanzen von *Webrollen* läuft eine Variante von Windows Server mit IIS, während auf Instanzen von *Workerrollen* dieselbe Windows Server-Variante ohne IIS läuft. Cloud Services-Anwendungen verwenden dieselbe Kombination dieser zwei Optionen.

Eine beliebige Kombination dieser zwei leicht unterschiedlichen VM Hostingoptionen sind in einem Clouddienst verfügbar:

* **Webrolle**  
  Führt Windows Server mit Ihrer Web-App aus, die automatisch auf IIS bereitgestellt wird.
* **Workerrolle**  
  Führt Windows-Server ohne IIS aus.

Einfache Anwendungen verwenden z. B. nur eine Webrolle, während komplexere Anwendungen eine Webrolle für die Verarbeitung eingehender Benutzeranforderungen verwenden und die aus diesen Anforderungen entstandene Arbeit an Workerrollen abgeben. (Diese Kommunikation erfolgt über einen [Service Bus](../articles/service-bus/fundamentals-service-bus-hybrid-solutions.md) oder über [Azure-Warteschlangen](../articles/storage/storage-introduction.md).)

Die obige Abbildung zeigt, dass alle VMs in einer einzigen Anwendung im gleichen Clouddienst laufen. Aus diesem Grund greifen die Benutzer über eine einzige öffentliche IP-Adresse auf die Anwendung zu, und die Anforderungen werden automatisch auf die einzelnen VMs verteilt. Die Plattform [skaliert und stellt](../articles/cloud-services/cloud-services-how-to-scale.md) die VMs in einer Cloud Services-Anwendung so bereit, dass einzelne Fehlerquellen vermieden werden.

Obwohl die Anwendungen in Virtual Machines laufen, ist es wichtig, zu betonen, dass Cloud Services PaaS und nicht IaaS bieten. Sie können sich dies auf folgende Weise vorstellen: Mit IaaS, z. B. mit Azure Virtual Machines, erstellen und konfigurieren Sie zunächst die Umgebung für Ihre Anwendung und stellen die Anwendung anschließend in Ihrer Umgebung bereit. Sie sind für die Verwaltung dieser Umgebung verantwortlich und müssen z. B. neue gepatchte Versionen des Betriebssystems in den einzelnen VM installieren. Mit PaaS dagegen existiert diese Umgebung bereits. Sie müssen nur noch Ihre Anwendung bereitstellen. Sie brauchen sich nicht um die Verwaltung der Plattform kümmern, inklusive neuer Versionen des Betriebssystems.

## Skalierung und Verwaltung
Mit Cloud Services erstellen Sie keine Virtual Machines. Stattdessen teilen Sie Azure über eine Konfigurationsdatei mit, wie viele Instanzen Sie benötigen, z. B. **drei Webrolleninstanzen** und zwei **Workerrolleninstanzen**, und die Plattform erstellt diese Instanzen für Sie. Sie können die [Größe](../articles/cloud-services/cloud-services-sizes-specs.md) diese Sicherungs-VMs wählen aber Sie erstellen diese Computer nicht selbst. Wenn Ihre Anwendung eine größere Last verarbeiten muss, können Sie von Azure weitere virtuelle Computer anfordern. Wenn die Last abnimmt, können Sie diese Instanzen abschalten und müssen diese nicht länger bezahlen.

Cloud Services-Anwendungen werden den Benutzern normalerweise in zwei Schritten bereitgestellt. Zunächst lädt ein Entwickler die [Anwendung in den Stagingbereich der Plattform hoch](../articles/cloud-services/cloud-services-how-to-create-deploy.md). Wenn die Entwickler die Anwendung live schalten möchten, können sie diese über das Azure-Verwaltungsportal in den Produktionsmodus umschalten. Der [Wechsel zwischen Staging und Produktion](../articles/cloud-services/cloud-services-nodejs-stage-application.md) erfolgt ohne Ausfallzeit. Daher können laufende Anwendungen ohne Beeinträchtigung der Benutzer auf neue Versionen aktualisiert werden.

## Überwachung
Cloud Services bietet außerdem Überwachungsfunktionen. Wie auch bei Azure Virtual Machines werden Ausfälle von physischen Servern erkannt und die entsprechenden virtuellen Computer auf einem anderen Server neu gestartet. Cloud Services erkennt jedoch neben Hardwarefehlern auch Ausfälle von virtuellen Computern und Anwendungen. Im Gegensatz zu Virtual Machines läuft in jeder Web- und Workerrolle ein Agent. Daher können im Fehlerfall neue virtuelle Computer und Anwendungsinstanzen gestartet werden.

Die PaaS-Funktionsweise von Cloud Services hat auch noch andere Auswirkungen. Zum Beispiel sollten Anwendungen für diese Technologie so entwickelt werden, dass sie auch dann korrekt funktionieren, wenn eine beliebige Web- oder Workerrolle ausfällt. Aus diesem Grund sollten Cloud Services-Anwendungen keinen Status im Dateisystem der eigenen virtuellen Computer speichern. Im Gegensatz zu virtuellen Computern mit Azure Virtual Machines sind Schreibvorgänge in virtuellen Cloud Services-Computern nicht persistent. Diese virtuellen Computer haben keine eigenen Datenträger. Cloud Services-Anwendungen sollten daher ihren Status in SQL-Datenbanken, Blobs, Tabellen oder andere externe Speichermedien schreiben. Die auf diese Weise erstellten Anwendungen sind skalierbarer und fehlerresistenter; zwei wichtige Ziele von Cloud Services.

<!---HONumber=Oct15_HO3-->