<properties 
   pageTitle="Warnungen in Log Analytics | Microsoft Azure"
   description="Mit Warnungen in Log Analytics werden wichtige Informationen in Ihrem OMS-Repository identifiziert, und Sie können proaktiv über Probleme informiert werden oder Aktionen aufrufen, um zu versuchen, die Probleme zu beheben. In diesem Artikel wird beschrieben, wie Sie eine Warnungsregel erstellen, und es werden die verschiedenen Aktionen vorgestellt, die Sie durchführen können."
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
   ms.date="05/12/2016"
   ms.author="bwren" />

# Warnungen in Log Analytics

Mit Warnungen werden in Log Analytics wichtige Informationen in Ihrem OMS-Repository identifiziert. Mit Warnungsregeln werden Protokollsuchen automatisch gemäß einem Zeitplan durchgeführt, und es wird ein Warnungsdatensatz erstellt, wenn die Ergebnisse bestimmte Kriterien erfüllen. Die Regel kann dann automatisch eine oder mehrere Aktionen ausführen, um Sie proaktiv über die Warnung zu informieren oder einen anderen Prozess aufzurufen.

![Log Analytics-Warnungen](media/log-analytics-alerts/overview.png)


## Warnungsaktionen

Zusätzlich zum Erstellen eines Warnungsdatensatzes können Sie die Warnungsregel so konfigurieren, dass automatisch eine oder mehrere Aktionen durchgeführt werden. Aktionen können Sie proaktiv über die Warnung informieren oder einige Prozesse aufrufen, mit denen versucht wird, das erkannte Problem zu beheben. In den folgenden Aktionen werden die Aktionen beschrieben, die derzeit verfügbar sind.

### E-Mail-Aktionen
Bei E-Mail-Aktionen wird eine E-Mail mit den Details der Warnung an einen oder mehrere Empfänger gesendet. Sie können den Betreff der E-Mail angeben, aber der Inhalt ist ein von Log Analytics erstelltes Standardformat. Darin sind zusammenfassende Informationen enthalten, z.B. der Name der Warnung sowie die Details von bis zu zehn Datensätzen, die von der Protokollsuche zurückgegeben werden. Außerdem ist ein Link zu einer Protokollsuche in Log Analytics enthalten, mit der alle Datensätze dieser Abfrage zurückgegeben werden. Der Absender der E-Mail lautet *Microsoft Operations Management Suite Team &lt;noreply@oms.microsoft.com&gt;*.


### Webhookaktionen

Mit Webhookaktionen können Sie einen externen Prozess über eine HTTP POST-Anforderung aufrufen. Der aufgerufene Dienst sollte Webhooks unterstützen, und es sollte festgelegt werden, wie empfangene Nutzlasten verwendet werden. Sie können auch eine REST-API aufrufen, die keine spezielle Unterstützung für Webhooks bietet, solange die Anforderung ein für die API verständliches Format hat. Beispiele für die Nutzung eines Webhooks als Antwort auf eine Warnung sind die Verwendung eines Diensts wie [Slack](http://slack.com), um eine Nachricht mit den Details der Warnung zu senden, oder das Erstellen eines Vorfalls in einem Dienst wie [PagerDuty](http://pagerduty.com/).

Eine vollständige exemplarische Vorgehensweise für die Erstellung einer Warnungsregel mit einem Webhook zum Aufrufen eines Beispieldiensts finden Sie unter [Webhooks in Log Analytics-Warnungen](log-analytics-alerts-webhooks.md).

Webhooks enthalten eine URL und eine Nutzlast im JSON-Format, wobei es sich um die an den externen Dienst gesendeten Daten handelt. Die Nutzlast enthält standardmäßig die Werte in der folgenden Tabelle. Sie können diese Nutzlast auch durch eine benutzerdefinierte eigene Nutzlast ersetzen. In diesem Fall können Sie die Variablen in der Tabelle für die einzelnen Parameter verwenden, um deren Wert in die benutzerdefinierte Nutzlast einzubinden.


| Parameter | Variable | Beschreibung |
|:--|:--|:--|
| AlertRuleName | #alertrulename | Name der Warnungsregel |
| AlertThresholdOperator | #thresholdoperator | Schwellenwertoperator für die Warnungsregel *Größer als* oder *Kleiner als* |
| AlertThresholdValue | #thresholdvalue | Wert des Schwellenwerts für die Warnungsregel |
| LinkToSearchResults | #linktosearchresults | Link zur Log Analytics-Protokollsuche, mit der die Datensätze aus der Abfrage zurückgegeben werden, mit der die Warnung erstellt wurde |
| ResultCount | #searchresultcount | Anzahl von Datensätzen in den Suchergebnissen |
| SearchIntervalEndtimeUtc | #searchintervalendtimeutc | Endzeit für die Abfrage im UTC-Format |
| SearchIntervalInSeconds | #searchinterval | Zeitfenster für die Warnungsregel |
| SearchIntervalStartTimeUtc | #searchintervalstarttimeutc | Startzeit für die Abfrage im UTC-Format |
| SearchQuery | #searchquery | Von der Warnungsregel verwendete Protokollsuchabfrage |
| SearchResults | Siehe unten | Von der Abfrage im JSON-Format zurückgegebene Datensätze. Auf die ersten 5.000 Datensätze beschränkt. |
| WorkspaceID | #workspaceid | ID Ihres OMS-Arbeitsbereichs |


Sie können beispielsweise die folgende benutzerdefinierte Nutzlast angeben, die einen einzelnen Parameter wie *text* enthält. Der Dienst, der von diesem Webhook aufgerufen wird, erwartet diesen Parameter.

	{
		"text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
	}

Diese Beispielnutzlast wird ähnlich wie hier dargestellt aufgelöst, wenn sie an den Webhook gesendet wird.

	{
		"text":"My Alert Rule fired with 18 records over threshold of 10 ."
	}

Um die Suchergebnisse in eine benutzerdefinierte Nutzlast einzubinden, fügen Sie der JSON-Nutzlast die folgende Zeile als Eigenschaft der obersten Ebene hinzu.

	"IncludeSearchResults":true

Sie können beispielsweise Folgendes verwenden, um eine benutzerdefinierte Nutzlast zu erstellen, die nur den Warnungsnamen und die Suchergebnisse enthält.

	{
	   "alertname":"#alertrulename",
	   "IncludeSearchResults":true
	}


Sie können unter [Beispiel für einen Webhook für eine Log Analytics-Warnung](log-analytics-alerts-webhooks.md) ein vollständiges Beispiel für die Erstellung einer Warnungsregel mit einem Webhook durchgehen, um einen externen Dienst zu starten.

### Runbookaktionen

Bei Runbookaktionen wird ein Runbook in Azure Automation gestartet. Zum Verwenden dieser Art von Aktion muss die [Automation-Lösung](log-analytics-add-solutions.md) im OMS-Arbeitsbereich installiert und konfiguriert sein. Wenn sie beim Erstellen einer neuen Warnungsregel nicht installiert ist, wird ein Link zur Installationsdatei angezeigt. Sie können eines der Runbooks im Automation-Konto auswählen, die Sie in der Automation-Lösung konfiguriert haben.

Bei Runbookaktionen wird das Runbook mit einem [Webhook](../automation/automation-webhooks.md) gestartet. Wenn Sie die Warnungsregel erstellen, wird für das Runbook automatisch ein neuer Webhook mit dem Namen **OMS Alert Remediation** gefolgt von einer GUID erstellt. Diese Warnungsregel sendet keine Nutzlast, und Sie können keine Parameter des Runbooks auffüllen. Daher müssen Sie ein Runbook verwenden, das keine Parameter erfordert.

Sie können ein Runbook auch über eine Warnung mit einer Webhookaktion starten. Dies wurde im vorherigen Abschnitt beschrieben. Webhookaktionen haben den Vorteil, dass eine Nutzlast zum Senden von Daten an das Runbook bereitgestellt wird. Der Vorteil von Runbookaktionen besteht darin, dass Sie den Webhook nicht selbst erstellen und auch nicht wissen müssen, wie er verwendet wird.

## Erstellen einer Warnungsregel
Beim Erstellen einer Warnungsregel beginnen Sie mit dem Erstellen einer Protokollsuche für die Datensätze, von denen die Warnung aufgerufen werden soll. Die Schaltfläche **Warnung** ist dann verfügbar, sodass Sie die Warnungsregel erstellen und konfigurieren können.

1.	Klicken Sie auf der Seite mit der OMS-Übersicht auf **Log Search** (Protokollsuche).
2.	Erstellen Sie entweder eine neue Protokollsuchabfrage, oder wählen Sie eine gespeicherte Protokollsuche aus. 
3.	Klicken Sie oben auf der Seite auf **Warnung**, um den Bildschirm **Warnungsregel hinzufügen** hinzuzufügen.
4. Details zu den Optionen zum Konfigurieren der Warnung finden Sie unten in den Tabellen.
5. Wenn Sie das Zeitfenster für die Warnungsregel angeben, wird die Anzahl von vorhandenen Datensätzen angezeigt, die mit den Suchkriterien für das Zeitfenster übereinstimmen. So können Sie die Häufigkeit bestimmen, um die zu erwartende Anzahl von Ergebnissen zu erhalten.
4.	Klicken Sie auf **Speichern**, um die Warnungsregel abzuschließen. Die Ausführung beginnt sofort.


![Warnungsregel hinzufügen](media/log-analytics-alerts/add-alert-rule.png)

| Eigenschaft | Beschreibung |
|:--|:--|
| **Alert information** (Warnungsinformationen) | |
| Name | Eindeutiger Name zum Identifizieren der Warnungsregel |
| Schweregrad | Schweregrad der Warnung, die mit dieser Regel erstellt wird |
| Suchabfrage | Wählen Sie die Option **Use current search query** (Aktuelle Suchabfrage verwenden), um die aktuelle Abfrage zu verwenden, oder wählen Sie in der Liste eine vorhandene gespeicherte Suche aus. Die Abfragesyntax ist im Textfeld angegeben, und Sie können sie darin bei Bedarf ändern. |
| Zeitfenster | Gibt den Zeitraum für die Abfrage an. Die Abfrage gibt nur Datensätze zurück, die innerhalb dieses aktuellen Zeitbereichs erstellt wurden. Dies kann ein beliebiger Wert zwischen 5 Minuten und 24 Stunden sein. Er sollte größer als oder gleich der Warnungshäufigkeit sein. <br><br> Wenn das Zeitfenster beispielsweise auf 60 Minuten festgelegt ist und die Abfrage um 13:15 Uhr ausgeführt wird, werden nur Datensätze zurückgegeben, die zwischen 12:15 und 13:15 Uhr erstellt wurden. |
| **Zeitplan** |     
| Schwellenwert | Kriterien für die Erstellung einer Warnung. Eine Warnung wird erstellt, wenn die Anzahl der von der Abfrage zurückgegebenen Datensätze dieses Kriterium erfüllt. |
| Alert frequency (Warnhäufigkeit) | Gibt an, wie oft die Abfrage ausgeführt werden soll. Dies kann ein beliebiger Wert zwischen 5 Minuten und 24 Stunden sein. Er sollte kleiner als oder gleich dem Zeitfensterwert sein. |
| Suppress alerts (Warnungen unterdrücken) | Wenn Sie die Unterdrückung für die Warnungsregel aktivieren, werden Aktionen für die Regel nach dem Erstellen einer neuen Warnung für einen vorher festgelegten Zeitraum deaktiviert. Die Regel wird weiter ausgeführt und erstellt Warnungsdatensätze, wenn die Kriterien erfüllt sind. Dies ist der Fall, damit Sie Zeit haben, das Problem zu beheben, ohne doppelte Aktionen durchzuführen. |
| **Aktionen** | |
| E-Mail-Benachrichtigung | Geben Sie **Ja** an, falls eine E-Mail gesendet werden soll, wenn die Warnung ausgelöst wird. |
| Betreff | Der Betreff der E-Mail. Sie können den Haupttext der E-Mail nicht ändern. |
| Empfänger | Adressen aller E-Mail-Empfänger. Verwenden Sie als Trennzeichen ein Semikolon (;), wenn Sie mehrere Adressen angeben. |
| Webhook | Geben Sie **Ja** an, wenn beim Auslösen der Warnung ein Webhook aufgerufen werden soll. |
| Webhook-URL | Die URL des Webhooks. |
| Include custom JSON payload (Benutzerdefinierte JSON-Nutzlast einbinden) | Wählen Sie diese Option, wenn Sie die Standardnutzlast durch eine benutzerdefinierte Nutzlast ersetzen möchten. |
| Enter your custom JSON payload (Benutzerdefinierte JSON-Nutzlast eingeben) | Die benutzerdefinierte Nutzlast für den Webhook. Details hierzu finden Sie vorherigen Abschnitt. |
| Runbook | Geben Sie **Ja** an, falls ein Azure Automation-Runbook gestartet werden soll, wenn die Warnung ausgelöst wird. |
| Runbook auswählen | Wählen Sie das zu startende Runbook aus den Runbooks im Automation-Konto aus, die in Ihrer Automation-Lösung konfiguriert sind. |
| Run on (Ausführen auf) | Wählen Sie **Azure** aus, um das Runbook in der Azure-Cloud auszuführen. Wählen Sie die Option **Hybrid Worker**, um das Runbook auf einem [Hybrid Runbook Worker](..\automation\automation-hybrid-runbook-worker.md) in Ihrer lokalen Umgebung auszuführen. |



## Festlegen von Zeitfenstern 

### Ereigniswarnungen

Zu Ereignissen gehören Datenquellen wie Windows-Ereignisprotokolle, Syslog und benutzerdefinierte Protokolle. Es kann ratsam sein, eine Warnung zu erstellen, wenn ein bestimmtes Fehlerereignis erstellt wird oder wenn mehrere Fehlerereignisse innerhalb eines bestimmten Zeitfensters erstellt werden.

Legen Sie zum Auslösen einer Warnung aufgrund eines einzelnen Ereignisses die Anzahl von Ergebnissen auf einen Wert größer als 0 und sowohl die Häufigkeit als auch das Zeitfenster auf 5 Minuten fest. Die Abfrage wird dann alle 5 Minuten ausgeführt. Außerdem wird eine Prüfung auf das Vorhandensein eines einzelnen Ereignisses durchgeführt, das seit der letzten Ausführung der Abfrage erstellt wurde. Bei einem längeren Intervall verlängert sich ggf. der Zeitraum zwischen der Ereigniserfassung und der Warnungserstellung.

Für einige Anwendungen wird unter Umständen gelegentlich ein Fehler protokolliert, für den nicht unbedingt eine Warnung ausgelöst werden muss. Es kann beispielsweise sein, dass die Anwendung versucht, den Vorgang mit dem Fehler erneut durchzuführen, und dass der Vorgang dann erfolgreich ist. In diesem Fall sollten Sie die Warnung ggf. nur erstellen, wenn mehrere Ereignisse innerhalb eines bestimmten Zeitfensters erstellt werden.

Es kann auch vorkommen, dass Sie eine Warnung erstellen möchten, ohne dass ein entsprechendes Ereignis vorliegt. Für einen Prozess können beispielsweise regelmäßig Ereignisse protokolliert werden, um anzugeben, dass er richtig funktioniert. Wenn innerhalb eines bestimmten Zeitfensters nicht eines dieser Ereignisse protokolliert wird, sollte eine Warnung erstellt werden. In diesem Fall sollten Sie den Schwellenwert auf *Kleiner als 1* festlegen.

### Leistungswarnungen

[Leistungsdaten](log-analytics-data-sources-performance-counters.md) werden ähnlich wie Ereignisse als Datensätze im OMS-Repository gespeichert. Der Wert jedes Datensatzes ist der über die letzten 30 Minuten gemessene Mittelwert. Falls gewarnt werden soll, wenn ein Leistungsindikator einen bestimmten Schwellenwert überschreitet, sollte dieser Schwellenwert in die Abfrage einbezogen werden.

Falls beispielsweise gewarnt werden soll, wenn der Prozessor 30 Minuten lang mit mehr als 90% läuft, können Sie eine Abfrage wie *Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90* verwenden und den Schwellenwert für die Warnungsregel auf *Größer als 0* festlegen.

 Da [Leistungsdatensätze](log-analytics-data-sources-performance-counters.md) alle 30 Minuten aggregiert werden – unabhängig von der Häufigkeit, mit der Sie die einzelnen Indikatoren erfassen –, werden bei einem Zeitfenster, das kleiner als 30 Minuten ist, unter Umständen keine Datensätze zurückgegeben. Durch das Festlegen des Zeitfensters auf 30 Minuten wird sichergestellt, dass Sie einen einzelnen Datensatz für jede verbundene Quelle erhalten, mit dem der Mittelwert in Abhängigkeit der Zeit dargestellt wird.

## Verwalten von Warnungsregeln
In den **Einstellungen** von Log Analytics können Sie im Menü **Warnungen** eine Liste mit allen Warnungsregeln abrufen.

![Warnungen verwalten](./media/log-analytics-alerts/configure.png)

1. Wählen Sie in der OMS-Konsole die Kachel **Einstellungen** aus.
2. Wählen Sie **Warnungen**.

In dieser Ansicht können Sie mehrere Aktionen ausführen.

- Deaktivieren Sie eine Regel, indem Sie daneben **Aus** auswählen.
- Bearbeiten Sie eine Warnungsregel, indem Sie daneben auf das Stiftsymbol klicken.
- Entfernen Sie eine Warnungsregel, indem Sie daneben auf das Symbol **X** klicken. 

## Warnungsdatensätze

Für Warnungsdatensätze, die in Log Analytics mit Warnungsregeln erstellt wurden, ist **Warnung** als **Typ** und **OMS** als **SourceSystem** festgelegt. Sie verfügen über die Eigenschaften, die in der folgenden Tabelle aufgeführt sind.

| Eigenschaft | Beschreibung |
|:--|:--|
| Typ | *Warnung* |
| SourceSystem | *OMS* |
| AlertSeverity | Schweregrad der Warnung. |
| AlertName | Name der Warnung. |
| Query | Der Text der Abfrage, die ausgeführt wurde. |
| QueryExecutionEndTime | Gibt das Ende des Zeitraums für die Abfrage an. |
| QueryExecutionStartTime | Gibt den Beginn des Zeitraums für die Abfrage an. |
| TimeGenerated | Datum und Uhrzeit der Warnungserstellung. |

Es gibt noch andere Arten von Warnungsdatensätzen, die von der [Alert Management-Lösung](log-analytics-solution-alert-management.md) und von [Power BI-Exporten](log-analytics-powerbi.md) erstellt werden. Für diese ist **Warnung** als **Typ** festgelegt, aber sie unterscheiden sich durch ihr **SourceSystem**.




## Nächste Schritte

- Installieren Sie die [Alert Management-Lösung](log-analytics-solution-alert-management.md), um die in Log Analytics erstellten Warnungen und die über System Center Operations Manager (SCOM) gesammelten Warnungen zu analysieren.
- Informieren Sie sich weiter über [Protokollsuchen](log-analytics-log-searches.md), bei denen Warnungen generiert werden können.
- Arbeiten Sie eine exemplarische Vorgehensweise für das [Konfigurieren eines Webooks](log-analytics-alerts-webhooks.md) mit einer Warnungsregel durch.  
- Informieren Sie sich darüber, wie Sie [Runbooks in Azure Automation](https://azure.microsoft.com/documentation/services/automation) schreiben, um von Warnungen identifizierte Probleme zu beheben.

<!---HONumber=AcomDC_0518_2016-->