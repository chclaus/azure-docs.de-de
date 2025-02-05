---
title: Verringern der Kosten für Azure Databricks durch Vorauszahlung
description: Hier erfahren Sie, wie Sie für Azure Databricks-Gebühren mit reservierter Kapazität vorauszahlen und so Geld sparen können.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 5ceef76bd5a5f6a2904d3619f310020e90cbec0e
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719693"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Verringern der Kosten für Azure Databricks durch Vorauszahlung

Wenn Sie Azure Databricks-Commit-Einheiten (Azure Databricks Commit Units, DBCUs) für ein oder drei Jahre vorab erwerben, können Sie DBU-Kosten (Azure Databricks Units) sparen. Sie können die vorab erworbenen DBCUs jederzeit während der Laufzeit des Kaufs nutzen. Im Gegensatz zu virtuellen Computern laufen die vorab erworbenen Einheiten nicht stündlich ab, und Sie können sie zu einem beliebigen Zeitpunkt während der Laufzeit des Kaufs nutzen.

Jede Azure Databricks-Nutzung wird automatisch auf die vorab erworbenen DBUs angerechnet. Um die Vorauszahlungsrabatte zu erhalten, ist es nicht erforderlich, für die DBU-Nutzung einen Plan mit Vorauszahlung erneut bereitzustellen oder Ihren Azure Databricks-Arbeitsbereichen zuzuweisen.

Der Vorauszahlungsrabatt gilt nur für die DBU-Nutzung. Andere Gebühren, z. B. Compute-, Speicher- und Netzwerkgebühren, werden separat abgerechnet.

## <a name="determine-the-right-size-to-buy"></a>Bestimmen der richtigen Größe für den Kauf

Databricks mit Vorauszahlung gilt für alle Databricks-Workloads und -Tarife. Sie können den Plan mit Vorauszahlung als Pool im Voraus bezahlter Databricks-Commit-Einheiten vorstellen. Die Nutzung wird unabhängig von der Arbeitsauslastung und dem Tarif auf den Pool angerechnet. Für die Anrechnung der Nutzung gilt das folgende Verhältnis:

| **Workload** | **Verhältnis der Anwendung von DBUs – Standard-Tarif** | **Verhältnis der Anwendung von DBUs – Premium-Tarif** |
| --- | --- | --- |
| Datenanalysen | 0,4 | 0,55 |
| Datentechnik | 0,15 | 0,30 |
| Datentechnik Light | 0,07 | 0,22 |

Wenn z. B. ein Betrag von Datenanalysen – Standard-Tarif genutzt wird, werden auf die vorab erworbenen Databricks-Commit-Einheiten 0,4 Einheiten angerechnet.

Berechnen Sie vor dem Kauf die DBU-Gesamtmenge, die für unterschiedliche Workloads und Tarife verwendet wird. Normalisieren Sie die DBCU-Menge anhand der oben genannten Verhältnisse, und führen Sie dann eine Projektion der Gesamtnutzung über die nächsten ein oder drei Jahre aus.

## <a name="purchase-databricks-commit-units"></a>Erwerben von Databricks-Commit-Einheiten

Sie können Databricks-Pläne im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D) erwerben. Zum Erwerben reservierter Kapazität müssen Sie über die Besitzerrolle für mindestens ein Enterprise-Abonnement verfügen.

- Der Voraberwerb ist derzeit nur für Kunden mit Enterprise Agreement verfügbar.
- Ihnen muss die Besitzerrolle für mindestens ein Enterprise-Abonnement zugeordnet sein.
- Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator des Abonnements sein.

**Durchführen des Kaufs:**

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Wählen Sie ein Abonnement aus. Wählen Sie in der Liste **Abonnement** das Abonnement aus, das für den Kauf der reservierten Kapazität verwendet wird. Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die reservierte Kapazität belastet. Die Gebühren werden vom Verpflichtungsguthaben der Reservierung abgezogen oder als Überschreitung belastet.
1. Wählen Sie einen Bereich aus. Wählen Sie in der Liste **Bereich** einen Abonnementbereich aus:
    - **Single resource group scope** (Bereich einer einzelnen Ressourcengruppe): Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an.
    - **Einzelnes Abonnement**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.
    - **Gemeinsam genutzt**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Für Kunden mit einem Enterprise Agreement ist der Abrechnungskontext die Registrierung.
1. Wählen Sie aus, wie viele Azure Databricks-Commit-Einheiten Sie erwerben möchten, und schließen Sie den Kauf ab.


![Beispiel für Azure Databricks-Kauf im Azure-Portal](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Ändern des Bereichs und Besitzers

Nach dem Kauf können Sie folgende Änderungen an einer Reservierung vornehmen:

- Reservierungsumfang aktualisieren
- Rollenbasierter Zugriff

Die vorab erworbenen Databricks-Commit-Einheiten können nicht aufgeteilt oder zusammengeführt werden. Weitere Informationen zum Verwalten von Reservierungen finden Sie unter [Verwalten von Reservierungen nach dem Kauf](billing-manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Stornierungen und Umtausch

Stornierung und Umtausch werden für vorab erworbene Databricks-Pläne nicht unterstützt. Alle Käufe sind endgültig.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
  - [Was sind Azure-Reservierungen?](billing-save-compute-costs-reservations.md)
  - [Anwenden eines Azure Databricks-Vorauszahlungsrabatts](billing-reservation-discount-databricks.md)
  - [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md)
