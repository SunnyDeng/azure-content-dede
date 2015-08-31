<properties
	pageTitle="Excel-Add-In für Machine Learning-Webdienste | Microsoft Azure"
	description="Direkter Zugriff auf Azure Machine Learning-Webdienste ohne Erstellung von Code."
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"
    tags=""/>

<tags
	ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/10/2015"
	ms.author="tedway;garye" />

# Excel-Add-In für Azure Machine Learning-Webdienste

Mit Excel können Webdienste auf einfache Weise direkt aufgerufen werden, ohne dafür Code erstellen zu müssen.

## Schritte zum Verwenden eines vorhandenen Webdiensts

1. Öffnen Sie die Excel-Beispieldatei hier oder auf der Registerkarte **DASHBOARD** der Webdienste.
2. Wählen Sie einen Webdienst aus, indem Sie darauf klicken – in diesem Beispiel ist das der "Titanic Survivor Predictor (Excel Add-In-Beispiel) [Ergebnis]".

    ![Webdienst auswählen][01]

3. Dadurch gelangen Sie zum Abschnitt **Predict**. Wählen Sie eine Zelle in Excel aus, und klicken Sie auf **Use sample data**. Bearbeiten Sie die Daten optional in Excel.

	![Abschnitt „Predict“][02]

4. Wählen Sie die Daten aus, und klicken Sie auf das Symbol für den Eingabedatenbereich.
5. Geben Sie unter **Output** die Zellennummer ein, in der die Ausgabe erfolgen soll.
6. Klicken Sie auf **Predict**.

## Schritte zum Hinzufügen eines neuen Webdiensts

1. Veröffentlichen eines Webdiensts (auf [dieser Seite](machine-learning-walkthrough-5-publish-web-service.md) erfahren Sie, wie das geht) oder Suchen eines vorhandenen Webdiensts.
2. Wenn Sie über einen Webdienst verfügen, klicken Sie im linken Bereich von Machine Learning Studio auf den Abschnitt **WEB SERVICES**, und wählen Sie dann den zu nutzenden Webdienst aus.
3. Kopieren Sie aus der Registerkarte **DASHBOARD** für den Webdienst den API-Schlüssel für den Webdienst.
4. Navigieren Sie in Excel zum Abschnitt **Webdienste** (wenn Sie sich im Abschnitt **Predict** befinden, klicken Sie auf den Rückwärtspfeil, um zur Liste der Webdienste zu gelangen).

	![Abschnitt „Webdienste“][03]

5. Klicken Sie auf **Webdienst hinzufügen**.
6. Fügen Sie den Schlüssel in das Textfeld mit der Beschriftung **API-Schlüssel** ein.
7. Klicken Sie auf der Registerkarte **DASHBOARD** für den Webdienst auf die Verknüpfung **API-Hilfeseite**.
8. Kopieren Sie den Anforderungs-URI unter **Request**, und fügen Sie dies in das Textfeld mit der Beschriftung **URL** ein.
9. Klicken Sie auf **Hinzufügen**.

	![URL und API-Schlüssel][04]

10.	Folgen Sie den Anweisungen „Schritte zum Verwenden eines vorhandenen Webdiensts“ oben, um den Webdienst zu verwenden.

## Freigeben Ihrer Arbeitsmappe

Wenn Sie Ihre Arbeitsmappe speichern, werden auch die API-Schlüssel der hinzugefügten Webdienste gespeichert. Das bedeutet, dass Sie die Arbeitsmappe nur für Personen freigeben sollten, denen Sie vertrauen.


[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png

<!---HONumber=August15_HO8-->