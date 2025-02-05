---
title: Gespeicherte Prozeduren für Azure Database for MySQL
description: In diesem Artikel werden gespeicherte Prozeduren speziell für Azure Database for MySQL erläutert.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/19/2019
ms.openlocfilehash: f01a0bf68e510133058dc0075f27cfcf6241c7a8
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71156548"
---
# <a name="azure-database-for-mysql-stored-procedures"></a>Gespeicherte Prozeduren für Azure Database for MySQL

Gespeicherte Prozeduren sind auf Azure Database for MySQL-Servern verfügbar, um die Verwaltung Ihres MySQL-Servers zu erleichtern. Dies schließt die Verwaltung der Verbindungen und Abfragen Ihres Servers sowie das Einrichten von Datenreplikation ein.  

## <a name="data-in-replication-stored-procedures"></a>Gespeicherte Prozeduren für die Datenreplikation

Mithilfe der Replikation eingehender Daten können Sie Daten von einem MySQL-Server, der lokal, auf virtuellen Computern oder von Datenbankdiensten ausgeführt wird, die von anderen Cloudanbietern gehostet werden, mit dem Azure Database for MySQL-Dienst synchronisieren.

Mit den folgenden gespeicherten Prozeduren wird die Datenreplikation zwischen einem Master und einem Replikat eingerichtet oder entfernt.

|**Name der gespeicherten Prozedur**|**Eingabeparameter**|**Ausgabeparameter**|**Hinweis zur Verwendung**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|–|Um Daten im SSL-Modus zu übertragen, übergeben Sie im Parameter „master_ssl_ca“ den Kontext des Zertifizierungsstellenzertifikats. </br><br>Um Daten ohne SSL zu übertragen, übergeben Sie im Parameter „master_ssl_ca“ eine leere Zeichenfolge.|
|*mysql.az_replication _start*|–|–|Startet die Replikation.|
|*mysql.az_replication _stop*|–|–|Beendet die Replikation.|
|*mysql.az_replication _remove_master*|–|–|Entfernt die Replikationsbeziehung zwischen dem Master und dem Replikat|
|*mysql.az_replication_skip_counter*|–|–|Überspringt einen Replikationsfehler.|

Informationen zum Einrichten der Datenreplikation zwischen einem Master und einem Replikat in Azure Database for MySQL finden Sie unter [Konfigurieren der Datenreplikation](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Andere gespeicherte Prozeduren

Die folgenden gespeicherten Prozeduren sind in Azure Database for MySQL zum Verwalten Ihres Servers verfügbar.

|**Name der gespeicherten Prozedur**|**Eingabeparameter**|**Ausgabeparameter**|**Hinweis zur Verwendung**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|–|Entspricht dem Befehl [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Beendet die der angegebenen „processlist_id“ zugeordnete Verbindung nach dem Beenden einer beliebigen Anweisung, die von der Verbindung ausgeführt wird.|
|*mysql.az_kill_query*|processlist_id|–|Entspricht dem Befehl [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Beendet die Anweisung, die derzeit von der Verbindung ausgeführt wird. Die Verbindung bleibt unverändert erhalten.|
|*mysql.az_load_timezone*|–|–|Lädt Zeitzonentabellen, damit der `time_zone`-Parameter auf benannte Werte festgelegt werden kann (z.B. „USA/Pazifik“).|

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie die [Datenreplikation einrichten](howto-data-in-replication.md).
- Erfahren Sie, wie Sie die [Zeitzonentabellen](howto-server-parameters.md#working-with-the-time-zone-parameter) verwenden.