<properties
	pageTitle="Gewusst wie: Migrieren von Logik-Apps zur Schemaversion 2015-08-01-preview | Microsoft Azure App Service"
	description="Sie können Logik-Apps einfach zur neuesten Schemaversion migrieren: Führen Sie einfach die folgenden Schritte aus:"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/25/2016"
	ms.author="deonhe"/>

# Gewusst wie: Migrieren von Logik-Apps zur Schemaversion 2015-08-01-preview

Um Ihre vorhandenen Logik-Apps in das neue Schema zu verschieben, führen Sie folgende Schritte aus:
1. Öffnen Sie Ihre Logik-App im Azure-Portal.  
2. Klicken Sie auf „Schema aktualisieren“.

 ![API-Symbol][step1] Die Seite „Schema aktualisieren“ wird angezeigt. Sie enthält einen Link zu einem Dokument, das Details zu den Verbesserungen des neuen Schemas bereitstellt: ![API-Symbol][step2]

>[AZURE.NOTE] Wenn Sie die Option **Schema aktualisieren** auswählen, führen wir automatisch die Migrationsschritte aus und stellen die Codeausgabe für Sie bereit. Hiermit können Sie Ihre Definition aktualisieren. Halten Sie sich bei der Codeerstellung jedoch an bewährte Verfahren, wie sie beispielsweise weiter unten im Abschnitt **Bewährte Methoden** beschrieben werden.

##Bewährte Methoden beim Migrieren von Logik-Apps zur neuesten Schemaversion:  
Nicht überschreiben, beginnen Sie mit der Migration zu den neuen APIs.

- Kopieren Sie das migrierte Skript in eine neue Logik-App – Überschreiben Sie nicht die alte App, bis Sie Ihre Tests abgeschlossen und bestätigt haben, dass die migrierte App wie erwartet funktioniert.
- Testen Sie Ihre Logik-App, **bevor** sie in Produktion geht.
- Beginnen Sie nach Abschluss der Migration mit der Aktualisierung Ihrer Logik-Apps, um nach Möglichkeit die [verwalteten APIs](./apis-list.md) zu nutzen. Beispielsweise können Sie Dropbox v2 dort verwenden, wo Sie bisher DropBox v1 verwendet haben.


## Nächste Schritte
-  [Informationen zum manuellen Migrieren von Logik-Apps](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png

<!---HONumber=AcomDC_0330_2016-->