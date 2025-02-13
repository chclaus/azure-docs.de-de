---
title: Gespeicherte Prozeduren in Azure Database for MariaDB
description: In diesem Artikel werden gespeicherte Prozeduren speziell für Azure Database for MariaDB erläutert.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: d9daaf619a19c0f4e4a591d4bbb4925679fd1fcb
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174565"
---
# <a name="azure-database-for-mariadb-stored-procedures"></a>Gespeicherte Prozeduren in Azure Database for MariaDB

Gespeicherte Prozeduren sind auf Azure Database for MariaDB-Servern verfügbar, um Sie beim Verwalten des MariaDB-Servers unterstützen. Dies schließt die Verwaltung der Verbindungen und Abfragen Ihres Servers sowie das Einrichten der Datenreplikation ein.  

## <a name="data-in-replication-stored-procedures"></a>Gespeicherte Prozeduren für die Datenreplikation

Mithilfe der Datenreplikation können Sie Daten von einem MariaDB-Server, der lokal, auf virtuellen Computern oder von Datenbankdiensten ausgeführt wird, die von anderen Cloudanbietern gehostet werden, mit dem Azure Database for MariaDB-Dienst synchronisieren.

Mit den folgenden gespeicherten Prozeduren wird die Datenreplikation zwischen einem Master und einem Replikat eingerichtet oder entfernt.

|**Name der gespeicherten Prozedur**|**Eingabeparameter**|**Ausgabeparameter**|**Hinweis zur Verwendung**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|–|Um Daten im SSL-Modus zu übertragen, übergeben Sie im Parameter „master_ssl_ca“ den Kontext des Zertifizierungsstellenzertifikats. </br><br>Um Daten ohne SSL zu übertragen, übergeben Sie im Parameter „master_ssl_ca“ eine leere Zeichenfolge.|
|*mysql.az_replication _start*|–|–|Startet die Replikation.|
|*mysql.az_replication _stop*|–|–|Beendet die Replikation.|
|*mysql.az_replication _remove_master*|–|–|Entfernt die Replikationsbeziehung zwischen dem Master und dem Replikat|
|*mysql.az_replication_skip_counter*|–|–|Überspringt einen Replikationsfehler.|

Informationen zum Einrichten der Datenreplikation zwischen einem Master und einem Replikat in Azure Database for MariaDB finden Sie unter [Konfigurieren der Replikation eingehender Daten](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Andere gespeicherte Prozeduren

Die folgenden gespeicherten Prozeduren sind in Azure Database for MariaDB zum Verwalten Ihres Servers verfügbar.

|**Name der gespeicherten Prozedur**|**Eingabeparameter**|**Ausgabeparameter**|**Hinweis zur Verwendung**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|–|Entspricht dem Befehl [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Beendet die der angegebenen „processlist_id“ zugeordnete Verbindung nach dem Beenden einer beliebigen Anweisung, die von der Verbindung ausgeführt wird.|
|*mysql.az_kill_query*|processlist_id|–|Entspricht dem Befehl [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Beendet die Anweisung, die von der Verbindung aktuell ausgeführt wird. Die Verbindung bleibt unverändert erhalten.|
|*mysql.az_load_timezone*|–|–|Lädt Zeitzonentabellen, damit der `time_zone`-Parameter auf benannte Werten (z.B. „USA/Pazifik“) festgelegt werden kann.|

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie die [Datenreplikation](howto-data-in-replication.md) einrichten.
- Erfahren Sie, wie Sie die [Zeitzonentabellen](howto-server-parameters.md#working-with-the-time-zone-parameter) verwenden.