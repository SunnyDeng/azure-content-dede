<properties
   pageTitle="Hinzufügen von Anmerkungen zu Datenquellen"
   description="Anleitungsartikel zum Hinzufügen von Anmerkungen zu Datenressourcen in Azure Data Catalog, einschließlich Anzeigenamen, Tags, Beschreibungen und Experten."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="08/17/2015"
   ms.author="maroche"/>


# Hinzufügen von Anmerkungen zu Datenquellen

## Einführung
**Microsoft Azure Data Catalog** ist ein vollständig verwalteter Clouddienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Mit anderen Worten ist es die Aufgabe von **Azure Data Catalog**, den Benutzern zu helfen, Datenquellen zu ermitteln, zu verstehen und zu nutzen, und Unternehmen zu helfen, mehr Nutzen aus ihren vorhandenen Daten zu ziehen. Wenn eine Datenquelle in **Azure Data Catalog** registriert wird, werden ihre Metadaten vom Dienst kopiert und indiziert. Dies ist aber noch nicht alles. **Azure Data Catalog** bietet Benutzern die Möglichkeit, die aus der Datenquelle extrahierten Metadaten durch eigene beschreibende Metadaten (z. B. Beschreibungen und Tags) zu ergänzen, um die Datenquelle für andere Benutzer verständlicher zu machen.

## Anmerkung und Crowdsourcing
Jeder hat eine Meinung. Und das ist auch gut so. **Azure Data Catalog** berücksichtigt die Tatsache, dass unterschiedliche Benutzer Unternehmensdatenquellen aus verschiedenen Perspektiven betrachten und jede dieser Perspektiven nützlich sein kann. Stellen Sie sich folgendes Szenario vor:

* Der Systemadministrator kennt die Vereinbarung zum Servicelevel für die Server und Dienste, die die Datenquelle hosten.
* Der Datenbankadministrator kennt den Sicherungszeitplan für jede Datenbank und die zulässigen ETL-Verarbeitungsfenster.
* Der Systembesitzer weiß, wie Benutzer Zugriff auf die Datenquelle anfordern.
* Der Data Steward weiß, wie die Ressourcen und Attribute in der Datenquelle dem Unternehmensdatenmodell zugeordnet sind.
* Der Analyst weiß, wie die Daten im Kontext der von ihm unterstützten Geschäftsprozesse verwendet werden.

Da jede dieser Perspektiven wertvoll ist, verwendet **Azure Data Catalog** einen Crowdsourcing-Ansatz für Metadaten. Auf diese Weise kann jede Perspektive berücksichtigt und ein lückenloses Bild der registrierten Datenquellen bereitgestellt werden. Mithilfe des **Azure Data Catalog**-Portals kann jeder Benutzer eigene Anmerkungen hinzufügen und bearbeiten und gleichzeitig die Anmerkungen anderer Benutzer anzeigen.

## Anmerkungstypen
In der Vorschauversion von **Azure Data Catalog** werden die folgenden Anmerkungstypen unterstützt:

| Anmerkung | Hinweise |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Anzeigename | Anzeigenamen können auf der Datenressourcenebene angegeben werden, um das Verständnis der Datenressource zu erleichtern. Anzeigenamen sind besonders hilfreich, wenn der zugrunde liegende Objektname unverständlich, abgekürzt oder für Benutzer nicht aussagekräftig ist. |
| Beschreibung | Beschreibungen können auf der Datenressourcen- und Attribut-/Spaltenebene angegeben werden. Beschreibungen sind kurze Textanmerkungen ohne vorgegebene Form, die beschreiben, was der Benutzer über die Datenressource weiß oder wie er sie verwendet. |
| Tags | Tags können auf der Datenressourcen- und Attribut-/Spaltenebene angegeben werden. Tags sind benutzerdefinierte Bezeichnungen, die zum Kategorisieren von Datenressourcen oder Attributen verwendet werden können. |
| Experten | Experten können auf der Datenressourcenebene angegeben werden. Experten sind Benutzer oder Gruppen, die über fundiertes Wissen über die Daten verfügen und als Ansprechpartner für Benutzer fungieren können, die die registrierten Datenquellen finden und Fragen haben, die durch die vorhandenen Anmerkungen nicht beantwortet werden. |
| Zugriff anfordern | Anmerkungen vom Typ „Zugriff anfordern“ können auf der Datenressourcenebene angegeben werden. Diese Anmerkung ist für Benutzer vorgesehen, die eine Datenquelle finden, für die sie noch keine Zugriffsberechtigung besitzen. Benutzer können die E-Mail-Adresse des Benutzers oder der Gruppe, der bzw. die für die Erteilung der Zugriffsberechtigung zuständig ist, die URL des für den Zugriff erforderlichen Prozesses oder Tools oder den Prozess selbst als Text eingeben. |

## Hinzufügen einer Anmerkung zu mehreren Datenressourcen
Benutzer können mehrere Datenressourcen im **Azure Data Catalog**-Portal auswählen und allen ausgewählten Ressourcen in nur einem Arbeitsschritt eine Anmerkung hinzufügen. Da Anmerkungen für alle ausgewählten Datenressourcen gelten, ist es ganz einfach, einheitliche Beschreibungen, Tags und Experten für verwandte Datenressourcen bereitzustellen.

> [AZURE.NOTE]Tags und Experten können auch beim Registrieren von Datenressourcen mit dem **Azure Data Catalog**-Tool zur Datenquellenregistrierung angegeben werden.

Bei Auswahl mehrerer Tabellen und Ansichten werden nur Spalten, die in allen Datenressourcen vorhanden sind, im **Azure Data Catalog**-Portal angezeigt. So können Benutzer Tags und Beschreibungen für alle Spalten hinzufügen, die für alle ausgewählten Datenressourcen den gleichen Namen aufweisen.

## Anmerkungen und Ermittlung
Genau wie die Metadaten, die bei der Registrierung aus der Datenquelle extrahiert und dem **Azure Data Catalog**-Suchindex hinzugefügt werden, werden auch die vom Benutzer angegebenen Metadaten indiziert. Anmerkungen erleichtern Benutzern somit nicht nur das Verständnis der gefundenen Daten, sondern auch die Ermittlung von Datenressourcen, da sie anhand der für sie sinnvollen Begriffe nach ihnen können.

## Zusammenfassung
Durch die Registrierung einer Datenquelle in **Azure Data Catalog** werden strukturelle und beschreibende Metadaten aus der Datenquelle in den Katalogdienst kopiert, sodass die darin enthaltenen Daten leichter auffindbar sind. Sobald eine Datenquelle registriert wurde, können Benutzer im **Azure Data Catalog**-Portal Anmerkungen hinzufügen, um das Verständnis der Daten zu erleichtern und die Ermittlung zu vereinfachen.

<!---HONumber=Oct15_HO3-->