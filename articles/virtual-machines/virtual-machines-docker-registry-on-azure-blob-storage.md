<properties title="Deploying Your Own Private Docker Registry on Azure"
  pageTitle="Bereitstellen einer privaten Docker-Registrierung in Azure"
  description="Beschreibt, wie Sie eine Docker-Registrierung zum Hosten von Containerimages im Azure-Blob-Speicherdienst verwenden."
  services="virtual-machines"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="" 
  tags="" />

<tags
  ms.service="virtual-machines"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="06/17/2015" 
  ms.author="ahmetb" />

# Bereitstellen einer privaten Docker-Registrierung in Azure

Dieses Dokument beschreibt, was eine private Docker-Registrierung ist, und zeigt, wie Sie einen Docker Registry 2.0-Container mit einer privaten Docker-Registrierung in Microsoft Azure mithilfe von Azure-Blob-Speicher bereitstellen.

In diesem Dokument wird von Folgendem ausgegangen:

1. Sie wissen, wie Docker verwendet wird, und verfügen über Docker-Images zum Speichern. (Andernfalls [sollten Sie sich über Docker informieren](https://www.docker.com).)
2. Sie haben einen Server, auf dem ein Docker-Modul installiert ist. (Andernfalls [installieren Sie ein Docker-Modul in Azure](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/).)


## Was ist eine private Docker-Registrierung?

Um Ihre Anwendungen in Containern in der Cloud bereitzustellen, erstellen Sie ein Docker-Containerimage und speichern es, sodass es von Ihnen selbst und von anderen Benutzern verwendet werden kann.

Das Erstellen des Containerimages und das Senden in die Cloud stellen kein Problem dar, es ist jedoch eine Herausforderung, das generierte Image zuverlässig zu speichern. Aus diesem Grund bietet Docker einen zentralisierten Dienst namens [Docker Hub][docker-hub] zum Speichern von Containerimages in der Cloud an, mit dem Sie jederzeit Container aus diesen Images erstellen können.

Obwohl [Docker Hub][docker-hub] ein kostenpflichtiger Dienst zur Speicherung Ihrer privaten Anwendungscontainerimages ist, geht Docker auf die Anforderungen der Entwickler ein und stellt ein Open-Source-Toolset zum Speichern von Images in einer eigenen privaten Docker-Registrierung hinter einer Firewall oder lokal zur Verfügung, ohne dass dafür auf das öffentliche Internet zugegriffen werden muss. Da Azure-Blob-Speicher leicht zu sichern sind, können Sie damit schnell eine private Docker-Registrierung in Azure erstellen, die Sie selbst kontrollieren.

## Warum sollten Sie eine Docker-Registrierung in Azure hosten?

Das Hosten der Docker-Registrierungsinstanz in Microsoft Azure und das Speichern der Images in Azure-Blob-Speicher bietet mehrere Vorteile:

**Sicherheit**: Ihre Docker-Images benötigen keine Azure-Rechenzentren. Sie haben daher keine Verbindung ins öffentliche Internet, wie es bei der Verwendung von Docker Hub der Fall wäre.
  
**Leistung**: Ihre Docker-Images werden innerhalb desselben Rechenzentrums oder Bereichs gespeichert wie Ihre Anwendungen. Dies bedeutet, dass die Images im Vergleich zu Docker Hub schneller und zuverlässiger sind.

**Zuverlässigkeit**: Mithilfe von Microsoft Azure-Blob-Speicher können Sie viele Speichereigenschaften nutzen, z. B. hohe Verfügbarkeit, Redundanz, Premium-Speicher (SSDs) usw.

## Konfigurieren von Docker-Registrierungen zur Verwendung von Azure-Blob-Speicher

(Sie sollten die [Docker Registry 2.0-Dokumentation][registry-docs] lesen, bevor Sie fortfahren.)

Sie können die Docker-Registrierung auf zwei unterschiedliche Arten [konfigurieren][registry-config]. Sie können:

1. Eine `config.yml`-Datei verwenden. In diesem Fall müssen Sie ein separates Docker-Image auf dem `registry`-Image erstellen.
2. Überschreiben Sie die Standardkonfigurationsdatei durch Umgebungsvariablen, sodass Sie kein separates Docker-Image erstellen und verwalten müssen.

Der Einfachheit halber wird in diesem Thema Option 2 mit Umgebungsvariablen genutzt.

Zum Ausführen einer Docker-Registrierungsinstanz, die: * Ihr Azure-Speicherkonto zum Speichern der Images verwendet * Port 5000 des virtuellen Computers überwacht * keine Authentifizierung konfiguriert hat (nicht empfohlen, siehe Hinweis unten)

Sie müssen den folgenden Docker-Befehl im Bash-Terminal ausführen (ersetzen Sie `<storage-account>` und `<storage-key>` mit Ihren Anmeldeinformationen):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Nach Abschluss des Befehls können Sie den Container, der Ihre private Docker-Registrierungsinstanz hostet, mit dem Befehl `docker ps` auf dem Host anzeigen:

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT]Das Konfigurieren der Sicherheit für die Docker-Registrierung wird in diesem Dokument nicht behandelt, und auf Ihre Registrierung können standardmäßig alle Benutzer ohne Authentifizierung zugreifen, wenn Sie den Registrierungsport am Endpunkt des virtuellen Computers oder des Lastenausgleichs durch die Verwendung des Bereitstellungsbefehls oben öffnen.
>
> Lesen Sie das Dokument zum [Konfigurieren von Docker-Registrierungen][registry-config] (in englischer Sprache), um zu erfahren, wie Sie die Registrierungsinstanz und Ihre Images schützen.

## Nächste Schritte

Sobald Sie Ihre Registrierung eingerichtet haben, ist es an der Zeit, sie verstärkt zu nutzen. Beginnen Sie unter [registry-docs].

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[registry-docs]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 

<!---HONumber=August15_HO6-->