<properties 
   pageTitle="Entwickeln von benutzerdefinierten U-SQL-Operatoren für Azure Data Lake Analytics-Aufträge | Azure" 
   description="Hier erfahren Sie, wie Sie benutzerdefinierte Operatoren zur Verwendung und Wiederverwendung in Data Lake Analytics-Aufträgen entwickeln." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/10/2016"
   ms.author="jgao"/>


# Entwickeln von benutzerdefinierten U-SQL-Operatoren für Azure Data Lake Analytics-Aufträge

Hier erfahren Sie, wie Sie benutzerdefinierte Operatoren zur Verwendung und Wiederverwendung in Data Lake Analytics-Aufträgen entwickeln. Im Folgenden wird erläutert, wie Sie einen benutzerdefinierten Operator zum Konvertieren von Ländernamen entwickeln.

##Voraussetzungen

- Visual Studio 2015, Visual Studio 2013 Update 4 oder Visual Studio 2012 mit Installation von Visual C++ 
- Microsoft Azure SDK für .NET, Version 2.5 oder höher. Führen Sie die Installation mit dem Webplattform-Installer durch.
- Data Lake Analytics-Konto. Siehe [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md).
- Arbeiten Sie das Lernprogramm [Erste Schritte mit U-SQL-Studio für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md) durch.
- Stellen Sie eine Verbindung mit Azure her (siehe [Erste Schritte mit U-SQL-Studio für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md#connect-to-azure)). 
- Laden Sie die Datenquelle hoch (siehe [Erste Schritte mit U-SQL-Studio für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md#upload-source-data-files)). 

## Definieren und Verwenden von benutzerdefinierten Operatoren in U-SQL

**So erstellen und übermitteln Sie einen U-SQL-Auftrag**

1. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.
2. Wählen Sie den Typ **U-SQL-Projekt** aus.

	![Neues U-SQL-Projekt in Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Klicken Sie auf **OK**. Visual Studio erstellt eine Projektmappe mit der Datei „Script.usql“.
4. Erweitern Sie im **Projektmappen-Explorer** die Datei „Script.usql“, und doppelklicken Sie dann auf **Script.usql.cs**.
5. Fügen Sie den folgenden Code in die Datei ein:

		using Microsoft.Analytics.Interfaces;
		using System.Collections.Generic;
		
		namespace USQL_UDO
		{
			public class CountryName : IProcessor
			{
				private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
				{
					{
						"Deutschland", "Germany"
					},
					{
						"Schwiiz", "Switzerland"
					},
					{
						"UK", "United Kingdom"
					},
					{
						"USA", "United States of America"
					},
					{
						"中国", "PR China"
					}
				};
		
				public override IRow Process(IRow input, IUpdatableRow output)
				{
		
					string UserID = input.Get<string>("UserID");
					string Name = input.Get<string>("Name");
					string Address = input.Get<string>("Address");
					string City = input.Get<string>("City");
					string State = input.Get<string>("State");
					string PostalCode = input.Get<string>("PostalCode");
					string Country = input.Get<string>("Country");
					string Phone = input.Get<string>("Phone");
		
					if (CountryTranslation.Keys.Contains(Country))
					{
						Country = CountryTranslation[Country];
					}
					output.Set<string>(0, UserID);
					output.Set<string>(1, Name);
					output.Set<string>(2, Address);
					output.Set<string>(3, City);
					output.Set<string>(4, State);
					output.Set<string>(5, PostalCode);
					output.Set<string>(6, Country);
					output.Set<string>(7, Phone);
		
					return output.AsReadOnly();
				}
			}
		}

5. Öffnen Sie die Datei „Script.usql“, und fügen Sie das folgende U-SQL-Skript ein:

		@drivers =
			EXTRACT UserID      string,
					Name        string,
					Address     string,
					City        string,
					State       string,
					PostalCode  string,
					Country     string,
					Phone       string
			FROM "/Samples/Data/AmbulanceData/Drivers.txt"
			USING Extractors.Tsv(Encoding.Unicode);
		
		@drivers_CountryName =
			PROCESS @drivers
			PRODUCE UserID string,
					Name string,
					Address string,
					City string,
					State string,
					PostalCode string,
					Country string,
					Phone string
			USING new USQL_UDO.CountryName();    
		
		OUTPUT @drivers_CountryName
			TO "/Samples/Outputs/Drivers.csv"
			USING Outputters.Csv(Encoding.Unicode);

6. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und klicken Sie dann auf **Skript erstellen**.
6. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und klicken Sie dann auf **Skript senden**.
7. Wenn Sie noch nicht mit Ihrem Azure-Abonnement verbunden sind, werden Sie zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto aufgefordert.
7. Klicken Sie auf **Senden**. Nach Abschluss der Übermittlung werden Ergebnisse und ein Auftragslink im Fenster „Ergebnisse“ angezeigt.
8. Sie müssen auf die Schaltfläche „Aktualisieren“ klicken, um den letzten Auftragsstatus anzuzeigen und den Bildschirm zu aktualisieren.

**So zeigen Sie die Auftragsausgabe an**

1. Erweitern Sie im **Server-Explorer** nacheinander **Azure**, **Data Lake Analytics**, Ihr Data Lake Analytics-Konto und **Speicherkonten**. Klicken Sie mit der rechten Maustaste auf den Standardspeicher, und klicken Sie dann auf **Explorer**. 
2. Erweitern Sie „Beispielcode“ und „Ausgaben“, und doppelklicken Sie dann auf **Drivers.csv**.


##Weitere Informationen

- [Erste Schritte mit Data Lake Analytics mithilfe von PowerShell](data-lake-analytics-get-started-powershell.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)
- [Verwenden von Data Lake-Tools für Visual Studio zum Entwickeln von U-SQL-Anwendungen](data-lake-analytics-data-lake-tools-get-started.md)

<!---HONumber=AcomDC_0218_2016-->