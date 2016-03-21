<properties 
	pageTitle="Verwenden des API-Inspektors zur Verfolgung von Aufrufen in Azure API Management" 
	description="Erfahren Sie, wie Sie den Ablauf von Aufrufen mit dem API-Inspektor in Azure API Management verfolgen können." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="sdanie"/>

# Verwenden des API-Inspektors zur Verfolgung von Aufrufen in Azure API Management

API Management stellt einen API-Inspektor bereit, der Sie bei Debugging und Problembehandlung Ihrer APIs unterstützt. Der API-Inspektor kann programmgesteuert eingesetzt oder direkt aus dem Entwicklerportal aufgerufen werden.

Zusätzlich zur Ablaufverfolgung von Vorgängen verfolgt der API-Inspektor auch Auswertungen von [Richtlinienausdrücken](https://msdn.microsoft.com/library/azure/dn910913.aspx). Eine Demonstration finden Sie in der [Cloud Cover-Episode 177 zu weiteren API Management-Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (führen Sie einen schnellen Vorlauf bis 21:00 durch).

Hier finden Sie eine schrittweise Anleitung für die Nutzung des API-Inspektors.

>[AZURE.NOTE] Nachverfolgungen mit dem API-Inspektor werden nur für Anforderungen generiert und verfügbar gemacht, die Abonnementschlüssel enthalten, die zum [Administratorkonto](api-management-howto-create-groups.md) gehören.

## <a name="trace-call"> </a> Verwenden des API-Inspektors zum Verfolgen von Aufrufen

Um den API-Inspektor zu verwenden, fügen Sie Ihrem Operationsaufruf einen **ocp-apim-trace: true**-Anforderungsheader hinzu. Anschließend können Sie die Ablaufverfolgung über die im **ocp-apim-trace-location**-Antwortheader angegebenen URL herunterladen und analysieren. Dies kann programmgesteuert in Ihren Anwendungen ausgeführt oder direkt aus dem Entwicklerportal aufgerufen werden.

In diesem Lernprogramm wird gezeigt, wie Sie mit dem API-Inspektor Vorgänge mithilfe der Basic Calculator-API verfolgen, die im Erste-Schritte-Lernprogramm zum [Verwalten Ihrer ersten API](api-management-get-started.md) konfiguriert wird. Falls Sie dieses Lernprogramm nicht abgeschlossen haben, dauert es nur wenige Minuten, die Basic Calculator-API zu importieren. Sie können jedoch auch eine andere API Ihrer Wahl verwenden, z. B. die Echo API. Jede API Management-Dienstinstanz enthält eine vorkonfigurierte Echo-API, die Sie für Tests und erste Schritte mit API Management nutzen können. Die Echo-API gibt einfach alle Eingaben zurück. Sie können diese API mit beliebigen HTTP-Verben aufrufen. Der Rückgabewert enthält wieder Ihre Eingaben.



Klicken Sie zunächst im klassischen Azure-Portal für Ihren API Management-Dienst auf **Entwicklerportal**. Operationen können direkt aus dem Entwicklerportal aufgerufen werden. Dies ist ein einfacher Weg, um die Operationen einer API anzuzeigen und zu testen.

>Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie im Abschnitt zum [Erstellen einer API Management-Dienstinstanz][] im Lernprogramm [Erste Schritte mit Azure API Management][] weitere Informationen.

![Entwicklerportal für API Management][api-management-developer-portal-menu]

Klicken Sie im oberen Menü auf **APIs** und anschließend auf **Basic Calculator**.

![Echo API][api-management-api]

Klicken Sie auf **Testen**, um die Operation **Add two integers** aufzurufen.

![Ausprobieren][api-management-open-console]

Behalten Sie die standardmäßigen Parameterwerte bei, und wählen Sie den Abonnementschlüssel in der Dropdownliste **Abonnementschlüssel** aus.

Im Entwicklerportal ist der Header **Ocp-Apim-Trace** standardmäßig bereits auf **true** festgelegt. Dieser Header bestimmt, ob eine Ablaufverfolgung generiert wird.

![Send][api-management-http-get]

Klicken Sie auf **Senden**, um den Vorgang aufzurufen.

![Send][api-management-send-results]

Die Antwortheader enthalten ein **ocp-apim-trace-location** mit einem ähnlichen Wert wie im folgenden Beispiel.

	ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

Sie können die Ablaufverfolgung vom angegebenen Speicherort herunterladen und wie im folgenden Schritt gezeigt analysieren.

## <a name="inspect-trace"> </a>Analysieren der Ablaufverfolgung

Laden Sie die Ablaufverfolgungsdatei über die **ocp-apim-trace-location**-URL herunter, um die Werte in der Ablaufverfolgung zu überprüfen. Es handelt sich um eine Textdatei im JSON-Format, die Werte ähnlich des folgenden Beispiels enthält.

	{
	    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
	    "traceEntries": {
	        "inbound": [
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0725926",
	                "data": {
	                    "request": {
	                        "method": "GET",
	                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
	                        "headers": [
	                            {
	                                "name": "Ocp-Apim-Subscription-Key",
	                                "value": "5d7c41af64a44a68a2ea46580d271a59"
	                            },
	                            {
	                                "name": "Connection",
	                                "value": "Keep-Alive"
	                            },
	                            {
	                                "name": "Host",
	                                "value": "contoso5.azure-api.net"
	                            }
	                        ]
	                    }
	                }
	            },
	            {
	                "source": "mapper",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0726213",
	                "data": {
	                    "configuration": {
	                        "api": {
	                            "from": "/calc",
	                            "to": {
	                                "scheme": "http",
	                                "host": "calcapi.cloudapp.net",
	                                "port": 80,
	                                "path": "/api",
	                                "queryString": "",
	                                "query": {},
	                                "isDefaultPort": true
	                            }
	                        },
	                        "operation": {
	                            "method": "GET",
	                            "uriTemplate": "/add?a={a}&b={b}"
	                        },
	                        "user": {
	                            "id": 1,
	                            "groups": [
	                                "Administrators",
	                                "Developers"
	                            ]
	                        },
	                        "product": {
	                            "id": 1
	                        }
	                    }
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0727522",
	                "data": {
	                    "message": "Request is being forwarded to the backend service.",
	                    "request": {
	                        "method": "GET",
	                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
	                        "headers": [
	                            {
	                                "name": "Ocp-Apim-Subscription-Key",
	                                "value": "5d7c41af64a44a68a2ea46580d271a59"
	                            },
	                            {
	                                "name": "X-Forwarded-For",
	                                "value": "33.52.215.35"
	                            }
	                        ]
	                    }
	                }
	            }
	        ],
	        "outbound": [
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1960601",
	                "data": {
	                    "response": {
	                        "status": {
	                            "code": 200,
	                            "reason": "OK"
	                        },
	                        "headers": [
	                            {
	                                "name": "Pragma",
	                                "value": "no-cache"
	                            },
	                            {
	                                "name": "Content-Length",
	                                "value": "124"
	                            },
	                            {
	                                "name": "Cache-Control",
	                                "value": "no-cache"
	                            },
	                            {
	                                "name": "Content-Type",
	                                "value": "application/xml; charset=utf-8"
	                            },
	                            {
	                                "name": "Date",
	                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
	                            },
	                            {
	                                "name": "Expires",
	                                "value": "-1"
	                            },
	                            {
	                                "name": "Server",
	                                "value": "Microsoft-IIS/8.5"
	                            },
	                            {
	                                "name": "X-AspNet-Version",
	                                "value": "4.0.30319"
	                            },
	                            {
	                                "name": "X-Powered-By",
	                                "value": "ASP.NET"
	                            }
	                        ]
	                    }
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1961112",
	                "data": {
	                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1963155",
	                "data": {
	                    "message": "Response body streaming to the caller is complete."
	                }
	            }
	        ]
	    }
	}

## <a name="next-steps"> </a>Nächste Schritte

-	Lesen Sie die anderen Themen im Lernprogramm [Erste Schritte bei der erweiterten API-Konfiguration][].
-	Eine Demonstration zu Richtlinienausdrücken für die Ablaufverfolgung finden Sie in der [Cloud Cover-Episode 177 zu weiteren API Management-Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Führen Sie einen schnellen Vorlauf bis 21:00 durch, um die Demo anzuzeigen.

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Erste Schritte mit Azure API Management]: api-management-get-started.md
[Erstellen einer API Management-Dienstinstanz]: api-management-get-started.md#create-service-instance
[Erste Schritte bei der erweiterten API-Konfiguration]: api-management-get-started-advanced.md
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 

<!---HONumber=AcomDC_0309_2016-->