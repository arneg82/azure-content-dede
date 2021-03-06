<properties
   pageTitle="ExpressRoute-Handbuch zur Problembehandlung: Abrufen von ARP-Tabellen | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Abrufen der ARP-Tabellen für eine ExpressRoute-Verbindung."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/06/2016"
   ms.author="ganesr"/>

# ExpressRoute-Handbuch zur Problembehandlung: Abrufen von ARP-Tabellen im klassischen Bereitstellungsmodell

> [AZURE.SELECTOR]
[PowerShell - Resource Manager](expressroute-troubleshooting-arp-resourcemanager.md)
[PowerShell - Classic](expressroute-troubleshooting-arp-classic.md)

Dieser Artikel führt Sie durch die Schritte zum Abrufen der ARP-Tabellen (Address Resolution Protocol) für Ihre Azure ExpressRoute-Verbindung.

>[AZURE.IMPORTANT] Dieses Dokument soll Ihnen helfen, einfache Probleme zu untersuchen und zu beheben. Es dient nicht als Ersatz für Microsoft-Support. Wenn Sie das Problem anhand der folgenden Anleitung nicht beheben können, öffnen Sie beim [Microsoft Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) eine Supportanfrage.

## Address Resolution Protocol (ARP) und ARP-Tabellen
ARP ist ein Schicht-2-Protokoll, das im [RFC 826](https://tools.ietf.org/html/rfc826) definiert ist. ARP dient zum Zuordnen einer Ethernet-Adresse (MAC-Adresse) zu einer IP-Adresse.

Eine ARP-Tabelle ermöglicht eine Zuordnung der IPv4-Adresse und MAC-Adresse für ein bestimmtes Peering. Die ARP-Tabelle für ein ExpressRoute-Verbindungspeering enthält die folgenden Informationen für jede (primäre und sekundäre) Schnittstelle:

1. Zuordnung einer IP-Adresse der lokalen Routerschnittstelle zu einer MAC-Adresse
2. Zuordnung einer IP-Adresse der ExpressRoute-Routerschnittstelle zu einer MAC-Adresse
3. Das Alter der Zuordnung

ARP-Tabellen dienen zum Überprüfen der Schicht-2-Konfiguration und Behandeln grundlegender Schicht-2-Verbindungsprobleme.

Es folgt ein Beispiel einer ARP-Tabelle:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Der folgende Abschnitt bietet Informationen zum Anzeigen der ARP-Tabellen, die von ExpressRoute-Edge-Routern erkannt werden.

## Voraussetzungen für das Verwenden von ARP-Tabellen

Stellen Sie sicher, dass Folgendes vorliegt, bevor Sie fortfahren:

 - Eine gültige ExpressRoute-Verbindung, die mit mindestens einem Peering konfiguriert ist. Die Verbindung muss vom Konnektivitätsanbieter vollständig konfiguriert werden. Von Ihnen (oder Ihrem Konnektivitätsanbieter) muss mindestens eines der Peerings (Azure privat, Azure öffentlich und Microsoft) für diese Verbindung konfiguriert werden.

 - IP-Adressbereiche für die Konfiguration der Peerings (Azure privat, Azure öffentlich und Microsoft). Überprüfen Sie die Beispielzuweisungen von IP-Adressen auf der Seite [ExpressRoute-Routinganforderungen](expressroute-routing.md), um zu verstehen, wie IP-Adressen auf Ihrer Seite und auf ExpressRoute-Seite zugeordnet werden. Auf der Seite [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung](expressroute-howto-routing-classic.md) erhalten Sie Informationen zur Konfiguration des Peerings.

 - Informationen von Ihrem Netzwerkteam/Konnektivitätsanbieter zu den MAC-Adressen der Schnittstellen, die mit diesen IP-Adressen verwendet werden.

 - Das neueste PowerShell-Modul für Azure (mindestens Version 1.50).

## ARP-Tabellen für Ihre ExpressRoute-Verbindung
Dieser Abschnitt enthält Informationen zur Anzeige von ARP-Tabellen für jeden Peeringtyp mithilfe von PowerShell. Bevor Sie fortfahren, muss das Peering entweder von Ihnen oder Ihrem Konnektivitätsanbieter konfiguriert werden. Jede Verbindung hat einen primären und sekundären Pfad. Sie können die ARP-Tabelle für jeden Pfad unabhängig überprüfen.

### IP-Adressen für privates Azure-Peering
Das folgende Cmdlet dient zum Abrufen der ARP-Tabellen für privates Azure-Peering:

		# Required variables
		$ckt = "<your Service Key here>

		# ARP table for Azure private peering--primary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

		# ARP table for Azure private peering--secondary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Nachstehend sehen Sie die Beispielausgabe für einen der Pfade:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### ARP-Tabellen für öffentliches Azure-Peering:
Das folgende Cmdlet dient zum Abrufen der ARP-Tabellen für öffentliches Azure-Peering:

		# Required variables
		$ckt = "<your Service Key here>

		# ARP table for Azure public peering--primary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

		# ARP table for Azure public peering--secondary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Nachstehend sehen Sie die Beispielausgabe für einen der Pfade:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Nachstehend sehen Sie die Beispielausgabe für einen der Pfade:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           64.0.0.1 ffff.eeee.dddd
		  0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### ARP-Tabellen für Microsoft-Peering
Das folgende Cmdlet dient zum Abrufen der ARP-Tabellen für Microsoft-Peering:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Nachstehend sehen Sie die Beispielausgabe für einen der Pfade:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           65.0.0.1 ffff.eeee.dddd
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## Nutzen dieser Informationen
Die ARP-Tabelle eines Peerings dient zum Bestimmen und Überprüfen der Schicht-2-Konfiguration und -Konnektivität. Dieser Abschnitt enthält eine Übersicht über ARP-Tabellen in verschiedenen Szenarien.

### ARP-Tabelle einer Verbindung im Betriebszustand (d.h. dem erwarteten Zustand)

 - Die ARP-Tabelle hat einen Eintrag für die lokale Seite mit einer gültigen IP- und MAC-Adresse und einen ähnlichen Eintrag für die Microsoft-Seite.
 - Das letzte Oktett der lokalen IP-Adresse ist immer eine ungerade Zahl.
 - Das letzte Oktett der IP-Adresse von Microsoft ist immer eine gerade Zahl.
 - Dieselbe MAC-Adresse wird auf Microsoft-Seite für alle drei Peerings (primär/sekundär) angezeigt.


		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           65.0.0.1 ffff.eeee.dddd
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### ARP-Tabelle, wenn auf der lokalen Seite bzw. beim Konnektivitätsanbieter Probleme auftreten

 Die ARP-Tabelle enthält nur einen Eintrag. Dieser zeigt die Zuordnung zwischen der MAC-Adresse und IP-Adresse, die auf der Microsoft-Seite verwendet wird.

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Wenn ein Problem wie dieses auftritt, öffnen Sie bei Ihrem Konnektivitätsanbieter eine Supportanfrage, um es zu beheben.


### ARP-Tabelle, wenn auf der Microsoft-Seite Probleme auftreten

 - Wenn auf der Microsoft-Seite Probleme vorliegen, wird für ein Peering keine ARP-Tabelle angezeigt.
 -  Öffnen Sie beim [Microsoft Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) eine Supportanfrage. Geben Sie an, dass Sie ein Problem mit der Schicht-2-Konnektivität haben.

## Nächste Schritte

 - Überprüfen der Schicht-3-Konfigurationen Ihrer ExpressRoute-Verbindung:
	 - Abrufen einer Routenübersicht zum Bestimmen des Status von BGP-Sitzungen
	 - Abrufen einer Routentabelle zum Bestimmen der für ExpressRoute angekündigten Präfixe
 - Überprüfen der Datenübertragung anhand der ein- und ausgehenden Bytes
 - Öffnen Sie beim [Microsoft Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) eine Supportanfrage, wenn weiterhin Probleme auftreten.

<!---HONumber=AcomDC_0713_2016-->