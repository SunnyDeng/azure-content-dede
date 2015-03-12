<properties 
	pageTitle="Erstellen eines Datenbankkontos | Azure" 
	description="Erfahren Sie, wie Sie ein DocumentDB-NoSQL-Dokumentdatenbankkonto erstellen und Kontoeinstellungen im Azure-Vorschauportal wählen." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/09/2014" 
	ms.author="mimig"/>

#Erstellen eines Datenbankkontos
Sie müssen ein DocumentDB-Konto erstellen, wenn Sie Microsoft Azure DocumentDB verwenden möchten.  Dieses Thema beschreibt die Erstellung eines DocumentDB-Kontos im Azure-Vorschauverwaltungsportal.  


1.	Melden Sie sich beim [Vorschauverwaltungsportal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) an.
2.	Klicken Sie auf "Neu -> DocumentDB-Konto".  
	![][1]  

	Durchsuchen Sie alternativ über das Startmenü den Azure Marketplace, wählen Sie die Kategorie für "Daten und Analysen" aus, klicken Sie auf **DocumentDB** und dann auf **Erstellen**.  

	![][2]   

3. Geben Sie im Fenster **New DocumentDB (Preview)** die gewünschte Konfiguration für das DocumentDB-Konto an. 
 
	![][3] 


	- Geben Sie in das Feld **Id** einen Namen zur Identifizierung des DocumentDB-Kontos ein. Dieser Wert wird zum Hostnamen innerhalb des URI. Die ID darf nur Kleinbuchstaben, Ziffern und den Bindestrich "-" enthalten und muss zwischen 3 und 50 Zeichen lang sein. Beachten Sie, dass "documents.azure.com" an den gewählten Endpunktnamen angehängt wird, um den DocumentDB-Konto-Endpunkt zu erstellen.

	- Der Bereich **Preisebene** ist gesperrt, da die DocumentDB-Vorschau nur eine Standardpreisebene unterstützt. Weitere Informationen finden Sie unter [DocumentDB-Preise](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- Der Bereich **Optionale Konfiguration** dient zur Angabe der Anfangskapazität, die Ihrem DocumentDB-Konto zugewiesen wird.  DocumentDB verwendet Kapazitätseinheiten, um Ihnen die Skalierung Ihres DocumentDB-Kontos zu ermöglichen, wobei jede Kapazitätseinheit reservierten Datenbankspeicher und Durchsatz umfasst.  Standardmäßig wird 1 Kapazitätseinheit bereitgestellt.  Sie können die Anzahl der Kapazitätseinheiten, die Ihrem DocumentDB-Konto zur Verfügung steht, jederzeit im [Vorschauverwaltungsportal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) anpassen. Ausführliche Informationen zur DocumentDB-Kontokapazität und zum Durchsatz finden Sie im Artikel [Verwalten der Kapazität und Leistung von DocumentDB][documentdb-manage].

	- Unter **Ressourcengruppe** können Sie eine Ressourcengruppe für Ihr DocumentDB-Konto auswählen oder erstellen.  Standardmäßig wird eine neue Ressourcengruppe erstellt.  Sie können jedoch eine vorhandene Ressourcengruppe auswählen, zu der Sie Ihr DocumentDB-Konto hinzufügen möchten. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/).

	- Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Sie mit dem DocumentDB-Konto verwenden möchten. Wenn Ihr Konto nur über ein Abonnement verfügt, wird dieses Konto automatisch ausgewählt.*
 
	- Unter **Standort** können Sie einen geografischen Standort angeben, an dem Ihr DocumentDB-Konto gehostet werden soll.   

3.	Klicken Sie nach der Konfiguration der Optionen für das neue DocumentDB-Konto auf **Erstellen**.  Die Erstellung des DocumentDB-Kontos kann einige Minuten dauern.  Sie können den Fortschritt über das Startmenü überwachen, um den Status zu überprüfen.  
	![][4]  
  
	Sie können den Status auch über den Hub "Benachrichtigungen" überwachen.  

	![][5]  

	![][6]

4.	Nachdem das DocumentDB-Konto erstellt wurde, kann es mit den Standardeinstellungen verwendet werden.

	*Die Standardkonsistenz des DocumentDB-Kontos wird auf "Session" eingestellt.  Sie können die Standardkonsistenzeinstellung über das [Vorschauverwaltungsportal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) ändern.*  
	![][7]  

5.	Außerdem können Sie im Fensterbereich **Durchsuchen** auf Ihre vorhandenen DocumentDB-Konten zugreifen.  
	![][8]

##<a id="NextSteps"></a>Nächste Schritte
Informationen zu den ersten Schritten mit Azure DocumentDB finden Sie in folgenden Ressourcen:

-	[Ressourcenmodell und Konzepte von DocumentDB](/documentation/articles/documentdb-resources/)
-	[Interagieren mit DocumentDB-Ressourcen](/documentation/articles/documentdb-interactions-with-resources/)
-	[Erstellen eines DocumentDB-Kontos](/documentation/articles/documentdb-create-account/)
-	[Erste Schritte mit einem DocumentDB-Konto](/documentation/articles/documentdb-get-started/)

Weitere Informationen zu DocumentDB finden Sie in der Azure DocumentDB-Dokumentation auf [azure.com](http://go.microsoft.com/fwlink/p/?LinkID=402319)

[Gewusst wie: Erstellen eines DocumentDB-Kontos]: #Howto
[Nächste Schritte]: #NextSteps
[documentdb-manage]:../documentdb-manage/

<!--Image references-->
[1]: ./media/documentdb-create-account/ca1.png
[2]: ./media/documentdb-create-account/ca2.png
[3]: ./media/documentdb-create-account/ca3.png
[4]: ./media/documentdb-create-account/ca4.png
[5]: ./media/documentdb-create-account/ca5.png
[6]: ./media/documentdb-create-account/ca6.png
[7]: ./media/documentdb-create-account/ca7.png
[8]: ./media/documentdb-create-account/ca8.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
