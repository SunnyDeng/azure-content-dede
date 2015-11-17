<properties
	pageTitle="Erste Schritte mit der ersten Azure Search-Anwendung in .NET | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Ein Lernprogramm zum Erstellen einer Visual Studio-Projektmappe unter Verwendung der .NET-Clientbibliothek aus dem Azure Search .NET SDK."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="11/05/2015"
	ms.author="heidist"/>

# Erste Schritte mit der ersten Azure Search-Anwendung in .NET

Erfahren Sie, wie Sie eine benutzerdefinierte .NET-Suchanwendung in Visual Studio 2013 oder höher erstellen, die Azure Search zum Suchen verwendet: einen in der Cloud gehosteten Suchdienst in Microsoft Azure. In diesem Lernprogramm werden das [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) zum Erstellen der Klassen für die in dieser Übung verwendeten Objekte und Vorgänge sowie die REST-API für den Azure Search-Dienst verwendet.

Um dieses Beispiel auszuführen, benötigen Sie einen Azure Search-Dienst, für den Sie sich im [Azure-Portal](https://portal.azure.com) anmelden können.

> [AZURE.TIP]Laden Sie den Quellcode für dieses Lernprogramm von [.NET-Beispiele für Azure Search](http://go.microsoft.com/fwlink/p/?LinkId=530196) herunter. Informationen zum Einrichten von Verbindungen und Senden von Anforderungen finden Sie unter [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md).

## Informationen zu den Daten

In dieser Beispielanwendung werden Daten von [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) verwendet, die nach dem Bundesstaat Rhode Island gefiltert wurden, um die Größe des Datasets zu verringern. Wir verwenden diese Daten, um eine Suchanwendung zu erstellen, die markante Gebäude, wie Krankenhäuser und Schulen, sowie geologische Merkmale, wie Flüsse, Seen und Gipfel, zurückgibt.

In dieser Anwendung erstellt das Programm **DataIndexer** den Index und lädt ihn unter Verwendung eines [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx)-Konstrukts, wobei das gefilterte USGS-Dataset aus einer öffentlichen Azure SQL-Datenbank abgerufen wird. Anmeldeinformationen und Verbindungsinformationen zur Onlinedatenquelle werden im Programmcode bereitgestellt. Es ist keine weitere Konfiguration erforderlich.

> [AZURE.NOTE]Wir haben einen Filter auf dieses Dataset angewendet, um unter dem Limit des kostenlosen Tarifs von maximal 10.000 Dokumenten zu bleiben. Wenn Sie den Standardtarif verwenden, gilt dieses Limit nicht. Ausführliche Informationen zur Kapazität der einzelnen Tarife finden Sie unter [Limits und Einschränkungen](search-limits-quotas-capacity.md).

## Erstellen des Azure Search-Diensts

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie über die seitliche Navigationsleiste zu **Neu** > **Daten + Speicher** > **Search**.

     ![][1]

3. Konfigurieren Sie den Dienstnamen, den Tarif, die Ressourcengruppe, das Abonnement und den Ort. Diese Einstellungen sind erforderlich und können nach der Dienstbereitstellung nicht mehr geändert werden.

     ![][2]

	- Der **Dienstname** muss eindeutig sein und aus weniger als 15 Kleinbuchstaben ohne Leerzeichen bestehen. Dieser Name wird Bestandteil des Endpunkts Ihres Azure Search-Diensts. Weitere Informationen zu den Benennungskonventionen finden Sie unter [Benennungsregeln](https://msdn.microsoft.com/library/azure/dn857353.aspx).

	- Der **Tarif** bestimmt die Kapazität und die Abrechnung. Beide Tarife bieten die gleichen Features, aber mit unterschiedlichen Ressourcen.

		- **Kostenlos** wird auf Clustern ausgeführt, die gemeinsam mit anderen Abonnenten genutzt werden. Diese Version bietet genügend Kapazität für Tutorials sowie zum Schreiben von Code für Machbarkeitsstudien, eignet sich jedoch nicht für den Einsatz in Produktionsumgebungen. Die Bereitstellung eines kostenlosen Diensts dauert in der Regel nur wenige Minuten.
		- **Standard** wird auf fest zugeordneten Ressourcen ausgeführt und ist flexibel skalierbar. Ein Standarddienst wird zunächst mit einem Replikat und einer Partition bereitgestellt, die Kapazität kann nach der Diensterstellung jedoch angepasst werden. Die Bereitstellung eines Standarddiensts dauert länger (üblicherweise etwa 15 Minuten).

	- **Ressourcengruppen** sind Container für Dienste und Ressourcen, die einem gemeinsamen Zweck dienen. Wenn Sie eine auf Azure Search, Azure Websites und Azure-Blobspeicher basierte Suchanwendung erstellen möchten, können Sie z. B. eine Ressourcengruppe erstellen, die diese Dienste in den Verwaltungsseiten im Portal gruppiert.

	- Unter **Abonnement** stehen ggf. mehrere Abonnements zur Auswahl, sofern Sie über mehrere Abonnements verfügen.

	- **Ort** ist die Rechenzentrumsregion. Derzeit müssen alle Ressourcen im gleichen Rechenzentrum ausgeführt werden. Die Aufteilung von Ressourcen auf mehrere Rechenzentren wird nicht unterstützt.

4. Klicken Sie auf **Erstellen**, um den Dienst bereitzustellen.

Achten Sie auf Benachrichtigungen in der Navigationsleiste. Wenn der Dienst verwendet werden kann, erscheint eine Benachrichtigung.

<a id="sub-2"></a>
## Ermitteln des Dienstnamens und der API-Schlüssel Ihres Azure Search-Diensts ##

Nachdem Sie den Dienst erstellt haben, kehren Sie zum Portal zurück, um die URL oder den `api-key` zu ermitteln. Für Verbindungen mit Ihrem Search-Dienst benötigen Sie sowohl die URL als auch einen `api-key`, um den Aufruf zu authentifizieren.

1. Klicken Sie auf der Navigationsleiste auf **Startseite** und anschließend auf den Search-Dienst, um das Service-Dashboard zu öffnen.

2. Auf dem Service-Dashboard werden Kacheln für wichtige Informationen sowie das Schlüsselsymbol für den Zugriff auf die Administratorschlüssel angezeigt.

  	![][3]

3. Kopieren Sie die Dienst-URL und einen Administratorschlüssel. Diese werden später benötigt, wenn Sie sie zu den Dateien "App.config" und "Web.config" in Visual Studio-Projekten hinzufügen.

## Öffnen eines neuen Projekts und einer Projektmappe

Die Lösung umfasst zwei Projekte:

- **DataIndexer**, eine Visual C#-Konsolenanwendung zum Laden von Daten.
- **SimpleSearchMVCApp**, eine Visual C# ASP.NET-MVC-Webanwendung, die zum Abfragen und Zurückgeben der Suchergebnisse verwendet wird.

In diesem Schritt erstellen Sie beide Projekte.

1. Öffnen Sie **Visual Studio** > **Neues Projekt** > **Visual C#** > **Konsolenanwendung**.
2. Nennen Sie das Projekt **DataIndexer**, und geben Sie der Projektmappe den Namen **AzureSearchDotNetDemo**.
3. Klicken Sie im Projektmappen-Explorer in der Projektmappe mit der rechten Maustaste auf **Hinzufügen** > **Neues Projekt** > **Visual C#** > **ASP.NET-Webanwendung**.
4. Nennen Sie das Projekt **SimpleSearchMVCApp**.
5. Wählen Sie im neuen ASP.NET-Projekt die MVC-Vorlage aus, und deaktivieren Sie die betreffenden Optionen, um die Erstellung von Programmartefakten zu vermeiden, die Sie in diesem Lernprogramm nicht verwenden werden.

   Deaktivieren Sie die Kontrollkästchen für Azure-Hosting und Komponententests, und legen Sie "Authentifizierung" auf "Keine" festgelegt.

   ![][10]

Nachdem Sie die Projekte erstellt haben, sollte Ihre Lösung dem unten dargestellten Beispiel ähneln.

   ![][4]

## Installieren der Clientbibliothek für .NET und Aktualisieren anderer Pakete

1. Klicken Sie im Projektmappen-Explorer in der Projektmappe mit der rechten Maustaste auf **NuGet-Pakete verwalten**.
2. Legen Sie **Updates** > **Nur stabile** > **Alle aktualisieren** fest.

   ![][11]

3. Akzeptieren Sie die zusätzlichen Paketinstallationen, damit auch alle Abhängigkeiten installiert werden.

4. Installieren Sie anschließend die .NET-Clientbibliothek für Azure Search. Geben Sie die Suche unbedingt ordnungsgemäß an, da Sie das Paket sonst nur schwer finden. Klicken Sie erneut mit der rechten Maustaste auf **NuGet-Pakete verwalten**.

5. Geben Sie **Online** > **nuget.org** > **Include Prerelease** an, und suchen Sie dann nach *azure.search*. Klicken Sie auf **Installieren**, um die Bibliothek zu installieren.

   ![][12]

Eine Auszug aus der Liste der in diesem Beispiel verwendeten Assemblys ist unten dargestellt.

   ![][5]

## Hinzufügen eines Verweises auf die Assembly System.Configuration

**DataIndexer** verwendet **System.Configuration** zum Lesen der Konfigurationseinstellungen in "App.config".

1. Klicken Sie mit der rechten Maustaste auf **DataIndexer** > **Hinzufügen** > **Verweis** > **Framework** > **System.Configuration**. Aktivieren Sie das Kontrollkästchen.
2. Klicken Sie auf **OK**.

## Aktualisieren der Konfigurationsdateien

Jedes Projekt enthält Konfigurationsdateien, die den Dienstnamen und den API-Schlüssel angeben.

1. Ersetzen Sie in **DataIndexer** die Datei "App.config" durch folgendes Beispiel, wobei Sie [SERVICE NAME] und [SERVICE KEY] mit den für Ihren Dienst gültigen Werten aktualisieren. Beachten Sie, dass der Dienstname nicht die vollständige URL ist. Wenn z. B. der Endpunkt Ihres Search-Diensts **https://mysearchsrv.search.microsoft.net* ist, dann würden Sie in "App.config" als Dienstnamen *mysearchsrv* eingeben.

	    <?xml version="1.0" encoding="utf-8"?>
	    <configuration>
	      <startup>
	         <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	      </startup>
	      <appSettings>
	        <add key="SearchServiceName" value="[SERVICE NAME]" />
	        <add key="SearchServiceApiKey" value="[SERVICE KEY]" />
	      </appSettings>
	    </configuration>

2. Ersetzen Sie in **SimpleSearchMVCApp** die Datei "Web.config" durch folgendes Beispiel, wobei Sie [SERVICE NAME] und [SERVICE KEY] mit den für Ihren Dienst gültigen Werten aktualisieren.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
		  For more information on how to configure your ASP.NET application, visit
		  http://go.microsoft.com/fwlink/?LinkId=152368
		  -->
		<configuration>
		  <configSections>
		    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
		    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
		  </configSections>
		  <connectionStrings>
		    <add name="DefaultConnection" providerName="System.Data.SqlClient" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-SimpleMVCApp-20150303114355;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-SimpleMVCApp-20150303114355.mdf" />
		  </connectionStrings>
		  <appSettings>
		    <add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
		    <add key="SearchServiceApiKey" value="[API KEY]" />

		    <add key="webpages:Version" value="2.0.0.0" />
		    <add key="webpages:Enabled" value="false" />
		    <add key="PreserveLoginUrl" value="true" />
		    <add key="ClientValidationEnabled" value="true" />
		    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
		  </appSettings>
		  <system.web>
		    <httpRuntime targetFramework="4.5" />
		    <compilation debug="true" targetFramework="4.5" />
		    <authentication mode="Forms">
		      <forms loginUrl="~/Account/Login" timeout="2880" />
		    </authentication>
		    <pages>
		      <namespaces>
		        <add namespace="System.Web.Helpers" />
		        <add namespace="System.Web.Mvc" />
		        <add namespace="System.Web.Mvc.Ajax" />
		        <add namespace="System.Web.Mvc.Html" />
		        <add namespace="System.Web.Optimization" />
		        <add namespace="System.Web.Routing" />
		        <add namespace="System.Web.WebPages" />
		      </namespaces>
		    </pages>
		    <profile defaultProvider="DefaultProfileProvider">
		      <providers>
		        <add name="DefaultProfileProvider" type="System.Web.Providers.DefaultProfileProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
		      </providers>
		    </profile>
		    <membership defaultProvider="DefaultMembershipProvider">
		      <providers>
		        <add name="DefaultMembershipProvider" type="System.Web.Providers.DefaultMembershipProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" enablePasswordRetrieval="false" enablePasswordReset="true" requiresQuestionAndAnswer="false" requiresUniqueEmail="false" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="6" minRequiredNonalphanumericCharacters="0" passwordAttemptWindow="10" applicationName="/" />
		      </providers>
		    </membership>
		    <roleManager defaultProvider="DefaultRoleProvider">
		      <providers>
		        <add name="DefaultRoleProvider" type="System.Web.Providers.DefaultRoleProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
		      </providers>
		    </roleManager>
		    <!--
		            If you are deploying to a cloud environment that has multiple web server instances,
		            you should change session state mode from "InProc" to "Custom". In addition,
		            change the connection string named "DefaultConnection" to connect to an instance
		            of SQL Server (including SQL Azure and SQL Compact) instead of to SQL Server Express.
		      -->
		    <sessionState mode="InProc" customProvider="DefaultSessionProvider">
		      <providers>
		        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
		      </providers>
		    </sessionState>
		  </system.web>
		  <system.webServer>
		    <validation validateIntegratedModeConfiguration="false" />
		    <handlers>
		      <remove name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" />
		      <remove name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" />
		      <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
		      <add name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness32" responseBufferLimit="0" />
		      <add name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness64" responseBufferLimit="0" />
		      <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
		      <remove name="OPTIONSVerbHandler" />
		      <remove name="TRACEVerbHandler" />
		    </handlers>
		  </system.webServer>
		  <entityFramework>
		    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
		      <parameters>
		        <parameter value="v12.0" />
		      </parameters>
		    </defaultConnectionFactory>
		  </entityFramework>
		  <runtime>
		    <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
		      <dependentAssembly>
		        <assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" culture="neutral" />
		        <bindingRedirect oldVersion="0.0.0.0-6.0.0.0" newVersion="6.0.0.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" culture="neutral" />
		        <bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" culture="neutral" />
		        <bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-5.2.3.0" newVersion="5.2.3.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.WebPages.Razor" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
		      </dependentAssembly>
		    </assemblyBinding>
		  </runtime>
		</configuration>


## Ändern von DataIndexer

Dieses Programm ist eine Konsolenanwendung, die entsprechend der Angabe in "App.config" eine Verbindung mit dem Search-Dienst herstellt, den Index erstellt und anschließend das in einer Azure SQL-Datenbank gespeicherte USGS-Dataset lädt.

Da die Vorabversion der Clientbibliothek zurzeit keine Indexer unterstützt, verwenden wir die REST-API, um einen Indexer für diesen Teil des Lernprogramms zu erstellen und zu verwenden.

Bevor Sie dieses Programm ausführen können, müssen Sie zwei Änderungen vornehmen:

- Fügen Sie die Datei **AzureSearchhelper.cs** hinzu, die beim Aufrufen der REST-API verwendet wird, um Verbindungen zu verwalten und JSON-Anforderungen und -Antworten zu serialisieren und zu deserialisieren.

- Ersetzen Sie die Datei **Program.cs**, die verwendet wird, um Index und Indexer zu erstellen, Daten zu laden und Meldungen zu schreiben.

### Erstellen von "AzureSearchHelper.cs"

Code, der die REST-API aufruft, sollte eine Klasse enthalten, die Verbindungen sowie die Serialisierung und Deserialisierung der JSON-Anforderungen und -Antworten verwaltet. Diese Klasse wird in den Beispielen zu Azure Search in der Regel **AzureSearchHelper.cs** genannt. Sie können diese Klasse erstellen und sie mit dem folgenden Code **DataIndexer** hinzufügen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **DataIndexer** > **Hinzufügen** > **Neues Element** > **Code** > **Klasse**.
2. Nennen Sie die Klasse **AzureSearchHelper**.
3. Ersetzen Sie den Standardcode durch den folgenden Code.

		//Copyright 2015 Microsoft

		//Licensed under the Apache License, Version 2.0 (the "License");
		//you may not use this file except in compliance with the License.
		//You may obtain a copy of the License at

		//       http://www.apache.org/licenses/LICENSE-2.0

		//Unless required by applicable law or agreed to in writing, software
		//distributed under the License is distributed on an "AS IS" BASIS,
		//WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		//See the License for the specific language governing permissions and
		//limitations under the License.

		using System;
		using System.Net.Http;
		using System.Text;
		using Newtonsoft.Json;
		using Newtonsoft.Json.Converters;
		using Newtonsoft.Json.Serialization;

		namespace DataIndexer
		{
		    public class AzureSearchHelper
		    {
		        public const string ApiVersionString = "api-version=2015-02-28";

		        private static readonly JsonSerializerSettings _jsonSettings;

		        static AzureSearchHelper()
		        {
		            _jsonSettings = new JsonSerializerSettings
		            {
		                Formatting = Formatting.Indented, // for readability, change to None for compactness
		                ContractResolver = new CamelCasePropertyNamesContractResolver(),
		                DateTimeZoneHandling = DateTimeZoneHandling.Utc
		            };

		            _jsonSettings.Converters.Add(new StringEnumConverter());
		        }

		        public static string SerializeJson(object value)
		        {
		            return JsonConvert.SerializeObject(value, _jsonSettings);
		        }

		        public static T DeserializeJson<T>(string json)
		        {
		            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
		        }

		        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
		        {
		            UriBuilder builder = new UriBuilder(uri);
		            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
		            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;

		            var request = new HttpRequestMessage(method, builder.Uri);

		            if (json != null)
		            {
		                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
		            }

		            return client.SendAsync(request).Result;
		        }

		        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
		        {
		            if (!response.IsSuccessStatusCode)
		            {
		                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
		                throw new Exception("Search request failed: " + error);
		            }
		        }
		    }
		}



### Aktualisieren der Datei "Program.cs"

1. Öffnen Sie im Projektmappen-Explorer **DataIndexer** > **Program.cs**.
2. Ersetzen Sie den Inhalt von "Program.cs" durch den folgenden Code.

		using Microsoft.Azure;
		using Microsoft.Azure.Search;
		using Microsoft.Azure.Search.Models;
		using Microsoft.Spatial;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Timers;

		namespace DataIndexer
		{
		    class Program
		    {
		        private static SearchServiceClient _searchClient;
		        private static SearchIndexClient _indexClient;

		        // This sample shows how to delete, create, upload documents and query an index
		        static void Main(string[] args)
		        {
		            string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
		            string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

		            // Create an HTTP reference to the catalog index
		            _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
		            _indexClient = _searchClient.Indexes.GetClient("geonames");

		            Console.WriteLine("{0}", "Deleting index...\n");
		            if (DeleteIndex())
		            {
		                Console.WriteLine("{0}", "Creating index...\n");
		                CreateIndex();
		                Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
		                SyncDataFromAzureSQL();
		            }
		            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
		            Console.ReadKey();
		        }

		        private static bool DeleteIndex()
		        {
		            // Delete the index if it exists
		            try
		            {
		                _searchClient.Indexes.Delete("geonames");
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error deleting index: {0}\r\n", ex.Message.ToString());
		                Console.WriteLine("Did you remember to add your SearchServiceName and SearchServiceApiKey to the app.config?\r\n");
		                return false;
		            }

		            return true;
		        }

		        private static void CreateIndex()
		        {
		            // Create the Azure Search index based on the included schema            try
		            {
		                var definition = new Index()
		                {
		                    Name = "geonames",
		                    Fields = new[]
		                    {
		                        new Field("FEATURE_ID",     DataType.String)         { IsKey = true,  IsSearchable = false, IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("FEATURE_NAME",   DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("FEATURE_CLASS",  DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("STATE_ALPHA",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("STATE_NUMERIC",  DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("COUNTY_NAME",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("COUNTY_NUMERIC", DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("ELEV_IN_M",      DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("ELEV_IN_FT",     DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("MAP_NAME",       DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("DESCRIPTION",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("HISTORY",        DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("DATE_CREATED",   DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("DATE_EDITED",    DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true}
		                    }
		                };

		                _searchClient.Indexes.Create(definition);
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error creating index: {0}\r\n", ex.Message.ToString());
		            }

		        }

		        private static void SyncDataFromAzureSQL()
		        {
		            // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
		            Uri _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
		            HttpClient _httpClient = new HttpClient();
		            _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);

		            Console.WriteLine("{0}", "Creating Data Source...\n");
		            Uri uri = new Uri(_serviceUri, "datasources/usgs-datasource");
		            string json = "{ 'name' : 'usgs-datasource','description' : 'USGS Dataset','type' : 'azuresql','credentials' : { 'connectionString' : 'Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;' },'container' : { 'name' : 'GeoNamesRI' }} ";
		            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
		            if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
		            {
		                Console.WriteLine("Error creating data source: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            Console.WriteLine("{0}", "Creating Indexer...\n");
		            uri = new Uri(_serviceUri, "indexers/usgs-indexer");
		            json = "{ 'name' : 'usgs-indexer','description' : 'USGS data indexer','dataSourceName' : 'usgs-datasource','targetIndexName' : 'geonames','parameters' : { 'maxFailedItems' : 10, 'maxFailedItemsPerBatch' : 5, 'base64EncodeKeys': false }}";
		            response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
		            if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
		            {
		                Console.WriteLine("Error creating indexer: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            Console.WriteLine("{0}", "Syncing data...\n");
		            uri = new Uri(_serviceUri, "indexers/usgs-indexer/run");
		            response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Post, uri);
		            if (response.StatusCode != HttpStatusCode.Accepted)
		            {
		                Console.WriteLine("Error running indexer: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            bool running = true;
		            Console.WriteLine("{0}", "Synchronization running...\n");
		            while (running)
		            {
		                uri = new Uri(_serviceUri, "indexers/usgs-indexer/status");
		                response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
		                if (response.StatusCode != HttpStatusCode.OK)
		                {
		                    Console.WriteLine("Error polling for indexer status: {0}", response.Content.ReadAsStringAsync().Result);
		                    return;
		                }

		                var result = AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
		                if (result.lastResult != null)
		                {
		                    switch ((string)result.lastResult.status)
		                    {
		                        case "inProgress":
		                            Console.WriteLine("{0}", "Synchronization running...\n");
		                            Thread.Sleep(1000);
		                            break;

		                        case "success":
		                            running = false;
		                            Console.WriteLine("Synchronized {0} rows...\n", result.lastResult.itemsProcessed.Value);
		                            break;

		                        default:
		                            running = false;
		                            Console.WriteLine("Synchronization failed: {0}\n", result.lastResult.errorMessage);
		                            break;
		                    }
		                }
		            }
		        }
		    }
		}



## Erstellen und Ausführen von DataIndexer

1. Klicken Sie mit der rechten Maustaste auf das Projekt **DataIndexer**.
2. Erstellen Sie das Projekt.
3. Drücken Sie **F5**, um es auszuführen.

Es sollte ein Konsolenfenster mit diesen Nachrichten angezeigt werden.

![][6]

Im Portal sollte ein neuer Index namens **Geonames** angezeigt werden. Es kann einige Minuten dauern, bis das Portal aktualisiert worden ist. Daher sollten Sie mit dem Überprüfen der Ergebnisse etwas warten.

![][7]

## Ändern von SimpleSearchMVCApp

**SimpleSearchMVC** ist eine Web-App, die lokal in IIS Express ausgeführt wird. Sie stellt ein Suchfeld bereit und präsentiert die Suchergebnisse in einer Tabelle.

Bevor Sie dieses Programm ausführen können, müssen Sie drei Änderungen vornehmen:

- Ersetzen Sie die Klasse **HomeController.cs**, mit der Benutzereingaben akzeptiert werden. In diesem Beispiel wird der Suchbegriff in einer Variablen namens *q* gespeichert.

- Ersetzen Sie **index.cshtml**, eine Webseite, die Suchbegriffseingaben liefert und die Suchergebnisse anzeigt.

- Fügen Sie die Klasse **FeatureSearch.cs** hinzu, die den Search-Client erstellt und die Suche durchführt.

### Aktualisieren der Datei "HomeController.cs"

Ersetzen Sie den Standardcode durch den folgenden Code.

	using Microsoft.Azure.Search.Models;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;

	namespace SimpleSearchMVCApp.Controllers
	{
	    public class HomeController : Controller
	    {
	        //
	        // GET: /Home/
	        private FeaturesSearch _featuresSearch = new FeaturesSearch();

	        public ActionResult Index()
	        {
	            return View();
	        }

	        public ActionResult Search(string q = "")
	        {
	            // If blank search, assume they want to search everything
	            if (string.IsNullOrWhiteSpace(q))
	                q = "*";

	            return new JsonResult
	            {
	                JsonRequestBehavior = JsonRequestBehavior.AllowGet,
	                Data = _featuresSearch.Search(q)
	            };
	        }


	    }
	}


### Aktualisieren der Datei "index.cshtml"

Ersetzen Sie den Standardcode durch den folgenden Code.

	@{
	    ViewBag.Title = "Azure Search - Feature Search";
	}

	<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js"></script>
	<script type="text/javascript">

	    $(function () {
	        // Execute search if user clicks enter
	        $("#q").keyup(function (event) {
	            if (event.keyCode == 13) {
	                Search();
	            }
	        });
	    });

	    function Search() {
	        // We will post to the MVC controller and parse the full results on the client side
	        // You may wish to do additional pre-processing on the data before sending it back to the client
	        var q = $("#q").val();

	        $.post('/home/search',
	        {
	            q: q
	        },
	        function (data) {
	            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
	            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
	            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
	            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
	            searchResultsHTML += "<td>DATE EDITED</td></tr>";
	            for (var i = 0; i < data.length; i++) {
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
	            }

	            $("#searchResults").html(searchResultsHTML);

	        });

	        function parseJsonDate(jsonDateString) {
	            if (jsonDateString != null)
	                return new Date(parseInt(jsonDateString.replace('/Date(', '')));
	            else
	                return "";
	        }
	    };

	</script>
	<h2>USGS Search for Rhode Island</h2>

	<div class="container">
	    <input type="search" name="q" id="q" autocomplete="off" size="100" /> <button onclick="Search();">Search</button>
	</div>
	<br />
	<div class="container">
	    <div class="row">
	        <table id="searchResults" border="1"></table>
	    </div>
	</div>


### Hinzufügen der Klasse "FeaturesSearch.cs"

Fügen Sie eine Klasse hinzu, welche die Suchfunktionen für Ihre Anwendung bereitstellt.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **SimpleSearchMVCApp** > **Hinzufügen** > **Neues Element** > **Code** > **Klasse**.
2. Nennen Sie die Klasse **FeaturesSearch**.
3. Ersetzen Sie den Standardcode durch den folgenden Code.

		using Microsoft.Azure.Search;
		using Microsoft.Azure.Search.Models;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Linq;
		using System.Web;

		namespace SimpleSearchMVCApp
		{
		    public class FeaturesSearch
		    {
		        private static SearchServiceClient _searchClient;
		        private static SearchIndexClient _indexClient;

		        public static string errorMessage;

		        static FeaturesSearch()
		        {
		            try
		            {
		                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
		                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

		                // Create an HTTP reference to the catalog index
		                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
		                _indexClient = _searchClient.Indexes.GetClient("geonames");
		            }
		            catch (Exception e)
		            {
		                errorMessage = e.Message.ToString();
		            }
		        }

		        public DocumentSearchResponse Search(string searchText)
		        {
		            // Execute search based on query string
		            try
		            {
		                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
		                return _indexClient.Documents.Search(searchText, sp);
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error querying index: {0}\r\n", ex.Message.ToString());
		            }
		            return null;
		        }

		    }
		}

### Festlegen von SimpleSearchMVCApp als Startprojekt

Klicken Sie mit der rechten Maustaste auf das Projekt **SimpleSearchMVCApp**, um es als Startprojekt festzulegen.

## Erstellen und Ausführen von SimpleSearchMVCApp

Wenn Sie dieses Programm erstellen und ausführen, sollte in Ihrem Standard-Webbrowser eine Webseite angezeigt werden, die ein Suchfeld für den Zugriff auf die USGS-Daten enthält, die im Index des Azure Search-Dienst gespeichert sind.

![][8]

### Suchen nach USGS-Daten

Das USGS-Dataset enthält Datensätze, die für den Bundesstaat Rhode Island relevant sind. Wenn Sie auf **Search** klicken und das Suchfeld leer ist, erhalten Sie die ersten 50 Einträge. Dies ist die Standardeinstellung.

Durch die Eingabe eines Suchbegriffs werden die Suchmaschinenkriterien zum Eingrenzen der Suche definiert. Geben Sie einen regionalen Namen ein. *Roger Williams* war die erste Gouverneur von Rhode Island. Zahlreiche Parks, Gebäude und Schulen sind nach ihm benannt.

![][9]

Sie können auch die folgenden Abfragen ausprobieren und Ausdrücke oder Operatoren hinzufügen oder entfernen, um den Suchbereich zu ändern:

- Pawtucket OR Pembroke
- goose +cape -neck


## Nächste Schritte

Dies ist das erste Azure Search-Lernprogramm, das auf .NET und dem USGS-Dataset basiert. Im Laufe der Zeit werden wir dieses Lernprogramm erweitern, um zusätzliche Suchfunktionen zu veranschaulichen, die Sie in benutzerdefinierten Lösungen vielleicht verwenden möchten.

Wenn Sie bereits über Erfahrungen mit Azure Search verfügen, können Sie dieses Beispiel als Ausgangspunkt für das Testen von Vorschlägen (Typ-ahead- oder AutoVervollständigen-Abfragen), Filtern und Facettennavigation verwenden. Sie können auch die Suchergebnisseite verbessern, indem Sie Statistiken hinzufügen und Dokumente mit Batchvorgängen verarbeiten, damit die Benutzer die Ergebnisse seitenweise anzeigen können.

Neu bei Azure Search? Es wird empfohlen, auch andere Lernprogramme zu bearbeiten, damit Sie ein Verständnis dafür entwickeln, was Sie erstellen können. Besuchen Sie unsere [Dokumentationsseite](http://azure.microsoft.com/documentation/services/search/), um weitere Ressourcen zu finden. Sie können auch die Links in unserer [Video- und Lernprogrammliste](search-video-demo-tutorial-list.md) besuchen, um auf weitere Informationen zuzugreifen.

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png
[5]: ./media/search-get-started-dotnet/AzSearch-DotNet-Assembly.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG

<!---HONumber=Nov15_HO3-->