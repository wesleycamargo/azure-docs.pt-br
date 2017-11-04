---
title: Usando o portal de administrador na pilha do Azure | Microsoft Docs
description: Como um operador de pilha do Azure, Aprenda como usar o portal do administrador.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3a1be7a08fab8ad0253f26e6a0683617bff4b7c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Usando o portal de administrador na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Há dois portais na pilha do Azure; o portal do administrador e o portal do usuário (também conhecido como o *locatário* portal). Como um operador de pilha do Azure, você pode usar o portal do administrador de gerenciamento diário e operações da pilha do Azure. 

## <a name="access-the-administrator-portal"></a>Acessar o portal do administrador

Para um ambiente do kit de desenvolvimento, você precisa primeiro certificar-se de que você pode [conectar ao host do kit de desenvolvimento](azure-stack-connect-azure-stack.md) por meio de Conexão de área de trabalho remota ou uma rede virtual privada (VPN).

Para acessar o portal do administrador, navegue até a URL de portal e entrar usando as credenciais de um operador de pilha do Azure. Para um sistema integrado, o portal de que URL varia com base no nome de região e nome de domínio totalmente qualificado (FQDN) externo de sua implantação do Azure pilha.

| Ambiente | URL do Portal de administrador |   
| -- | -- | 
| Kit de desenvolvimento| https://adminportal.local.azurestack.external  |
| Sistemas integrados | https://adminportal. &lt; *região*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![O portal do administrador](media/azure-stack-manage-portals/image1.png)

No portal do administrador, você pode fazer coisas como:

* gerenciar a infraestrutura (incluindo a integridade do sistema, atualizações, capacidade, etc.)
* Preencher o marketplace
* criar planos e ofertas
* Criar assinaturas para usuários

No **tutorial de início rápido** lado a lado, há links para documentação on-line para as tarefas mais comuns.
 
Embora não haja a capacidade de um operador criar recursos, como máquinas virtuais, redes virtuais e contas de armazenamento no portal do administrador, você deve [entrar no portal do usuário](user/azure-stack-use-portal.md) para criar e testar recursos. (O **criar uma máquina virtual** link no bloco do tutorial de início rápido você criar uma máquina virtual no portal do administrador, mas esse procedimento é apenas para validar a pilha do Azure após a implantação inicial.)

## <a name="subscription-behavior"></a>Comportamento de assinatura
 
Há apenas uma assinatura que está disponível no portal do administrador. Esta assinatura está o *assinatura do provedor padrão*. Você não pode adicionar outras assinaturas para uso no portal do administrador.

Como um operador de pilha do Azure, você pode adicionar assinaturas para seus usuários (incluindo você) a partir do portal do administrador. Os usuários (incluindo você) podem acessar e usar essas assinaturas do portal do usuário. O portal do usuário não fornece acesso a qualquer um dos recursos administrativos ou operacionais do portal do administrador.

Os portais de administrador e usuário contam por instâncias separadas do Gerenciador de recursos do Azure. Devido a separação do Gerenciador de recursos, as assinaturas não passam portais. Por exemplo, se você como um operador de pilha do Azure entra portal do usuário, você não poderá acessar a assinatura de provedor padrão. Portanto, você não tem acesso a todas as funções administrativas. Você pode criar assinaturas por conta própria ofertas públicas, mas são considerados um usuário de locatário.

  >[!NOTE]
  No ambiente do kit de desenvolvimento, se um usuário pertencer ao mesmo diretório do locatário o operador de pilha do Azure, eles não são impedidos de entrar portal do administrador. No entanto, eles não podem acessar qualquer uma das funções administrativas. Além disso, no portal de administrador, eles não podem adicionar assinaturas ou acesso oferece que ficam disponíveis para eles no portal do usuário.

## <a name="administrator-portal-tips"></a>Dicas de portal do administrador

### <a name="customize-the-dashboard"></a>Personalizar o painel

O painel contém um conjunto de blocos padrão. Você pode clicar em **Editar painel** para modificar o painel padrão ou clique em **novo painel** adicionar painéis personalizados. Você pode adicionar facilmente blocos ao painel. Por exemplo, você pode clicar em **novo**, clique com botão direito **oferece + planos**e, em seguida, clique em **fixar no painel**.

### <a name="quick-access-to-online-documentation"></a>Acesso rápido a documentação online

Para acessar a documentação de operador de pilha do Azure, clique no ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, clique em **ajuda + suporte**.

### <a name="quick-access-to-help-and-support"></a>Acesso rápido a Ajuda e suporte

Se você clique no ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, clique em **nova solicitação de suporte**, isto faz o seguinte:

- Se você estiver usando um sistema integrado, essa ação abrirá um site onde você pode abrir diretamente um tíquete de suporte com serviços de suporte de cliente do Microsoft (CSS). Consulte a seção "Onde obter suporte" de [Noções básicas de administração do Azure pilha](azure-stack-manage-basics.md) para entender quando você deve entrar por meio do suporte da Microsoft ou o suporte do fornecedor de hardware fabricante (OEM).
- Se você estiver usando o kit de desenvolvimento, essa ação abre o site de fóruns do Azure pilha diretamente. Esses fóruns são monitorados regularmente. Porque o kit de desenvolvimento é um ambiente de avaliação, não há nenhum suporte oficial oferecido pelo Microsoft CSS.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciamento de região na pilha do Azure](azure-stack-region-management.md)
