---
title: Usando o portal de administrador no Azure Stack | Microsoft Docs
description: Como um operador do Azure Stack, saiba como usar o portal do administrador.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: mabrigg
ms.openlocfilehash: 58856875fa7d7bb3ba63c489fb17790e68f99aec
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872179"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Usando o portal de administrador no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Há dois portais no Azure Stack; o portal do administrador e o portal do usuário (também conhecido como o *locatário* portal.) Como um operador do Azure Stack, você pode usar o portal do administrador para operações do Azure Stack e o gerenciamento diários.

## <a name="access-the-administrator-portal"></a>Acessar o portal do administrador

Para um ambiente do kit de desenvolvimento, você precisa primeiro certifique-se de que você pode [conectado ao host do kit de desenvolvimento](azure-stack-connect-azure-stack.md) por meio de Conexão de área de trabalho remota ou uma rede virtual privada (VPN).

Para acessar o portal do administrador, navegue até a URL de portal e de entrada, usando as credenciais de um operador do Azure Stack. Para um sistema integrado, o portal de que URL varia com base no nome da região e nome de domínio totalmente qualificado (FQDN) externo de sua implantação do Azure Stack.

| Ambiente | URL do Portal do administrador |   
| -- | -- | 
| Kit de desenvolvimento| https://adminportal.local.azurestack.external  |
| Sistemas integrados | https://adminportal.&lt; *região*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![O portal do administrador](media/azure-stack-manage-portals/admin-portal.png)

Observe que a zona de tempo padrão para todas as implantações do Azure Stack é definida para o tempo Universal Coordenado (UTC). Você pode selecionar um fuso horário ao instalar o Azure Stack, no entanto, ele será revertido automaticamente para UTC como padrão durante a instalação.

No portal do administrador, você pode fazer coisas como:

* Gerenciar a infraestrutura (incluindo a integridade do sistema, atualizações, capacidade, etc.)
* Preencher o marketplace
* Criar assinaturas para usuários
* Criar planos e ofertas

O **tutorial de início rápido** lado a lado fornece links para documentação online para as tarefas mais comuns.

Embora tenha um operador pode criar recursos como máquinas virtuais, redes virtuais e contas de armazenamento no portal do administrador, você deve [entrar no portal do usuário](user/azure-stack-use-portal.md) para criar e testar recursos.

>[!NOTE]
>O **criar uma máquina virtual** link no bloco do tutorial de início rápido ajuda a criar uma máquina virtual no portal do administrador, mas isso é destinado apenas para validar o Azure Stack depois que ele é implantado pela primeira vez.

## <a name="understand-subscription-behavior"></a>Entender o comportamento de assinatura

Há apenas uma assinatura disponível para uso no portal do administrador. Essa assinatura é o *assinatura do provedor padrão*. Você não pode adicionar quaisquer outras assinaturas e usá-los no portal do administrador.

Como um operador do Azure Stack, você pode adicionar assinaturas para seus usuários (inclusive você mesmo) do portal do administrador. Usuários (inclusive você mesmo) podem acessar e usar essas assinaturas com o **usuário** portal. No entanto, o portal do usuário não fornece acesso a qualquer uma das funcionalidades administrativas ou operacionais do portal do administrador.

Os portais de administrador e usuário são apoiados por instâncias separadas do Azure Resource Manager. Devido a essa separação do Gerenciador de recursos, assinaturas não cruzam portais. Por exemplo, se você, como um operador do Azure Stack, entrar no portal do usuário, você não poderá acessar o *assinatura do provedor padrão*. Embora você não tiver acesso a todas as funções administrativas, você pode criar assinaturas por conta própria de ofertas públicas disponíveis. Desde que você está conectado ao portal do usuário, você é considerado um usuário do locatário.

  >[!NOTE]
  >No ambiente do kit de desenvolvimento, se um usuário pertencer ao mesmo diretório do locatário de operador do Azure Stack, eles não estão impedidos de entrar portal do administrador. No entanto, eles não podem acessar qualquer uma das funções administrativas. Além disso, no portal de administrador, eles não podem adicionar assinaturas ou acesso oferece que estão disponíveis para eles no portal do usuário.

## <a name="administrator-portal-tips"></a>Dicas de portal do administrador

### <a name="customize-the-dashboard"></a>Personalizar o painel

O painel contém um conjunto de blocos padrão. Você pode selecionar **Editar painel** para modificar o painel padrão ou selecione **novo painel** para adicionar um painel personalizado. Você pode adicionar facilmente os blocos em um painel. Por exemplo, você pode selecionar **+ criar um recurso**, clique com botão direito **oferece + planos**e, em seguida, selecione **fixar no painel**.

Às vezes, você poderá ver um painel em branco no portal. Para recuperar o painel, clique em **Editar painel**e, em seguida, clique com botão direito e selecione **retornarão ao estado padrão**.

### <a name="quick-access-to-online-documentation"></a>Acesso rápido a documentação online

Para acessar a documentação do operador do Azure Stack, use a Ajuda e suporte de ícone (ponto de interrogação) no canto superior direito do portal do administrador. Mova o cursor para o ícone e, em seguida, selecione **ajuda + suporte**.

### <a name="quick-access-to-help-and-support"></a>Acesso rápido a Ajuda e suporte

Se você selecionar o ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, selecione **nova solicitação de suporte**, ocorrem por um dos seguintes resultados:

- Se você estiver usando um sistema integrado, essa ação abrirá um site onde você pode abrir diretamente um tíquete de suporte com serviços de suporte de cliente do Microsoft (CSS). Consulte a [onde obter suporte](azure-stack-manage-basics.md#where-to-get-support) para entender quando você deve entrar por meio do suporte da Microsoft ou por meio de suporte do fornecedor de hardware seu fabricante de equipamento original (OEM).
- Se você estiver usando o kit de desenvolvimento, essa ação abre o site dos fóruns do Azure Stack diretamente. Esses fóruns são monitorados regularmente. Como o kit de desenvolvimento é um ambiente de avaliação, não há nenhum suporte oficial oferecida por meio do Microsoft CSS.

### <a name="quick-access-to-the-azure-roadmap"></a>Acesso rápido para o roteiro do Azure

Se você selecionar **ajuda e suporte** (o ponto de interrogação) no canto superior direito do administrador do portal e, em seguida, selecione **roteiro do Azure**, uma nova guia do navegador é aberta e leva você até o roteiro do Azure. Digitando **do Azure Stack** na **produtos** caixa de pesquisa, você pode ver todas as atualizações de roteiro do Azure Stack.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciamento de região no Azure Stack](azure-stack-region-management.md)
