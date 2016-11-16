---
title: "Gerenciar pontos de extremidade no Gerenciador de Tráfego do Azure | Microsoft Docs"
description: "Este artigo o ajudará a adicionar, remover, habilitar e desabilitar pontos de extremidade do Gerenciador de Tráfego do Azure."
services: traffic-manager
documentationcenter: 
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: ade2bbc2-35a7-43c5-8001-4698f7254526
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6af6652d39fad5812c15e19fa29c757595a78b6

---

# <a name="add-disable-enable-or-delete-endpoints"></a>Adicionar, desabilitar, habilitar ou excluir pontos de extremidade

O recurso de aplicativos Web no Serviço de Aplicativo do Azure já fornecem failover e funcionalidade de roteamento de tráfego de round robin para sites em um datacenter, independentemente do modo do site. O Gerenciador de Tráfego do Azure permite que você especifique o failover e o roteamento de tráfego para sites e serviços de nuvem em datacenters diferentes. A primeira etapa necessária fornecer essa funcionalidade é adicionar o serviço de nuvem ou ponto de extremidade de site ao Gerenciador de Tráfego.

> [!NOTE]
> Este artigo explica como usar o Portal Clássico. O Portal Clássico do Azure oferece suporte apenas a criação e atribuição de serviços de nuvem e aplicativos Web como pontos de extremidade. O novo [Portal do Azure](https://portal.azure.com) é a interface preferida.

Você também pode desabilitar pontos de extremidade individuais que fazem parte de um perfil do Gerenciador de Tráfego. A desabilitação de um ponto de extremidade o mantém como parte do perfil, mas o perfil age como se o ponto de extremidade não estivesse incluído nele. Essa ação é útil para remover temporariamente um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Depois que o ponto de extremidade estiver funcionando novamente, ele poderá ser habilitado

> [!NOTE]
> A desabilitação de um ponto de extremidade não tem nada a ver com seu estado de implantação no Azure. Um ponto de extremidade íntegro permanecerá ativo e capaz de receber tráfego mesmo quando for desabilitado no Gerenciador de Tráfego. Além disso, a desabilitação de um ponto de extremidade em um perfil não afeta seu status em outro perfil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Para adicionar um serviço de nuvem ou ponto de extremidade de site

1. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar. Para abrir a página de configurações, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **Pontos de extremidade** para exibir os pontos de extremidade que já fazem parte de sua configuração.
3. Na parte inferior da página, clique em **Adicionar** para acessar a página **Adicionar Pontos de Extremidade de Serviço**. Por padrão, a página lista os serviços de nuvem em **Pontos de Extremidade de Serviço**.
4. Para serviços de nuvem, selecione os serviços de nuvem na lista para adicioná-los como pontos de extremidade para esse perfil. Limpar o nome do serviço de nuvem o remove da lista de pontos de extremidade.
5. Para sites, clique na lista suspensa **Tipo de Serviço** e selecione **Aplicativo Web**.
6. Selecione os sites na lista para adicioná-los como pontos de extremidade a esse perfil. Limpar o nome do site o remove da lista de pontos de extremidade. Você só pode selecionar um único site por datacenter do Azure (também conhecido como região). Quando você seleciona o primeiro site, os outros sites no mesmo datacenter ficam indisponíveis para seleção. Observe também que apenas sites padrão são listados.
7. Depois que você selecionar os pontos de extremidade para esse perfil, clique na marca de seleção no canto inferior direito para salvar suas alterações.

> [!NOTE]
> Depois de adicionar ou remover um ponto de extremidade de um perfil usando o método de roteamento de tráfego *Failover*, a lista de prioridade de failover poderá não ser ordenada da forma desejada. Você pode ajustar a ordem da Lista de Prioridade de Failover na página de Configuração. Para obter mais informações, consulte [Configurar roteamento de tráfego de Failover](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Para desabilitar um ponto de extremidade

1. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar. Para abrir a página de configurações, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **Pontos de Extremidade** para exibir os pontos de extremidade incluídos em sua configuração.
3. Clique no ponto de extremidade que você deseja desabilitar e, em seguida, clique em **Desabilitar** na parte inferior da página.
4. Os clientes continuam a enviar o tráfego para o ponto de extremidade durante o tempo de vida (TTL). Você pode alterar a TTL na página Configuração do perfil do Gerenciador de Tráfego.

## <a name="to-enable-an-endpoint"></a>Para habilitar um ponto de extremidade

1. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar. Para abrir a página de configurações, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **Pontos de Extremidade** para exibir os pontos de extremidade incluídos em sua configuração.
3. Clique no ponto de extremidade que você deseja habilitar e, em seguida, clique em **Habilitar** na parte inferior da página.
4. Os clientes são direcionados para o ponto de extremidade habilitado, conforme determinado pelo perfil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Para excluir um serviço de nuvem ou ponto de extremidade de site

1. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar. Para abrir a página de configurações, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **Pontos de extremidade** para exibir os pontos de extremidade que já fazem parte de sua configuração.
3. Na página Pontos de Extremidade, clique no nome do ponto de extremidade que você deseja excluir do perfil.
4. Na parte inferior da página, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar perfis do Gerenciador de Tráfego](traffic-manager-manage-profiles.md)
* [Configurar métodos de roteamento](traffic-manager-configure-routing-method.md)
* [Solucionando problemas de estado degradado do Gerenciador de Tráfego](traffic-manager-troubleshooting-degraded.md)
* [Considerações sobre desempenho do Gerenciador de Tráfego](traffic-manager-performance-considerations.md)
* [Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO2-->


