---
title: Sicherheitsempfehlungen für Azure Key Vault
description: Sicherheitsempfehlungen für Azure Key Vault Die Umsetzung dieser Anleitung erleichtert es Ihnen, Ihre Sicherheitspflichten zu erfüllen, die in unserem Modell der gemeinsamen Verantwortung beschrieben werden.
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 09/30/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: deefbf049d5f8daa004db942f60ff5e31bda0fe8
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695410"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Sicherheitsempfehlungen für Azure Key Vault

Dieser Artikel enthält Sicherheitsempfehlungen für Azure Key Vault. Die Umsetzung dieser Empfehlungen erleichtert es Ihnen, Ihre Sicherheitspflichten zu erfüllen, die in unserem Modell der gemeinsamen Verantwortung beschrieben werden. Weitere Informationen dazu, wie Microsoft seiner Verantwortung als Dienstanbieter nachkommt, finden Sie unter [Gemeinsame Verantwortung für das Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Einige der in diesem Artikel enthaltenen Empfehlungen können automatisch durch Azure Security Center überwacht werden. Azure Security Center ist die erste Verteidigungslinie zum Schutz Ihrer Ressourcen in Azure. Der Dienst analysiert regelmäßig den Sicherheitsstatus Ihrer Azure-Ressourcen, um potenzielle Sicherheitsrisiken zu erkennen. Anschließend erhalten Sie Empfehlungen dazu, wie damit umgegangen werden kann.

- Weitere Informationen zu Empfehlungen von Azure Security Center finden Sie unter [Sicherheitsempfehlungen in Azure Security Center](../security-center/security-center-recommendations.md).
- Weitere Informationen zu Azure Security Center finden Sie unter [Was ist Azure Security Center?](../security-center/security-center-intro.md)

## <a name="data-protection"></a>Datenschutz

| Empfehlung | Kommentare | Security Center |
|-|----|--|
|Aktivieren von „Vorläufiges Löschen“ | Durch das [vorläufige Löschen](key-vault-ovw-soft-delete.md) haben Sie die Möglichkeit, gelöschte Tresore und Tresorobjekte wiederherzustellen. |  - |
| Beschränken des Zugriffs auf Tresordaten  | Befolgen Sie das Prinzip der geringsten Rechte und schränken Sie ein, welche Mitglieder Ihrer Organisation Zugriff auf Tresordaten haben. |  - |

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Empfehlung | Kommentare | Security Center |
|-|----|--|
| Beschränken der Anzahl von Benutzern mit Zugriff für Mitwirkende | Falls ein Benutzer für eine Key Vault-Verwaltungsebene über Mitwirkungsberechtigungen verfügt, kann er sich durch Festlegen einer Key Vault-Zugriffsrichtlinie selbst Zugriff auf die Datenebene gewähren. Sie sollten stets kontrollieren, wer als Mitwirkender auf Ihre Schlüsseltresore zugreifen kann. Stellen Sie sicher, dass nur Personen mit einer erforderlichen Zugriffsberechtigung auf Ihre Tresore zugreifen und diese verwalten können. Weitere Informationen finden Sie unter [Sicherer Zugriff auf einen Schlüsseltresor](key-vault-secure-your-key-vault.md). | - |

## <a name="monitoring"></a>Überwachung

| Empfehlung | Kommentare | Security Center |
|-|----|--|
 Diagnoseprotokolle in Key Vault sollten aktiviert sein. | Aktivieren Sie Protokolle, und bewahren Sie sie bis zu ein Jahr lang auf. Auf diese Weise können Sie vergangene Aktivitäten nachvollziehen, wenn Sie Sicherheitsincidents untersuchen oder Ihr Netzwerk gefährdet ist. | [Ja](../security-center/security-center-identity-access.md) |
| Einschränken des Zugriffs auf Ihre Azure Key Vault-Protokolle | [Key Vault-Protokolle](key-vault-logging.md) speichern Informationen zu den in Ihrem Tresor ausgeführten Aktivitäten, z. B. zum Erstellen oder Löschen von Tresoren, Schlüsseln, Geheimnissen, die während einer Untersuchung verwendet werden können. |  - |

## <a name="networking"></a>Netzwerk

| Empfehlung | Kommentare | Security Center |
|-|----|--|
|Einschränken der Offenlegung im Netzwerk | Der Netzwerkzugriff sollte auf die virtuellen Netzwerke beschränkt sein, die von Lösungen verwendet werden, die Zugriff auf den Tresor erfordern. Überprüfen Sie die Informationen unter [VNET-Dienstendpunkte für Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). | - |

## <a name="next-steps"></a>Nächste Schritte

Erkundigen Sie sich bei Ihrem Anwendungsanbieter, ob weitere Sicherheitsanforderungen bestehen. Weitere Informationen zum Entwickeln sicherer Anwendungen finden Sie unter [Dokumentation zur sicheren Entwicklung](../security/fundamentals/abstract-develop-secure-apps.md).
