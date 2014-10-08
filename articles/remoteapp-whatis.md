<properties title="What is RemoteApp?" pageTitle="What is RemoteApp?" description="Learn about RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Was ist Azure RemoteApp?

Mit RemoteApp können Sie Programme erstellen, die remote in Azure ausgeführt werden und dabei den Anschein erwecken, dass sie auf dem lokalen Computer des Endbenutzers laufen. Diese Programme werden auch als RemoteApp-Programme bezeichnet. Anstatt im Desktop des Remotedesktop-Sitzungshosts (RD-Sitzungshost) werden die RemoteApp-Programme in den Desktop des Clients integriert. Das RemoteApp-Programm wird in einem eigenen Fenster mit veränderbarer Größe ausgeführt, kann über mehrere Monitore verschoben werden und hat einen eigenen Eintrag in der Taskleiste. Wenn ein Benutzer mehr als ein RemoteApp-Programm auf demselben RD-Sitzungshostserver ausführt, teilen sich die RemoteApp-Programme eine gemeinsame Remotedesktopdienste-Sitzung.

RemoteApps können in vielen Situationen Komplexität und Verwaltungsaufwand senken, z. B. in den folgenden Szenarien:

-   Filialen mit eingeschränktem IT-Support und eingeschränkter Netzwerkbandbreite.
-   Situationen, in denen Benutzer remote auf Programme zugreifen müssen.
-   Bereitstellung von Branchenanwendungen (LOB), insbesondere angepasste LOB-Programme.
-   Umgebungen wie z. B. "Hot Desk"- oder "Hoteling"-Arbeitsbereiche, in denen Benutzer keine fest zugeordneten Computer verwenden.
-   Bereitstellung mehrerer Versionen eines Programms, insbesondere wenn die parallele lokale Installation unterschiedlicher Versionen Konflikte verursachen würde.

Im Gegensatz zu herkömmlichen Remotedesktopdiensten wird Azure RemoteApp im Azure-Verwaltungsportal ausgeführt. Ihre Benutzer erreichen die Programme über das Portal, während Sie die Programme und Benutzer über das Administratorportal verwalten.

Es existieren zwei Bereitstellungsarten für RemoteApps:

-   Eine Cloud-Bereitstellung ist in der Cloud gehostet und speichert alle Daten für Programme in der Azure-Cloud.
-   Eine Hybridbereitstellung wird in der Azure-Cloud gehostet, allerdings können Benutzer auf Daten aus dem lokalen Netzwerk zugreifen.

Unabhängig von der gewählten Bereitstellungsart veröffentlichen Sie die Programme nach der Erstellung des Diensts im Endbenutzer-Feed. Dieser Feed enthält eine Liste der verfügbaren Programme, die Ihre Benutzer über das Azure-Portal erreichen können. Beachten Sie, dass im Feed alle Programme aus allen Diensten angezeigt werden, die zu Ihrem Abonnement zugeordnet sind.

