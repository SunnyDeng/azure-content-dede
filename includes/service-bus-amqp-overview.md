# Unterstützung für AMQP 1.0 im Servicebus


Sowohl der Azure Service Bus-Cloud-Dienst als auch Service Bus für Windows Server (Service Bus 1.1) unterstützen das Advanced Message Queueing Protocol (AMQP) 1.0. AMQP gibt Ihnen die Möglichkeit, plattformübergreifende Hybridanwendungen mit einem offenen Standard zu erstellen. Sie können Anwendungen erstellen, deren Komponenten unter Verwendung unterschiedlicher Sprachen und Frameworks erstellt wurden und unter unterschiedlichen Betriebssystemen ausgeführt werden. Alle diese Komponenten können über den Servicebus eine Verbindung herstellen und strukturierte Geschäftsnachrichten effizient und sicher nahtlos austauschen.

## Einführung: Was ist AMQP 1.0 und warum ist es wichtig?

Traditionell nutzen nachrichtenorientierte Middlewareprodukte proprietäre Protokolle für die Kommunikation zwischen Clientanwendungen und Brokern. Dies bedeutet, dass Sie nach der Entscheidung für den Messaging Broker eines bestimmten Anbieters auch dessen Bibliotheken zur Anbindung der Clientanwendungen an den Broker verwenden müssen. Dies bringt eine gewisse Anbieterabhängigkeit mit sich, da eine Portierung einer Anwendung auf ein anderes Produkt Codeänderungen an allen verbundenen Anwendungen erforderlich machen. 

Zudem ist es schwierig, Messaging Broker unterschiedlicher Anbieter zu kombinieren. In der Regel ist hierfür eine Überbrückung auf Anwendungsebene erforderlich, um Nachrichten von einem System in ein anderes zu übertragen und die jeweils proprietären Nachrichtenformate zu konvertieren. Dies ist eine häufig gestellte Anforderung, etwa um eine neue, vereinheitlichte Oberfläche für ältere, dezentrale Systeme bereitzustellen oder nach einem Unternehmenszusammenschluss die IT-Systeme zu integrieren.

Die Softwarebranche ist äußerst schnelllebig, und neue Programmiersprachen und Anwendungsframeworks werden in einem manchmal verblüffenden Tempo erfunden. Ganz ähnlich entwickeln sich die Anforderungen von IT-Systemen im Laufe der Zeit, und Entwickler möchten die Vorteile der neuesten Plattformfeatures nutzen. Manchmal jedoch unterstützt der gewählte Messaginganbieter nicht die gewünschten Plattformen. Da die Protokolle des Messagingprodukts proprietär sind, ist es nicht möglich, dass Dritte die Bibliotheken für diese neuen Plattformen bereitstellen. Daher müssen Sie Ansätze wie z. B. Gateways oder Bridges entwickeln, damit Sie das Messagingprodukt weiterhin verwenden können.

All diese Probleme waren der Grund für die Entwicklung von AMQP (Advanced Message Queuing Protocol) 1.0. Das Projekt wurde angestoßen von JP Morgan Chase, da dieses Unternehmen wie die meisten Finanzdienstleister nachrichtenorientierte Middleware in großem Umfang nutzt. Das Ziel war ganz einfach: ein Messagingprotokoll mit offenem Standard zu entwickeln, das die Erstellung nachrichtenbasierter Anwendungen aus Komponenten ermöglicht, die auf unterschiedlichen Sprachen, Frameworks und Betriebssystemen beruhen, um so die jeweils besten Komponenten verschiedenster Hersteller einsetzen zu können.

## Technische Merkmale von AMQP 1.0

AMQP 1.0 ist ein effizientes, zuverlässiges Messagingprotokoll auf Wire-Ebene, mit dem Sie robuste und plattformübergreifende Messaginganwendungen erstellen können. Das Protokoll hat einen eindeutigen Zweck: die Mechanismen der sicheren, zuverlässigen und effizienten Nachrichtenübertragung zwischen zwei Parteien zu definieren. Die Nachrichten selbst werden mithilfe einer portierbaren Datendarstellung codiert, die den Austausch strukturierter Geschäftsnachrichten zwischen heterogenen Sendern und Empfängern bei voller Vertraulichkeit ermöglicht. Die folgende Zusammenfassung listet die wichtigsten Merkmale auf:

*    **Effizienz**: AMQP 1.0 ist ein verbindungsorientiertes Protokoll, das eine binäre Codierung für die Protokollanweisungen und die darüber übertragenen Geschäftsnachrichten nutzt. Es beinhaltetet ausgereifte Schemata zur Flusssteuerung, um die Auslastung von Netzwerk und angeschlossenen Komponenten zu maximieren. Vor diesem Hintergrund verfolgt das Protokoll das Ziel, ein Gleichgewicht zwischen Effizienz, Flexibilität und Interoperabilität zu gewährleisten.
*    **Verlässlichkeit**: Mit dem AMQP 1.0-Protokoll können Nachrichten mit Verlässlichkeitsgarantien unterschiedlicher Niveaus gesendet werden: von "Fire-and-Forget" bis hin zu zuverlässiger, genau einmal bestätigter Lieferung.
*    **Flexibilität**: AMQP 1.0 ist ein flexibles Protokoll, das zur Unterstützung unterschiedlicher Topologien genutzt werden kann. Ein und dasselbe Protokoll kann für die Kommunikation von Client zu Client, von Client zu Broker und von Broker zu Broker dienen.
*    **Unabhängigkeit vom Brokermodell**: Die AMQP 1.0-Spezifizierung stellt keine Anforderungen an das von einem Broker verwendete Messagingmodell. Vorhandene Messagingbroker können also ganz einfach um die Unterstützung von AMQP 1.0 erweitert werden.

## AMQP 1.0 ist ein Standard (im wahrsten Sinne des Wortes)

Die Entwicklung von AMQP 1.0 wird seit 2008 von einer Kerngruppe aus über 20 Unternehmen - Technologielieferanten wie auch Endnutzern - vorangetrieben. In diesem Prozess haben die Abnehmerfirmen vermittelt, welche Anforderungen im echten Geschäftsleben auftreten, und die Technologieanbieter daraus ein entsprechend ausgelegtes Protokoll entwickelt. Während der Entwicklungszeit haben die Hersteller in Workshops die Interoperabilität ihrer Implementierungen gemeinsam überprüft.

Im Oktober 2011 ging die Entwicklungsarbeit an einen technischen Ausschuss der Organization for the Advancement of Structured Information Standards (OASIS) über; der OASIS AMQP 1.0-Standard wurde im Oktober 2012 veröffentlicht. Während der Entwicklung des Standards waren die folgenden Unternehmen im technischen Ausschuss vertreten:

*    **Technologieanbieter**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
*    **Abnehmerfirmen**: Bank of America, Credit Suisse, Deutsche Börse, Goldman Sachs, JPMorgan Chase.

Zu den am häufigsten genannten Vorteilen offener Standards gehören:

*    Geringere Gefahr von Anbieterabhängigkeit
*    Interoperabilität
*    Breite Verfügbarkeit von Bibliotheken und Werkzeugen
*    Schutz vor Obsoleszenz
*    Verfügbarkeit qualifizierter Mitarbeiter
*    Geringeres und überschaubares Risiko

## AMQP 1.0 und Servicebus

Durch die zusätzliche AMQP 1.0-Unterstützung für Service Bus können Sie jetzt dessen Brokermessagingfunktionen für Warteschlangen und Veröffentlichungen/Abonnements mithilfe eines effizienten binären Protokolls auf unterschiedlichen Plattformen nutzen. Zudem können Sie Anwendungen erstellen, deren Komponenten mit einer Mischung aus Sprachen, Frameworks und Betriebssystemen erstellt wurden.

Das folgende Diagramm zeigt das Beispiel einer Bereitstellung, bei der auf Linux ausgeführte Java-Clients, die mit der Standard-Programmierschnittstelle Java Message Service (JMS) geschrieben wurden, und auf Windows ausgeführte .NET-Clients unter Verwendung von AMQP 1.0 Nachrichten über Servicebus austauschen.

![][0]

**Abbildung 1: Beispielszenario für eine Bereitstellung mit plattformübergreifender Nachrichtenübermittlung mittels Servicebus und AMQP 1.0**

Aktuell sind die folgenden Clientbibliotheken bekannt, die mit Servicebus arbeiten:

<table>
  <tr>
    <th>Sprache</th>
    <th>Bibliothek</th>
  </tr>
  <tr>
    <td>Java</td>
    <td>Apache Qpid Java Message Service (JMS)-Client<br/>
        IIT Software SwiftMQ Java-Client</td>
  </tr>
  <tr>
    <td>C</td>
    <td>Apache Qpid Proton-C</td>
  </tr>
  <tr>
    <td>PHP</td>
    <td>Apache Qpid Proton-PHP</td>
  </tr>
  <tr>
    <td>Python</td>
    <td>Apache Qpid Proton-Python</td>
  </tr>

</table>


**Abbildung 2: Tabelle der AMQP 1.0 Clientbibliotheken**

Weitere Informationen zur Beschaffung dieser Bibliotheken und ihrer Verwendung mit Servicebus entnehmen Sie dem [Entwicklerhandbuch für Service Bus AMQP][]. Weitere Informationen finden Sie im untenstehenden Abschnitt "Referenzen".

## Zusammenfassung

*    AMQP 1.0 ist ein offenes, zuverlässiges Messagingprotokoll, mit dem Sie plattformübergreifende Hybridanwendungen erstellen können. AMQP 1.0 ist ein OASIS-Standard.
*    Unterstützung für AMQP 1.0 ist nun für Azure Service Bus und Service Bus für Windows Server (Service Bus 1.1) verfügbar. Die Preise weichen nicht von denen existierender Protokolle ab.

## Referenzen

*    [Verwenden von AMQP 1.0 mit der Service Bus .NET-API](http://aka.ms/lym3vk)
*    [Verwenden der JMS-Programmierschnittstelle (Java Message Service) mit Service Bus & AMQP 1.0](http://aka.ms/ll1fm3)
*    [Entwicklerhandbuch für Service Bus AMQP](http://msdn.microsoft.com/library/jj841071.aspx)
*    [Spezifikation für OASIS Advanced Message Queuing Protocol (AMQP) Version 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)

[0]: ./media/service-bus-amqp-overview/Example1.png
[Entwicklerhandbuch für Service Bus AMQP]: http://msdn.microsoft.com/library/jj841071.aspx

<!--HONumber=47-->
