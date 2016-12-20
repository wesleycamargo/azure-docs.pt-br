---
title: "Desabilitar, habilitar ou excluir um perfil do Gerenciador de Tráfego| Microsoft Docs"
description: "Este artigo o ajudará a trabalhar com os perfis do Gerenciador de Tráfego."
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: f19143e2-2f4f-4ead-bb00-89b95ac92cb4
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3ea5860767cd21d263c0f0126362d3f6d92d58c6


---
<!-- repub for nofollow -->

# <a name="disable-enable-or-delete-a-profile"></a>Desabilitar, habilitar ou excluir um perfil
Você pode desabilitar um perfil existente do Gerenciador de Tráfego para que ele não referencie solicitações de usuários para seus pontos de extremidade configurados. Quando você desabilita um perfil do Gerenciador de Tráfego, o próprio perfil e as informações contidas nele permanecem intactos e podem ser editados na interface do Gerenciador de Tráfego. Quando quiser habilitar o perfil novamente, você poderá fazer isso facilmente no Portal do Azure, e as referências serão retomadas. Quando você cria um perfil do Gerenciador de Tráfego no Portal do Azure, ele é automaticamente habilitado.

## <a name="to-disable-a-profile"></a>Para desabilitar um perfil
1. Modifique o registro de recurso DNS no servidor DNS da Internet para usar o tipo de registro e ponteiro apropriados para outro nome ou o endereço IP de um local específico na Internet. Em outras palavras, altere o registro de recurso DNS no servidor DNS da Internet para que ele não use um registro de recurso CNAME que aponte para o nome de domínio de seu perfil do Gerenciador de Tráfego.
2. O tráfego deixará de ser direcionado para os pontos de extremidade por meio das configurações de perfil do Gerenciador de Tráfego.
3. Selecione o perfil que você deseja desabilitar. Para selecionar o perfil, na página do Gerenciador de Tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Não clique no nome do perfil ou na seta ao lado do nome, pois você será levado para a página de configurações do perfil.
4. Depois de selecionar o perfil, clique em Desabilitar na parte inferior da página.

## <a name="to-enable-a-profile"></a>Para habilitar um perfil
1. Selecione o perfil que você deseja habilitar. Para selecionar o perfil, na página do Gerenciador de Tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Não clique no nome do perfil ou na seta ao lado do nome, pois você será levado para a página de configurações do perfil.
2. Depois de selecionar o perfil, clique em Habilitar na parte inferior da página.
3. Modifique o registro de recurso DNS no servidor DNS da Internet para usar o tipo de registro CNAME, que mapeia o nome de domínio da empresa para o nome de domínio de seu perfil do Gerenciador de Tráfego. Para obter mais informações, consulte [Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md).
4. O tráfego começará a ser direcionado para os pontos de extremidade novamente.

## <a name="delete-a-profile"></a>Excluir um perfil
1. Verifique se o registro de recurso DNS no servidor DNS da Internet não usa mais um registro de recurso CNAME que aponta para o nome de domínio de seu perfil do Gerenciador de Tráfego.
2. Selecione o perfil que você deseja excluir. Para selecionar o perfil, na página do Gerenciador de Tráfego, realce o perfil
3. clicando na coluna ao lado do perfil. Não clique no nome do perfil ou na seta ao lado do nome, pois você será levado para a página de configurações do perfil.
4. Depois de selecionar o perfil, clique em Excluir na parte inferior da página.

## <a name="next-steps"></a>Próximas etapas
[Gerenciador de Tráfego - Desabilitar ou habilitar um ponto de extremidade](disable-or-enable-an-endpoint.md)

[Configurar o método de roteamento de failover](traffic-manager-configure-failover-routing-method.md)

[Configurar o método de roteamento de round robin](traffic-manager-configure-round-robin-routing-method.md)

[Configurar o método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)

[Solucionando problemas de estado degradado do Gerenciador de Tráfego](traffic-manager-troubleshooting-degraded.md)




<!--HONumber=Nov16_HO3-->


