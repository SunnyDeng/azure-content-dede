<properties pageTitle="How to use the API Inspector to trace calls in Azure API Management" metaKeywords="" description="Learn how to trace calls using the API Inspector in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to use the API Inspector to trace calls in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Verwenden des API-Inspektors zur Verfolgung von Aufrufen in der Azure API-Verwaltung

Die API-Verwaltung (Vorschau) enthält ein API-Inspektor-Tool, das Sie beim Debuggen und der Problembehandlung für Ihre APIs unterstützt. Der API-Inspektor kann programmgesteuert in Ihren Anwendungen eingesetzt oder direkt aus dem Entwicklerportal aufgerufen werden. Hier finden Sie eine schrittweise Anleitung für die Nutzung des API-Inspektors.

## In diesem Thema

-   [Verwenden des API-Inspektors zur Verfolgung von Aufrufen][Verwenden des API-Inspektors zur Verfolgung von Aufrufen]
-   [Analysieren der Ablaufverfolgung][Analysieren der Ablaufverfolgung]
-   [Nächste Schritte][Nächste Schritte]

## <a name="trace-call"> </a>Verwenden des API-Inspektors zur Verfolgung von Aufrufen

Um den API-Inspektor zu verwenden, fügen Sie zunächst einen **ocp-apim-trace: true**-Anforderungsheader zu Ihrem Operationsaufruf hinzu. Anschließend können Sie die Ablaufverfolgung unter der im **ocp-apim-trace-location**-Antwortheader angegebenen URL herunterladen und analysieren. Dies kann programmgesteuert in Ihren Anwendungen ausgeführt oder direkt aus dem Entwicklerportal aufgerufen werden.

Dieses Lernprogramm beschreibt die Verwendung des API-Inspektors zur Verfolgung von Aufrufen in der Echo-API.

> Jede API-Verwaltungs-Dienstinstanz enthält eine vorkonfigurierte Echo-API, die Sie für Tests und erste Schritte mit der API-Verwaltung nutzen können. Die Echo-API gibt einfach alle Eingaben zurück. Sie können diese API mit beliebigen HTTP-Verben aufrufen. Der Rückgabewert enthält wieder Ihre Eingaben.

Klicken Sie zunächst auf **Entwicklerportal** im Azure-Portal für Ihren API-Verwaltungsdienst. Daraufhin gelangen Sie zum Verwaltungsportal für die API-Verwaltung.

> Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][Erstellen einer API-Verwaltungsinstanz] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][Erste Schritte mit der Azure API-Verwaltung].

![Entwicklerportal für die API-Verwaltung][Entwicklerportal für die API-Verwaltung]

Operationen können direkt aus dem Entwicklerportal aufgerufen werden. Dies ist ein einfacher Weg, um die Operationen einer API anzuzeigen und zu testen. In diesem Schritt des Lernprogramms werden Sie die **Get Resource**-Methode der **Echo-API** aufrufen.

Klicken Sie auf **APIs** im Hauptmenü und anschließend auf **Echo API**.

![Echo API][Echo API]

> Falls nur eine API konfiguriert oder für Ihr Konto sichtbar ist, können Sie auf APIs klicken, um direkt zu den Operationen für diese API zu gelangen.

Wählen Sie die Operation **GET Resource** aus und klicken Sie auf **Konsole öffnen**.

![Konsole öffnen][Konsole öffnen]

Behalten Sie die Standard-Parameterwerte bei und wählen Sie Ihren Abonnementschlüssel in der Dropdownliste **Abonnement-Schlüssel** aus.

Geben Sie **ocp-apim-trace: true** in das Textfeld **Anforderungsheader** ein und klicken Sie auf **HTTP Get**.

![HTTP Get][HTTP Get]

Die Antwortheader enthalten ein **ocp-apim-trace-location** mit einem ähnlichen Wert wie im folgenden Beispiel.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

Sie können die Ablaufverfolgung vom angegebenen Ort herunterladen und analysieren, wie im folgenden Schritt gezeigt.

## <a name="inspect-trace"> </a>Analysieren der Ablaufverfolgung

Laden Sie die Ablaufverfolgungsdatei unter der **ocp-apim-trace-location**-URL herunter, um die Werte in der Ablaufverfolgung zu überprüfen. Es handelt sich um eine Textdatei im JSON-Format, die Werte ähnlich des folgenden Beispiels enthält.

    {
      "validityGuid":"a43a07a03de04fcb8b1425df38514742",
      "logEntries":[
        {
            "timestamp":"2014-05-03T21:00:13.2182473Z",
            "source":"Microsoft.WindowsAzure.ApiManagement.Proxy.Gateway.Handlers.DebugLoggingHandler",
            "data":{
            "originalRequest":{
                "method":"GET",
                "url":"https://contosoltd.current.int-azure-api.net/echo/resource?param1=sample&subscription-key=...",
                "headers":[
                {
                    "name":"ocp-apim-tracing",
                    "value":"true"
                },
                {
                    "name":"Host",
                    "value":"contosoltd.current.int-azure-api.net"
                }
                ]
            }
            }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"request handler",
          "data":{
            "configuration":{
              "api":{
                "from":"echo",
                "to":"http://echoapi.cloudapp.net/api"
              },
              "operation":{
                "method":"GET",
                "uriTemplate":"/resource"
              },
              "user":{
                "id":1,
                "groups":[
              
                ]
              },
              "product":{
                "id":1
              }
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "message":"Sending request to the service.",
            "request":{
              "method":"GET",
              "url":"http://echoapi.cloudapp.net/api/resource?param1=sample&subscription-key=...",
              "headers":[
                {
                  "name":"X-Forwarded-For",
                  "value":"138.91.78.77"
                },
                {
                  "name":"ocp-apim-tracing",
                  "value":"true"
                }
              ]
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "status":{
              "code":200,
              "reason":"OK"
            },
            "headers":[
              {
                "name":"Pragma",
                "value":"no-cache"
              },
              {
                "name":"Host",
                "value":"echoapi.cloudapp.net"
              },
              {
                "name":"X-Forwarded-For",
                "value":"138.91.78.77"
              },
              {
                "name":"ocp-apim-tracing",
                "value":"true"
              },
              {
                "name":"Content-Length",
                "value":"0"
              },
              {
                "name":"Cache-Control",
                "value":"no-cache"
              },
              {
                "name":"Expires",
                "value":"-1"
              },
              {
                "name":"Server",
                "value":"Microsoft-IIS/8.0"
              },
              {
                "name":"X-AspNet-Version",
                "value":"4.0.30319"
              },
              {
                "name":"X-Powered-By",
                "value":"Azure API Management - http://api.azure.com/,ASP.NET"
              },
              {
                "name":"Date",
                "value":"Sat, 03 May 2014 21:00:17 GMT"
              }
            ]
          }
        }
      ]
    }

## <a name="next-steps"> </a>Nächste Schritte

-   Lesen Sie die anderen Themen im Lernprogramm [Erste Schritte bei der erweiterten API-Konfiguration][Erste Schritte bei der erweiterten API-Konfiguration].

  [Verwenden des API-Inspektors zur Verfolgung von Aufrufen]: #trace-call
  [Analysieren der Ablaufverfolgung]: #inspect-trace
  [Nächste Schritte]: #next-steps
  [Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance
  [Erste Schritte mit der Azure API-Verwaltung]: ../api-management-get-started
  [Entwicklerportal für die API-Verwaltung]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
  [Echo API]: ./media/api-management-howto-api-inspector/api-management-echo-api.png
  [Konsole öffnen]: ./media/api-management-howto-api-inspector/api-management-open-console.png
  [HTTP Get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
  [Erste Schritte bei der erweiterten API-Konfiguration]: ../api-management-get-started-advanced
