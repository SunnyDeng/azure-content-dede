<properties
   pageTitle="Verwalten von Rollen in den Azure-Clouddienstprojekten mit Visual Studio | Microsoft Azure"
   description="Informationen zum Hinzufügen neuer Rollen zu Ihrem Azure-Clouddienstprojekt oder Entfernen von Rollen daraus mit Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/19/2015"
   ms.author="tarcher" />

# Verwalten von Rollen in den Azure-Clouddienstprojekten mit Visual Studio

Nach dem Erstellen des Azure-Clouddienstprojekts können Sie ihm neue Rollen hinzufügen oder vorhandene Rollen daraus entfernen. Darüber hinaus können Sie ein vorhandenes Projekt importieren und es in eine Rolle konvertieren. Sie können z. B. eine ASP.NET-Webanwendung importieren und sie als Webrolle festlegen.

## Hinzufügen und Entfernen von Rollen

**So fügen Sie eine Rolle hinzu**

Um eine Rolle hinzuzufügen, öffnen Sie **Projektmappen-Explorer** im Clouddienstprojekt das Kontextmenü für den Knoten **Rollen** und wählen **Hinzufügen** aus. Sie können eine vorhandene Web- oder Workerrolle in der aktuellen Projektmappe auswählen oder ein neues Web- oder Workerrollenprojekt erstellen. Sie können auch ein entsprechendes Projekt auswählen, z. B. ein ASP.NET-Webanwendungsprojekt, und es einem Rollenprojekt zuordnen.

**So entfernen Sie eine Rollenzuordnung**

Öffnen Sie zum Entfernen einer Rollenzuordnung im Clouddienstprojekt das Kontextmenü für den Knoten **Rollen**, und wählen Sie **Entfernen** aus.

## Entfernen und Hinzufügen von Rollen im Clouddienst

Wenn Sie eine Rolle aus Ihrem Clouddienstprojekt entfernen, sie aber später wieder zum Projekt hinzufügen möchten, werden nur die Rollendeklaration und grundlegende Attribute wie Endpunkte und Diagnoseinformationen hinzugefügt. Es werden keine zusätzlichen Ressourcen oder Verweise der Datei "ServiceDefinition.csdef" oder "ServiceConfiguration.cscfg" hinzugefügt. Wenn Sie diese Informationen hinzufügen möchten, müssen Sie sie wieder manuell diesen Dateien hinzufügen.

Beispielsweise könnten Sie eine Webdienstrolle entfernen und diese Rolle später wieder der Projektmappe hinzufügen. Wenn Sie dies tun, tritt ein Fehler auf. Fügen Sie zur Vermeidung des Fehlers das im folgenden XML-Code dargestellte `<LocalResources>`-Element wieder der Datei "ServiceDefinition.csdef" hinzu. Verwenden Sie den Namen der Webdienstrolle, die Sie dem Projekt wieder hinzugefügt haben, als Teil des Namensattributs für das **<LocalStorage>**-Element. In diesem Beispiel lautet der Name der Webdienstrolle **WCFServiceWebRole1**:

	<WebRole name="WCFServiceWebRole1">
	    <Sites>
	      <Site name="Web">
	        <Bindings>
	          <Binding name="Endpoint1" endpointName="Endpoint1" />
	        </Bindings>
	      </Site>
	    </Sites>
	    <Endpoints>
	      <InputEndpoint name="Endpoint1" protocol="http" port="80" />
	    </Endpoints>
	    <Imports>
	      <Import moduleName="Diagnostics" />
	    </Imports>
	   <LocalResources>
	      <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
	   </LocalResources>
	</WebRole>

## Nächste Schritte

Weitere Informationen zum Konfigurieren von Rollen in Visual Studio finden Sie unter [Konfigurieren der Rollen für einen Azure-Clouddienst mit Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

<!---HONumber=AcomDC_1223_2015-->