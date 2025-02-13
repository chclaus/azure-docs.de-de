---
title: Automatisierte SaaS-App-Benutzerbereitstellung in Azure AD | Microsoft-Dokumentation
description: Einführung in die Verwendung von Azure AD für die automatisierte Bereitstellung, Bereitstellungsaufhebung und fortlaufende Aktualisierung von Benutzerkonten für verschiedene SaaS-Drittanbieteranwendungen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac78029ba2d1f45ef67ef0d858fdd2917bd4a97a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033343"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory

Mit Azure Active Directory (Azure AD) können Sie die Erstellung, Wartung und Entfernung von Benutzeridentitäten in Cloudanwendungen ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) wie Dropbox, Salesforce, ServiceNow usw. automatisieren. Dies wird als automatisierte Benutzerbereitstellung für SaaS-Apps bezeichnet.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

Dieses Feature ermöglicht Ihnen Folgendes:

- Automatisches Erstellen neuer Konten in den richtigen Systemen für neue Benutzer, wenn diese Ihr Team oder Ihre Organisation verstärken
- Automatisches Deaktivieren von Konten in den richtigen Systemen, wenn Benutzer das Team oder die Organisation verlassen
- Sicherstellen, dass die Identitäten in Ihren Apps und Systemen bei Änderungen im Verzeichnis oder in Ihrem Personalsystem auf den neuesten Stand gebracht werden
- Bereitstellen benutzerfremder Objekte (beispielsweise Gruppen) für Anwendungen mit entsprechender Unterstützung

Die automatisierte Benutzerbereitstellung bietet außerdem die folgende Funktionalität:

- Abgleich vorhandener Identitäten zwischen Quell- und Zielsystemen.
- Anpassbare Attributzuordnungen, die definieren, welche Benutzerdaten vom Quellsystem an das Zielsystem fließen sollen.
- Optionale E-Mail-Warnungen zu Bereitstellungsfehlern.
- Berichterstellung und Aktivitätsprotokolle unterstützen Sie bei der Überwachung und Problembehandlung.

## <a name="why-use-automated-provisioning"></a>Argumente für die automatisierte Bereitstellung

Nachfolgend werden einige Gründe aufgeführt, die für die Verwendung dieser Funktion sprechen:

- Vermeiden von Kosten, Ineffizienz und Fehlern, die in Zusammenhang mit manuellen Bereitstellungsvorgängen auftreten.
- Vermeiden der Kosten im Zusammenhang mit dem Hosten und Verwalten benutzerdefiniert entwickelter Bereitstellungslösungen und Skripts.
- Schützen Ihrer Organisation, indem Benutzeridentitäten sofort aus wichtigen SaaS-Apps entfernt werden, wenn Benutzer die Organisation verlassen.
- Einfaches Importieren einer großen Anzahl von Benutzern in eine bestimmte SaaS-Anwendung oder in ein bestimmtes System.
- Vorteil eines einzelnen Richtliniensatzes für die Ermittlung, wer bereitgestellt wird und wer sich bei einer App anmelden kann.

## <a name="how-does-automatic-provisioning-work"></a>Funktionsweise der automatischen Bereitstellung

Der **Azure AD-Bereitstellungsdienst** stellt Benutzer für SaaS-Apps und andere Systeme bereit, indem er eine Verbindung mit Endpunkten der Benutzerverwaltungs-API herstellt, die von den jeweiligen Anwendungsanbietern zur Verfügung gestellt werden. Diese Endpunkte der Benutzerverwaltungs-API ermöglichen Azure AD das programmgesteuerte Erstellen, Aktualisieren und Entfernen von Benutzern. Bei bestimmten Anwendungen kann der Bereitstellungsdienst auch zusätzliche identitätsbezogene Objekte wie Gruppen und Rollen erstellen, aktualisieren und entfernen.

![Azure AD-Bereitstellungsdienst](./media/user-provisioning/provisioning0.PNG)
*Abbildung 1: Der Azure AD-Bereitstellungsdienst*

![Workflow der ausgehenden Benutzerbereitstellung](./media/user-provisioning/provisioning1.PNG)
*Abbildung 2: Workflow für die ausgehende Benutzerbereitstellung von Azure AD in beliebten SaaS-Anwendungen*

![Workflow der eingehenden Benutzerbereitstellung](./media/user-provisioning/provisioning2.PNG)
*Abbildung 3: Workflow für die eingehende Benutzerbereitstellung aus beliebten Personalverwaltungsanwendungen (Human Capital Management, HCM) in Azure Active Directory und Windows Server Active Directory*

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Welche Anwendungen und Systeme kann ich mit der automatischen Benutzerbereitstellung von Azure AD verwenden?

Azure AD unterstützt standardmäßig viele beliebte SaaS-Apps und Personalsysteme und bietet allgemeine Unterstützung für Apps, die bestimmte Teile des SCIM 2.0-Standards implementieren.

### <a name="pre-integrated-applications"></a>Vorab integrierte Anwendungen

Eine Aufstellung aller Anwendungen, für die Azure AD einen vorab integrierten Bereitstellungsconnector unterstützt, finden Sie in der [Liste mit den Anwendungstutorials zur Benutzerbereitstellung](../saas-apps/tutorial-list.md).

Wenn Sie das Azure AD-Entwicklungsteam kontaktieren möchten, um Bereitstellungsunterstützung für zusätzliche Anwendungen anzufordern, senden Sie uns eine Nachricht über das [Azure Active Directory-Feedbackforum](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Damit eine Anwendung die automatisierte Benutzerbereitstellung unterstützt, müssen zunächst die erforderlichen APIs für die Benutzerverwaltung bereitgestellt werden, die externen Programmen die Automatisierung der Erstellung, Wartung und Entfernung von Benutzern ermöglichen. Daher sind nicht alle SaaS-Apps mit diesem Feature kompatibel. Für Apps, die Benutzerverwaltungs-APIs unterstützen, kann das Azure AD-Entwicklerteam anschließend einen Bereitstellungsconnector erstellen. Diese Tätigkeit wird in Abhängigkeit von den Anforderungen bestehender und potenzieller Kunden priorisiert.

### <a name="connecting-applications-that-support-scim-20"></a>Verbinden von Anwendungen mit SCIM 2.0-Unterstützung

Informationen zum generischen Verbinden von Anwendungen, die SCIM 2.0-basierte APIs für die Benutzerverwaltung implementieren, finden Sie unter [Verwenden von SCIM (System for Cross-domain Identity Management) für die automatische Bereitstellung von Benutzern und Gruppen aus Azure Active Directory für Anwendungen](use-scim-to-provision-users-and-groups.md).

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Wie richte ich die automatische Bereitstellung für eine Anwendung ein?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Konfigurieren Sie im Azure Active Directory-Portal den Azure AD-Bereitstellungsdiensts für eine ausgewählte Anwendung.

1. Öffnen Sie das **[Azure Active Directory-Portal](https://aad.portal.azure.com)** .
1. Wählen Sie im linken Bereich die Option **Unternehmensanwendungen** aus. Daraufhin wird eine Liste aller konfigurierten Apps angezeigt.
1. Wählen Sie **+ Neue Anwendung** aus, um eine Anwendung hinzuzufügen. Fügen Sie je nach Szenario eine der folgenden Anwendungen hinzu:

   - Die Option **Eigene App hinzufügen** unterstützt benutzerdefiniert entwickelte SCIM-Integrationen.
   - Alle Anwendungen im Abschnitt **Aus Katalog hinzufügen** > **Ausgewählte Anwendungen** unterstützen die automatische Bereitstellung. Weitere Anwendungen finden Sie in der [Liste mit den Anwendungstutorials zur Benutzerbereitstellung](../saas-apps/tutorial-list.md).

1. Geben Sie die Details an, und wählen Sie **Hinzufügen** aus. Die neue App wird der Liste der Unternehmensanwendungen hinzugefügt und auf dem Anwendungsverwaltungsbildschirm geöffnet.
1. Wählen Sie **Bereitstellung** aus, um die Einstellungen für die Bereitstellung von Benutzerkonten für die App zu verwalten.

   ![Zeigt den Bildschirm für die Bereitstellungseinstellungen an](./media/user-provisioning/provisioning_settings0.PNG)

1. Wählen Sie für den **Bereitstellungsmodus** die Option „Automatisch“ aus, um die Einstellungen für Administratoranmeldeinformationen, Zuordnungen, zum Starten und Beenden und für die Synchronisierung anzugeben.

   - Erweitern Sie **Administratoranmeldeinformationen**, um die Anmeldeinformationen einzugeben, die Azure AD zum Herstellen einer Verbindung mit der Benutzerverwaltungs-API der Anwendung benötigt. In diesem Abschnitt können Sie auch E-Mail-Benachrichtigungen aktivieren, wenn für die Anmeldeinformationen ein Fehler auftritt oder der Bereitstellungsauftrag in [Quarantäne](#quarantine) versetzt wird.
   - Erweitern Sie **Zuordnungen**, um die Benutzerattribute anzuzeigen und zu bearbeiten, die beim Bereitstellen oder Aktualisieren von Benutzerkonten zwischen Azure AD und der Zielanwendung übertragen werden. Wenn die Zielanwendung dies unterstützt, können Sie in diesem Abschnitt auch optional die Bereitstellung von Gruppen und Benutzerkonten konfigurieren. Wählen Sie in der Tabelle eine Zuordnung aus, um den Zuordnungs-Editor auf der rechten Seite zu öffnen, in dem Sie Benutzerattribute anzeigen und anpassen können.

     **Bereichsfilter** teilen dem Bereitstellungsdienst mit, welche Benutzer und Gruppen aus dem Quellsystem im Zielsystem bereitgestellt werden sollen oder für welche Benutzer und Gruppen die Bereitstellung aufgehoben werden soll. Wählen Sie im Bereich **Attributzuordnung** die Option **Quellobjektbereich** aus, um nach bestimmten Attributwerten zu filtern. So können Sie beispielsweise angeben, dass nur Benutzer mit dem Abteilungsattribut „Vertrieb“ zum Geltungsbereich für die Bereitstellung gehören sollen. Weitere Informationen finden Sie unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](define-conditional-rules-for-provisioning-user-accounts.md).

     Weitere Informationen finden Sie unter [Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](customize-application-attributes.md).

   - **Einstellungen** steuern den Betrieb des Bereitstellungsdiensts für eine Anwendung (unter anderem, ob er derzeit ausgeführt wird). Über das Menü **Bereich** können Sie angeben, ob nur zugewiesene Benutzer und Gruppen zum Bereich für die Bereitstellung gehören oder ob alle Benutzer im Azure AD-Verzeichnis bereitgestellt werden sollen. Informationen zum Zuweisen von Benutzern und Gruppen finden Sie unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App in Azure Active Directory](assign-user-or-group-access-portal.md).

Wählen Sie auf dem Bildschirm für die App-Verwaltung die Option **Provisioning logs (preview)** (Bereitstellungsprotokolle (Vorschau)) aus, um die Datensätze aller vom Azure AD-Bereitstellungsdienst ausgeführten Vorgänge anzuzeigen. Weitere Informationen finden Sie in der [Anleitung zur Erstellung von Bereitstellungsberichten](check-status-user-account-provisioning.md).

![Beispiel: Bildschirm mit Bereitstellungsprotokollen für eine App](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Der Benutzerbereitstellungsdienst von Azure AD kann auch über die [Microsoft Graph-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) konfiguriert und verwaltet werden.

## <a name="what-happens-during-provisioning"></a>Vorgänge während der Bereitstellung

Wenn Azure AD das Quellsystem ist, verwendet der Bereitstellungsdienst das [Feature „Differenzielle Abfragen“ der Azure AD Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query), um Benutzer und Gruppen zu überwachen. Der Bereitstellungsdienst führt einen ersten Zyklus für das Quellsystem und das Zielsystem aus, gefolgt von regelmäßigen inkrementellen Zyklen.

### <a name="initial-cycle"></a>Erster Zyklus

Nachdem der Bereitstellungsdienst gestartet wurde, umfasst die erste jemals ausgeführte Synchronisierung die folgenden Vorgänge:

1. Abfragen aller Benutzer und Gruppen aus dem Quellsystem und Abrufen aller Attribute, die in den [Attributzuordnungen](customize-application-attributes.md) definiert sind.
1. Filtern der zurückgegebenen Benutzer und Gruppen, indem alle konfigurierten [Zuweisungen](assign-user-or-group-access-portal.md) oder [attributbasierten Bereichsfilter](define-conditional-rules-for-provisioning-user-accounts.md) verwendet werden.
1. Wenn ein Benutzer zugewiesen ist oder in den Bereich für die Bereitstellung fällt, fragt der Dienst das Zielsystem anhand der angegebenen [Zuordnungsattribute](customize-application-attributes.md#understanding-attribute-mapping-properties) nach einem übereinstimmenden Benutzer ab. Beispiel: Wenn der userPrincipal-Name auf dem Quellsystem das übereinstimmende Attribut ist und dem userName auf dem Zielsystem zugeordnet ist, fragt der Bereitstellungsdienst beim Zielsystem userNames ab, die mit den Werten von userPrincipal-Namen auf dem Quellsystem übereinstimmen.
1. Wenn auf dem Zielsystem kein übereinstimmender Benutzer gefunden werden kann, wird ein Benutzer mit den vom Quellsystem zurückgegebenen Attributen erstellt. Nach der Erstellung des Benutzerkontos wird die ID des Zielsystems für den neuen Benutzer vom Bereitstellungsdienst erkannt und zwischengespeichert. Diese ID wird zum Ausführen aller zukünftigen Vorgänge für diesen Benutzer verwendet.
1. Wenn ein übereinstimmender Benutzer gefunden wird, erfolgt eine Aktualisierung mit den vom Quellsystem bereitgestellten Attributen. Nach der Zuordnung des Benutzerkontos wird die ID des Zielsystems für den neuen Benutzer vom Bereitstellungsdienst erkannt und zwischengespeichert. Diese ID wird zum Ausführen aller zukünftigen Vorgänge für diesen Benutzer verwendet.
1. Wenn die Attributzuordnungen „Referenzattribute“ enthalten, führt der Dienst auf dem Zielsystem zusätzliche Aktualisierungen aus, um die referenzierten Objekte zu erstellen und zu verknüpfen. Ein Benutzer kann auf dem Zielsystem beispielsweise über das Attribut „Manager“ verfügen, das mit einem anderen Benutzer verknüpft ist, der im Zielsystem erstellt wurde.
1. Legen Sie am Ende des ersten Zyklus einen Grenzwert fest, der als Startpunkt für die nachfolgenden inkrementellen Zyklen dient.

Einige Anwendungen, z. B. ServiceNow, G Suite und Box, unterstützen nicht nur die Bereitstellung von Benutzern, sondern auch die Bereitstellung von Gruppen und ihrer Mitglieder. Wenn die Gruppenbereitstellung in den [Zuordnungen](customize-application-attributes.md) aktiviert ist, synchronisiert der Bereitstellungsdienst die Benutzer und Gruppen und später dann die Gruppenmitgliedschaften.

### <a name="incremental-cycles"></a>Inkrementelle Zyklen

Nach dem ersten Zyklus führen alle anderen Zyklen folgende Aktionen aus:

1. Fragen Sie vom Quellsystem alle Benutzer und Gruppen ab, die aktualisiert wurden, seitdem der letzte Grenzwert gespeichert wurde.
1. Filtern der zurückgegebenen Benutzer und Gruppen, indem alle konfigurierten [Zuweisungen](assign-user-or-group-access-portal.md) oder [attributbasierten Bereichsfilter](define-conditional-rules-for-provisioning-user-accounts.md) verwendet werden.
1. Wenn ein Benutzer zugewiesen ist oder in den Bereich für die Bereitstellung fällt, fragt der Dienst das Zielsystem anhand der angegebenen [Zuordnungsattribute](customize-application-attributes.md#understanding-attribute-mapping-properties) nach einem übereinstimmenden Benutzer ab.
1. Wenn auf dem Zielsystem kein übereinstimmender Benutzer gefunden werden kann, wird ein Benutzer mit den vom Quellsystem zurückgegebenen Attributen erstellt. Nach der Erstellung des Benutzerkontos wird die ID des Zielsystems für den neuen Benutzer vom Bereitstellungsdienst erkannt und zwischengespeichert. Diese ID wird zum Ausführen aller zukünftigen Vorgänge für diesen Benutzer verwendet.
1. Wenn ein übereinstimmender Benutzer gefunden wird, erfolgt eine Aktualisierung mit den vom Quellsystem bereitgestellten Attributen. Wenn es sich um ein neu zugewiesenes Konto handelt, für das die Zuordnung erfolgt, wird die ID des Zielsystems für den neuen Benutzer vom Bereitstellungsdienst erkannt und zwischengespeichert. Diese ID wird zum Ausführen aller zukünftigen Vorgänge für diesen Benutzer verwendet.
1. Wenn die Attributzuordnungen „Referenzattribute“ enthalten, führt der Dienst auf dem Zielsystem zusätzliche Aktualisierungen aus, um die referenzierten Objekte zu erstellen und zu verknüpfen. Ein Benutzer kann auf dem Zielsystem beispielsweise über das Attribut „Manager“ verfügen, das mit einem anderen Benutzer verknüpft ist, der im Zielsystem erstellt wurde.
1. Wenn ein Benutzer, der sich zuvor im Bereich für die Bereitstellung befunden hat, aus dem Bereich entfernt (und die Zuweisung aufgehoben) wird, deaktiviert der Dienst den Benutzer im Zielsystem per Update.
1. Wenn ein Benutzer, der sich zuvor im Bereich für die Bereitstellung befunden hat, im Quellsystem deaktiviert oder vorläufig gelöscht wird, deaktiviert der Dienst den Benutzer im Zielsystem per Update.
1. Wenn ein Benutzer, der sich zuvor im Bereich für die Bereitstellung befunden hat, im Quellsystem endgültig gelöscht wird, löscht der Dienst den Benutzer im Zielsystem. In Azure AD werden Benutzer 30 Tage nach dem vorläufigen Löschen endgültig gelöscht.
1. Legen Sie am Ende des inkrementellen Zyklus einen neuen Grenzwert fest, der als Startpunkt für die nachfolgenden inkrementellen Zyklen dient.

> [!NOTE]
> Optional können Sie die Vorgänge **Erstellen**, **Aktualisieren** und **Löschen** deaktivieren. Dazu verwenden Sie im Abschnitt **Zuordnungen** die Kontrollkästchen für [Zielobjektaktionen](customize-application-attributes.md). Die Logik zum Deaktivieren eines Benutzers während eines Updates wird ebenfalls per Attributzuordnung über ein Feld wie „accountEnabled“ gesteuert.

Der Bereitstellungsdienst führt aufeinander folgende inkrementelle Zyklen in bestimmten (in [den jeweiligen Anwendungstutorials](../saas-apps/tutorial-list.md) angegebenen) Intervallen aus, bis eines der folgenden Ereignisse eintritt:

- Der Dienst wird mit dem Azure-Portal oder mit dem entsprechenden Graph-API-Befehl manuell beendet. 
- Ein neuer erster Zyklus wird ausgelöst, indem im Azure-Portal die Option **Clear state and restart** (Status löschen und neu starten) oder der entsprechende Graph-API-Befehl verwendet wird. Durch diese Aktion werden alle gespeicherten Grenzwerte gelöscht und alle Quellobjekte erneut ausgewertet.
- Ein neuer erster Zyklus wird aufgrund einer Änderung in den Attributzuordnungen oder Bereichsfiltern ausgelöst. Durch diese Aktion werden auch alle gespeicherten Grenzwerte gelöscht und alle Quellobjekte erneut ausgewertet.
- Der Bereitstellungsprozess wird aufgrund einer hohen Fehlerrate in Quarantäne versetzt (siehe unten) und bleibt mehr als vier Wochen lang in Quarantäne. In diesem Fall wird der Dienst automatisch deaktiviert.

### <a name="errors-and-retries"></a>Fehler und Wiederholungen

Wenn ein einzelner Benutzer aufgrund eines Zielsystemfehlers nicht hinzugefügt, aktualisiert oder gelöscht werden kann, wird der jeweilige Vorgang im nächsten Synchronisierungszyklus wiederholt. Falls für den Benutzer weiterhin ein Fehler auftritt, wird die Häufigkeit der Wiederholungen allmählich verringert, bis schließlich nur noch ein Versuch pro Tag durchgeführt wird. Zum Beheben des Fehlers müssen Administratoren die [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) prüfen, um die Grundursache ermitteln und dann entsprechende Maßnahmen ergreifen zu können. Beispiele für häufig auftretende Fehler sind:

- Für Benutzer ist auf dem Quellsystem ein bestimmtes Attribut nicht angegeben, das auf dem Zielsystem erforderlich ist.
- Benutzer verfügen auf dem Quellsystem über einen Attributwert, für den auf dem Zielsystem eine eindeutige Einschränkung besteht, und der gleiche Wert ist auch in einem anderen Benutzerdatensatz vorhanden.

Diese Fehler können behoben werden, indem die Attributwerte für den betroffenen Benutzer im Quellsystem oder die Attributzuordnungen angepasst werden, um Konflikte zu vermeiden.

### <a name="quarantine"></a>Quarantäne

Falls die meisten oder alle Aufrufe an das Zielsystem aufgrund eines Fehlers (z. B. bei ungültigen Administratoranmeldeinformationen) dauerhaft nicht erfolgreich sind, wird der Bereitstellungsauftrag in den Zustand „Quarantäne“ versetzt. Dieser Zustand ist im [Zusammenfassungsbericht für die Bereitstellung](check-status-user-account-provisioning.md) angegeben, und wenn im Azure-Portal E-Mail-Benachrichtigungen konfiguriert wurden, erfolgt auch eine entsprechende Mitteilung per E-Mail.

In der Quarantäne wird die Häufigkeit der inkrementellen Zyklen allmählich auf einmal pro Tag verringert.

Die Quarantäne für den Bereitstellungsauftrag wird aufgehoben, nachdem alle relevanten Fehler behoben wurden, und der nächste Synchronisierungszyklus wird gestartet. Falls der Bereitstellungsauftrag länger als vier Wochen in Quarantäne verbleibt, wird er deaktiviert.

## <a name="how-long-will-it-take-to-provision-users"></a>Wie lange dauert die Bereitstellung von Benutzern?

Die Leistung ist davon abhängig, ob Ihr Bereitstellungsauftrag einen ersten Bereitstellungszyklus oder einen inkrementellen Zyklus ausführt. Weitere Informationen zur Dauer einer Bereitstellung und zur Überwachung des Status des Bereitstellungsdiensts finden Sie unter [Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann](application-provisioning-when-will-provisioning-finish-specific-user.md).

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Wie kann ich feststellen, ob Benutzer ordnungsgemäß bereitgestellt werden?

Alle vom Benutzerbereitstellungsdienst ausgeführten Vorgänge werden in Azure AD unter [Provisioning logs (preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) (Bereitstellungsprotokollen (Vorschau)) erfasst. Dies umfasst alle Lese- und Schreibvorgänge in den Quell- und Zielsystemen sowie die Benutzerdaten, die im Rahmen des jeweiligen Vorgangs gelesen oder geschrieben wurden.

Informationen zum Lesen der Bereitstellungsprotokolle im Azure-Portal finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Wie kann ich Probleme bei der Benutzerbereitstellung behandeln?

Einen szenariobasierten Leitfaden zur Problembehandlung bei der automatischen Benutzerbereitstellung finden Sie unter [Probleme bei der Konfiguration und Bereitstellung von Benutzern für eine Anwendung](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Welche Best Practices gibt es für das Rollout der automatischen Benutzerbereitstellung?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Einen ausführlichen exemplarischen Bereitstellungsplan für die ausgehende Benutzerbereitstellung einer Anwendung finden Sie im [Identitätsbereitstellungsleitfaden für die Benutzerbereitstellung](https://aka.ms/userprovisioningdeploymentplan).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Funktioniert automatische Benutzerbereitstellung für SaaS-Apps mit B2B-Benutzern in Azure AD?

Ja, der Azure AD-Benutzerbereitstellungsdienst kann zum Bereitstellen von B2B-Benutzern (oder Gastbenutzern) in Azure AD für SaaS-Anwendungen verwendet werden.

Damit B2B-Benutzer sich bei der SaaS-Anwendung mithilfe von Azure AD anmelden können, muss die Funktion „SAML-basiertes einmaliges Anmelden“ der SaaS-Anwendung jedoch auf bestimmte Weise konfiguriert sein. Weitere Informationen zum Konfigurieren von SaaS-Anwendungen, sodass sie Anmeldungen von B2B-Benutzern unterstützen, finden Sie unter [Konfigurieren von SaaS-Apps für die B2B-Zusammenarbeit]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Funktioniert automatische Benutzerbereitstellung für SaaS-Apps mit dynamischen Gruppen in Azure AD?

Ja. Wenn die Konfiguration auf „Nur zugewiesene Benutzer und Gruppen synchronisieren“ festgelegt ist, kann der Azure AD-Benutzerbereitstellungsdienst Benutzer in einer SaaS-Anwendung basierend darauf bereitstellen oder ihre Bereitstellung aufheben, ob sie Mitglieder einer [dynamischen Gruppe](../users-groups-roles/groups-create-rule.md) sind. Dynamische Gruppen funktionieren auch mit der Option „Alle Benutzer und Gruppen synchronisieren“.

Die Verwendung dynamischer Gruppen kann jedoch die Gesamtleistung der End-to-End-Benutzerbereitstellung aus dem Azure AD in SaaS-Anwendungen beeinträchtigen. Berücksichtigen Sie beim Verwenden dynamischer Gruppen die folgenden Einschränkungen und Empfehlungen:

- Wie schnell ein Benutzer in einer dynamischen Gruppe bereitgestellt oder seine Bereitstellung in einer SaaS-Anwendung aufgehoben wird, hängt davon ab, wie schnell die dynamische Gruppe Änderungen an der Mitgliedschaft auswerten kann. Informationen, wie Sie den Verarbeitungsstatus einer dynamischen Gruppe überprüfen, finden Sie unter [Überprüfen des Verarbeitungsstatus für eine Mitgliedschaftsregel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Bei Verwendung dynamischer Gruppen muss bei den Regeln sorgfältig die Benutzerbereitstellung und Aufhebung von Benutzerbereitstellungen berücksichtigt werden, weil ein Verlust der Mitgliedschaft zu einem Ereignis der Aufhebung der Bereitstellung führt.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Funktioniert automatische Benutzerbereitstellung für SaaS-Apps mit geschachtelten Gruppen in Azure AD?

Nein. Wenn die Konfiguration auf „Nur zugewiesene Benutzer und Gruppen synchronisieren“ festgelegt ist, kann der Azure AD-Benutzerbereitstellungsdienst Benutzer, die sich in geschachtelten Gruppen befinden, weder lesen noch bereitstellen. Er kann nur Benutzer lesen und bereitstellen, die direkte Mitglieder der explizit zugewiesenen Gruppe sind.

Dies ist eine Einschränkung „gruppenbasierter Zuweisungen zu Anwendungen“, die sich auch auf das einmalige Anmelden auswirkt und unter [Verwenden einer Gruppe zum Verwalten des Zugriffs auf SaaS-Anwendungen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ) beschrieben ist.

Als Problemumgehung sollten Sie die Gruppen, die die Benutzer enthalten, die bereitgestellt werden müssen, explizit zuweisen (oder auf andere Weise [den Bereich festlegen](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)).

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Wird für die Bereitstellung zwischen Azure AD und einer Zielanwendung ein verschlüsselter Kanal verwendet?

Ja. Wir verwenden die HTTPS-SSL-Verschlüsselung für das Serverziel.

## <a name="related-articles"></a>Verwandte Artikel

- [Liste der Tutorials zur Integration von SaaS-Apps](../saas-apps/tutorial-list.md)
- [Anpassen von Attributzuordnungen für die Benutzerbereitstellung](customize-application-attributes.md)
- [Schreiben von Ausdrücken für Attributzuordnungen](functions-for-customizing-application-data.md)
- [Bereichsfilter für die Benutzerbereitstellung](define-conditional-rules-for-provisioning-user-accounts.md)
- [Verwenden von SCIM für die automatische Bereitstellung von Benutzern und Gruppen aus Azure AD für Anwendungen](use-scim-to-provision-users-and-groups.md)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Azure AD-Synchronisierung – API-Übersicht)
