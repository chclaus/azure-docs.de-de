---
title: Anfordern benutzerdefinierter Ansprüche mithilfe von MSAL für iOS und macOS | Microsoft Identity Platform
description: Hier erfahren Sie, wie Sie benutzerdefinierte Ansprüche anfordern.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e09d58742bffd74f07f79b3ec55c1e81533632
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269364"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Gewusst wie: Anfordern benutzerdefinierter Ansprüche mithilfe von MSAL für iOS und macOS

Mit OpenID Connect können Sie optional die Rückgabe einzelner Ansprüche vom UserInfo-Endpunkt und/oder im ID-Token anfordern. Eine claims-Anforderung wird als JSON-Objekt dargestellt, das eine Liste der angeforderten Ansprüche enthält. Weitere Informationen finden Sie unter [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter).

Mit der Microsoft Authentication Library (MSAL) für iOS und macOS können Sie spezifische Ansprüche sowohl in interaktiven als auch in automatischen Tokenabrufszenarios anfordern. Verwenden Sie hierfür den Parameter `claimsRequest`.

Dieser Parameter ist für mehrere Szenarios erforderlich. Beispiel:

- Zum Anfordern von Ansprüchen, die nicht als Standardansprüche für Ihre Anwendung festgelegt sind.
- Zum Anfordern von bestimmten Kombinationen der Standardansprüche, die nicht mithilfe von Bereichen für Ihre Anwendung angegeben werden können. Wird ein Zugriffstoken beispielsweise aufgrund fehlender Ansprüche abgelehnt, kann die Anwendung die fehlenden Ansprüche mithilfe von MSAL anfordern.

> [!NOTE]
> MSAL umgeht den Zugriffstokencache, sobald eine claims-Anforderung angegeben wird. Der Parameter `claimsRequest` darf nur bereitgestellt werden, wenn zusätzliche Ansprüche benötigt werden (und nicht bei jedem Aufruf durch die MSAL-API).

`claimsRequest` kann in `MSALSilentTokenParameters` und `MSALInteractiveTokenParameters` angegeben werden:

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest` kann aus der NSString-Darstellung einer claims-Anforderung im JSON-Format erstellt werden. 

Objective-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Der Parameter kann auch durch die Anforderung von zusätzlichen spezifischen Ansprüchen geändert werden:

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest` sollte in diesem Fall in den Tokenparametern festgelegt und in einer der MSAL-Tokenabruf-APIs bereitgestellt werden:

Objective-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr zu [Authentifizierungsflows und Anwendungsszenarios](authentication-flows-app-scenarios.md).
