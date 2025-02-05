---
title: Überwachen der Leistung von AKS-Clustern mit Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie mit Azure Monitor für Container die Leistung analysieren und Daten protokollieren können.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: magoedte
ms.openlocfilehash: 945dc6c35eacab99db28172703e1aebed10bd58a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067085"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Verstehen der Leistung von AKS-Clustern mit Azure Monitor für Container
Mit Azure Monitor für Container können Sie die Leistungsdiagramme und den Integritätsstatus verwenden, um die Workload Ihrer AKS-Cluster (Azure Kubernetes Service) aus zwei Perspektiven zu überwachen. Sie können die Überwachung direkt aus einem AKS-Cluster durchführen, oder Sie können alle AKS-Cluster unter einem Abonnement mit Azure Monitor überwachen. Die Anzeige von Azure Container Instances ist auch möglich, wenn Sie einen bestimmten AKS-Cluster überwachen.

In diesem Artikel werden die beiden Perspektiven beschrieben, und Sie erfahren, wie Sie erkannte Probleme mit Azure Monitor schnell bewerten, untersuchen und beheben.

Weitere Informationen zum Aktivieren von Azure Monitor für Container finden Sie unter [Onboarding von Azure Monitor für Container](container-insights-onboard.md).

Azure Monitor verfügt über eine Multi-Cluster-Ansicht, in der der Integritätsstatus aller überwachten AKS-Cluster angezeigt wird, auf denen Linux und Windows Server 2019 ausgeführt wird und die in Ressourcengruppen in Ihrem Abonnement bereitgestellt wurden. Es werden auch ermittelte AKS-Cluster angezeigt, die nicht von der Lösung überwacht werden. Sie können die Clusterintegrität auf einen Blick einschätzen und von diesem Punkt aus einen Drilldown zur Seite mit der Knoten- bzw. Controllerleistung ausführen oder alternativ zu den Leistungsdiagrammen für den Cluster navigieren. Für AKS-Cluster, die ermittelt und als nicht überwacht erkannt wurden, können Sie die Überwachung jederzeit aktivieren. 

Die wichtigsten Unterschiede zwischen der Überwachung eines Windows Server-Clusters mit Azure Monitor für Container gegenüber einem Linux-Cluster sind:

- Für Windows-Knoten und -Container ist keine Arbeitsspeicher-RSS-Metrik verfügbar.
- Für Windows-Knoten sind keine Informationen zur Speicherkapazität des Datenträgers verfügbar.
- Eine Unterstützung von Liveprotokollen ist verfügbar, ausgenommen Windows-Containerprotokolle.
- Nur Pod-Umgebungen werden überwacht, nicht aber Docker-Umgebungen.
- Mit der Vorschauversion werden maximal 30 Windows Server-Container unterstützt. Diese Einschränkung gilt nicht für Linux-Container. 

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

## <a name="multi-cluster-view-from-azure-monitor"></a>Multi-Cluster-Ansicht in Azure Monitor

Zum Anzeigen des Integritätsstatus aller bereitgestellten AKS-Cluster wählen Sie im linken Seitenbereich im Azure-Portal die Option **Überwachen** aus. Wählen Sie im Abschnitt **Insights** die Option **Container** aus. 

![Beispiel für ein Multi-Cluster-Dashboard in Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Die Registerkarte **Überwachte Cluster** enthält die folgenden Informationen:

- Wie viele Cluster sich in einem kritischen oder fehlerhaften Zustand im Vergleich zur Anzahl von Clustern in fehlerfreiem Zustand oder zu den Clustern befinden, die keine Daten übermitteln (als Zustand „Unbekannt“ bezeichnet)
- Ob alle Bereitstellungen der [Azure Kubernetes Engine (AKS-engine)](https://github.com/Azure/aks-engine) fehlerfrei sind
- Wie viele Knoten, Benutzer und Systempods pro Cluster bereitgestellt wurden
- Wie viel Speicherplatz auf dem Datenträger verfügbar ist und ob ein Kapazitätsproblem besteht

Folgende Integritätsstatus sind enthalten: 

* **Fehlerfrei**: Für die VM wurden keine Probleme erkannt, und sie funktioniert wie gewünscht. 
* **Kritisch**: Mindestens ein kritischer Fehler wurde entdeckt, der behandelt werden muss, um den normalen Betriebszustand wiederherzustellen.
* **Warnung**: Es wurde mindestens ein Problem entdeckt, das behandelt werden muss, weil sonst ggf. der Übergang zu einem kritischen Integritätszustand erfolgt.
* **Unbekannt:** Wenn der Dienst keine Verbindung mit einem Knoten oder einem Pod herstellen konnte, wechselt der Status zu „Unbekannt“.
* **Nicht gefunden**: Entweder der Arbeitsbereich, die Ressourcengruppe oder das Abonnement mit dem Arbeitsbereich für diese Lösung wurde gelöscht.
* **Nicht autorisiert**: Der Benutzer verfügt nicht über die erforderlichen Berechtigungen, um die Daten im Arbeitsbereich zu lesen.
* **Fehler**: Beim Versuch, Daten aus dem Arbeitsbereich zu lesen, ist ein Fehler aufgetreten.
* **Falsch konfiguriert**: Azure Monitor für Container wurde im angegebenen Arbeitsbereich nicht richtig konfiguriert.
* **Keine Daten**: In den letzten 30 Minuten wurden keine Daten an den Arbeitsbereich gemeldet.

Für den Integritätszustand wird der allgemeine Clusterstatus als *schlechtester* der drei Status berechnet, mit einer Ausnahme: Falls einer der drei Status „Unbekannt“ lautet, wird auch für den allgemeinen Clusterstatus **Unbekannt** angezeigt. 

Die folgende Tabelle enthält eine Aufschlüsselung der Berechnung, mit der die Integritätszustände für einen überwachten Cluster in der Multi-Cluster-Ansicht gesteuert werden.

| |Status |Verfügbarkeit |  
|-------|-------|-----------------|  
|**Benutzerpod**| | |  
| |Healthy |100 % |  
| |Warnung |90–99 % |  
| |Kritisch |<90 % |  
| |Unknown |Wenn keine Meldung in den letzten 30 Minuten erfolgt ist |  
|**Systempod**| | |  
| |Healthy |100 % |
| |Warnung |– |
| |Kritisch |<100 % |
| |Unknown |Wenn keine Meldung in den letzten 30 Minuten erfolgt ist |
|**Node** | | |
| |Healthy |>85 % |
| |Warnung |60–84 % |
| |Kritisch |<60 % |
| |Unknown |Wenn keine Meldung in den letzten 30 Minuten erfolgt ist |

In der Liste mit den Clustern können Sie einen Drilldown zur Seite **Cluster** ausführen, indem Sie den Namen des Clusters auswählen. Navigieren Sie dann zur Seite **Knoten** mit der Leistungsanzeige, indem Sie in der Spalte **Knoten** den Rollup der Knoten für den jeweiligen Cluster auswählen. Außerdem können Sie auch einen Drilldown zur Leistungsseite **Controller** ausführen, indem Sie den Rollup der Spalte **Benutzerpods** oder **Systempods** auswählen.  

## <a name="view-performance-directly-from-an-aks-cluster"></a>Direktes Anzeigen der Leistung auf einem AKS-Cluster

Der Zugriff auf Azure Monitor für Container ist direkt aus einem AKS-Cluster möglich, indem Sie im linken Randbereich die Option **Insights** auswählen. Die Informationen zu Ihrem AKS-Cluster sind in vier Perspektiven unterteilt:

- Cluster
- Nodes 
- Controllers 
- Container

Die Standardseite wird geöffnet, wenn Sie **Insights** > **Cluster** auswählen. In vier Leistungsliniendiagrammen werden wichtige Leistungsmetriken Ihres Clusters angezeigt. 

![Beispielleistungsdiagramme auf der Registerkarte „Cluster“](./media/container-insights-analyze/containers-cluster-perfview.png)

In den Leistungsdiagrammen werden vier Leistungsmetriken angezeigt:

- **Knoten-CPU-Auslastung&nbsp;%** : Eine aggregierte Ansicht der CPU-Auslastung für den gesamten Cluster. Wählen Sie zum Filtern der Ergebnisse für den Zeitbereich im Perzentilselektor oberhalb des Diagramms **Mittelw.** , **Min.** , **50.** , **90.** , **95.** oder **Max.** aus. Die Filter können einzeln oder gemeinsam verwendet werden. 
- **Knotenspeicherauslastung&nbsp;%** : Eine aggregierte Ansicht der Speicherauslastung für den gesamten Cluster. Wählen Sie zum Filtern der Ergebnisse für den Zeitbereich im Perzentilselektor oberhalb des Diagramms **Mittelw.** , **Min.** , **50.** , **90.** , **95.** oder **Max.** aus. Die Filter können einzeln oder gemeinsam verwendet werden. 
- **Knotenanzahl**: Die Anzahl von Knoten und der Status von Kubernetes. Für die dargestellten Clusterknoten werden die Status „Gesamt“, „Bereit“ und „Nicht bereit“ angezeigt. Sie können im Selektor oberhalb des Diagramms einzeln oder gemeinsam gefiltert werden. 
- **Anzahl aktiver Pods**: Die Podanzahl und der Status von Kubernetes. Als Status der dargestellten Pods werden „Gesamt“, „Ausstehend“, „Wird ausgeführt“, „Unbekannt“, „Erfolgreich“ oder „Fehler“ angezeigt. Sie können im Selektor oberhalb des Diagramms einzeln oder gemeinsam gefiltert werden. 

Verwenden Sie die Pfeiltasten nach links und rechts, um die einzelnen Datenpunkte des Diagramms zu durchlaufen. Verwenden Sie die Pfeiltasten nach oben und unten, um die Perzentilzeilen zu durchlaufen. Wählen Sie das Stecknadelsymbol in der oberen rechten Ecke eines Diagramms aus, um das ausgewählte Diagramm an das zuletzt angezeigte Azure-Dashboard anzuheften. Über dem Dashboard können Sie die Größe des Diagramms ändern und es neu positionieren. Die Auswahl des Diagramms über das Dashboard leitet Sie zu Azure Monitor für Container weiter und lädt den richtigen Bereich und die richtige Ansicht.

Azure Monitor für Container unterstützt auch den Azure Monitor-[Metrik-Explorer](../platform/metrics-getting-started.md), in dem Sie eigene Boxplotdiagramme erstellen, Trends korrelieren und untersuchen und an Dashboards anheften können. Im Metrik-Explorer können Sie die Kriterien, die Sie für die Visualisierung Ihrer Metriken festgelegt haben, auch als Grundlage für eine [metrikbasierte Warnungsregel](../platform/alerts-metric.md) verwenden. 

## <a name="view-container-metrics-in-metrics-explorer"></a>Anzeigen von Containermetriken im Metrik-Explorer

Im Metrik-Explorer können Sie Metriken für aggregierte Knoten und Podnutzung aus Azure Monitor für Container anzeigen. In der folgenden Tabelle sind die Details zur Verwendung der Metrikdiagramme für die Visualisierung von Containermetriken zusammengefasst.

|Namespace | Metrik | BESCHREIBUNG | 
|----------|--------|-------------|
| insights.container/nodes | |
| | cpuUsageMillicores | Aggregierte Messung der CPU-Auslastung im gesamten Cluster. Hierbei handelt es sich um eine Aufteilung des CPU-Kerns in 1000 Einheiten (Milli = 1000). Dient zum Bestimmen der Verwendung von Kernen in einem Container, in dem viele Anwendungen einen einzigen Kern verwenden können.| 
| | cpuUsagePercentage | Aggregierte durchschnittliche CPU-Auslastung in Prozent für den gesamten Cluster.|
| | memoryRssBytes | Verwendeter RSS-Arbeitsspeicher des Containers in Byte.| 
| | memoryRssPercentage | Verwendeter RSS-Arbeitsspeicher des Containers in Prozent.|
| | memoryWorkingSetBytes | Verwendeter Arbeitssatz-Arbeitsspeicher des Containers.| 
| | memoryWorkingSetPercentage | Verwendeter Arbeitssatz-Arbeitsspeicher des Containers in Prozent. | 
| | nodesCount | Die Knotenanzahl von Kubernetes.|
| insights.container/pods | |
| | PodCount | Die Podanzahl von Kubernetes.|

Sie können eine Metrik [teilen](../platform/metrics-charts.md#apply-splitting-to-a-chart), um sie nach Dimension anzuzeigen und damit zu visualisieren, wie verschiedene Segmente der Metrik zusammenhängen. Für einen Knoten können Sie das Diagramm nach der Dimension *Host* segmentieren. Über einen Pod können Sie die Segmentierung nach den folgenden Dimensionen durchführen:

* Controller
* Kubernetes-Namespace
* Knoten
* Phase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analysieren von Knoten, Controllern und der Containerintegrität

Wenn Sie zur Registerkarte **Knoten**, **Controller** oder **Container** wechseln, wird rechts auf der Seite automatisch ein Eigenschaftenbereich angezeigt. Dort werden die Eigenschaften des ausgewählten Elements angezeigt. Dies umfasst auch die Bezeichnungen, die Sie definieren, um die Kubernetes-Objekte zu organisieren. Wenn ein Linux-Knoten ausgewählt ist, werden im Abschnitt **Local Disk Capacity** (Kapazität der lokalen Festplatte) auch der verfügbare Speicherplatz auf der Festplatte und der prozentual für jede Festplatte auf dem Knoten genutzte Speicherplatz angezeigt. Wählen Sie im Bereich den Link **>>** aus, um den Bereich ein- und auszublenden.

![Beispiel für Eigenschaftenbereiche von Kubernetes-Perspektiven](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Wenn Sie die Objekte in der Hierarchie erweitern, wird der Eigenschaftenbereich auf Basis des ausgewählten Objekts aktualisiert. Im Bereich können Sie auch Kubernetes-Ereignisse mit vordefinierten Protokollsuchen anzeigen, indem Sie am oberen Rand des Bereichs die Option **Kubernetes-Ereignisprotokolle anzeigen** auswählen. Weitere Informationen zum Anzeigen von Kubernetes-Protokolldaten finden Sie unter [Suchen von Protokollen zur Datenanalyse](container-insights-log-search.md). Beim Überprüfen von Clusterressourcen können Sie Protokolle zu Containern und Ereignissen in Echtzeit sehen. Weitere Informationen zu diesem Feature und zu der Konfiguration, die für die Gewährung und Steuerung des Zugriffs erforderlich ist, finden Sie unter [Anzeigen von Containerprotokollen in Echtzeit mit Azure Monitor für Container](container-insights-live-logs.md). 

Verwenden Sie die Option **+ Filter hinzufügen** oben auf der Seite, um die Ergebnisse für die Ansicht nach **Dienst**, **Knoten**, **Namespace** oder **Knotenpool** zu filtern. Nachdem Sie den Filterbereich ausgewählt haben, wählen Sie einen der Werte im Feld **Wert(e) auswählen** aus. Nachdem der Filter konfiguriert wurde, wird er global auf alle Perspektiven des AKS-Clusters angewendet. Die Formel unterstützt nur das Gleichheitszeichen. Sie können zusätzlich zum obersten weitere Filter hinzufügen, um Ihre Ergebnisse weiter einzugrenzen. Wenn Sie beispielsweise einen Filter nach **Knoten** angeben, können Sie für den zweiten Filter nur **Dienst** oder **Namespace** auswählen.

![Beispiel zur Verwendung des Filters zum Eingrenzen der Ergebnisse](./media/container-insights-analyze/add-filter-option-01.png)

Die Angabe eines Filters auf einer Registerkarte wird weiterhin angewendet, wenn Sie eine andere Registerkarte auswählen. Sie wird gelöscht, wenn Sie das Symbol **x** neben dem angegebenen Filter auswählen. 

Auf der Registerkarte **Knoten** folgt die Zeilenhierarchie dem Kubernetes-Objektmodell, das mit einem Knoten in Ihrem Cluster beginnt. Wenn Sie den Knoten erweitern, können Sie mindestens einen Pod anzeigen, der auf dem Knoten ausgeführt wird. Wenn mehrere Container zu einem Pod zusammengefasst sind, werden sie als letzte Zeile in der Hierarchie angezeigt. Sie können auch anzeigen, wie viele nicht auf Pods bezogene Workloads auf dem Host ausgeführt werden, falls Prozessor oder Arbeitsspeicher des Hosts überlastet sind.

![Beispiel für die Kubernetes-Knotenhierarchie in der Leistungsansicht](./media/container-insights-analyze/containers-nodes-view.png)

Windows Server-Container, auf denen das Betriebssystem Windows Server 2019 ausgeführt wird, werden in der Liste nach allen Linux-basierten Knoten angezeigt. Wenn Sie einen Windows Server-Knoten erweitern, können Sie einen oder mehrere Pods und Container anzeigen, die auf dem Knoten ausgeführt werden. Nachdem ein Knoten ausgewählt wurde, werden im Eigenschaftenbereich Versionsinformationen angezeigt. Agent-Informationen sind ausgeschlossen, weil für Windows Server-Knoten kein Agent installiert ist. 

![Beispiel-Knotenhierarchie mit aufgeführten Windows Server-Knoten](./media/container-insights-analyze/nodes-view-windows.png) 

Virtuelle Knoten mit Azure Container Instances, auf denen das Linux-Betriebssystem ausgeführt wird, werden nach dem letzten AKS-Clusterknoten in der Liste angezeigt. Wenn Sie einen virtuellen Container Instances-Knoten erweitern, können Sie mindestens einen Container Instances-Pod und die auf dem Knoten ausgeführten Container anzeigen. Metriken werden nicht für Knoten gesammelt und gemeldet, sondern nur für Pods.

![Beispielknotenhierarchie mit aufgelisteten Container Instances](./media/container-insights-analyze/nodes-view-aci.png)

Auf einem erweiterten Knoten können Sie per Drilldown von dem Pod oder Container, der auf dem Knoten ausgeführt wird, zum Controller navigieren, um für diesen Controller gefilterte Leistungsdaten anzuzeigen. Wählen Sie den Wert in der Spalte **Controller** für den spezifischen Knoten aus.
 
![Exemplarischer Drilldownvorgang vom Knoten zum Controller in der Leistungsansicht](./media/container-insights-analyze/drill-down-node-controller.png)

Wählen Sie oben auf der Seite Controller oder Container aus, um den Status und die Ressourcenauslastung für diese Objekte zu überprüfen. Wählen Sie zum Überprüfen der Arbeitsspeicherauslastung in der Dropdownliste **Metrik** die Option **Arbeitsspeicher RSS** oder **Arbeitssatz für Arbeitsspeicher** aus. **Arbeitsspeicher RSS** wird nur für die Kubernetes-Version 1.8 und höher unterstützt. Andernfalls werden Werte für **Min&nbsp;%** als *NaN&nbsp;%* angezeigt. Dieser numerische Datentypwert stellt einen nicht definierten oder nicht darstellbaren Wert dar.

![Leistungsansicht zu den Containerknoten](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Im **Arbeitsspeicher-Arbeitssatz** sind der „residente“ Speicher und der virtuelle Speicher (Cache) enthalten. Dies ist der gesamte Speicher, der von der Anwendung verwendet wird. Unter **Arbeitsspeicher-RSS** wird nur der Hauptspeicher angezeigt (dies ist in anderen Worten nichts anderes als der residente Speicher). Diese Metrik zeigt die tatsächliche Kapazität des verfügbaren Arbeitsspeichers an. Worin besteht der Unterschied zwischen residentem Speicher und virtuellem Speicher?

- Residenter Speicher oder Hauptspeicher ist die tatsächliche Menge an Arbeitsspeicher, die auf dem Computer für die Knoten des Clusters verfügbar ist.

- Beim virtuellen Speicher handelt es sich um reservierten Festplattenspeicher (Cache), der vom Betriebssystem verwendet wird, um bei hoher Speicherauslastung Daten aus dem Arbeitsspeicher auf den Datenträger auszulagern und bei Bedarf in den Arbeitsspeicher zurückzuholen.

Standardmäßig beziehen sich Leistungsdaten auf die letzten sechs Stunden, aber Sie können den Zeitraum mit der Option **TimeRange** oben links ändern. Außerdem können Sie die Ergebnisse im Zeitbereich filtern, indem Sie im Perzentilselektor die Optionen **Min.** , **Mittelw.** , **50.** , **90.** , **95.** und **Max.** auswählen. 

![Perzentilauswahl für die Datenfilterung](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Wenn Sie den Mauszeiger auf das Balkendiagramm unter der Spalte **Trend** bewegen, zeigt jeder Balken innerhalb eines Stichprobenzeitraums von 15 Minuten entweder die CPU- oder Speicherauslastung an – je nachdem, welche Metrik ausgewählt ist. Verwenden Sie nach dem Auswählen des Trenddiagramms per Tastatur die Tastenkombination ALT+BILD-AUF oder ALT+BILD-AB, um die Balken einzeln zu durchlaufen. Sie erhalten die gleichen Details wie beim Bewegen des Mauszeigers auf den Balken.

![Beispiel für ein Trendbalkendiagramm mit Mauszeigerbewegung](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

Im nächsten Beispiel hat **Container** für den ersten Knoten in der Liste (*aks-nodepool1-* ) den Wert 9. Dieser Wert ist ein Rollup der gesamten bereitgestellten Container.

![Beispiel für ein Rollup der Container pro Knoten](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Mit diesen Informationen können Sie schnell feststellen, ob Sie das richtige Verhältnis zwischen Containern und Knoten im Cluster verwenden. 

In der folgenden Tabelle sind die Informationen beschrieben, die beim Anzeigen der Registerkarte **Knoten** angezeigt werden.

| Column | BESCHREIBUNG | 
|--------|-------------|
| NAME | Der Name des Hosts. |
| Status | Kubernetes-Ansicht des Knotenstatus. |
| Min.&nbsp;%, Mittelw.&nbsp;%, 50.&nbsp;%, 90.&nbsp;%, 95.&nbsp;%, Max.&nbsp;%  | Durchschnittlicher Prozentsatz von Knoten basierend auf dem Perzentil für die ausgewählte Dauer. |
| Min., Mittelw., 50., 90., 95., Max. | Tatsächlicher Durchschnittswert der Knoten basierend auf dem Perzentil für den ausgewählten Zeitraum. Der Mittelwert wird ausgehend vom festgelegten CPU-/Speichergrenzwert für einen Knoten gemessen. Für Pods und Container ist dies der vom Host gemeldete Durchschnittswert. |
| Container | Anzahl von Containern |
| Betriebszeit | Stellt den Zeitraum dar, der seit dem Start oder Neustart eines Knotens verstrichen ist. |
| Controller | Nur für Container und Pods. Zeigt an, unter welchem Controller sich ein Element befindet. Nicht alle Pods befinden sind in einem Controller, sodass einige Spalten **N/V** anzeigen. | 
| Trend Min.&nbsp;%, Mittelw.&nbsp;%, 50.&nbsp;%, 90.&nbsp;%, 95.&nbsp;%, Max.&nbsp;% | Balkendiagrammtrend, der die durchschnittliche Perzentilmetrik des Controllers in Prozent anzeigt. |

Wählen Sie im Selektor **Controller** aus.

![Auswählen der Controlleransicht](./media/container-insights-analyze/containers-controllers-tab.png)

Hier können Sie die Leistungsintegrität Ihrer Controller und virtuellen Container Instances-Knoten oder virtuellen Knotenpods anzeigen, die nicht mit einem Controller verbunden sind.

![Leistungsansicht des Controllers \<Name>](./media/container-insights-analyze/containers-controllers-view.png)

Die Zeilenhierarchie beginnt mit einem Controller. Wenn Sie einen Controller erweitern, wird mindestens ein Pod angezeigt. Wenn Sie einen Pod erweitern, wird in der letzten Zeile der Container angezeigt, der im Pod gruppiert ist. Von einem erweiterten Controller aus können Sie per Drilldown zu dem Knoten navigieren, auf dem er ausgeführt wird, um für diesen Controller gefilterte Leistungsdaten anzuzeigen. Container Instances-Pods, die nicht mit einem Controller verbunden sind, werden in der Liste als Letztes aufgeführt.

![Beispielcontrollerhierarchie mit aufgelisteten Container Instances-Pods](./media/container-insights-analyze/controllers-view-aci.png)

Wählen Sie den Wert in der Spalte **Knoten** für den spezifischen Controller aus.

![Exemplarischer Drilldownvorgang vom Knoten zum Controller in der Leistungsansicht](./media/container-insights-analyze/drill-down-controller-node.png)

In der folgenden Tabelle sind die Informationen beschrieben, die bei der Anzeige von Controllern erscheinen.

| Column | BESCHREIBUNG | 
|--------|-------------|
| NAME | Der Name des Controllers.|
| Status | Der Rollupstatus der Container, wenn deren Ausführung mit einem Status abgeschlossen wurde, z. B. *OK*, *Abgebrochen*, *Fehler*, *Beendet* oder *Angehalten*. Wenn der Container ausgeführt wird, aber der Status entweder nicht richtig angezeigt oder vom Agent nicht übernommen wurde und seit über 30 Minuten keine Antwort erfolgt ist, lautet der Status *Unbekannt*. Zusätzliche Informationen zum Statussymbol finden Sie in der folgenden Tabelle.|
| Min.&nbsp;%, Mittelw.&nbsp;%, 50.&nbsp;%, 90.&nbsp;%, 95.&nbsp;%, Max.&nbsp;%| Durchschnittliches Rollup des durchschnittlichen Prozentsatzes jeder Entität für die ausgewählte Metrik und das ausgewählte Perzentil. |
| Min., Mittelw., 50., 90., 95., Max.  | Rollup der durchschnittlichen CPU-Millicore oder Speicherleistung des Containers für das ausgewählte Perzentil. Der Mittelwert wird ausgehend vom festgelegten CPU-/Speichergrenzwert für einen Pod gemessen. |
| Container | Gesamtanzahl der Container für den Controller oder Pod. |
| Neustarts | Rollup der Anzahl von Containerneustarts. |
| Betriebszeit | Stellt den Zeitraum dar, der seit dem Start eines Containers verstrichen ist. |
| Knoten | Nur für Container und Pods. Zeigt an, unter welchem Controller sich ein Element befindet. | 
| Trend Min.&nbsp;%, Mittelw.&nbsp;%, 50.&nbsp;%, 90.&nbsp;%, 95.&nbsp;%, Max.&nbsp;% | Balkendiagrammtrend, der die durchschnittliche Perzentilmetrik des Controllers anzeigt. |

Die Symbole im Statusfeld zeigen den Onlinestatus von Containern an.
 
| Symbol | Status | 
|--------|-------------|
| ![Statussymbol für bereit und ausgeführt](./media/container-insights-analyze/containers-ready-icon.png) | Wird ausgeführt (bereit)|
| ![Statussymbol für „Wartend oder angehalten“](./media/container-insights-analyze/containers-waiting-icon.png) | Wartend oder angehalten|
| ![Symbol für zuletzt gemeldeten Ausführungsstatus](./media/container-insights-analyze/containers-grey-icon.png) | Zuletzt als ausgeführt gemeldet, hat aber seit mehr als 30 Minuten nicht geantwortet|
| ![Statussymbol für erfolgreiches Anhalten/Stoppen](./media/container-insights-analyze/containers-green-icon.png) | Erfolgreich beendet oder Fehler beim Beenden|

Das Statussymbol zeigt basierend auf dem, was der Pod bereitstellt, eine Anzahl an. Es werden die beiden schlechtesten Status angezeigt. Wenn Sie den Mauszeiger über den Status bewegen, wird ein Rollupstatus aller Pods im Container angezeigt. Wenn der Status „Bereit“ nicht vorliegt, wird der Statuswert **(0)** angezeigt.

Wählen Sie im Selektor **Container** aus.

![Auswählen der Containeransicht](./media/container-insights-analyze/containers-containers-tab.png)

Hier können Sie die Leistungsintegrität Ihrer Azure Kubernetes- und Azure Container Instances-Container anzeigen. 

![Leistungsansicht des Containers \<Name>](./media/container-insights-analyze/containers-containers-view.png)

Von einem Container aus können Sie per Drilldown zu einem Pod oder Knoten navigieren, um für das entsprechende Objekt gefilterte Leistungsdaten anzuzeigen. Wählen Sie den Wert in der Spalte **Pod** oder **Knoten** für den spezifischen Container aus.

![Exemplarischer Drilldownvorgang vom Knoten zu Containern in der Leistungsansicht](./media/container-insights-analyze/drill-down-controller-node.png)

In der folgenden Tabelle sind die Informationen beschrieben, die bei der Anzeige von Containern erscheinen.

| Column | BESCHREIBUNG | 
|--------|-------------|
| NAME | Der Name des Controllers.|
| Status | Status der Container, sofern vorhanden. Zusätzliche Informationen zum Statussymbol finden Sie in der folgenden Tabelle.|
| Min.&nbsp;%, Mittelw.&nbsp;%, 50.&nbsp;%, 90.&nbsp;%, 95.&nbsp;%, Max.&nbsp;% | Rollup des durchschnittlichen Prozentsatzes jeder Entität für die ausgewählte Metrik und das ausgewählte Perzentil. |
| Min., Mittelw., 50., 90., 95., Max. | Rollup der durchschnittlichen CPU-Millicore oder Speicherleistung des Containers für das ausgewählte Perzentil. Der Mittelwert wird ausgehend vom festgelegten CPU-/Speichergrenzwert für einen Pod gemessen. |
| Pod | Container, in dem sich der Pod befindet.| 
| Knoten |  Der Knoten, in dem sich der Container befindet. | 
| Neustarts | Stellt den Zeitraum dar, der seit dem Start eines Containers verstrichen ist. |
| Betriebszeit | Stellt den Zeitraum dar, der seit dem Start oder Neustart eines Containers verstrichen ist. |
| Trend Min.&nbsp;%, Mittelw.&nbsp;%, 50.&nbsp;%, 90.&nbsp;%, 95.&nbsp;%, Max.&nbsp;% | Balkendiagrammtrend, der die durchschnittliche Perzentilmetrik des Containers in Prozent anzeigt. |

Die Symbole im Statusfeld zeigen die Onlinestatus der Pods an, die in der folgenden Tabelle beschrieben sind.
 
| Symbol | Status |  
|--------|-------------|  
| ![Statussymbol für bereit und ausgeführt](./media/container-insights-analyze/containers-ready-icon.png) | Wird ausgeführt (bereit)|  
| ![Statussymbol für „Wartend oder angehalten“](./media/container-insights-analyze/containers-waiting-icon.png) | Wartend oder angehalten|  
| ![Symbol für zuletzt gemeldeten Ausführungsstatus](./media/container-insights-analyze/containers-grey-icon.png) | Zuletzt als ausgeführt gemeldet, hat aber seit mehr als 30 Minuten nicht geantwortet|  
| ![Statussymbol für „Beendet“](./media/container-insights-analyze/containers-terminated-icon.png) | Erfolgreich beendet oder Fehler beim Beenden|  
| ![Symbol für den Status „Fehler“](./media/container-insights-analyze/containers-failed-icon.png) | Status „Fehler“ |  

## <a name="workbooks"></a>Arbeitsmappen

Arbeitsmappen kombinieren Text,  [Protokollabfragen](../log-query/query-language.md), [Metriken](../platform/data-platform-metrics.md) und Parameter zu umfassenden interaktiven Berichten. Arbeitsmappen können von anderen Teammitgliedern bearbeitet werden, die Zugriff auf dieselben Azure-Ressourcen haben.

Azure Monitor für Container enthält vier Arbeitsmappen für den Einstieg:

- **Datenträgerkapazität**: Diese Arbeitsmappe zeigt interaktive Diagramme zur Datenträgerauslastung für jeden Datenträger, der dem Knoten in einem Container bereitgestellt wird, anhand der folgenden Gesichtspunkte:

    - Prozentuale Datenträgerauslastung für alle Datenträger.
    - Freier Speicherplatz für alle Datenträger.
    - Ein Raster, in dem der Datenträger jedes Knotens, der prozentual genutzte Speicherplatz mit dem zugehörigen Trend sowie der freie Speicherplatz auf dem Datenträger (GiB) mit dem Trend (GiB) angezeigt werden. Wenn in der Tabelle eine Zeile ausgewählt wird, wird der Prozentsatz des belegten und freien Speicherplatzes auf dem Datenträger unterhalb der Zeile angezeigt (in GiB). 

- **Datenträger-E/A**: Diese Arbeitsmappe zeigt interaktive Diagramme zur Datenträgerauslastung für jeden Datenträger, der dem Knoten in einem Container bereitgestellt wird, anhand der folgenden Gesichtspunkte:

    - Zusammenfassung der Datenträger-E/A auf allen Datenträgern nach gelesenen Bytes/Sek., geschriebenen Bytes/Sek. und Trends der gelesenen und geschriebenen Bytes/Sek.
    - In acht Leistungsdiagrammen werden wichtige Leistungsindikatoren angezeigt, um Datenträger-E/A-Engpässe zu messen und zu identifizieren.

- **Kubelet**: Enthält zwei Raster, die die Hauptknoten-Betriebsstatistiken anzeigen:

    - „Übersicht nach Knotenraster“ fasst alle Vorgänge, alle Fehler und die erfolgreichen Vorgänge für jeden Knoten in Prozent und Trend zusammen.
    - „Übersicht nach Vorgangstyp“ fasst die Vorgänge für jeden Typ, alle Fehler und die erfolgreichen Vorgänge in Prozent und Trend zusammen.

- **Netzwerk:** Stellt interaktive Netzwerkauslastungs-Diagramme für die Netzwerkadapter der einzelnen Knoten und ein Raster dar, in dem die wichtigsten Leistungsindikatoren zum Messen der Leistung der Netzwerkadapter angezeigt werden.

Sie greifen auf diese Arbeitsmappen zu, indem Sie sie jeweils in der Dropdownliste **Arbeitsmappen anzeigen** auswählen.

![Dropdownliste „Arbeitsmappen anzeigen“](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen von Warnungen für hohe CPU- und Arbeitsspeicherauslastung zur Unterstützung Ihrer DevOps oder Betriebsprozesse und -verfahren finden Sie unter [Erstellen von Leistungswarnungen mit Azure Monitor für Container](container-insights-alerts.md).
- Sehen Sie sich die [Beispiele zu Protokollabfragen](container-insights-log-search.md#search-logs-to-analyze-data) an, die auch vordefinierte Abfragen enthalten. Mit diesen Materialien können Sie Auswertungen bzw. Anpassungen für Warnungen, Visualisierungen und Analysen von Clustern vornehmen.
