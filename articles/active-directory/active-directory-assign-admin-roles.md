---
title: "Atribuindo funções de administrador no Azure Active Directory | Microsoft Docs"
description: "Explica quais funções de administrador estão disponíveis com o Active Directory do Azure e como atribuí-las."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: fdb4ed95b25519fb7ff319fe20a0dc5c54506506
ms.contentlocale: pt-br
ms.lasthandoff: 04/28/2017


---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Atribuindo funções de administrador no Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-assign-admin-roles-azure-portal.md)
> * [Portal clássico do Azure](active-directory-assign-admin-roles.md)
>
>

Usando o Azure Active Directory (Azure AD), você pode designar administradores separados para atender a diferentes funções. Esses administradores terão acesso a vários recursos no portal do Azure ou portal clássico do Azure e, dependendo da sua função, poderão criar ou editar usuários, atribuir funções administrativas a outros usuários, redefinir senhas de usuários, gerenciar licenças de usuários e gerenciar domínios, entre outras coisas. Um usuário ao qual é atribuída uma função administrativa terá as mesmas permissões em todos os serviços de nuvem que sua organização tenha assinado, independentemente de você ter atribuído a função no portal do Office 365 ou no portal clássico do Azure ou usando o módulo do Azure AD para Windows PowerShell.

As seguintes funções de administrador estão disponíveis:

* **Administrador de cobrança**: faz compras, gerencia as assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.
* **Administrador global/Administrador da Empresa**: tem acesso a todos os recursos administrativos. A pessoa que se inscreve para a conta do Azure torna-se um administrador global. Somente os administradores globais podem atribuir outras funções de administrador. Pode haver mais de um administrador global na sua empresa.

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador da Empresa". É "Administrador Global" no [portal do Azure](https://portal.azure.com).
  >
  >

* **Administrador de conformidade**: os usuários com essa função têm permissões de gerenciamento no Centro de Conformidade e Segurança do Office 365 e no Centro de Administração do Exchange, e acesso de leitura a logs de auditoria no Centro de Administração do Office 365. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrador de Serviços do CRM**: os usuários com essa função têm permissões globais no Microsoft CRM Online, quando o serviço estiver presente. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
* **Aprovador de acesso do Sistema de Proteção de Dados do Cliente**: quando o Sistema Proteção está habilitado, os usuários com essa função podem aprovar solicitações de engenheiros da Microsoft para acessar as informações da empresa. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
* **Administradores do dispositivo**: os usuários com essa função se tornam Administradores em todos os dispositivos Windows 10 associados ao Azure Active Directory.
* **Leitores de diretório**: esta é uma função herdada que deve ser atribuída aos aplicativos que não dão suporte a [Estrutura de Consentimento](active-directory-integrating-applications.md). Ele não deve ser atribuído a nenhum usuário.
* **Contas de sincronização de diretório**: não use. Essa função é automaticamente atribuída ao serviço do Azure AD Connect e não tem intenção ou suporte para outros usos.
* **Gravadores de diretório**: esta é uma função herdada que deve ser atribuída aos aplicativos que não dão suporte a [Estrutura de Consentimento](active-directory-integrating-applications.md). Ele não deve ser atribuído a nenhum usuário.
* **Administrador de serviço do Exchange**: os usuários com essa função têm permissões globais no Microsoft Exchange Online, quando o serviço estiver presente. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
* **Administrador de serviços do Intune**: os usuários com essa função têm permissões globais no Microsoft Intune Online, quando o serviço estiver presente. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
* **Administrador de serviços do Skype for Business**: os usuários com essa função têm permissões globais no Microsoft Skype for Business, quando o serviço estiver presente. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d). Esta função era conhecida anteriormente como função de **Administrador de serviço do Lync** .
* **Emissor do convite**: os usuários nessa função podem gerenciar convites. Ela não inclui nenhuma outra permissão.
* **Administrador de Caixa de Correio**: essa função é usada somente para o suporte por email do Exchange Online em dispositivos RIM Blackberry. Se sua organização não usar o email do Exchange Online em dispositivos RIM Blackberry, não use essa função.
* **Suporte ao parceiro de Nível 1**: não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.
* **Suporte ao parceiro de Nível 2**: não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.
* **Administrador de senha/Administrador de assistência técnica**: redefine as senhas, gerencia as solicitações de serviço e monitora a integridade do serviço. Administradores de senha podem redefinir senhas somente para os usuários e outros administradores de senha.

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador da Assistência Técnica".
  >
  >

* **Administrador de serviços do Power BI**: os usuários com essa função têm permissões globais no Microsoft Power BI, quando o serviço estiver presente. Mais informações em [Facilitar a administração do Power BI](https://powerbi.microsoft.com/en-us/blog/making-it-easier-to-administer-power-bi/).
* **Administrador da função com privilégios**: os usuários com essa função podem gerenciar o Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md) e atualizar atribuições de função para outros usuários.
* **Administrador do serviço SharePoint**: os usuários com essa função têm permissões globais no Microsoft SharePoint Online, quando o serviço estiver presente. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
* **Administrador de serviço**: gerencia as solicitações de serviço e monitora a integridade do serviço.

  > [!NOTE]
  > Para atribuir a função de administrador de serviços a um usuário, o administrador global deve primeiro atribuir permissões administrativas para o usuário no serviço, como o Exchange Online, e, em seguida, atribuir a função de administrador de serviço para o usuário no Portal de Gerenciamento do Azure.
  >
  >
* **Administrador da conta de usuário**: redefine as senhas, monitora a integridade do serviço e gerencia contas de usuário, grupos de usuários e solicitações de serviço. Algumas limitações se aplicam às permissões de um administrador de gerenciamento de usuário. Por exemplo, eles não podem excluir um administrador global ou criar outros administradores. Além disso, eles não podem redefinir senhas para cobrança, globais e administradores de serviço.
* **Leitor de segurança**: acesso somente leitura a um número de recursos de segurança do Identity Protection Center, Privileged Identity Management, Monitorar Integridade de Serviço do Office 365 e Centro de Segurança e Conformidade do Office 365.
* **Administrador de segurança**: todas as permissões somente leitura da função **Leitor de segurança**, mais um número de permissões administrativas adicionais para os mesmos serviços: Identity Protection Center, Privileged Identity Management, Monitorar Integridade de Serviço do Office 365 e Centro de Segurança e Conformidade do Office 365.

## <a name="administrator-permissions"></a>Permissões de administrador
### <a name="billing-administrator"></a>Administrador de cobrança
| O que ele pode fazer | O que não pode fazer |
| --- | --- |
| <p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Executar operações de cobrança e compra de produtos do Office</p> |<p>Redefinir senhas de usuário</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Exibir relatórios</p> |

### <a name="global-administrator"></a>Administrador global
| O que ele pode fazer | O que não pode fazer |
| --- | --- |
| <p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Executar operações de cobrança e compra de produtos do Office</p> <p>Redefinir senhas de usuário</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Habilitar ou desabilitar autenticação multifator</p><p>Exibir relatórios</p> |N/D |

### <a name="password-administrator"></a>Administrador de senha
| O que ele pode fazer | O que não pode fazer |
| --- | --- |
| <p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Redefinir senhas de usuário</p> |<p>Executar operações de cobrança e compra de produtos do Office</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Exibir relatórios</p> |

### <a name="service-administrator"></a>Administrador de serviço
| O que ele pode fazer | O que não pode fazer |
| --- | --- |
| <p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p> |<p>Redefinir senhas de usuário</p><p>Executar operações de cobrança e compra de produtos do Office</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Exibir relatórios</p> |

### <a name="user-administrator"></a>Administrador de usuários
| O que ele pode fazer | O que não pode fazer |
| --- | --- |
| <p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Redefinir senhas de usuário, com limitações. Eles não podem redefinir senhas para administradores de cobrança, globais e de serviço.</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário, com limitações. Eles não podem excluir um administrador global ou criar outros administradores.</p> |<p>Executar operações de cobrança e compra de produtos do Office</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Habilitar ou desabilitar autenticação multifator</p><p>Exibir relatórios</p> |

### <a name="security-reader"></a>Leitor de segurança
| Nesse | O que ele pode fazer |
| --- | --- |
| Identity Protection Center |Ler todos os relatórios de segurança e informações de configurações para recursos de segurança<ul><li>Anti-spam<li>Criptografia<li>Prevenção de perda de dados<li>Antimalware<li>Proteção avançada contra ameaças<li>Antiphishing<li>Regras de fluxo de mensagens |
| Privileged Identity Management |<p>Tem acesso somente leitura a todas as informações exibidas no Azure AD PIM: políticas e relatórios de atribuições de função do Azure AD, análises de segurança e, no futuro, acesso de leitura aos dados de política e relatórios para cenários além da atribuição de função do Azure AD.<p>**Não pode** se inscrever no Azure AD PIM nem fazer alterações nele. No portal do PIM ou por meio do PowerShell, alguém nesta função poderá ativar funções adicionais (por exemplo, administrador global ou administrador com função com privilégios) se o usuário for um candidato a elas. |
| <p>Monitorar a integridade do serviço Office 365</p><p>Centro de Conformidade e Segurança do Office 365</p> |<ul><li>Ler e gerenciar alertas<li>Ler políticas de segurança<li>Ler informações sobre inteligência contra ameaças, Cloud App Discovery e quarentena em Pesquisar e Investigar<li>Ler todos os relatórios |

### <a name="security-administrator"></a>Administrador de segurança
| Nesse | O que ele pode fazer |
| --- | --- |
| Identity Protection Center |<ul><li>Todas as permissões da função Leitor de Segurança.<li>Além disso, a capacidade de executar todas as operações de IPC, exceto para a redefinição de senhas. |
| Privileged Identity Management |<ul><li>Todas as permissões da função Leitor de Segurança.<li>**Não é possível** gerenciar associações de função ou configurações do Azure AD. |
| <p>Monitorar a integridade do serviço Office 365</p><p>Centro de Conformidade e Segurança do Office 365 |<ul><li>Todas as permissões da função Leitor de Segurança.<li>Pode configurar todas as configurações no recurso de Proteção avançada contra ameaças (proteção contra malware e vírus, configuração de URL mal-intencionado, rastreamento de URL, etc). |

## <a name="details-about-the-global-administrator-role"></a>Detalhes sobre a função de administrador global
O administrador global tem acesso a todos os recursos administrativos. Por padrão, a pessoa que se inscreve para uma assinatura do Azure recebe a função de administrador global para o diretório. Somente os administradores globais podem atribuir outras funções de administrador.

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador
1. No [portal clássico do Azure](https://manage.windowsazure.com), clique em **Active Directory**e no nome do diretório de sua organização.
2. Na página **Usuários** , clique no nome de exibição do usuário que deseja editar.
3. Na lista **Função Organizacional**, selecione a função de administrador que você deseja atribuir a este usuário ou selecione **Usuário** se você quiser remover uma função de administrador existente.
4. No campo **Endereço de email alternativo** , digite um endereço de email. Este endereço de email é usado para notificações importantes, incluindo redefinição automática de senha, por isso, o usuário deve ser capaz de acessar a conta de email independentemente de poder acessar o Azure ou não.
5. Selecione **Permitir** ou **Bloquear** para especificar se deseja permitir que o usuário entre e acesse os serviços.
6. Especifique um local na lista suspensa **Local de Uso** .
7. Ao terminar, clique em **Salvar**.

## <a name="deprecated-roles"></a>Funções preteridas

As seguintes funções não devem ser usadas. Elas foram preteridas e serão removidas do Azure AD no futuro.

* Administrador de Licenças AdHoc
* Criador de Usuário Verificado por Email
* Ingresso de Dispositivo
* Gerenciadores de Dispositivo
* Usuários de Dispositivo
* Ingresso no Dispositivo no Local de Trabalho

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como alterar administradores para uma assinatura do Azure, veja [Como adicionar ou alterar as funções de administrador do Azure](../billing-add-change-azure-subscription-administrator.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso aos recursos do Azure](active-directory-understanding-resource-access.md)
* Para saber mais sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, confira [Como as assinaturas do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerenciar usuários](active-directory-create-users.md)
* [Gerenciar senhas](active-directory-manage-passwords.md)
* [Gerenciar grupos](active-directory-manage-groups.md)

