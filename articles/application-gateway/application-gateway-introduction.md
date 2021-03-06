<properties
   pageTitle="Einführung in Application Gateway | Microsoft Azure"
   description="Diese Seite bietet eine Übersicht über den Application Gateway-Dienst für Lastenausgleich der Ebene 7, einschließlich Gatewaygrößen, HTTP-Lastenausgleich, cookiebasierte Sitzungsaffinität und SSL-Auslagerung."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace"/>

# Übersicht über Application Gateway

Microsoft Azure Application Gateway bietet einen von Azure verwalteten HTTP-Lastenausgleichsdienst, der auf Lastenausgleich der HTTP-Ebene 7 basiert. Einfach ausgedrückt, akzeptiert der Dienst Datenverkehr und leitet ihn auf der Grundlage von definierten Regeln an die entsprechenden Back-End-Instanzen weiter.

Mit dem Anwendungslastenausgleich können IT-Administratoren und Entwickler HTTP-basierte Routingregeln für Netzwerkdatenverkehr erstellen. Der Application Gateway-Dienst bietet hohe Verfügbarkeit und gute Kontrolle. Die Vereinbarung zum Servicelevel und die Preise finden Sie auf den Seiten [SLA](https://azure.microsoft.com/support/legal/sla/) und [Preise](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway unterstützt derzeit die Anwendungsbereitstellung der Ebene 7 mit folgenden Features:

- **HTTP-Lastenausgleich:** Die Hauptfunktion von Application Gateway ist die Bereitstellung von Lastenausgleich. Der Lastenausgleich findet auf Ebene 7 und ausschließlich für HTTP-Datenverkehr statt.
- **Cookiebasierte Sitzungsaffinität:** Dieses Feature ist hilfreich, wenn eine Benutzersitzung auf dem gleichen Back-End bleiben soll. Mithilfe von Cookies kann Application Gateway nachfolgenden Datenverkehr einer Benutzersitzung zur Verarbeitung an das gleiche Back-End weiterleiten. Dieses Feature ist wichtig, wenn für eine Benutzersitzung etwas lokal auf dem Back-End-Server gespeichert wird.
- **[Secure Sockets Layer (SSL)-Auslagerung:](application-gateway-ssl-arm.md)** Dieses Feature befreit Ihre Webserver von der kostspieligen Entschlüsselung von HTTPS-Datenverkehr. Durch die Beendigung der SSL-Verbindung bei Application Gateway und die unverschlüsselte Weiterleitung der Anforderung an den Server wird der Webserver nicht durch die Entschlüsselung belastet. Die Antwort wird von Application Gateway vor der Rückgabe an den Client wieder verschlüsselt. Dieses Feature ist nützlich, wenn sich das Back-End im gleichen geschützten virtuellen Netzwerk befindet wie die Application Gateway in Azure.
- **[URL-basiertes Inhaltsrouting:](application-gateway-url-route-overview.md)** Dieses Feature ermöglicht die Verwendung unterschiedlicher Back-End-Server für unterschiedlichen Datenverkehr. So kann beispielsweise Datenverkehr für einen Ordner auf dem Webserver oder für ein CDN an ein anderes Back-End weitergeleitet werden, um Back-Ends, die keine spezifischen Inhalte bereitstellen, nicht unnötig zu belasten.
- **[Multisiterouting:](application-gateway-multi-site-overview.md)** Mit Application Gateway können Sie bis zu 20 Websites in einem einzelnen Anwendungsgateway zusammenfassen.
- **[WebSocket-Unterstützung:](application-gateway-websocket.md)** Die native WebSocket-Unterstützung ist ein weiteres tolles Feature von Application Gateway.


## Lastenausgleich der HTTP-Ebene 7

Das Anwendungsgateway wendet die Routingregeln auf den HTTP-Datenverkehr an und ermöglicht so Lastenausgleich auf Ebene 7 (HTTP). Wenn Sie ein Anwendungsgateway erstellen, wird ein Endpunkt (VIP) zugeordnet und als öffentliche IP-Adresse für eingehenden Netzwerkverkehr verwendet. Azure bietet einen Ebene-4-Lastenausgleich über Azure Load Balancer, der auf der Transportebene (TCP/UDP) eingesetzt wird und den Lastenausgleich des gesamten eingehenden Netzwerkverkehrs für den Application Gateway-Dienst übernimmt. Das Anwendungsgateway leitet den HTTP-Datenverkehr auf Grundlage der Konfiguration weiter: virtueller Computer, Clouddienst, Web-App oder eine externe IP-Adresse.

Lastenausgleich der HTTP-Ebene 7 eignet sich für:

- Anwendungen, bei denen Anforderungen einer Benutzer-/Clientsitzung den gleichen virtuellen Back-End-Computer erreichen müssen. Beispiele für solche Anwendungen wären Einkaufswagen-Apps und E-Mail-Webserver.
- Anwendungen, die für Webserverfarmen den Mehraufwand für die SSL-Beendigung unterbinden möchten.
- Anwendungen, wie z.B. ein Content Delivery Network, für die mehrere HTTP-Anforderungen über die gleiche lange bestehende TCP-Verbindung an verschiedene Back-End-Server weitergeleitet werden bzw. für die dort ein Lastenausgleich erfolgen muss.
- Anwendungen, die WebSocket-Datenverkehr unterstützen, zur Überwachung aber weiterhin über einen HTTP-Endpunkt verfügen.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Gatewaygrößen und -instanzen

Application Gateway wird derzeit in drei Größen angeboten: klein, mittel und groß. Kleine Instanzen sind für Entwicklungs- und Testszenarien vorgesehen.

Sie können bis zu 50 Application Gateways pro Abonnement erstellen, und jedes Application Gateway kann jeweils bis zu 10 Instanzen aufweisen. Der Application Gateway-Lastenausgleich als ein von Azure verwalteter Dienst ermöglicht die Bereitstellung eines Lastenausgleichs der Ebene 7 hinter dem Azure Load Balancer.

Die folgende Tabelle zeigt einen durchschnittlichen Leistungsdurchsatz für jede Anwendungsgatewayinstanz:

| Back-End-Seitenantwort | Klein | Mittel | Groß|
|---|---|---|---|
| 6000 | 7,5 MBit/s | 13 MBit/s | 50 MBit/s |
|100\.000 | 35 MBit/s | 100 MBit/s| 200 MBit/s |

>[AZURE.NOTE] Hierbei handelt es sich um ungefähre Werte für den Durchsatz des Anwendungsgateways. Der tatsächliche Durchsatz ist abhängig von verschiedenen Umgebungsdetails wie etwa durchschnittliche Seitengröße, Speicherort der Back-End-Instanzen und Verarbeitungszeit für die Seitenbereitstellung.

## Systemüberwachung

Azure Application Gateway überprüft die Integrität der Back-End-Instanzen automatisch mithilfe einfacher oder benutzerdefinierter Integritätstests. Weitere Informationen finden Sie unter [Systemüberwachung von Application Gateway – Übersicht](application-gateway-probe-overview.md).

## Konfigurieren und Verwalten

Application Gateway kann für seinen Endpunkt eine öffentliche IP-Adresse, eine private IP-Adresse oder beides besitzen (sofern konfiguriert). Application Gateway wird innerhalb eines virtuellen Netzwerks in einem eigenen Subnetz konfiguriert. Das für Application Gateway erstellte oder verwendete Subnetz darf außer anderen Anwendungsgateways keine anderen Arten von Ressourcen enthalten.

Sie können ein Anwendungsgateway mit REST-APIs, mit PowerShell-Cmdlets, mithilfe der Azure-Befehlszeilenschnittstelle oder über das [Azure-Portal](https://portal.azure.com/) erstellen und verwalten.

## Nächste Schritte

Nachdem Sie sich mit Anwendungsgateways vertraut gemacht haben, können Sie [ein Anwendungsgateway erstellen](application-gateway-create-gateway-portal.md) oder [ein Anwendungsgateway für SSL-Auslagerung erstellen](application-gateway-ssl-arm.md), um einen Lastenausgleich für HTTPS-Verbindungen durchzuführen.

Weitere Informationen zum Erstellen eines Anwendungsgateways mit URL-basiertem Inhaltsrouting finden Sie unter [Erstellen eines Anwendungsgateways mit URL-basiertem Routing](application-gateway-create-url-route-arm-ps.md).

<!---HONumber=AcomDC_0914_2016-->