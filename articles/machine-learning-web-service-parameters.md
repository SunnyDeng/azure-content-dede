<properties 
	pageTitle="Verwenden von Azure Machine Learning-Webdienstparametern | Azure" 
	description="Anleitung: Verwenden von Azure Machine Learning Webdienstparametern, um das Verhalten des Modells beim Zugriff auf den Webdienst zu ändern." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="raymondl;garye"/>

#Verwenden von Azure Machine Learning-Webdienstparametern

Ein Azure Machine Learning-Webdienst wird erstellt, indem ein Experiment veröffentlicht wird, das Module mit konfigurierbaren Parametern enthält. In einigen Fällen kann es vielleicht erforderlich sein, das Verhalten des Moduls zu ändern, während der Webdienst ausgeführt wird. *Webdienstparameter* erlauben Ihnen, dies zu tun.

Ein typisches Beispiel ist das Einrichten des [Reader][reader]-Moduls, damit die Benutzer des veröffentlichten Webdiensts beim Zugreifen auf den Webdienst eine andere Datenquelle angeben können. Ein weiteres Beispiel ist die Konfiguration des [Writer][writer]-Moduls, damit ein anderes Ziel angegeben werden kann. Zu weiteren Beispielen zählen die Änderung der Anzahl von Bits für das Modul [Feature Hashing][feature-hashing] oder der Anzahl der gewünschten Funktionen für das Modul  [Filter-Based Feature Selection][filter-based-feature-selection].

Sie können Webdienstparameter definieren und einem oder mehreren Modulparametern zuordnen, und Sie können angeben, ob diese Parameter erforderlich oder optional sind. Der Benutzer des Webdiensts kann dann beim Zugreifen auf den Dienst Werte für diese Parameter angeben. Das Verhalten des Moduls wird dann entsprechend angepasst.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../includes/machine-learning-free-trial.md)]


##Festlegen und Verwenden von Webdienstparametern

Sie definieren einen Webdienstparameter, indem Sie auf das Symbol neben dem Parameter für ein Modul klicken und "Set as web service parameter" \(Als Webdienstparameter festlegen\) auswählen. Daraufhin wird ein neuer Webdienstparameter erstellt und mit diesem Modulparameter verbunden. Wenn auf den Webdienst zugegriffen wird, kann der Benutzer kann einen Wert für den Webdienstparameter angeben, der dann auf den Modulparameter angewendet wird.

Nachdem Sie einen Web Service-Parameter definiert haben, steht dieser für andere Modulparameter im Experiment zur Verfügung. Sie können einen Webdienstparameter, den Sie definiert und einem Modulparameter zugeordnet haben, auch für einen Parameter eines anderen Moduls verwenden, sofern der betreffende Parameter denselben Typ von Wert erwartet. Wenn der Webdienstparameter z. B. ein numerischer Wert ist, kann er nur für Modulparameter verwendet werden, die einen numerischen Wert erwarten. Wenn der Benutzer einen Wert für den Webdienstparameter festlegt, wird dieser auf alle zugehörigen Modulparameter angewendet.

Sie können entscheiden, ob Sie einen Standardwert für den Webdienstparameter angeben. Wenn Sie dies tun, ist der Parameter für die Benutzer des Webdiensts optional. Geben Sie keinen Standardwert an, dann muss der Benutzer einen Wert eingeben, wenn auf den Webdienst zugegriffen wird.

Die Dokumentation für den Webdienst \(die über den Link zur **API-Hilfeseite** im Webdienst-**DASHBOARD** in Machine Learning Studio bereitgestellt wird\) enthält Informationen, der die Webdienstbenutzer entnehmen können, wie der Webdienstparameter beim Zugriff auf den Webdienst programmgesteuert angegeben werden kann.


##Beispiel

Nehmen wir beispielsweise an, wir hätten ein Experiment mit einem [Writer][writer]-Modul, das Informationen zum Azure-Blob-Speicher sendet. Wir definieren einen Webdienstparameter namens "Blob path", mit dem die Benutzer des Webdienst beim Zugriff auf den Dienst den Blob-Speicherpfad ändern können.

1.	Klicken Sie in Machine Learning Studio auf das [Writer][writer]-Modul, um es auszuwählen. Die Moduleigenschaften werden rechts neben dem Canvas mit dem Experiment im Eigenschaftenbereich angezeigt.

2.	Geben Sie den Speichertyp an:

    - Wählen Sie unter **Please specify data destination** \(Datenziel angeben\) das Ziel "Azure Blob Storage" \(Azure-Blob-Speicher" aus.
    - Wählen Sie unter **Please specify authentication type** \(Authentifizierungstyp angeben\) die Option "Account" \(Konto\) aus.
    - Geben Sie die Kontoinformationen für den Azure-Blob-Speicher ein. <p />

3.	Klicken Sie auf das Symbol rechts neben **Path to blob beginning with container parameter** \(Blob-Pfad beginnt mit Containerparameter\). Es sieht folgendermaßen aus:

	![Symbol für Webdienstparameter][icon]

    Wählen Sie "Set as web service parameter" \(Als Webdienstparameter festlegen\) aus.

    Ein Eintrag wird unter **Web Service Parameters** \(Webdienstparameter\) am unteren Rand des Eigenschaftenbereichs mit dem Namen "Path to blob beginning with container" \(Blob-Pfad beginnt mit Container\) hinzugefügt. Dies ist der Webdienstparameter, der nun diesem [Writer][writer]-Modulparameter zugeordnet ist.

4.	Wenn Sie den Webdienstparameter umbenennen möchten, klicken Sie auf den Namen, geben Sie "Blob-Pfad" ein, und drücken Sie die **EINGABETASTE**.
 
5.	Um einen Standardwert für den Webdienstparameter bereitzustellen, klicken Sie auf das Symbol neben dem Namen, wählen Sie "Provide default value" \(Standardwert bereitstellen\) aus, geben Sie einen Wert \(z. B. "container1/output1.csv"\) ein, und drücken Sie die **EINGABETASTE**.

	![Webdienstparameter][parameter]

6.	Klicken Sie auf **Run** \(Ausführen\).

7.	Klicken Sie auf **PUBLISH WEB SERVICE** \(Webdienst veröffentlichen\), um den Webdienst zu veröffentlichen.

Benutzer des Webdiensts können jetzt beim Zugriff auf den Webdienst ein neues Ziel für das [Writer][writer]-Modul angeben.

##Weitere Informationen

Ein ausführlicheres Beispiel finden Sie unter dem Eintrag [Web Service Parameter](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) im [Machine Learning Blog](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Weitere Informationen zum Zugreifen auf einen Machine Learning-Webdienst finden Sie unter [Nutzen eines veröffentlichten Webdiensts für maschinelles Lernen](machine-learning-consume-web-services.md).



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

<!--HONumber=54-->