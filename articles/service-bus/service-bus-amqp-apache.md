<properties 
   pageTitle="Installieren von Apache Qpid Proton-C auf einem virtuellen Linux-Computer | Microsoft Azure"
   description="Sie erfahren, wie Sie einen virtuellen CentOS Linux-Computer mit Azure Virtual Machines erstellen und die Apache Qpid Proton-C-Bibliothek erstellen und installieren."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="sethm" />

# Installieren von Apache Qpid Proton-C auf einem virtuellen Azure-Linux-Computer

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

In diesem Abschnitt wird gezeigt, wie Sie einen virtuellen CentOS Linux-Computer mit Azure Virtual Machines erstellen und die Apache Qpid Proton-C-Bibliothek zusammen mit den Python- und PHP-Sprachbindungen herunterladen, erstellen und installieren. Nachdem Sie diese Schritte ausgeführt haben, können Sie die Python- und PHP-Beispiele ausführen, die in diesem Leitfaden enthalten sind.

Der erste Schritt wird mit dem [Azure-Portal][] ausgeführt. Der folgende Screenshot zeigt die Erstellung eines virtuellen CentOS-Computers mit dem Namen „scott-centos“:

![Proton auf einem virtuellen Azure-Linux-Computer][0]

Nach der Bereitstellung wird im Portal Folgendes angezeigt:

![Proton auf einem virtuellen Azure-Linux-Computer][1]

Um sich am Computer anzumelden, müssen Sie den Endpunktport für SSH kennen. Sie können diesen Wert aus dem Portal abrufen, indem Sie den neu erstellten virtuellen Computer auswählen und auf die Registerkarte **Endpunkte** klicken. Der folgende Screenshot zeigt, dass der öffentliche SSH-Port für diesen Computer 57146 lautet.

![Proton auf einem virtuellen Azure-Linux-Computer][2]

Sie können über SSH jetzt eine Verbindung mit dem Computer herstellen. In diesem Beispiel wird das PuTTY-Tool verwendet, wie im folgenden Screenshot zu sehen:

![Proton auf einem virtuellen Azure-Linux-Computer][3]

Für die Python- und PHP-Apps werden in diesem Beispiel die Proton-Clientbibliotheken von Apache verwendet. Diese Bibliotheken stehen unter [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html) zum Download zur Verfügung. In der Infodatei im Verteilungspaket werden die erforderlichen Schritte zum Installieren der Abhängigkeiten und zum Erstellen von Proton erläutert. Zusammenfassung der Schritte:

1.  Bearbeiten Sie die yum-Konfigurationsdatei (/etc/yum.conf), und kommentieren Sie den Ausschluss für Updates von Kernelheadern (# exclude=kernel*) aus. Dies ist erforderlich, um den GCC-Compiler zu installieren.

2.  Installieren Sie die Pakete mit den erforderlichen Komponenten:

	```
	# required dependencies 
	yum install gcc cmake libuuid-devel
	
	# dependencies needed for ssl support
	yum install openssl-devel
	
	# dependencies needed for bindings
	yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
	
	# dependencies needed for python docs
	yum install epydoc
	```

1.  Laden Sie die Proton-Bibliothek herunter:

	```
	[azureuser@this-user ~]$ wget http://www.bizdirusa.com/mirrors/apache/qpid/proton/0.4/qpid-proton-0.4.tar.gz 
		--2013-05-23 21:27:55-- http://www.bizdirusa.com/mirrors/apache/qpid/proton/0.4/qpid-proton-0.4.tar.gz 
		Resolving www.bizdirusa.com... 205.186.175.195 
		Connecting to www.bizdirusa.com|205.186.175.195|:80... connected. 
		HTTP request sent, awaiting response... 200 OK 
		Length: 456693 (446K) [application/x-gzip] 
		Saving to: âqpid-proton-0.4.tar.gzâ

		100%[======================================>] 456,693 --.-K/s in 0.06s

		2015-05-23 21:27:55 (6.84 MB/s) - qpid-proton-0.4.tar.gz
	```

1.  Extrahieren Sie den Proton-Code aus dem Verteilungsarchiv:

	```
	tar xvfz qpid-proton-0.4.tar.gz
	```

1.  Erstellen und installieren Sie den Code mit den folgenden Schritten aus der Infodatei:

	```
	From the directory where you found this README file:	
	
	mkdir build cd build
			
	# Set the install prefix. You may need to adjust depending on your		
	# system.		
	cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
			
	# Omit the docs target if you do not wish to build or install		
	# documentation.		
	make all docs
			
	# Note that this step will require root privileges.		
	make install
	```

Nach Durchführung dieser Schritte ist Proton auf dem Computer installiert und einsatzbereit.

## Nächste Schritte

Möchten Sie mehr erfahren? Besuchen Sie die folgenden Links:

- [Übersicht über Service Bus AMQP]
- [Verwenden von Service Bus aus .NET mit AMQP]
- [Verwenden von Service Bus aus Java mit AMQP]
- [Verwenden von Service Bus aus Python mit AMQP]
- [Verwenden von Service Bus aus PHP mit AMQP]

[Übersicht über Service Bus AMQP]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Azure-Portal]: http://manage.windowsazure.com
[Verwenden von Service Bus aus .NET mit AMQP]: service-bus-amqp-dotnet.md
[Verwenden von Service Bus aus Java mit AMQP]: service-bus-amqp-java.md
[Verwenden von Service Bus aus Python mit AMQP]: service-bus-amqp-python.md
[Verwenden von Service Bus aus PHP mit AMQP]: service-bus-amqp-php.md

<!---HONumber=Oct15_HO2-->