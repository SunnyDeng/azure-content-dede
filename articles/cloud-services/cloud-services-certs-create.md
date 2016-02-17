<properties 
	pageTitle="Clouddienste und Verwaltungszertifikate | Microsoft Azure" 
	description="Informationen zum Erstellen und Verwenden von Zertifikaten mit Microsoft Azure" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2016"
	ms.author="adegeo"/>

# Übersicht über Zertifikate für Azure Cloud Services
Zertifikate werden in Azure für Clouddienste verwendet ([Dienstzertifikate](#what-are-service-certificates)), und sie werden für die Authentifizierung mit der Verwaltungs-API genutzt ([Verwaltungszertifikate](#what-are-management-certificates), wenn das klassische Azure-Portal und nicht Azure-Ressourcen-Manager verwendet wird). Dieses Thema bietet eine allgemeine Übersicht über beide Zertifikattypen sowie über deren [Erstellung](#create) und [Bereitstellung](#deploy) in Azure.

Die in Azure verwendeten Zertifikate sind X.509 v3-Zertifikate und können von einem anderen vertrauenswürdigen Zertifikat signiert werden oder selbstsigniert sein. Ein selbstsigniertes Zertifikat wird vom eigenen Ersteller signiert und ist daher standardmäßig nicht vertrauenswürdig. Die meisten Browser können dies ignorieren. Nur Sie selbst sollten beim Entwickeln und Testen Ihrer Clouddienste selbstsignierte Zertifikate verwenden.

Die in Azure verwendeten Zertifikate können einen privaten oder einen öffentlichen Schlüssel enthalten. Zertifikate verfügen über einen Fingerabdruck, durch den sie eindeutig identifiziert werden. Mithilfe dieses Fingerabdrucks wird in der Azure-[Konfigurationsdatei](cloud-services-configure-ssl-certificate.md) ermittelt, welches Zertifikat ein Clouddienst verwenden soll.

## Was sind Dienstzertifikate?
Dienstzertifikate werden an Clouddienste angefügt und ermöglichen die sichere Kommunikation zu und von den Diensten. Wenn Sie beispielsweise eine Webrolle bereitgestellt haben, sollten Sie ein Zertifikat angeben, das einen verfügbar gemachten HTTPS-Endpunkt authentifizieren kann. Dienstzertifikate, die in der Dienstdefinition definiert sind, werden automatisch auf dem virtuellen Computer bereitgestellt, auf dem eine Instanz der Rolle ausgeführt wird.

Sie können Dienstzertifikate entweder über das klassische Arzure-Portal oder mithilfe der Dienstverwaltungs-API in das klassische Azure-Portal hochladen. Dienstzertifikate sind einem bestimmten Clouddienst zugeordnet und einer Bereitstellung in der Dienstdefinitionsdatei zugewiesen.

Dienstzertifikate können gesondert von Ihren Diensten sowie von verschiedenen Personen verwaltet werden. Beispielsweise kann ein Entwickler ein Dienstpaket hochladen, das auf ein Zertifikat verweist, das ein IT-Manager zuvor in Azure hochgeladen hat. Ein IT-Manager kann dieses Zertifikat verwalten und erneuern und die Konfiguration des Diensts ändern, ohne ein neues Dienstpaket hochladen zu müssen. Dies ist möglich, da der logische Name für das Zertifikat und sein Speichername und Speicherort in der Dienstdefinitionsdatei angegeben sind, während der Zertifikatfingerabdruck in der Dienstkonfigurationsdatei angegeben ist. Um das Zertifikat zu aktualisieren, muss lediglich ein neues Zertifikat hochgeladen und der Fingerabdruckwert in der Dienstkonfigurationsdatei geändert werden.

## Was sind Verwaltungszertifikate?
Mit Verwaltungszertifikaten können Sie eine Authentifizierung mit der Dienstverwaltungs-API durchführen, die vom klassischen Azure bereitgestellt wird. Diese Zertifikate werden in vielen Programmen und Tools (z. B. Visual Studio oder Azure SDK) zum Automatisieren der Konfiguration und Bereitstellung verschiedener Azure-Dienste verwendet. Diese stehen eigentlich nicht in Zusammenhang mit Clouddiensten.

>[AZURE.WARNING] Vorsicht ist geboten! Alle Personen, die die Authentifizierung mit diesen Zertifikaten durchführen, können das zugeordnete Abonnement verwalten.

### Einschränkungen
Es sind maximal 100 Verwaltungszertifikate pro Abonnement möglich. Ebenso sind maximal 100 Verwaltungszertifikate für alle Abonnements unter der Benutzer-ID eines bestimmten Dienstadministrators zulässig. Wenn über die Benutzer-ID für den Kontoadministrator bereits 100 Verwaltungszertifikate hinzugefügt wurden und weitere Zertifikate benötigt werden, können Sie einen Co-Administrator festlegen, um die zusätzlichen Zertifikate hinzuzufügen.

Prüfen Sie vor dem Hinzufügen von mehr als 100 Zertifikaten, ob Sie stattdessen nicht ein vorhandenes Zertifikat wiederverwenden können. Durch die Verwendung von Co-Administratoren kann sich Ihr Zertifikatverwaltungsprozess unnötigerweise verkomplizieren.


<a name="create"></a>
## Erstellen eines neuen selbstsignierten Zertifikats
Ein selbstsigniertes Zertifikat können Sie mit allen verfügbaren Tools erstellen, sofern die folgenden Einstellungen beachtet werden:

* Es muss sich um ein X.509-Zertifikat handeln.
* Es muss einen privaten Schlüssel enthalten.
* Es ist für den Schlüsselaustausch erstellt (PFX-Datei).
* Der Name des Antragstellers muss der Domäne entsprechen, über die auf den Clouddienst zugegriffen wird.
    > Sie können kein SSL-Zertifikat für die Domäne cloudapp.net (oder für eine andere Domäne in Zusammenhang mit Azure) beziehen; der Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die auf Ihre Anwendung zugegriffen wird. Beispielsweise **contoso.net**, nicht **contoso.cloudapp.net**.
* Mindestens 2048-Bit-Verschlüsselung.
* **Nur Dienstzertifikat:** Das clientseitige Zertifikat muss sich im *persönlichen* Zertifikatspeicher befinden.

Es gibt zwei einfache Möglichkeiten zum Erstellen eines Zertifikats unter Windows: mithilfe des Dienstprogramms `makecert.exe` oder mit IIS.

### makecert.exe

Dieses Dienstprogramm wird mit Visual Studio 2013/2015 installiert. Es handelt sich um ein Konsolendienstprogramm, mit dem Sie Zertifikate erstellen und installieren können. Wenn Sie die Verknüpfung **Developer-Eingabeaufforderung für VS2015** starten, die bei der Installation von Visual Studio erstellt wird, wird eine Eingabeaufforderung angezeigt, in der dieses Tool im Pfad angegeben ist.

    makecert -sky exchange -r -n "CN=[CertificateName]" -pe -a sha1 -len 2048 -ss My -sv [CertificateName].pvk [CertificateName].cer


### Internetinformationsdienste (IIS)

Im Internet wird auf vielen Seiten erläutert, wie mit IIS Zertifikate erstellt werden können. [Hier](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) eine Seite, auf der dies nach meinem Empfinden anschaulich erklärt wird.

### Java
Auch mit Java können Sie [ein Zertifikat erstellen](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### Linux
In [diesem](../virtual-machines/virtual-machines-linux-use-ssh-key.md) Artikel wird beschrieben, wie Zertifikate mit SSH erstellt werden.

## Nächste Schritte

[Hochladen des Dienstzertifikats in das klassische Azure-Portal](cloud-services-configure-ssl-certificate.md) (oder das [Azure-Portal](cloud-services-configure-ssl-certificate-portal.md)).

Hochladen des [Verwaltungs-API-Zertifikats](../azure-api-management-certs.md) in das klassische Azure-Portal.

>[AZURE.NOTE] Im Azure-Portal erfolgt der Zugriff auf die API nicht über Verwaltungszertifikate, sondern über Benutzerkonten.

<!---HONumber=AcomDC_0204_2016-->