---
title: Daemon-App, die Web-APIs aufruft (Übergang in die Produktion) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Daemon-App erstellen, die Web-APIs aufruft (Übergang in die Produktion).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c509e061c43c81f72682fb428529a8e72b34066a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056320"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon-App, die Web-APIs aufruft (Übergang in die Produktion)

Da Sie nun wissen, wie Sie ein Token für einen Dienst-zu-Dienst-Aufruf abrufen und verwenden, erfahren Sie im Folgenden, wie Sie Ihre App in die Produktion bringen.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Bereitstellung: Daemon-Apps mit mehreren Mandanten

Wenn Sie ein unabhängiger Softwarehersteller sind, der eine Daemon-App erstellt, die in mehreren Mandanten ausgeführt werden kann, müssen Sie sicherstellen, dass die Mandantenadministratoren folgende Anforderungen erfüllen:

- Sie müssen einen Dienstprinzipal für die Anwendung bereitstellen
- Sie müssen der Anwendung Einwilligung gewähren

Sie müssen Ihren Kunden erklären, wie diese Vorgänge durchgeführt werden. Weitere Informationen finden Sie im Abschnitt zum [Anfordern der Zustimmung für einen gesamten Mandanten](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nächste Schritte

Im Folgenden finden Sie Links zu weiteren Informationen:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- Sehen Sie sich den [Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über eine Konsolen-App anhand der Identität der App](./quickstart-v2-netcore-daemon.md) an, sofern Sie es noch nicht getan haben.
- In der Dokumentation finden Sie Informationen zum:
  - Instanziieren von [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Aufrufen von [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Weitere Beispiele und Tutorials:
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) umfasst eine einfache .NET Core-Daemon-Konsolenanwendung, die die Benutzer eines Mandanten anzeigt, der Microsoft Graph abfragt.

    ![Topologie](media/scenario-daemon-app/daemon-app-sample.svg)

    Dasselbe Beispiel veranschaulicht die Variation der Zertifikate.

    ![Topologie](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) umfasst eine ASP.NET MVC-Webanwendung, die Daten von Microsoft Graph mithilfe der Identität der Anwendung (statt im Auftrag eines Benutzers) synchronisiert. Das Beispiel veranschaulicht auch den Prozess für die Administratoreinwilligung.

    ![Topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL Python befindet sich derzeit in der Public Preview.
Weitere Informationen finden Sie in den [Beispielen im MSAL Python-Repository](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample).

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j (MSAL.Java) ist derzeit als öffentliche Vorschauversion (Public Preview) verfügbar. Weitere Informationen finden Sie in den [Beispielen im MSAL Java-Repository](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
