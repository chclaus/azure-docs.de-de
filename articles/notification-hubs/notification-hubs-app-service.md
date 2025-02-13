---
title: Integration in Mobile App Service-Apps
description: Erfahren Sie, wie Azure Notification Hubs mit Azure App Service Mobile-Apps zusammenarbeitet.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d6747193b8c82119e45a24e3e4bffc065db14e51
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212158"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integration in Mobile App Service-Apps

> [!NOTE]
> Microsoft ist bestrebt, Azure App Service Mobile-Apps in vollem Umfang zu unterstützen, einschließlich Unterstützung für die neueste Betriebssystemversion, Programmfehlerbehebungen, Verbesserungen der Dokumentation und Community-PR-Bewertungen. Bitte beachten Sie, dass das Produktteam aktuell nicht mit Arbeit an neuen Features für Azure Mobile Apps befasst ist. Wir schätzen Communitybeiträge zu allen Gebieten von Azure Mobile Apps sehr.

Um eine nahtlose und einheitliche Verwendung in Azure-Diensten zu ermöglichen, ist in [Mobile App Service-Apps](../app-service-mobile/app-service-mobile-value-prop.md) die Unterstützung von Pushbenachrichtigungen, die über Notification Hubs abgewickelt werden, bereits integriert. [Mobile App Service-Apps](../app-service-mobile/app-service-mobile-value-prop.md) bietet eine hochgradig skalierbare, global verfügbare Entwicklungsplattform für mobile Anwendungen für Unternehmensentwickler und Systemintegratoren, die umfassende Funktionen für mobile Entwickler bereitstellt.

Mobile Apps-Entwickler können Notification Hubs mit dem folgenden Workflow verwenden:

1. Abrufen des PNS-Gerätehandle
2. Registrieren des Geräts bei Notification Hubs über die praktische Registrierungs-API der Mobile Apps-Client-SDKs

    > [!NOTE]
    > Beachten Sie, dass Mobile Apps aus Sicherheitsgründen alle Tags für Registrierungen entfernt. Arbeiten Sie direkt von Ihrem Back-End aus mit Notification Hubs, um Tags Geräten zuzuordnen.

3. Senden von Benachrichtigungen von Ihrem App-Back-End mit Notification Hubs

Einige Vorteile, die sich aus dieser Integration für Entwickler ergeben:

- **Mobile Apps-Client-SDKs**:  Diese plattformübergreifenden SDKs bieten einfache APIs für die Registrierung und die Kommunikation mit dem Notification Hub, der automatisch mit der mobilen App verknüpft wird. Entwickler müssen benötigen keine Notification Hubs-Anmeldeinformationen und müssen keinen zusätzlichen Dienst verwenden.
  - *Push an Benutzer:* Die SDKs markieren automatisch das angegebene Gerät mit einer bei Mobile Apps authentifizierten Benutzer-ID, um das Push-an-Benutzer-Szenario zu ermöglichen.
  - *Push an Gerät:* Die SDKs verwenden die Installations-ID von Mobile Apps automatisch als GUID für die Registrierung bei Notification Hubs, sodass Entwickler nicht mehrere Dienst-GUIDs verwalten müssen.
- **Installationsmodell:**  Mobile Apps verwendet das neueste Notification Hubs-Pushmodell zur Darstellung aller einem Gerät zugeordneten Pusheigenschaften in einer JSON-Installation, das auf Push Notification Services ausgerichtet ist und sich einfach verwenden lässt.
- **Flexibilität:**  Entwickler können auch bei eingerichteter Integration immer mit Notification Hubs direkt arbeiten.
- **Integration in das [Azure-Portal](https://portal.azure.com):**  Push als Funktion ist in Mobile Apps visuell dargestellt, sodass Entwickler problemlos mit dem zugeordneten Notification Hub über Mobile Apps arbeiten können.
