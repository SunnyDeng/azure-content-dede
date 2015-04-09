<properties 
	pageTitle="Leistungsebenen in DocumentDB | Azure" 
	description="Erfahren Sie, wie Sie mithilfe von Leistungsebenen in DocumentDB den Durchsatz pro Sammlung reservieren können." 
	services="documentdb" 
	authors="johnfmacintyre" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="johnmac"/>

# Leistungsebenen in DocumentDB #

Dieser Artikel bietet einen Überblick über die Leistungsebenen in [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/). 

> [AZURE.IMPORTANT] Leistungsebenen werden entsprechend der allgemeinen Verfügbarkeit von Azure DocumentDB verfügbar sein. Die Veröffentlichung dieser Version ist für April 2015 geplant.

Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten:  

-	Was ist eine Leistungsebene?
-	Wie lässt sich der Durchsatz für ein Datenbankkonto reservieren?
-	Wie arbeite ich mit Leistungsebenen?
-	Wie werden Leistungsebenen abgerechnet?

##<a id="Sub1"></a>Einführung in Leistungsebenen##

Jede DocumentDB-Sammlung, die unter einem Standardkonto erstellt wird, wird mit einer zugewiesenen Leistungsebene bereitgestellt. Leistungsebenen werden mit S1, S2 oder S3 bezeichnet (von der niedrigsten bis zur höchsten Leistung). Die Leistungsebene der Sammlung bestimmt die Menge der Serviceressourcen, die für Ihre Anwendung reserviert wird. Jede Sammlung in einer Datenbank kann über individuelle Leistungsebenen verfügen. So können Sie häufig genutzten Sammlungen mehr Durchsatz und seltener genutzten Sammlungen weniger Durchsatz zuweisen. 

Jede Leistungsebene verfügt über eine Beschränkung der Anforderungseinheit(RU)-Rate. Dies ist der Durchsatz, der für die Sammlung basierend auf ihrer Leistungsebene reserviert wird und ausschließlich von dieser Sammlung verwendet werden kann. Sammlungen können über das [Azure-Portal](http://portal.azure.com) oder eines der [DocumentDB-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) erstellt werden. Mithilfe der DocumentDB-APIs können Sie die Leistungsebene einer Sammlung festlegen. 

<table> 
<tbody>
<tr>
<td valign="top" ><p><b>Leistungsebene der Sammlung</b></p></td>
<td valign="top" ><p><b>Reservierter Durchsatz</b></p></td>
</tr>

<tr>
<td valign="top" ><p>S1</p></td>
<td valign="top" ><p>250 RU/Sek.</p></td>
</tr>

<tr>
<td valign="top" ><p>S2</p></td>
<td valign="top" ><p>1000 RU/Sek.</p></td>
</tr>

<tr>
<td valign="top" ><p>S3</p></td>
<td valign="top" ><p>2500 RU/Sek.</p></td>
</tr>

</tbody>
</table>

DocumentDB ermöglicht eine Vielzahl von Datenbankvorgängen, wie z. B. Abfragen, Abfragen mit benutzerdefinierten Funktionen (UDFs), gespeicherte Prozeduren und Trigger. Die Verarbeitungskosten im Zusammenhang mit diesen Vorgängen variieren basierend auf CPU, E/A und Speicher, die/der für den jeweiligen Vorgang erforderlich ist. Anstatt sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.

> [AZURE.NOTE] Leistungsebenen werden in Anforderungseinheiten gemessen. Jede Leistungsebene verfügt über eine maximale Rate der Anforderungseinheiten pro Sekunde. Die Leistungsebene einer Sammlung kann über die APIs oder das [Azure-Portal](https://portal.azure.com/) angepasst werden.

##<a id="Sub2"></a>Festlegen von Leistungsebenen für Sammlungen##
Sobald eine Sammlung erstellt wurde, wird die vollständige Zuweisung von RUs basierend auf der angegebenen Leistungsebene für die Sammlung reserviert. Beispiel: Wenn für eine Sammlung "S3" festgelegt wird, kann die Sammlung 2.500 RUs/Sek. verarbeiten. Jede Sammlung reserviert den festgelegten Durchsatz sowie 10 GB Datenbankspeicher. Der Preis der Sammlung variiert je nach ausgewählter Leistungsebene (S1, S2, S3). Beachten Sie, dass der Betrieb von DocumentDB von der Kapazitätsreservierung abhängig ist. Indem Sie eine Sammlung erstellen, reserviert die Anwendung den entsprechenden Durchsatz und wird basierend auf diesem Durchsatz und dem Datenbankspeicher abgerechnet. Hierbei spielt es keine Rolle, wie viel Speicher und Durchsatz tatsächlich genutzt wird.

Nach dem Erstellen einer Sammlung können Sie die Leistungsebene über die DocumentDB-SDKs oder das Azure-Verwaltungsportal anpassen. 

> [AZURE.IMPORTANT] DocumentDB-Standardsammlungen werden auf Stundenbasis abgerechnet. Hierbei wird für jede erstellte Sammlung eine Verwendung von mindestens einer Stunde berechnet. 

Wenn Sie die Leistungsebene einer Sammlung innerhalb einer Stunde anpassen, wird die höchste, während dieser Stunde festgelegte Leistungsebene berechnet. Beispiel: Wenn Sie die Leistungsebene für eine Sammlung um 8:53 Uhr erhöhen, wird die neue Ebene ab 8:00 Uhr berechnet. Wenn Sie die Leistungsebene um 8:53 Uhr verringern, gilt der neue Satz ab 9:00 Uhr.

Anforderungseinheiten werden für jede Sammlung basierend auf der Leistungsebene reserviert. Der Verbrauch von Anforderungseinheiten wird als Pro-Sekunde-Rate bemessen. Anwendungen, die die bereitgestellte Anforderungseinheitsrate (oder Leistungsebene) einer Sammlung überschreiten, werden gedrosselt, bis die Rate unter das für die Sammlung reservierte Niveau fällt. Wenn für Ihre Anwendung ein höherer Durchsatz erforderlich ist, können Sie die Leistungsebene für jede Sammlung erhöhen.

> [AZURE.NOTE] Wenn Ihre Anwendung die Leistungsebene für eine oder mehrere Sammlungen überschreitet, werden die Anforderungen pro Sammlung beschränkt. Dies bedeutet, dass einige Anforderungen erfolgreich sind, während andere aufgrund der Beschränkung fehlschlagen.

##<a id="Sub3"></a>Arbeiten mit Leistungsebenen##
Mit DocumentDB-Sammlungen können Sie Ihre Daten basierend auf den Abfragemustern und Leistungsanforderungen Ihrer Anwendung partitionieren. Ausführliche Informationen zum Partitionieren von Daten mit DocumentDB finden Sie in der [Dokumentation zur Partitionierung von Daten](documentdb-partition-data.md). Aufgrund der DocumentDB-Unterstützung für automatische Indizierung und Abfragen ist es üblich, heterogene Dokumente in derselben Sammlung zusammenzustellen. Die wichtigsten Überlegungen bei der Entscheidung für oder gegen die Verwendung separater Sammlungen umfassen:

- Abfragen - eine Sammlung ist der Bereich für die Abfrageausführung. Wenn Sie eine Abfrage über eine Reihe von Dokumenten hinweg durchführen müssen, sind Lesevorgänge effizienter, wenn diese Dokumente in einer einzigen Sammlung zusammengestellt werden.
- Transaktionen - eine Sammlung ist die Transaktionsdomäne für gespeicherte Prozeduren und Trigger. Alle Transaktionen sind auf eine Sammlung beschränkt. 
- Leistung - eine Sammlung verfügt über eine zugewiesene Leistungsebene. So wird sichergestellt, dass jede Sammlung eine vorhersagbare Leistung durch reservierte RUs erbringt. Daten können verschiedenen Sammlungen mit unterschiedlichen Leistungsebenen (basierend auf der Zugriffshäufigkeit) zugeordnet werden.

> [AZURE.IMPORTANT] Es ist wichtig, zu verstehen, dass die Abrechnung zu vollständigen Standardsätzen basierend auf der Anzahl der innerhalb Ihrer Anwendung erstellten Sammlungen erfolgt.

Es wird empfohlen, dass Ihre Anwendung eine geringe Anzahl von Sammlungen verwendet und dass Sie über ein umfassendes Verständnis bezüglich der Anwendungsmuster für die Erstellung neuer Sammlungen verfügen. Sie können die Sammlungserstellung als Verwaltungsaktion reservieren, die außerhalb Ihrer Anwendung durchgeführt wird. Ähnlich ändert sich der Stundensatz, über den die Sammlung abgerechnet wird, wenn Sie die Leistungsebene für eine Sammlung anpassen. Wenn Ihre Anwendung die Leistungsebenen dynamisch anpasst, sollten Sie diese überwachen.

##<a name="NextSteps"></a>Nächste Schritte

Weitere Informationen zu Preisen und der Datenverwaltung mit Azure DocumentDB finden Sie in folgenden Ressourcen:
 
- [DocumentDB-Preise](http://azure.microsoft.com/pricing/details/documentdb/)
- [Verwalten der DocumentDB-Kapazität](documentdb-manage.md) 
- [Modellieren von Daten in DocumentDB](documentdb-modeling-data.md)
- [Partitionieren von Daten in DocumentDB](documentdb-partition-data.md)

Weitere Informationen zu DocumentDB finden Sie in der Azure DocumentDB-[Dokumentation](http://azure.microsoft.com/documentation/services/documentdb/). 



<!--HONumber=49-->