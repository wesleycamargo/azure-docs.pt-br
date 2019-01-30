---
title: Usando o portal de administração do Azure Stack | Microsoft Docs
description: Como um operador do Azure Stack, saiba como usar o portal de administração.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/07/2019
ms.author: jeffgilb
ms.reviewer: ''
ms.lastreviewed: 01/07/2019
ms.openlocfilehash: dacd628098d263234f56b20879a5ac2fdf87a809
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243157"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>Início rápido: usar o portal de administração do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Há dois portais no Azure Stack; o portal de administração e o portal do usuário (também conhecido como o *locatário* portal.) Como um operador de pilha do Azure, você pode usar o portal de administração para operações do Azure Stack e o gerenciamento diários.

## <a name="access-the-administrator-portal"></a>Acessar o portal do administrador

Para acessar o portal do administrador, navegue até a URL de portal e de entrada, usando as credenciais de um operador do Azure Stack. Para um sistema integrado, o portal de que URL varia com base no nome da região e nome de domínio totalmente qualificado (FQDN) externo de sua implantação do Azure Stack. O portal de administração de URL é sempre o mesmo para implantações do Azure Stack desenvolvimento ASDK (Kit). 

| Ambiente | URL do Portal do administrador |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| Sistemas integrados | https://adminportal.&lt;*region*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> Para um ambiente ASDK, você precisa primeiro certifique-se de que você pode [conectado ao host do kit de desenvolvimento](azure-stack-connect-azure-stack.md) por meio de Conexão de área de trabalho remota ou uma rede virtual privada (VPN).

 ![O portal de administração](media/azure-stack-manage-portals/admin-portal.png)

O fuso horário de padrão para todas as implantações do Azure Stack é definido para o tempo Universal Coordenado (UTC). 

No portal do administrador, você pode fazer coisas como:

* [Registre-se a pilha do Azure com o Azure](azure-stack-registration.md)
* [Preencher o marketplace](azure-stack-download-azure-marketplace-item.md)
* [Criar planos, ofertas e assinaturas para usuários](azure-stack-plan-offer-quota-overview.md)
* [Monitorar integridade e alertas](azure-stack-monitor-health.md)
* [Gerenciar atualizações do Azure Stack](azure-stack-updates.md)

O **tutorial de início rápido** lado a lado fornece links para documentação online para as tarefas mais comuns.

Embora um operador pode criar recursos, como máquinas virtuais, redes virtuais e contas de armazenamento no portal de administração, você deve [entrar no portal do usuário](user/azure-stack-use-portal.md) para criar e testar recursos.

>[!NOTE]
>O **criar uma máquina virtual** link no bloco do tutorial de início rápido ajuda a criar uma máquina virtual no portal de administração, mas isso é destinado apenas para validar que o Azure Stack foi implantado com êxito.

## <a name="understand-subscription-behavior"></a>Entender o comportamento de assinatura

Há três assinaturas criadas por padrão no portal de administração; consumo de provedor padrão e de medição. Como um operador, você poderá usar principalmente a *assinatura do provedor padrão*. Você não pode adicionar quaisquer outras assinaturas e usá-los no portal de administração. 

Outras assinaturas são criadas por usuários no portal do usuário com base nos planos e ofertas que você cria para eles. No entanto, o portal do usuário não fornece acesso a qualquer uma das funcionalidades administrativas ou operacionais do portal de administração.

Os portais de administração e de usuário são apoiados por instâncias separadas do Azure Resource Manager. Devido a essa separação do Azure Resource Manager, as assinaturas não cruzam portais. Por exemplo, se você, como um operador de pilha do Azure, entrar no portal do usuário, você não poderá acessar o *assinatura do provedor padrão*. Embora você não tiver acesso a todas as funções administrativas, você pode criar assinaturas por conta própria de ofertas públicas disponíveis. Desde que você está conectado ao portal do usuário, você é considerado um usuário do locatário.

  >[!NOTE]
  >Em um ambiente ASDK, se um usuário pertencer ao mesmo diretório do locatário como o operador de pilha do Azure, eles não estão impedidos de entrar no portal de administração. No entanto, eles não podem acessar qualquer uma das funções administrativas ou adicionar assinaturas para acessar oferece que estão disponíveis para eles no portal do usuário.

## <a name="administration-portal-tips"></a>Dicas de portal de administração

### <a name="customize-the-dashboard"></a>Personalizar o painel

O painel contém um conjunto de blocos padrão. Você pode selecionar **Editar painel** para modificar o painel padrão ou selecione **novo painel** para adicionar um painel personalizado. Você pode adicionar facilmente os blocos em um painel. Por exemplo, você pode selecionar **+ criar um recurso**, clique com botão direito **oferece + planos**e, em seguida, selecione **fixar no painel**.

Às vezes, você poderá ver um painel em branco no portal. Para recuperar o painel, clique em **Editar painel**e, em seguida, clique com botão direito e selecione **retornarão ao estado padrão**.

### <a name="quick-access-to-online-documentation"></a>Acesso rápido a documentação online

Para acessar a documentação do operador do Azure Stack, use a Ajuda e suporte de ícone (ponto de interrogação) no canto superior direito do portal do administrador. Mova o cursor para o ícone e, em seguida, selecione **ajuda + suporte**.

### <a name="quick-access-to-help-and-support"></a>Acesso rápido a Ajuda e suporte

Se você selecionar o ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, selecione **nova solicitação de suporte**, ocorra um dos seguintes resultados:

- Se você estiver usando um sistema integrado, essa ação abrirá um site onde você pode abrir diretamente um tíquete de suporte com serviços de suporte de cliente do Microsoft (CSS). Consulte a [onde obter suporte](azure-stack-manage-basics.md#where-to-get-support) para entender quando você deve entrar por meio do suporte da Microsoft ou por meio de suporte do fornecedor de hardware seu fabricante de equipamento original (OEM).
- Se você estiver usando o ASDK, essa ação abre o [site dos fóruns do Azure Stack](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) diretamente. Esses fóruns são monitorados regularmente. Como o ASDK é um ambiente de avaliação, não há nenhum oficial de suporte oferecido por meio do Microsoft CSS.

### <a name="quick-access-to-the-azure-roadmap"></a>Acesso rápido para o roteiro do Azure

Se você selecionar **ajuda e suporte** (o ponto de interrogação) no canto superior direito da administração do portal e, em seguida, selecione **roteiro do Azure**, uma nova guia do navegador é aberta e leva você até o roteiro do Azure. Digitando **do Azure Stack** na **produtos** caixa de pesquisa, você pode ver todas as atualizações de roteiro do Azure Stack.

## <a name="next-steps"></a>Próximas etapas

[Registre-se a pilha do Azure com o Azure](azure-stack-registration.md) e preencha o [marketplace do Azure Stack](azure-stack-marketplace.md) com itens para oferecer a seus usuários. 
