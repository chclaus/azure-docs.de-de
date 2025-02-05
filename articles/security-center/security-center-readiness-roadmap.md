---
title: Roadmap für Azure Security Center-Bereitschaft | Microsoft-Dokumentation
description: Dieses Dokument enthält eine Roadmap für die Bereitschaft zur Nutzung von Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: b2b4dc1aa9486e432fb2fae48343c21fba620221
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996696"
---
# <a name="azure-security-center-readiness-roadmap"></a>Roadmap für Azure Security Center-Bereitschaft
Dieses Dokument enthält eine Roadmap für die Bereitschaft, die Ihnen als Hilfe bei den ersten Schritten mit Azure Security Center dient.

## <a name="understanding-security-center"></a>Grundlegendes zu Security Center
Azure Security Center ermöglicht eine einheitliche Sicherheitsverwaltung und erweiterten Schutz vor Bedrohungen für Workloads, die in Azure, lokal und in anderen Clouds ausgeführt werden. 

Verwenden Sie für die ersten Schritte mit Security Center die folgenden Ressourcen:

Artikel
* [Einführung in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Schnellstarthandbuch zu Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

Videos
* [Kurzes Einführungsvideo](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Overview of Security Center Prevention, Detection and Response Capabilities](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/) (Übersicht über Präventions-, Erkennungs- und Antwortfunktionen für Security Center)

## <a name="planning-and-operations"></a>Planung und Betrieb
Zur optimalen Nutzung von Security Center sollten Sie verstanden haben, wie der Dienst von den verschiedenen Personen oder Teams in Ihrer Organisation genutzt wird, um die Anforderungen in Bezug auf sicheren Betrieb, Überwachung, Governance und Reaktionen auf Vorfälle zu erfüllen.

Verwenden Sie die folgenden Ressourcen als Hilfe bei der Planung und beim Betrieb.


Artikel
* [Planungs- und Betriebshandbuch für Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

Video
* [Hybrid cloud workload protection with Security Center](https://mva.microsoft.com/training-courses/hybrid-cloud-workload-protection-with-azure-security-center-18173?l=X4WqTA3jE_1106218965) (Hybrid Cloud-Workloadschutz mit Security Center)

### <a name="onboarding-computers-to-security-center"></a>Onboarding von Computern für Security Center
Security Center erkennt automatisch alle Azure-Abonnements oder -Arbeitsbereiche, für die Security Center Standard nicht aktiviert ist. Dazu zählen unter anderem Azure-Abonnements mit Security Center Free sowie Arbeitsbereiche, für die die Sicherheitslösung nicht aktiviert ist.

Verwenden Sie die folgenden Ressourcen als Hilfe während des Onboarding-Prozesses.

Artikel
* [Integrieren von Azure Security Center Standard für erhöhte Sicherheit](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Video
* [Azure Security Center Hybrid – Übersicht](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Beheben von Sicherheitsproblemen mit Security Center
Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von verbundenen Partnerlösungen, z.B. Lösungen zum Schutz von Firewalls und Endpunkten, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren.

Verwenden Sie die folgenden Ressourcen als Hilfe beim Verwalten von Sicherheitswarnungen und Schützen Ihrer Ressourcen.

Artikel    
* [Überwachen der Sicherheitsintegrität in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Schützen von Computern und Anwendungen im Azure Security Center](security-center-virtual-machine-protection.md)
* [Schützen Ihres Netzwerks in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Schützen des Azure SQL-Diensts und Ihrer Daten in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Video   
* [Beheben von Sicherheitsproblemen mit Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Verwenden von Security Center zum Reagieren auf Vorfälle
Zur Reduzierung von Kosten und Schäden ist es wichtig, dass ein Plan zur Reaktion auf Vorfälle vorhanden ist, bevor es zu einem Angriff kommt. Sie können Azure Security Center in unterschiedlichen Phasen einer Reaktion auf Vorfälle nutzen.

Verwenden Sie die folgenden Ressourcen, um sich darüber zu informieren, wie Security Center in Ihren Prozess zur Reaktion auf Vorfälle integriert werden kann.

Videos  
* [Azure Security Center in Incident Response](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response) (Azure Security Center zur Reaktion auf Vorfälle)
* [Respond quickly to threats with next-generation security operation, and investigation](https://youtu.be/e8iFCz5RM4g) (Schnelles Reagieren auf Bedrohungen mit Sicherheitsabläufen und -untersuchungen der nächsten Generation)

Artikel    
* [Verwenden von Azure Security Center zur Reaktion auf Vorfälle](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Sicherheitsplaybook in Azure Security Center (Vorschauversion)](https://docs.microsoft.com/azure/security-center/security-center-playbooks)

## <a name="advanced-cloud-defense"></a>Erweiterter Cloudschutz

Für Azure-VMs können erweiterte Funktionen für die Cloudverteidigung in Security Center genutzt werden. Zu diesen Funktionen gehören auch der bedarfsabhängige Zugriff auf virtuelle Computer (VMs) und die adaptive Anwendungssteuerung.

Verwenden Sie die folgenden Ressourcen, um sich darüber zu informieren, wie Sie diese Funktionen in Security Center verwenden.

Videos  
* [Azure Security Center – Just-in-Time VM Access](https://youtu.be/UOQb2FcdQnU) (Azure Security Center – Bedarfsabhängiger VM-Zugriff)
* [Azure Security Center - Adaptive Application Controls](https://youtu.be/wWWekI1Y9ck) (Azure Security Center – Adaptive Anwendungssteuerungen)

Artikel    
* [Verwalten des Zugriffs auf virtuelle Computer mithilfe des Just-In-Time-Features](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Adaptive Anwendungssteuerungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Praktische Aktivitäten

* [Praktische Übungen zu Security Center](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Web Application Firewall (WAF) recommendation playbook in Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff) (Playbook zu Web Application Firewall (WAF) mit Empfehlungen zu Security Center)
* [Azure Security Center-Playbook: Sicherheitswarnungen](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Seite mit der Security Center-Dokumentation](https://docs.microsoft.com/azure/security-center/)
* [Seite mit der Dokumentation zur Security Center-REST-API](https://msdn.microsoft.com/library/mt704034.aspx)
* [Azure Security Center – Häufig gestellte Fragen](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/)
* [Azure-Identitätsverwaltung und Sicherheit der Zugriffssteuerung – Bewährte Methoden](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [Bewährte Methoden für die Netzwerksicherheit](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [PaaS-Empfehlungen](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/due-diligence-checklist)
* [Log Analytics-Kunden können jetzt ihre Hybrid Cloud-Workloads mit Azure Security Center schützen](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Communityressourcen

* [Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Security Center-Communityforum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



