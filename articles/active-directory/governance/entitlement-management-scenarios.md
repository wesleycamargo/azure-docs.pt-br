---
title: Cenários comuns no gerenciamento de direitos do AD do Azure (visualização) – Azure Active Directory
description: Conheça as etapas de alto nível que você deve seguir para cenários comuns de gerenciamento de direitos do Active Directory do Azure (visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96442a9d49581da6841fa7acb8329354ec727f60
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918468"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Cenários comuns no gerenciamento de direitos do AD do Azure (visualização)

> [!IMPORTANT]
> Gerenciamento de direitos do Active Directory (Azure AD) do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Há várias maneiras que você pode configurar o gerenciamento de direitos para sua organização. No entanto, se você estiver apenas começando, é útil compreender os cenários comuns para administradores, aprovadores e solicitantes.

## <a name="administrators"></a>Administradores

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Eu sou novo para o gerenciamento de direitos e quero que o ajuda com o guia de Introdução

> [!div class="mx-tableFixed"]
> | Etapas | Exemplo |
> | --- | --- |
> | [Siga o tutorial para criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md) | [![Ícone do portal do Azure](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Eu quero permitir que os usuários no meu diretório para solicitar acesso a grupos, aplicativos ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Etapas | Exemplo |
> | --- | --- |
> | **1.** [Criar um novo pacote de acesso em um catálogo](entitlement-management-access-package-create.md#start-new-access-package) | ![Criar um pacote de acesso](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Adicionar funções de recurso para acessar o pacote](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Grupos</li><li>Aplicativos</li><li>Sites do SharePoint</li></ul> | ![Adicionar funções de recurso](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Adicionar uma política](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>Para usuários em seu diretório</li><li>Exigir aprovação</li><li>Configurações de expiração</li></ul> | ![Adicionar política](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Eu quero permitir que os usuários do meu parceiros comerciais (incluindo os usuários ainda não no meu diretório) para solicitar acesso a grupos, aplicativos ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Etapas | Exemplo |
> | --- | --- |
> | **1.** [Criar um novo pacote de acesso em um catálogo](entitlement-management-access-package-create.md#start-new-access-package) | ![Criar um pacote de acesso](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Adicionar funções de recurso para acessar o pacote](entitlement-management-access-package-edit.md#add-resource-roles) | ![Adicionar funções de recurso](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Adicionar uma política para usuários externos](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Para que os usuários não em seu diretório</li><li>Exigir aprovação</li><li>Configurações de expiração</li></ul> | ![Adicionar política para usuários externos](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Envie o link do portal de meu acesso para solicitar o pacote de acesso para seu parceiro de negócios](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Parceiro de negócios pode compartilhar o link com seus usuários</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Quero alterar os grupos, aplicativos ou sites do SharePoint em um pacote de acesso

> [!div class="mx-tableFixed"]
> | Etapas | Exemplo |
> | --- | --- |
> | **1.** Abra o pacote de acesso | ![Adicionar funções de recurso](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Adicionar ou remover funções de recurso](entitlement-management-access-package-edit.md#add-resource-roles) | ![Adicionar funções de recurso](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Quero ver quem tem uma atribuição a grupos, aplicativos ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Etapas | Exemplo |
> | --- | --- |
> | **1.** Abrir um pacote de acesso | ![Adicionar funções de recurso](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Exibir atribuições](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Exibir quais usuários têm acesso a um pacote de acesso</li><li>Exibição de acesso de usuário que tenha expirado</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Eu quero exibir grupos, aplicativos ou um usuário tem acesso a sites do SharePoint

> [!div class="mx-tableFixed"]
> | Etapas | Exemplo |
> | --- | --- |
> | [Exibir relatório de atribuições de usuário](entitlement-management-reports.md)<ul><li>Modo de exibição quando solicitados e quem aprovada</li></ul> |  |

## <a name="approvers"></a>Aprovadores

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Eu quero aprovar solicitações para acessar os grupos, aplicativos ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Etapas | Exemplo |
> | --- | --- |
> | **1.** [Solicitação aberta no portal de acesso Meus](entitlement-management-request-approve.md#open-request) | [![Meu ícone do portal de acesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Aprovar solicitação de acesso](entitlement-management-request-approve.md#approve-or-deny-request) | ![Aprovar o acesso](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Solicitantes

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Eu quero exibir os grupos, aplicativos ou sites do SharePoint disponíveis para mim e solicitar acesso

> [!div class="mx-tableFixed"]
> | Etapas | Exemplo |
> | --- | --- |
> | **1.** [Entre portal do meu acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Meu ícone do portal de acesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Localizar o pacote de acesso |  |
> | **3.** [Solicitar acesso](entitlement-management-request-access.md#request-an-access-package) | ![Solicitar acesso](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Eu sou um usuário externo e eu quero solicitar acesso a grupos, aplicativos ou sites do SharePoint com um link direto

> [!div class="mx-tableFixed"]
> | Etapas | Exemplo |
> | --- | --- |
> | **1.** [Localize o link de portal meu acesso que você recebeu](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Entre portal do meu acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Meu ícone do portal de acesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Solicitar acesso](entitlement-management-request-access.md#request-an-access-package) | ![Solicitação de acesso de usuário externo](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Eu quero exibir os grupos, aplicativos ou sites do SharePoint que já tenho acesso ao

> [!div class="mx-tableFixed"]
> | Etapas | Exemplo |
> | --- | --- |
> | **1.** [Entre portal do meu acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Meu ícone do portal de acesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Exibir pacotes de acesso do Active Directory |  |

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md)
- [Editar e gerenciar um pacote existente de acesso](entitlement-management-access-package-edit.md)
