<properties 
	pageTitle="Verwalten der Infrastrukturkapazität"
	description="Mit dem Capacity Planning Intelligence Pack in Microsoft Azure Operational Insights können Sie die Kapazität Ihrer Serverinfrastruktur erfassen."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor="tysonn" />

<tags 
	ms.service="operational-insights"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="03/20/2015"
	ms.author="banders" />

# Verwalten der Infrastrukturkapazität

Mit dem Capacity Planning Intelligence Pack in Microsoft Azure Operational Insights können Sie die Kapazität Ihrer Serverinfrastruktur erfassen. Sie installieren das Intelligence Pack, um den Operations Manager-Agent und das Basiskonfigurationsmodul für Operational Insights zu aktualisieren. Das Intelligence Pack liest die Leistungsindikatoren auf dem überwachten Server und sendet Daten zur Verarbeitung an den Operational Insights-Dienst in der Cloud. Auf die Nutzungsdaten wird Logik angewendet, und der Cloud-Dienst zeichnet die Daten auf. Mit der Zeit entstehen Verwendungsmuster, und die Kapazität wird anhand des aktuellen Verbrauchs prognostiziert.

Beispielsweise kann anhand einer Prognose ermittelt werden, wann zusätzliche Prozessorkerne oder zusätzlicher Arbeitsspeicher für einen einzelnen Server benötigt wird. In diesem Beispiel kann die Prognose darauf hinweisen, dass der Server in 30 Tagen zusätzlichen Arbeitsspeicher benötigen wird. Damit können Sie ein Speicherupgrade für das nächste Wartungsfenster des Servers planen, das zum Beispiel einmal alle zwei Wochen ansteht.

## Das Dashboard "Capacity Management"

Bevor Sie das Dashboard "Capacity Management" in Microsoft Azure Operational Insights verwenden können, müssen Sie das Intelligence Pack installiert haben. Weitere Informationen zum Installieren von Intelligence Packs finden Sie unter [Verwenden der Galerie zum Hinzufügen oder Entfernen von Intelligence Packs](operational-insights-add-intelligence-packs.md). Nach der Installation des Capacity Planning Intelligence Packs können Sie die Kapazität der überwachten Server in Operational Insights über die Kachel **Capacity Planning** auf der Seite Dashboard **Overview** anzeigen. 

![Abbildung der Kachel "Capacity Planning"](./media/operational-insights-capacity/overview-cap-plan.png)

Über die Kachel wird das Dashboard **Capacity Planning** geöffnet, auf der Sie eine Zusammenfassung Ihrer Serverkapazität anzeigen können. Die Seite zeigt die folgenden Kacheln, auf die Sie klicken können:

- *Virtual machine count*: Zeigt die Anzahl der Tage, die für die Kapazität virtueller Computer verbleiben

- *Compute*: Zeigt Prozessorkerne und verfügbaren Speicher

- *Storage*: Zeigt den Speicherplatz und die durchschnittliche Latenz der Datenträger

- *Search*: Daten-Explorer, in dem Sie Daten im Operational Insights-System suchen können

>[AZURE.NOTE] Um Kapazitätsverwaltungsdaten anzuzeigen, müssen Sie die Operations Manager-Verbindung mit Virtual Machine Manager (VMM) aktivieren. Zusätzliche Informationen zum Verbinden der Systeme finden Sie unter "Verbinden von VMM mit Operations Manager".

![Abbildung des Dashboards "Capacity Management"](./media/operational-insights-capacity/gallery-capacity-01.png)

### So zeigen Sie eine Kapazitätsseite an

- Klicken Sie auf der Seite **Overview** auf **Capacity Management** und dann auf **Compute** oder **Storage**.

## Seite "Compute"

Im Dashboard **Compute** in Microsoft Azure Operational Insights können Sie Kapazitätsdaten zur Auslastung, projizierte Tage von Kapazität und die Effizienz Ihrer Infrastruktur anzeigen. Im Bereich **Utilization** können Sie die CPU-Kern- und Arbeitsspeicherauslastung der Hosts der virtuellen Computer anzeigen. Mithilfe des Projektionstools können Sie schätzen, wie viel Kapazität in einem bestimmten Zeitraum verfügbar sein wird. Im Bereich **Efficiency** können Sie feststellen, wie effizient die Hosts Ihrer virtuellen Computer sind. Sie können Informationen zu verknüpften Elementen anzeigen, indem Sie darauf klicken.

Sie können eine Excel-Arbeitsmappe für die folgenden Kategorien erstellen:

- Wichtigste Hosts mit der höchsten Kernauslastung

- Wichtigste Hosts mit der höchsten Arbeitsspeicherauslastung

- Wichtigste Hosts mit ineffizienten virtuellen Computern

- Wichtigste Hosts nach Auslastung

- Am wenigsten wichtige Hosts nach Auslastung

![Abbildung der "Capacity Management"-Seite "Compute"](./media/operational-insights-capacity/gallery-capacity-02.png)


Die folgenden Bereiche werden auf dem Dashboard **Compute** gezeigt:

**Utilization**: CPU-Kern- und Arbeitsspeicherauslastung der Hosts Ihrer virtuellen Computer.

- *Used Cores*: Summe für alle Hosts (% der CPU-Auslastung multipliziert mit der Anzahl der physischen Kerne auf dem Host).

- *Free Cores*: Gesamtanzahl physischer Kerne minus verwendete Kerne.

- *Percentage Cores Available*: Freie physische Kerne dividiert durch die Gesamtanzahl physischer Kerne.

- *Virtual Cores per VM*: Gesamtanzahl virtueller Kerne im System dividiert durch die Gesamtanzahl virtueller Computer im System.

- *Virtual Cores to Physical Cores Ratio*: Verhältnis der Gesamtanzahl physischer Kerne und der physischen Kerne, die von virtuellen Computern im System verwendet werden.

- *Number of virtual Cores Available*: Verhältnis virtueller Kerne und physischer Kerne multipliziert mit den verfügbaren physischen Kernen.

- *Used Memory*: Gesamtmenge des von allen Hosts verwendeten Arbeitsspeichers.

- *Free Memory*: Gesamter physischer Arbeitsspeicher minus verwendeter Arbeitsspeicher.

- *Percentage Memory Available*: Freier physischer Arbeitsspeicher dividiert durch den gesamten physischen Arbeitsspeicher.

- *Virtual Memory per VM*: Gesamter virtueller Arbeitsspeicher im System dividiert durch die Gesamtzahl der virtuellen Computer im System.

- *Virtual Memory to Physical Memory Ratio*: Gesamter virtueller Arbeitsspeicher im System dividiert durch den gesamten physischen Arbeitsspeicher im System.

- *Virtual Memory Available*: Verhältnis von virtuellem und physischem Arbeitsspeicher multipliziert mit dem verfügbaren physischen Arbeitsspeicher.

**Projektionstool**

Mithilfe des Projektionstools können Sie historische Trends für die Ressourcenauslastung anzeigen. Dies schließt die Auslastungstrends für virtuelle Computer, Arbeitsspeicher, Kerne und Speicher ein. Die Projektionsfunktion verwendet einen Projektionsalgorithmus, über den Ihnen mitgeteilt wird, wann die einzelnen Ressourcen knapp werden. Dadurch lässt sich eine exakte Kapazitätsplanung durchführen, sodass Sie wissen, wann Sie mehr Kapazität (z. B. Arbeitsspeicher, Kerne oder Speicher) erwerben müssen.

**Effizienz**

- *Idle VM*: Verwendung von weniger als 10 % der CPU-Leistung und 10 % des Arbeitsspeichers im angegebenen Zeitraum.

- *Overutilized VM*: Verwendung von mehr als 90 % der CPU-Leistung und 90 % des Arbeitsspeichers im angegebenen Zeitraum.

- *Idle Host*: Verwendung von weniger als 10 % der CPU-Leistung und 10 % des Arbeitsspeichers im angegebenen Zeitraum.

- *Overutilized Host*: Verwendung von mehr als 90 % der CPU-Leistung und 90 % des Arbeitsspeichers im angegebenen Zeitraum.

### Arbeiten mit Elementen auf der Seite "Compute"

1. Im Dashboard **Compute** können Sie im Bereich **Utilization** die Kapazitätsinformationen zu den verwendeten CPU-Kernen und zum verwendeten Arbeitsspeicher anzeigen.

2. Klicken Sie auf ein Element, um es auf der Seite **Search** zu öffnen und detaillierte Informationen dazu anzuzeigen.

3. Verschieben Sie im Tool **Projection** den Datumsschieberegler, um eine Projektion für die Kapazität anzuzeigen, die an dem von Ihnen gewählten Datum verwendet werden wird.

3. Im Bereich **Efficiency** können Sie Kapazitätseffizienzinformationen zu virtuellen Computern und Hosts für virtuelle Computer anzeigen.

##Seite "Direct Attached Storage"

Mithilfe des Dashboards **Direct Attached Storage** in Microsoft Azure Operational Insights können Sie Kapazitätsinformationen zur Speicherplatzauslastung, Datenträgerleistung und zu den projizierten Tagen der Datenträgerkapazität anzeigen. Im Bereich **Auslastung** können Sie die Speicherplatzbelegung der Hosts Ihrer virtuellen Computer anzeigen. m Bereich **Datenträgerleistung** können Sie den Datenträgerdurchsatz und die Latenz der Hosts Ihrer virtuellen Computer anzeigen. Sie können außerdem mit dem Projektionstool abschätzen, wie viel Kapazität für einen bestimmten Zeitraum voraussichtlich verfügbar sein wird. Sie können Informationen zu verknüpften Elementen anzeigen, indem Sie darauf klicken.

Sie können aus diesen Kapazitätsinformationen eine Excel-Arbeitsmappe für die folgenden Kategorien generieren:

- Maximale Speicherplatzbelegung nach Host

- Maximale durchschnittliche Latenz nach Host

Die folgenden Bereiche werden auf der Seite **Storage** angezeigt:

- *Utilization*: Anzeigen der Speicherplatzbelegung auf den Hosts Ihrer virtuellen Computer.

- *Total Disk Space*: Summe (logischer Speicherplatz) für alle Hosts.

- *Used Disk Space*: Summe (verwendeter logische Speicherplatz) für alle Hosts.

- *Available Disk Space*: Gesamter Speicherplatz abzüglich verwendeter Speicherplatz.

- *Percentage Disk Used*: Verwendeter Speicherplatz geteilt durch den Gesamtspeicherplatz.

- *Percentage Disk Available*: Verfügbarer Speicherplatz geteilt durch den Gesamtspeicherplatz.

![Abbildung der "Capacity Management"-Seite "Direct Attached Storage"](./media/operational-insights-capacity/gallery-capacity-03.png)

**Datenträgerleistung**

Mithilfe von Operational Insights können Sie den historischen Nutzungstrend Ihres Speicherplatzes anzeigen. Die Projektionsfunktion verwendet einen Algorithmus, um die künftige Verwendung vorherzusagen. Insbesondere bei der Speicherplatzverwendung ermöglicht Ihnen die Projektionsfunktion die Vorhersage, wann Sie eventuell keinen Speicherplatz mehr zur Verfügung haben werden. Dies hilft Ihnen bei der Planung von ausreichendem Speicher, sodass Sie wissen, wann Sie zusätzlichen Speicher erwerben müssen.

**Projektionstool**

Mithilfe des Projektionstools können Sie historische Trends für die Speicherplatzauslastung anzeigen. Die Projektionsfunktion ermöglicht Ihnen außerdem die Vorhersage, wann nicht mehr genügend Speicherplatz vorhanden sein wird. Dies hilft Ihnen bei der Planung von ausreichender Kapazität, sodass Sie wissen, wann Sie zusätzliche Speicherkapazität erwerben müssen.

### Arbeiten mit Elementen auf der Seite "Direct Attached Storage"

1. Auf der Seite **Direct Attached Storage** können Sie im Bereich **Utilization** die Informationen zur Datenträgerauslastung anzeigen.

2. Klicken Sie auf ein verknüpftes Element, um es auf der Seite **Search** zu öffnen und detaillierte Informationen dazu anzuzeigen.

3. lm Bereich **Disk Performance** können Sie den Datenträgerdurchsatz und Informationen zur Latenz anzeigen.

4. Verschieben Sie im Tool **Projection** den Datumsschieberegler, um eine Projektion für die Kapazität anzuzeigen, die an dem von Ihnen gewählten Datum verwendet werden wird.



<!--HONumber=52-->