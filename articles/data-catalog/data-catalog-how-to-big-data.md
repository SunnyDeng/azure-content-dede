<properties
   pageTitle="Arbeiten mit Big Data-Datenquellen"
   description="Gewusst-wie-Artikel, der die Muster zur Verwendung von Azure Data Catalog mit Big Data-Datenquellen hervorhebt, einschließlich Azure Blob Storage, Azure Data Lake und Hadoop HDFS."
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


# Arbeiten mit großen Datenquellen in Azure Data Catalog

## Einführung
**Microsoft Azure Data Catalog** ist ein vollständig verwalteter Clouddienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Mit anderen Worten ist es die Aufgabe von **Azure Data Catalog**, den Benutzern zu helfen, Datenquellen zu ermitteln, zu verstehen und zu nutzen, und Unternehmen zu helfen, mehr Nutzen aus ihren vorhandenen Datenquellen zu ziehen, einschließlich solchen mit Big Data.

**Azure Data Catalog** unterstützt die Registrierung von Azure-Blobspeicher-Blobs und -Verzeichnissen sowie Hadoop HDFS-Dateien und -Verzeichnisse. Die halbstrukturierte Natur dieser Datenquellen bietet eine umfassende Flexibilität, bedeutet aber auch, dass Benutzer die Organisation der Datenquellen berücksichtigen müssen, um durch das Registrieren bei **Azure Data Catalog** den größtmöglichen Nutzen zu ziehen.

## Verzeichnisse als logische Datensätze

Ein allgemeines Muster für die Organisation von großen Datenquellen ist, Verzeichnisse als logische Datensätze zu behandeln. Verzeichnisse der obersten Ebene werden verwendet, um einen Datensatz zu definieren, während Unterordner Partitionen definieren und die darin enthaltenen Dateien die Daten selbst speichern.

Ein Beispiel für dieses Muster könnte sein:

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

In diesem Beispiel stellen „vehicle\_maintenance\_events“ und „location\_tracking\_events“ logische Datensätze dar. Jeder dieser Ordner enthält die Datendateien, die nach Jahr und Monat in Unterordnern organisiert sind. Jeder dieser Ordner kann potenziell hunderte oder tausende von Dateien enthalten.

In diesem Muster ist es vermutlich nicht sinnvoll, einzelne Dateien bei **Azure Data Catalog** zu registrieren. Registrieren Sie stattdessen die Verzeichnisse, die die Datensätze darstellen, die für die mit den Daten arbeitenden Benutzer von Bedeutung sind.

## Verweisdatendateien

Ein ergänzendes Muster besteht darin, Verweisdatensätze als einzelne Dateien zu speichern. Diese Datensätze können als der "kleine" Teil der Big Data betrachtet werden und ähneln oft Dimensionen in einem analytischen Datenmodell. Verweisdatendateien enthalten Datensätze, die zum Bereitstellen des Kontexts für den größten Teil der Datendateien verwendet werden, die an anderer Stelle im Big Data-Speicher liegen.

Ein Beispiel für dieses Muster könnte sein:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Wenn ein Analytiker oder Datenwissenschaftler mit den in größeren Verzeichnisstrukturen enthaltenen Daten arbeitet, können die Daten in diesen Verweisdateien dazu verwendet werden, detailliertere Informationen zu Entitäten bereitzustellen, die nur durch den Namen oder die ID im größeren Datensatz bezeichnet werden.

In diesem Muster ist es sinnvoll, die einzelnen Verweisdatendateien bei **Azure Data Catalog** zu registrieren. Jede Datei stellt einen Datensatz dar, und jede kann einzeln kommentiert und ermittelt werden.

## Alternative Muster

Die oben beschriebenen Muster sind nur zwei Möglichkeiten, wie ein großer Datenspeicher organisiert werden kann. Jede Implementierung ist dabei anders. Unabhängig davon, wie Ihre Datenquellen strukturiert sind, sollten Sie sich beim Registrieren großer Datenquellen bei **Azure Data Catalog** darauf konzentrieren, die Dateien und Verzeichnisse zu registrieren, die die Datensätze darstellen, die für andere Personen in Ihrer Organisation von Nutzen sind. Das Registrieren aller Dateien und Verzeichnisse kann den Katalog überlasten, wodurch es den Benutzern erschwert wird, das zu suchen, was sie benötigen.

## Zusammenfassung
Das Registrieren von Datenquellen bei **Azure Data Catalog** erleichtert deren Ermittlung und Verständnis. Durch das Registrieren und Kommentieren der Big Data-Dateien und -Verzeichnisse, die logische Datensätze darstellen, können Ihre Benutzer leichter von ihnen benötigte große Datenquellen suchen und verwenden.

<!---HONumber=AcomDC_0211_2016-->