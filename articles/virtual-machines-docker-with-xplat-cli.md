<properties 
	pageTitle="Verwenden der Docker-VM-Erweiterung für Linux auf Azure" 
	description="Beschreibt die Docker- und Azure Virtual Machines-Erweiterungen und zeigt die programmgesteuerte Erstellung von virtuellen Computern auf Azure, die Docker hostet, über die Befehlszeile mithilfe der "azure-cli"-Befehlsschnittstelle." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="02/02/2015" 
	ms.author="rasquill"/>
# Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Schnittstelle (xplat-cli)
Dieses Thema beschreibt das Erstellen eines virtuellen Computers mit der Docker-VM-Erweiterung über "xplat-cli" auf jeder Plattform. [Docker](https://www.docker.com/) ist einer der beliebtesten Virtualisierungsansätze, der für das Isolieren von Daten und Computing auf gemeinsam genutzten Ressourcen [Linux-Container](http://en.wikipedia.org/wiki/LXC) statt virtueller Computer verwendet. Verwenden Sie die Docker-VM-Erweiterung des [Azure Linux Agent](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/), um einen virtuellen Docker-Computer zu erstellen, der eine beliebige Anzahl von Containern für Ihre Anwendungen in Azure hostet. Eine allgemeine Diskussion über die Container und ihre Vorteile finden Sie unter [Docker High Level Whiteboard](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard) (Allgemeines Whiteboard zu Docker, in englischer Sprache).

+ [Verwenden der Docker-VM-Erweiterung mit Azure]
+ [Erweiterungen für virtuelle Linux- und Windows-Computer] 
+ [Container und Container-Verwaltungsressourcen für Azure]
+ [Nächste Schritte]



## <a id='How to use the Docker VM Extension with Azure'>Verwenden der Docker-VM-Erweiterung mit Azure</a>
Zum Verwenden der Docker-VM-Erweiterung mit Azure müssen Sie eine Version der [plattformübergreifenden Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-sdk-tools-xplat) (wird in diesem Thema als **xplat-cli** bezeichnet) höher als 0.8.6 (zum Zeitpunkt der Erstellung dieses Dokuments ist die aktuelle Version 0.8.10) installieren. Sie können die xplat-cli auf Mac, Linux und Windows installieren. 

> [AZURE.NOTE] Auch wenn Sie die xplat-cli auf Microsoft Windows installieren können, wurde Docker mit Kernelabhängigkeiten für Linux erstellt. Daher müssen Sie eine vollständige Linux-Verteilung als einen virtuellen Computer in Hyper-V oder einem anderen Hypervisor hosten, um Windows als einen Docker-Client verwenden zu können. Dann können Sie die xplat-cli und die Docker-Befehle in diesem Dokument und die Befehle von Docker verwenden. Um dieses Setup zu automatisieren, können Sie auch das Setupprogramm für Windows [Boot2Docker](https://docs.docker.com/installation/windows/) von Docker verwenden.

Die Verwendung von Docker auf Azure ist einfach:

+ Installieren Sie die xplat-cli-Befehlszeilentools und ihre Abhängigkeiten auf dem Computer, von dem aus Sie Azure steuern möchten (in Windows ist dies eine Linux-Verteilung, die als virtueller Computer ausgeführt wird).
+ Verwenden Sie die xplat-cli Docker-Befehle, um einen Docker-VM-Host in Azure zu erstellen.
+ Verwenden Sie die lokalen Docker-Befehle, um die Docker-Container im virtuellen Docker-Computer in Azure zu verwalten.

> [AZURE.NOTE] Die xplat-cli (Command Line Interface, Befehlszeilenschnittstelle) ist derzeit die einzige Möglichkeit zum Erstellen eines Docker-gesteuerten virtuellen Computers auf Azure, um Docker-Container zu hosten. 

### Installieren der plattformübergreifenden Befehlszeilenschnittstelle (xplat-cli)
Informationen zum Installieren und Konfigurieren der plattformübergreifenden Befehlszeilenschnittstelle finden Sie unter [Gewusst wie: Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](http://azure.microsoft.com/documentation/articles/xplat-cli/#install). Um die Installation zu bestätigen, geben Sie  `azure` an der Eingabeaufforderung ein, nach wenigen Sekunden sollte die xplat-cli ASCII Art angezeigt werden, die die verfügbaren grundlegenden Befehle auflistet. War die Installation erfolgreich, sollten Sie bei Eingabe von  `azure help vm` unter den aufgelisteten Befehlen "docker" finden.

> [AZURE.NOTE] Wenn Sie eine Ubuntu 14.04 LTS-Installation verwenden, weicht die Knoteninstallation für dieses Image geringfügig ab und erfordert ggf. zusätzlichen Aufwand. Ein Möglichkeit ist die direkte Installation der neuesten Version von nodejs, die auf Ubuntu 14.04 LTS-Verteilungen gut zu funktionieren scheint. Sie finden Sie [hier](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server) im Abschnitt **Installieren mithilfe eines PPA**. 

### Verknüpfen der xplat-cli mit Ihrem Azure-Konto
Bevor Sie die xplat-cli verwenden können, müssen Sie Ihre Azure-Anmeldeinformationen mit der xplat-cli auf der Plattform verknüpfen. Der Abschnitt [Gewusst wie: Verbinden mit Ihrem Azure-Abonnement](http://azure.microsoft.com/documentation/articles/xplat-cli/#configure) erläutert, wie Ihre **PUBLISHSETTINGS**-Datei heruntergeladen und importiert wird oder wie Ihre xplat-cli-Befehlszeile mit einer Organisations-ID verknüpft wird. 

> [AZURE.NOTE] Beide Methoden der Authentifizierung verhalten sich unterschiedlich. Lesen Sie daher unbedingt das genannte Dokument, um die Unterschiede zu verstehen. 

### Installieren und Verwenden der Docker-VM-Erweiterung für Azure
Folgen Sie den [Installationsanweisungen für Docker](https://docs.docker.com/installation/#installation), um Docker auf Ihrem Computer lokal zu installieren. Bei den meisten Betriebssystemen und Verteilungen geben Sie dazu  `apt-get install docker.io` ein. Bestätigen Sie, dass es sich um die Docker-Version 1.0 oder höher handelt.

Damit Docker mit einem virtuellen Azure-Computer verwendet werden kann, muss das für den virtuellen Computer verwendete Linux-Image über den installierten [Azure Linux VM Agent](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/) verfügen. Zurzeit stehen nur zwei Imagetypen zur Verfügung, die diesen bereitstellen:

+ Ein Ubuntu-Image aus dem Azure-Image-Katalog oder 

+ Ein benutzerdefiniertes Linux-Image, das Sie mit dem installierten und konfigurierten Azure Linux VM Agent erstellt haben. Unter [Azure Linux VM Agent](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/) finden Sie weitere Informationen über das Erstellen eines benutzerdefinierten virtuellen Linux-Computers mit dem Azure VM Agent.

### Verwenden des Azure-Image-Katalogs

Verwenden Sie in einer Bash- oder Terminalsitzung den folgenden xplat-cli-Befehl zum Suchen des neuesten Ubuntu-Images, das verwendet werden soll, im VM-Katalog, indem Sie

`azure vm image list | grep Ubuntu-14_04`

eingeben und einen der Imagenamen wie  `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-de-de-30GB` auswählen sowie den folgenden Befehl zum Erstellen eines neuen virtuellen Computers mithilfe dieses Image verwenden. 

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-de-de-30GB" <username> <password>
``` 

Hinweis:

+ *&lt;vm-cloudservice name&gt;* ist der Name des virtuellen Computers, der zum Docker-Containerhostcomputer in Azure wird.

+  *&lt;username&gt;* ist der Benutzername des standardmäßigen Stammbenutzers des virtuellen Computers.

+ *&lt;password&gt;* ist das Kennwort des Kontos  *username*, das die Komplexitätsstandards für Azure erfüllt. 
 
> [AZURE.NOTE] Derzeit muss ein Kennwort aus mindestens 8 Zeichen bestehen und dabei einen Kleinbuchstaben, einen Großbuchstaben, eine Ziffer und ein Sonderzeichen wie die im Folgenden aufgeführten enthalten: `!@#$%^&+=`. Der Punkt am Ende des vorhergehenden Satzes zählt nicht zu den Sonderzeichen. 

War die Eingabe erfolgreich, sollte Ihnen in etwa das Folgende angezeigt werden, abhängig von den genauen Argumenten und Optionen, die Sie verwendet haben:

![](./media/virtual-machines-docker/dockercreateresults.png)

> [AZURE.NOTE] Das Erstellen eines virtuellen Computers kann einige Minuten in Anspruch nehmen. Nach der Bereitstellung wird der Docker-Daemon (der Docker-Dienst) jedoch gestartet, und Sie können eine Verbindung mit dem Docker-Containerhost herstellen.

Um den virtuellen Docker-Computer, den Sie in Azure erstellt haben, zu testen, geben Sie Folgendes ein:

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

*<vm-name-you-used>* ist der Name des virtuellen Computers, den Sie in Ihrem Aufruf von  `azure vm docker create` verwendet haben. Es sollte etwas angezeigt werden, das in etwa dem Folgenden entspricht, welches angibt, dass Ihr virtueller Docker-Hostcomputer betriebsbereit ist, in Azure ausgeführt wird und auf Ihre Befehle wartet.

![](./media/virtual-machines-docker/connectingtodockerhost.png)

### Authentifizierung des virtuellen Docker-Hostcomputers
Zusätzlich zur Erstellung des virtuellen Docker-Computers erstellt der Befehl  `azure vm docker create` automatisch auch die nötigen Zertifikate, um Ihrem Docker-Clientcomputer über HTTPS die Verbindung mit dem Azure-Containerhost zu ermöglichen. Die Zertifikate werden sowohl auf dem Client- als auch auf dem Hostcomputer gespeichert. Bei den nachfolgenden Ausführungen werden die Zertifikate erneut verwendet und an den neuen Host freigegeben.

Standardmäßig werden die Zertifikate unter `~/.docker` abgelegt, und Docker wird für die Ausführung auf Port **4243** konfiguriert. Wenn Sie einen anderen Port oder ein anderes Verzeichnis verwenden möchten, dann können Sie eine der folgenden  `azure vm docker create`-Befehlszeilenoptionen verwenden, damit der virtuelle Docker-Containerhost einen anderen Port oder andere Zertifikate für Clients verwendet, von denen eine Verbindung hergestellt wird:

```
-dp, --docker-port [port]              Port to use for docker [4243]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Der Docker-Daemon auf dem Host ist so konfiguriert, dass er Clientverbindungen auf dem angegebenen Port mithilfe der durch den Befehl  `azure vm docker create` generierten Zertifikate abhört und authentifiziert. Der Clientcomputer muss über diese Zertifikate verfügen, um Zugriff auf den Docker-Host zu erlangen. 

> [AZURE.NOTE] Ein vernetzter Host ist ohne diese Zertifikate anfällig für jeden, der sich mit dem Computer verbindet. Bevor Sie die Standardkonfiguration ändern, setzen Sie sich mit den Risiken für Ihre Computer und Anwendungen auseinander.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Sie sind nun bereit, das [Docker-Benutzerhandbuch] und Ihren virtuellen Docker-Computer zu verwenden. Informationen zum Erstellen eines Docker-fähigen virtuellen Computers im neuen Portal finden Sie unter [Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Portal].

<!--Anchors-->
[Unterüberschrift 1]: #subheading-1
[Unterüberschrift 2]: #subheading-2
[Unterüberschrift 3]: #subheading-3
[Nächste Schritte]: #next-steps

[Verwenden der Docker-VM-Erweiterung mit Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Erweiterungen für virtuelle Linux- und Windows-Computer]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container und Container-Verwaltungsressourcen für Azure]: #Container-and-Container-Management-Resources-for-Azure

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Link 1 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../virtual-machines-windows-tutorial/
[Link 2 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../web-sites-custom-domain-name/
[Link 3 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../storage-whatis-account/
[Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Portal]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Docker-Benutzerhandbuch]: https://docs.docker.com/userguide/

<!--HONumber=45--> 
