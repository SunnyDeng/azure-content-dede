<properties 
	pageTitle="Erstellen einer App Service-Umgebung" 
	description="Beschreibung der Erstellung von App Service-Umgebungen" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Erstellen einer App Service-Umgebung #

App Service-Umgebungen sind eine Premium-Dienstoption von Azure App Service, die sich aktuell in der Vorschau befindet. Sie bietet eine erweiterte Konfigurationsmöglichkeit, die in den mehrinstanzenfähigen Verwaltungseinheiten nicht verfügbar ist. Um ein besseres Verständnis von den Möglichkeiten von App Service-Umgebungen zu erhalten, lesen Sie die Dokumentation [Was ist eine App Service-Umgebung][WhatisASE].

### Übersicht ###

Mit der Funktion der App Service-Umgebungen wird im Wesentlichen Azure App Service im VNET eines Kunden bereitgestellt. Hierzu sind folgende Voraussetzungen zu erfüllen:

- Ein regionales VNET ist erforderlich mit 512 (/23) oder mehr Adressen.
- Ein Subnetz in diesem VNET ist erforderlich mit 256 (/24) oder mehr Adressen.
- Das Subnetz **darf keinen anderen Computeressourcen enthalten**. In einem Subnetz kann nur eine App Service-Umgebung bereitgestellt werden. Der Erstellungsversuch misslingt, wenn das Subnetz bereits andere Compute-Ressourcen enthält.

Wenn Sie noch kein VNET besitzen, das Sie zum Hosten Ihrer App Service-Umgebung verwenden möchten, können Sie bei der Erstellung der App Service-Umgebung eines erstellen.

Jede Bereitstellung einer App Service-Umgebung ist ein gehosteter Dienst, der von Azure verwaltet und gewartet wird. Die Compute-Ressourcen, welche die Systemrollen der App Service-Umgebung hosten, sind für den Kunden nicht zugänglich. Der Kunde verwaltet jedoch die Menge der Instanzen und deren Größen.

## Erstellen einer App Service-Umgebung ##

Es gibt zwei Möglichkeiten, auf die Benutzeroberfläche zur Erstellung der App Service-Umgebung zuzugreifen. Durchsuchen Sie den Azure Marketplace nach ***App Service-Umgebung***, oder wählen Sie "Neu" -> "Web + Mobil".

### Schnellerfassung ###
Nach dem Aufrufen der Benutzeroberfläche zur Erstellung können Sie durch die Eingabe eines Namens für die Bereitstellung schnell eine App Service-Umgebung erstellen. Dadurch werden wiederum ein VNET mit 512 Adressen, ein Subnetz mit 256 Adressen in diesem VNET sowie eine App Service-Umgebung mit zwei Front-Ends und zwei Workern im Workerpool 1 erstellt. Achten Sie darauf, den Standort, an dem sich das System befinden soll, sowie das zugehörige Abonnement auszuwählen. Die einzigen Konten, die die App Service-Umgebung zum Hosten von Inhalten verwenden können, müssen in dem Abonnement enthalten sein, das zur Erstellung verwendet wurde.

Der Name, der für die App Service-Umgebung angegeben wurde, wird für die in der App Service-Umgebung erstellten Web-Apps verwendet. Wenn der Name der App Service-Umgebung "appsvcenvdemo" ist, lautet der Domänenname ".*appsvcenvdemo.p.azurewebsites.net*". Wenn Sie also eine Web-App mit dem Namen "mytestapp" erstellt haben, wäre diese unter der Adresse *mytestapp.appsvcenvdemo.p.azurewebsites.net* aufrufbar. Sie können im Namen keine Leerzeichen verwenden. Bei Verwendung von Großbuchstaben im Namen wird der entsprechende Domänenname dennoch vollständig in Kleinbuchstaben geschrieben.


![][1]

### Compute-Ressourcenpools ###

Die für die App Service-Umgebung verwendeten Compute-Ressourcen werden in Compute-Ressourcenpools verwaltet. Dadurch kann zusätzlich zu deren Größe auch die Anzahl der Compute-Ressourcen im Pool konfiguriert werden. Eine App Service-Umgebung besteht aus Front-End-Servern und Workern. Die Front-End-Server verarbeiten die Verbindungslast, und Worker führen den App-Code aus. Die Front-End-Server werden in einem dedizierten Compute-Ressourcenpool verwaltet. Die Worker werden wiederum in drei separaten Compute-Ressourcenpools verwaltet namens

- Workerpool 1
- Workerpool 2
- Workerpool 3

Wenn Sie über eine große Anzahl von Anforderungen für einfache Web-Apps verfügen, würden Sie Ihre Front-Ends wahrscheinlich zentral hochskalieren und weniger Worker verwenden. Falls Sie über CPU- oder arbeitsspeicherintensive Web-Apps mit wenig Datenverkehr verfügen, brauchen Sie nicht viele Front-Ends, jedoch höchstwahrscheinlich mehr oder größere Worker.

Unabhängig von der Größe der Compute-Ressourcen sind mindestens zwei Front-End-Server und zwei Worker erforderlich. Eine App Service-Umgebung kann für die Verwendung von bis zu 55 Compute-Ressourcen insgesamt konfiguriert werden. Von diesen 55 Compute-Ressourcen können nur 50 zum Hosten von Workloads eingesetzt werden. Dafür gibt es zwei Gründe. Es sind mindestens zwei Front-End-Compute-Ressourcen erforderlich. So bleiben bis zu 53 zur Unterstützung der Workerpoolzuweisung übrig. Um jedoch Fehlertoleranz bereitzustellen, muss nach den folgenden Regeln eine zusätzliche Compute-Ressource zugewiesen werden:

- Jeder Workerpool benötigt mindestens eine weitere Compute-Ressource, der keine Workload zugewiesen werden kann
- Wenn die Menge von Compute-Ressourcen in einem Pool einen bestimmten Wert überschreitet, ist eine weitere Compute-Ressource erforderlich

Im Rahmen der Fehlertoleranzanforderungen gilt innerhalb eines einzelnen Workerpools Folgendes für einen bestimmten Wert von X Ressourcen, die einem Workerpool zugewiesen sind:

- Wenn X zwischen 2 und 20 liegt, beträgt die Anzahl der Compute-Ressourcen, die für Workloads verwendet werden können, X-1
- Wenn X zwischen 21 und 40 liegt, beträgt die Anzahl der Compute-Ressourcen, die für Workloads verwendet werden können, X-2
- Wenn X zwischen 41 und 53 liegt, beträgt die Anzahl der Compute-Ressourcen, die für Workloads verwendet werden können, X-3

Sie können nicht nur die Menge der Compute-Ressourcen verwalten, die einem bestimmten Pool zugewiesen werden können, Sie besitzen außerdem auch die Kontrolle über die Größe. In App Service-Umgebungen können Sie aus vier unterschiedlichen Größen mit den Namen P1 bis P4 auswählen. Ausführliche Informationen zu diesen Größen und Preisen finden Sie unter [App Service-Preise][AppServicePricing]. Die Compute-Ressourcengrößen P1 bis P3 entsprechen denen, die in den mehrinstanzenfähigen Umgebungen verfügbar sind. Die P4-Compute-Ressource bietet 8 Kerne mit 14 GB Arbeitsspeicher und steht nur in einer App Service-Umgebung zur Verfügung.

Die Preisgebung für App Service-Umgebungen erfolgt anhand der zugewiesenen Compute-Ressourcen. Sie bezahlen für die Compute-Ressourcen, die Ihrer App Service-Umgebung zugewiesen sind – unabhängig davon, ob diese Workloads hosten.



### VNET-Erstellung ###
Auch wenn es eine schnelle Funktion zum automatischen Erstellen eines neuen VNET gibt, unterstützt die Funktion auch die Auswahl eines vorhandenen VNET und die manuelle Erstellung eines VNET. Sie können ein vorhandenes VNET auswählen, wenn es für die Unterstützung der Bereitstellung einer App Service-Umgebung groß genug ist. Das VNET muss mindestens 512 Adressen aufweisen. Wenn Sie ein bereits vorhandenes VNET auswählen, müssen Sie auch ein Subnetz angeben oder ein neues erstellen. Das Subnetz muss mindestens 256 Adressen aufweisen.

Beim Durchlaufen der Benutzeroberfläche zur VNET-Erstellung müssen Sie Folgendes bereitstellen:

- VNET-Name
- VNET-Adressbereich in CIDR-Schreibweise
- Subnetzname
- Subnetzbereich in CIDR-Schreibweise

Wenn Sie mit der CIDR-Schreibweise nicht vertraut sind: Diese verwendet das Format 10.0.0.0/22, wobei /22 den Bereich angibt. In diesem Beispiel kennzeichnet /22 einen Bereich von 1024 Adressen oder von 10.0.0.0 bis 10.0.3.255. /23 bedeutet 512 Adressen und so weiter.

![][2]

### Definieren der Größe der App Service-Umgebung ###

Als nächstes konfigurieren Sie die Skalierung des Systems. Standardmäßig liegen zwei Front-End-P2-Compute-Ressourcen, zwei P1-Worker und eine IP-Adresse vor. Es gibt zwei Front-Ends, um eine hohe Verfügbarkeit bereitzustellen und die Last zu verteilen. Die Mindestgröße für die Front-Ends ist P2, um eine ausreichende Kapazität zur Unterstützung eines anspruchslosen Systems sicherzustellen. Wenn Sie wissen, dass das System eine hohe Anzahl von Anforderungen unterstützen muss, können Sie die Menge der Front-Ends und die verwendete Servergröße anpassen.

Wie bereits erwähnt, gibt es innerhalb einer App Service-Umgebung drei Workerpools, die vom Kunden definiert werden können. Die Größe der Compute-Ressourcen kann zwischen P1 und P4 liegen. Standardmäßig sind nur zwei P1-Worker im Workerpool 1 konfiguriert. Dies reicht aus, um einen einzelnen App Service-Plan mit einer Instanz zu unterstützen.

Die Schieberegler werden automatisch angepasst und entsprechen der gesamten Compute-Kapazität in der App Service-Umgebung. Während die Schieberegler innerhalb eines Pools angepasst werden, ändern sich die anderen Schieberegler, um die verbleibende Menge der verfügbaren Compute-Ressourcen bis zum Erreichen von 55 anzuzeigen.
 
![][3]

Das Hinzufügen neuer Instanzen, die zur Verfügung gestellt werden sollen, nimmt etwas Zeit in Anspruch. Wenn Sie wissen, dass Sie zusätzliche Compute-Ressourcen benötigen werden, sollten Sie diese weit im Voraus bereitstellen. Die Bereitstellungszeit kann mehrere Stunden dauern, je nachdem, wie viele zum System hinzugefügt werden. Stellen Sie sicher, dass Ihr System die Anforderungen an die Fehlertoleranz erfüllen kann. In jeder App Service-Umgebung muss in jedem Workerpool eine Reserveinstanz zur Verfügung stehen.

Standardmäßig umfasst eine App Service-Umgebung eine IP-Adresse, die für IP-SSL verfügbar ist. Wenn Sie wissen, dass Sie weitere benötigen werden, können Sie dies hier angeben oder nach der Erstellung verwalten.
  
### Nach Erstellung einer App Service-Umgebung ###

Nach der Erstellung einer App Service-Umgebung können Sie die folgenden Einstellungen anpassen:

- Anzahl der Front-Ends (Minimum: 2)
- Anzahl der Worker (Minimum: 2)
- Anzahl der IP-Adressen
- Größen der Compute-Ressourcen, die von den Front-Ends oder den Workern verwendet werden (Mindestgröße für Front-Ends ist P2)

Folgende Einstellungen können Sie nicht ändern:

- Standort
- Abonnement
- Ressourcengruppe
- Verwendetes VNET
- Verwendetes Subnetz

Hier finden Sie weitere Details zur Verwaltung und Überwachung von App Service-Umgebungen: [Konfigurieren einer App Service-Umgebung][ASEConfig]

Es gibt zusätzliche Abhängigkeiten, die nicht zur Anpassung zur Verfügung stehen, wie beispielsweise Datenbank und Speicher. Diese werden von Azure verwaltet und sind im System enthalten. Der Systemspeicher unterstützt bis zu 500 GB für die gesamte App Service-Umgebung.


## Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Einführung in App Service-Umgebungen][WhatisASE]

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/createaseblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/createasenetwork.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/createasescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/

<!---HONumber=July15_HO3-->