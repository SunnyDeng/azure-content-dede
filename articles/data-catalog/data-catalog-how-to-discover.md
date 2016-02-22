<properties
   pageTitle="Ermitteln von Datenquellen"
   description="Anleitungsartikel zum Ermitteln von Datenressourcen, die in Azure Data Catalog registriert wurden, einschließlich der Verwendung der Funktionen für Suchen und Filtern sowie Treffermarkierung im Azure Data Catalog-Portal."
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
   ms.date="02/08/2016"
   ms.author="maroche"/>


# Ermitteln von Datenquellen

## Einführung
**Microsoft Azure Data Catalog** ist ein vollständig verwalteter Clouddienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Mit anderen Worten ist es die Aufgabe von **Azure Data Catalog**, den Benutzern zu helfen, Datenquellen zu ermitteln, zu verstehen und zu nutzen, und Unternehmen zu helfen, mehr Nutzen aus ihren vorhandenen Daten zu ziehen. Nachdem eine Datenquelle in **Azure Data Catalog** registriert wurde, werden ihre Metadaten vom Dienst indiziert, sodass Benutzer benötigte Daten mühelos ermitteln können.

## Suchen und Filtern

Für die Ermittlung in **Azure Data Catalog** werden zwei primäre Mechanismen verwendet: Suchen und Filtern.

Die Suchfunktion ist intuitiv und leistungsstark. Suchbegriffe werden standardmäßig mit jeder Eigenschaft im Katalog abgeglichen, auch mit den von Benutzern hinzugefügten Anmerkungen.

Die Filterfunktion dient als Ergänzung der Suchfunktion. Benutzer können bestimmte Merkmale wie Experten, Datenquellentyp, Objekttyp und Tags auswählen, um nur übereinstimmende Datenressourcen anzuzeigen und Suchergebnisse auf übereinstimmende Ressourcen zu begrenzen.

Benutzer können durch eine Kombination von Suchen und Filtern schnell durch die in **Azure Data Catalog** registrierten Datenquellen navigieren, um die gewünschten Datenquellen zu ermitteln.

## Suchsyntax

Obwohl die standardmäßige Freitextsuche einfach und intuitiv ist, können Benutzer auch die Suchsyntax von **Azure Data Catalog** verwenden, um die Suchergebnisse noch präziser zu steuern. Die Suchfunktion von **Azure Data Catalog** unterstützt die folgenden Techniken:

| Technik | Verwenden Sie | Beispiel |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Standardsuche | Standardsuche mit mindestens einem Suchbegriff. Als Ergebnisse werden alle Ressourcen zurückgegeben, bei denen eine beliebige Eigenschaft mit den angegebenen Begriffen übereinstimmt. | Umsatzdaten |
| Eigenschaftsbasierte Suche | Es werden nur Datenquellen zurückgegeben, bei denen der Suchbegriff mit der angegebenen Eigenschaft übereinstimmt. | Name:Finanzen |
| Boolesche Operatoren | Die Suche wird mithilfe von booleschen Vorgängen erweitert oder eingegrenzt. | Finanzen NOT Unternehmen |
| Gruppierung mit Klammern | Teile der Abfrage werden mithilfe von Klammern gruppiert, um eine logische Isolierung zu erzielen (insbesondere in Verbindung mit booleschen Operatoren). | Name:Finanzen AND (Tags:Q1 OR Tags:Q2) |
| Vergleichsoperatoren | Für Eigenschaften mit numerischen Datentypen und Datumsdatentypen können andere Vergleiche als Gleichheit verwendet werden. | modifiedTime > "11/05/2014" |

Weitere Informationen zur Suche in **Azure Data Catalog** finden Sie unter [https://msdn.microsoft.com/library/azure/mt267594.aspx](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## Treffermarkierung
Beim Anzeigen von Suchergebnissen werden alle angezeigten Eigenschaften, die mit den angegebenen Suchbegriffen übereinstimmen (z. B. Name der Datenressource, Beschreibung und Tags), hervorgehoben, damit leichter zu erkennen ist, weshalb eine bestimmte Datenressource zurückgegeben wurde.

> [AZURE.NOTE] Benutzer können die Treffermarkierung bei Bedarf mit der Option „Hervorheben“ im **Azure Data Catalog**-Portal deaktivieren.

Beim Anzeigen von Suchergebnissen ist es selbst bei aktivierter Treffermarkierung u. U. nicht immer offensichtlich, warum eine Datenressource in den Ergebnissen enthalten ist. Da standardmäßig alle Eigenschaften durchsucht werden, kann eine Datenressource aufgrund einer Übereinstimmung mit einer Eigenschaft auf Spaltenebene zurückgegeben werden. Und da mehrere Benutzer registrierten Datenressourcen eigene Anmerkungen (Tags, Beschreibungen usw.) hinzufügen können, werden möglicherweise nicht alle Metadaten in der Liste der Suchergebnisse angezeigt.

In der standardmäßigen Kachelansicht enthält jede in den Suchergebnissen angezeigte Kachel ein Symbol „Übereinstimmungen mit dem Suchbegriff anzeigen“, mit dem der Benutzer schnell die Anzahl von Treffern und ihre Position anzeigen und ggf. zu den Treffern springen kann.

 ![Treffermarkierung und Suchergebnisse im Azure Data Catalog-Portal](./media/data-catalog-how-to-discover/search-matches.png)

## Zusammenfassung
Durch die Registrierung einer Datenquelle in **Azure Data Catalog** werden strukturelle und beschreibende Metadaten aus der Datenquelle in den Katalogdienst kopiert, sodass die Datenquelle leichter zu ermitteln und zu verstehen ist. Nachdem eine Datenquelle registriert wurde, kann sie mithilfe der Filter- und Suchfunktionen im **Azure Data Catalog**-Portal von Benutzern ermittelt werden.

<!---HONumber=AcomDC_0211_2016-->