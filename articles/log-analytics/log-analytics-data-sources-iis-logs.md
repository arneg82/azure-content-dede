<properties
   pageTitle="IIS-Protokolle in Log Analytics | Microsoft Azure"
   description="IIS (Internet Information Services, Internetinformationsdienste) speichern Benutzeraktivitäten in Protokolldateien, die von Log Analytics gesammelt werden können. Dieser Artikel beschreibt die Konfiguration der Sammlung von IIS-Protokollen sowie Details zu den Datensätzen, die im OMS-Repository erstellt werden."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="bwren" />

# IIS-Protokolle in Log Analytics
IIS (Internet Information Services, Internetinformationsdienste) speichern Benutzeraktivitäten in Protokolldateien, die von Log Analytics gesammelt werden können.

![IIS-Protokolle](media/log-analytics-data-sources-iis-logs/overview.png)

## Konfigurieren von IIS-Protokollen
Log Analytics sammelt Einträge aus von IIS erstellten Protokolldateien. Daher müssen Sie [IIS für die Protokollierung konfigurieren](https://technet.microsoft.com/library/hh831775.aspx) und die Felder auswählen, aus denen Log Analytics Daten sammeln soll. IIS protokolliert standardmäßig nicht alle Felder, Sie sollten daher manuell weitere Felder auswählen.

Log Analytics unterstützt nur IIS-Protokolldateien, die im W3C-Format gespeichert wurden. Daten aus Protokollen im NCSA- oder nativen IIS-Format werden nicht gesammelt.

Konfigurieren Sie die IIS-Protokolle in Log Analytics über das [Menü „Daten“ in den Log Analytics-Einstellungen](log-analytics-data-sources.md/configuring-data-sources). Abgesehen von der Auswahl der Option **IIS-Protokolldateien im W3C-Format sammeln** ist keine Konfiguration erforderlich.

Wenn Sie die IIS-Protokollsammlung aktivieren, sollten Sie für jeden Server die Einstellung für das IIS-Protokollrollover konfigurieren.


## Datensammlung

Log Analytics sammelt etwa alle 15 Minuten IIS-Protokolleinträge aus jeder verbundenen Quelle. Der Agent zeichnet seine Position in jedem Ereignisprotokoll auf, aus dem er Daten sammelt. Wenn der Agent für einen bestimmten Zeitraum offline geht, sammelt Log Analytics Ereignisse ab dem Zeitpunkt der letzten Sammlung, unabhängig davon, ob die Ereignisse erstellt wurden, während der Agent offline war.


## Eigenschaften der IIS-Protokolldatensätze

IIS-Protokolldatensätze weisen den Typ **W3CIISLog** auf und besitzen die in der folgenden Tabelle aufgeführten Eigenschaften:

| Eigenschaft | Beschreibung |
|:--|:--|
| Computer | Name des Computers, auf dem das Ereignis gesammelt wurde. |
| cIP | IP-Adresse des Clients. |
| csMethod | Methode der Anforderung, beispielsweise GET oder POST. |
| csReferer | Website, von der der Benutzer über einen Link auf die aktuelle Website gelangt ist. |
| csUserAgent | Browsertyp des Clients. |
| csUserName | Name des authentifizierten Benutzers, der auf den Server zugegriffen hat. Anonyme Benutzer werden durch einen Bindestrich gekennzeichnet. |
| csUriStem | Ziel der Anforderung, beispielsweise eine Webseite. |
| csUriQuery | Abfrage, die der Client versucht hat auszuführen. |
| ManagementGroupName | Bei SCOM-Agents: Der Name der Verwaltungsgruppe. Bei anderen Agents lautet diese „AOI-<Arbeitsbereich-ID>“. |
| RemoteIPCountry | Land der IP-Adresse des Clients. |
| RemoteIPLatitude | Breitengrad der Client-IP-Adresse. |
| RemoteIPLongitude | Längengrad der Client-IP-Adresse. |
| scStatus | HTTP-Statuscode. |
| scSubStatus | Unterstatus-Fehlercode. |
| scWin32Status | Windows-Statuscode. |
| sIP | IP-Adresse des Webservers. |
| SourceSystem | Operations Manager |
| sPort | Port auf dem Server, mit dem der Client verbunden ist. |
| sSiteName | Name der IIS-Website. |
| TimeGenerated | Datum und Uhrzeit, zu der der Eintrag protokolliert wurde. |
| TimeTaken | Verarbeitungsdauer der Anforderung in Millisekunden. |

## Protokollsuchvorgänge mit IIS-Protokollen

Die folgende Tabelle zeigt verschiedene Beispiele für Protokollabfragen, die IIS-Protokolldatensätze abrufen.

| Abfrage | Beschreibung |
|:--|:--|
| Type=IISLog | Alle IIS-Protokolldatensätze. |
| Type=IISLog EventLevelName=error | Alle Windows-Ereignisse mit dem Schweregrad „error“. |
| Type=W3CIISLog &#124; Measure count() by cIP | Anzahl der IIS-Protokolleinträge nach Client-IP-Adresse. |
| Type=W3CIISLog csHost="www.contoso.com" &#124; Measure count() by csUriStem | Anzahl der IIS-Protokolleinträge nach URL für den Host www.contoso.com. |
| Type=W3CIISLog &#124; Measure Sum(csBytes) by Computer &#124; top 500000| Gesamtzahl an Bytes, die von jedem IIS-Computer empfangen wurden. |

## Nächste Schritte

- Konfigurieren Sie Log Analytics für die Sammlung von Daten aus anderen [Datenquellen](log-analytics-data-sources.md) zur Analyse.
- Informieren Sie sich über [Protokollsuchvorgänge](log-analytics-log-searches.md) zum Analysieren der aus Datenquellen und Lösungen gesammelten Daten.
- Konfigurieren Sie Warnungen in Log Analytics, sodass Sie proaktiv benachrichtigt werden, wenn in IIS-Protokollen wichtige Probleme gefunden werden.

<!---HONumber=AcomDC_0518_2016-->