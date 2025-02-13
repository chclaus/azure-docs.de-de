---
title: Verwalten des Zugriffs auf Azure-Ressourcen für externe Gastbenutzer mit RBAC | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcen für organisationsexterne Benutzer mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) verwalten.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 09/12/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 5ed9088bcc5776fe1fb0d09e6ae771adabb1b879
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802682"
---
# <a name="manage-access-to-azure-resources-for-external-guest-users-using-rbac"></a>Verwalten des Zugriffs auf Azure-Ressourcen für externe Gastbenutzer mit RBAC

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) ermöglicht eine bessere Sicherheitsverwaltung für große Organisationen sowie für kleine und mittelständische Unternehmen, die mit externen Projektmitarbeitern, Lieferanten oder Freiberuflern arbeiten und für diese Zugriff auf bestimmte Ressourcen in der Umgebung benötigen, nicht aber auf die gesamte Infrastruktur oder abrechnungsrelevante Bereiche. Mithilfe der Funktionen in [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md) können Sie mit externen Gastbenutzern zusammenarbeiten, und Sie können mithilfe von RBAC nur die Berechtigungen erteilen, die Gastbenutzer in Ihrer Umgebung benötigen.

## <a name="when-would-you-invite-guest-users"></a>Wann würden Sie Gastbenutzer einladen?

Einige Beispielszenarios, in denen Sie möglicherweise Gastbenutzer in Ihre Organisation einladen und Berechtigungen erteilen:

- Zugriff für einen externen selbstständigen Anbieter, der nur über ein E-Mail-Konto verfügt, auf Ihre Azure-Ressourcen für ein Projekt
- Zugriff für einen externen Partner zum Verwalten bestimmter Ressourcen oder eines ganzen Abonnements
- Temporärer Zugriff für Supporttechniker außerhalb Ihrer Organisation (z.B. Microsoft-Support) auf Ihre Azure-Ressource zur Behebung von Problemen

## <a name="permission-differences-between-member-users-and-guest-users"></a>Berechtigungsunterschiede zwischen Mitgliedsbenutzern und Gastbenutzern

Native Mitglieder eines Verzeichnisses (Mitgliedsbenutzer) verfügen über andere Berechtigungen als Benutzer, die als B2B-Zusammenarbeitsgast (Gastbenutzer) aus einem anderen Verzeichnis eingeladen wurden. Ein Mitgliedsbenutzer kann z. B. fast alle Verzeichnisinformationen lesen, während Gastbenutzer über eingeschränkte Verzeichnisberechtigungen verfügen. Weitere Informationen zu Mitglieds- und Gastbenutzern finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Hinzufügen eines Gastbenutzers zu Ihrem Verzeichnis

Führen Sie diese Schritte aus, um einen Gastbenutzer über die Azure Active Directory-Seite zu Ihrem Verzeichnis hinzuzufügen.

1. Stellen Sie sicher, dass die Einstellungen für externe Zusammenarbeit Ihrer Organisation so konfiguriert sind, dass Sie Gäste einladen dürfen. Weitere Informationen finden Sie unter [Aktivieren der externen B2B-Zusammenarbeit und Steuern, wer Gäste einladen kann](../active-directory/b2b/delegate-invitations.md).

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** > **Benutzer** > **Neuer Gastbenutzer**.

    ![Neues Feature „Gastbenutzer“ im Azure-Portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Befolgen Sie die Schritte, um einen neuen Gastbenutzer hinzuzufügen. Weitere Informationen finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory).

Nachdem Sie dem Verzeichnis einen Gastbenutzer hinzugefügt haben, können Sie dem Gastbenutzer einen direkten Link zu einer freigegebenen App senden, oder der Gastbenutzer kann auf die Einlösungs-URL in der Einladungs-E-Mail klicken.

![E-Mail zum Einladen von Gastbenutzern](./media/role-assignments-external-users/invite-email.png)

Damit der Gastbenutzer auf Ihr Verzeichnis zugreifen kann, muss er den Einladungsprozess abschließen.

![Berechtigungen zum Überprüfen der Gastbenutzereinladung](./media/role-assignments-external-users/invite-review-permissions.png)

Weitere Informationen zum Einladungsprozess finden Sie unter [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](../active-directory/b2b/redemption-experience.md).

## <a name="grant-access-to-a-guest-user"></a>Gewähren des Zugriffs für einen Gastbenutzer

In RBAC weisen Sie zum Gewähren des Zugriffs eine Rolle zu. Um einen Gastbenutzer den Zugriff zu gewähren, befolgen Sie [dieselben Schritte](role-assignments-portal.md#add-a-role-assignment) wie für einen Mitgliedsbenutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität. Gehen Sie wie folgt vor, um einem Gastbenutzer Zugriff auf verschiedene Bereiche zu gewähren.

1. Klicken Sie im Azure-Portal auf **Alle Dienste**.

1.  Wählen Sie den Satz von Ressourcen aus, für den der Zugriff gilt, der auch als „Bereich“ bezeichnet wird. Sie können beispielsweise **Verwaltungsgruppen**, **Abonnements**, **Ressourcengruppen** oder eine Ressource auswählen.

1. Klicken Sie auf die gewünschte Ressource.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

    Der folgende Screenshot zeigt ein Beispiel für das Blatt „Zugriffssteuerung (IAM)“ für eine Ressourcengruppe. Wenn Sie hier Änderungen an der Zugriffssteuerung vornehmen, gelten diese nur für die Ressourcengruppe.

    ![Blatt „Zugriffssteuerung (IAM)“ für eine Ressourcengruppe](./media/role-assignments-external-users/access-control-resource-group.png)

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um alle Rollenzuweisungen für diesen Bereich anzuzeigen.

1. Klicken Sie auf **Hinzufügen** > **Rollenzuweisung hinzufügen**, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

    Wenn Sie keine Berechtigungen zum Zuweisen von Rollen haben, ist die Option „Rollenzuweisung hinzufügen“ deaktiviert.

    ![Menü „Hinzufügen“](./media/role-assignments-external-users/add-menu.png)

1. Wählen Sie in der Dropdownliste **Rolle** eine Rolle aus, etwa **Mitwirkender für virtuelle Computer**.

1. Wählen Sie in der Liste **Auswählen** den Gastbenutzer aus. Wird der Benutzer in der Liste nicht angezeigt, können Sie im Feld **Auswählen** einen Begriff eingeben, um das Verzeichnis nach Anzeigenamen, E-Mail-Adressen und Objektbezeichner zu durchsuchen.

   ![Bereich „Rollenzuweisung hinzufügen“](./media/role-assignments-external-users/add-role-assignment.png)

1. Klicken Sie auf **Speichern**, um die Rolle für den ausgewählten Bereich zuzuweisen.

    ![Rollenzuweisung für Mitwirkender von virtuellen Computern](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="grant-access-to-a-guest-user-not-yet-in-your-directory"></a>Gewähren des Zugriffs für einen Gastbenutzer, der sich noch nicht in Ihrem Verzeichnis befindet

In RBAC weisen Sie zum Gewähren des Zugriffs eine Rolle zu. Um einen Gastbenutzer den Zugriff zu gewähren, befolgen Sie [dieselben Schritte](role-assignments-portal.md#add-a-role-assignment) wie für einen Mitgliedsbenutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität.

Wenn sich der Gastbenutzer noch nicht in Ihrem Verzeichnis befindet, können Sie den Benutzer direkt über den Bereich „Rollenzuweisung hinzufügen“ einladen.

1. Klicken Sie im Azure-Portal auf **Alle Dienste**.

1.  Wählen Sie den Satz von Ressourcen aus, für den der Zugriff gilt, der auch als „Bereich“ bezeichnet wird. Sie können beispielsweise **Verwaltungsgruppen**, **Abonnements**, **Ressourcengruppen** oder eine Ressource auswählen.

1. Klicken Sie auf die gewünschte Ressource.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um alle Rollenzuweisungen für diesen Bereich anzuzeigen.

1. Klicken Sie auf **Hinzufügen** > **Rollenzuweisung hinzufügen**, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

    ![Menü „Hinzufügen“](./media/role-assignments-external-users/add-menu.png)

1. Wählen Sie in der Dropdownliste **Rolle** eine Rolle aus, etwa **Mitwirkender für virtuelle Computer**.

1. Geben Sie in der Liste **Auswählen** die E-Mail-Adresse der einzuladenden Person ein, und wählen Sie diese Person dann aus.

   ![Einladen eines Gastbenutzers im Bereich „Rollenzuweisung hinzufügen“](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Klicken Sie auf **Speichern**, um den Gastbenutzer zu Ihrem Verzeichnis hinzuzufügen, die Rolle zuzuweisen und eine Einladung zu senden.

    Nach einigen Momenten werden eine Benachrichtigung über die Rollenzuweisung sowie Informationen zur Einladung angezeigt.

    ![Rollenzuweisung und Benachrichtigung des eingeladenen Benutzers](./media/role-assignments-external-users/invited-user-notification.png)

1. Um den Gastbenutzer manuell einzuladen, klicken Sie mit der rechten Maustaste und kopieren den Link der Einladung in der Benachrichtigung. Klicken Sie nicht auf den Link zur Einladung, da dadurch der Einladungsprozess gestartet wird.

    Der Link zur Einladung hat das folgende Format:

    `https://invitations.microsoft.com/redeem/...`

1. Senden Sie den Einladungslink an den Gastbenutzer, um den Einladungsprozess abzuschließen.

    Weitere Informationen zum Einladungsprozess finden Sie unter [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](../active-directory/b2b/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Entfernen eines Gastbenutzers aus Ihrem Verzeichnis

Bevor Sie einen Gastbenutzer aus einem Verzeichnis entfernen, sollten Sie zunächst alle Rollenzuweisungen für diesen Gastbenutzer entfernen. Führen Sie die folgenden Schritte aus, um einen Gastbenutzer aus einem Verzeichnis zu entfernen.

1. Öffnen Sie das Blatt **Zugriffssteuerung (IAM)** für einen Bereich, z. B. für eine Verwaltungsgruppe, ein Abonnement, eine Ressourcengruppe oder eine Ressource, für den der Gastbenutzer über eine Rollenzuweisung verfügt.

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um alle Rollenzuweisungen anzuzeigen.

1. Aktivieren Sie in der Liste der Rollenzuweisungen den Gastbenutzer mit der zu entfernenden Rollenzuweisung.

   ![Entfernen von Rollenzuweisungen](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Klicken Sie auf **Entfernen**.

   ![Nachricht zum Entfernen der Rollenzuweisung](./media/role-assignments-external-users/remove-role-assignment.png)

1. Klicken Sie in der angezeigten Meldung zum Entfernen der Rollenzuweisung auf **Ja**.

1. Klicken Sie auf der linken Navigationsleiste auf **Azure Active Directory** > **Benutzer**.

1. Klicken Sie auf den zu entfernenden Gastbenutzer.

1. Klicken Sie auf **Löschen**.

   ![Löschen eines Gastbenutzers](./media/role-assignments-external-users/delete-guest-user.png)

1. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

## <a name="troubleshoot"></a>Problembehandlung

### <a name="guest-user-cannot-browse-the-directory"></a>Gastbenutzer können das Verzeichnis nicht durchsuchen.

Gastbenutzer erhalten eingeschränkte Verzeichnisberechtigungen. Gastbenutzer können z. B. das Verzeichnis nicht durchsuchen und nicht nach Gruppen oder Anwendungen suchen. Weitere Informationen finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![Gastbenutzer können Benutzer in einem Verzeichnis nicht durchsuchen.](./media/role-assignments-external-users/directory-no-users.png)

Wenn ein Gastbenutzer zusätzliche Berechtigungen im Verzeichnis benötigt, können Sie dem Gastbenutzer eine Verzeichnisrolle zuweisen. Wenn Sie einem Gastbenutzer tatsächlich uneingeschränkten Lesezugriff auf Ihr Verzeichnis gestatten möchten, können Sie den Gastbenutzer der Rolle [Verzeichnisleseberechtigte](../active-directory/users-groups-roles/directory-assign-admin-roles.md) in Azure AD hinzufügen. Weitere Informationen finden Sie unter [Gewähren von Berechtigungen für Benutzer von Partnerorganisationen in Ihrem Azure Active Directory-Mandanten](../active-directory/b2b/add-guest-to-role.md).

![Zuweisen der Rolle „Verzeichnisleseberechtigte“](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Gastbenutzer können keine Benutzer, Gruppen oder Dienstprinzipale durchsuchen, um Rollen zuzuweisen.

Gastbenutzer erhalten eingeschränkte Verzeichnisberechtigungen. Selbst wenn ein Gastbenutzer ein [Besitzer](built-in-roles.md#owner) eines Bereichs ist, kann er die Liste der Benutzer, Gruppen oder Dienstprinzipale nicht durchsuchen, wenn er versucht, eine Rollenzuweisung zu erstellen, um anderen Personen Zugriff zu gewähren.

![Gastbenutzer können keine Sicherheitsprinzipale durchsuchen, um Rollen zuzuweisen.](./media/role-assignments-external-users/directory-no-browse.png)

Wenn der Gastbenutzer den genauen Anmeldenamen einer Person im Verzeichnis kennt, kann er Zugriff gewähren. Wenn Sie einem Gastbenutzer tatsächlich uneingeschränkten Lesezugriff auf Ihr Verzeichnis gestatten möchten, können Sie den Gastbenutzer der Rolle [Verzeichnisleseberechtigte](../active-directory/users-groups-roles/directory-assign-admin-roles.md) in Azure AD hinzufügen. Weitere Informationen finden Sie unter [Gewähren von Berechtigungen für Benutzer von Partnerorganisationen in Ihrem Azure Active Directory-Mandanten](../active-directory/b2b/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Gastbenutzer können keine Anwendungen registrieren oder Dienstprinzipale erstellen.

Gastbenutzer erhalten eingeschränkte Verzeichnisberechtigungen. Wenn ein Gastbenutzer Anwendungen registrieren oder Dienstprinzipale erstellen können muss, können Sie den Gastbenutzer der Rolle [Anwendungsentwickler](../active-directory/users-groups-roles/directory-assign-admin-roles.md) in Azure AD hinzufügen. Weitere Informationen finden Sie unter [Gewähren von Berechtigungen für Benutzer von Partnerorganisationen in Ihrem Azure Active Directory-Mandanten](../active-directory/b2b/add-guest-to-role.md).

![Gastbenutzer können keine Anwendungen registrieren.](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Gastbenutzern wird das neue Verzeichnis nicht angezeigt.

Wenn ein Gastbenutzer Zugriff auf ein Verzeichnis hat, aber das neue Verzeichnis nicht im Azure-Portal angezeigt wird, wenn Sie versuchen, in ihren Bereich **Verzeichnis und Abonnement** zu wechseln, stellen Sie sicher, dass der Gastbenutzer den Einladungprozess abgeschlossen hat. Weitere Informationen zum Einladungsprozess finden Sie unter [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](../active-directory/b2b/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>Gastbenutzern werden keine Ressourcen angezeigt.

Wenn einem Gastbenutzer der Zugriff auf ein Verzeichnis gewährt wurde, er aber die Ressourcen, auf die er im Azure-Portal Zugriff erhalten hat, nicht sieht, stellen Sie sicher, dass der Gastbenutzer das richtige Verzeichnis ausgewählt hat. Ein Gastbenutzer kann Zugriff auf mehrere Verzeichnisse erhalten. Klicken Sie zum Wechseln des Verzeichnisses oben links auf **Verzeichnis und Abonnement**, und klicken Sie dann auf das entsprechende Verzeichnis.

![Bereich „Verzeichnisse und Abonnements“ im Azure-Portal](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Nächste Schritte

- [Add Azure Active Directory B2B collaboration users in the Azure portal](../active-directory/b2b/add-users-administrator.md) (Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal)
- [Eigenschaften eines Azure Active Directory B2B-Zusammenarbeitsbenutzers](../active-directory/b2b/user-properties.md)
- [Die Elemente der Einladungs-E-Mail für die B2B-Zusammenarbeit – Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [Hinzufügen eines Gastbenutzers als Co-Administrator](classic-administrators.md#adding-a-guest-user-as-a-co-administrator)