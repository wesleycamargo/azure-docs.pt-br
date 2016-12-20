---
title: "Gerenciar perfis do Gerenciador de Tráfego do Azure | Microsoft Docs"
description: "Este artigo ajudará você a criar, desabilitar, habilitar, excluir e exibir o histórico de um perfil do Gerenciador de Tráfego do Azure."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 7e7de7dc1eca6903403afef03fdd6afb98ff16c9

---

# <a name="manage-an-azure-traffic-manager-profile"></a>Gerenciar um perfil de Gerenciador de tráfego do Azure

Perfis do Gerenciador de Tráfego usam métodos de roteamento de tráfego para controlar a distribuição de tráfego para os serviços de nuvem ou pontos de extremidade de site. Este artigo explica como criar e gerenciar esses perfis.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Criar um perfil do Gerenciador de Tráfego usando a Criação Rápida

Você pode criar rapidamente um perfil do Gerenciador de Tráfego usando a Criação Rápida no portal clássico do Azure. Criação Rápida permite criar perfis com configurações básicas. No entanto, você não pode usar a Criação Rápida para configurações como do conjunto de pontos de extremidade (serviços de nuvem e sites), da ordem de failover para o método de roteamento de tráfego de failover ou configurações de monitoramento. Depois de criar seu perfil, você pode definir essas configurações no portal clássico do Azure. O Gerenciador de Tráfego dá suporte a até 200 pontos de extremidade por perfil. No entanto, a maioria dos cenários de uso exigem apenas um pequeno número de pontos de extremidade.

### <a name="to-create-a-traffic-manager-profile"></a>Para criar um perfil do Gerenciador de Tráfego

1. **Implante seus serviços de nuvem e sites no ambiente de produção.** Para obter mais informações sobre serviços de nuvem, consulte [Serviços de Nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obter mais informações sobre sites, consulte [Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Faça logon no portal clássico do Azure.** Clique em **Novo** na parte inferior esquerda do portal, clique em **Serviços de Rede > Gerenciador de Tráfego**, e, em seguida, clique em **Criação Rápida** para começar a configurar seu perfil.
3. **Configure o prefixo DNS.**  Dê ao seu perfil do gerenciador de tráfego um nome do prefixo DNS único. Você pode especificar apenas o prefixo para um nome de domínio do Gerenciador de Tráfego.
4. **Selecione a assinatura.**  Selecione a assinatura do Azure apropriada. Cada perfil está associado uma única assinatura. Se você tiver apenas uma assinatura, esta opção não aparecerá.
5. **Selecione o método de roteamento de tráfego.** Selecione o método de roteamento de tráfego em **Política de roteamento de tráfego**. Para obter mais informações sobre os métodos de roteamento de tráfego, consulte [Sobre os métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md).
6. **Clique em "Criar" para criar o perfil**. Quando a configuração do perfil for concluída, você poderá localizar seu perfil no painel do Gerenciador de Tráfego, no portal clássico do Azure.
7. **Configure pontos de extremidade, monitoramento e configurações adicionais no portal clássico do Azure.** Usar a Criação Rápida configura apenas as configurações básicas. É necessário definir configurações adicionais, como a lista de pontos de extremidade e a ordem de failover do ponto de extremidade.

## <a name="disable-enable-or-delete-a-profile"></a>Desabilitar, habilitar ou excluir um perfil

Você pode desabilitar um perfil existente para que o Gerenciador de Tráfego não referencie solicitações de usuários para os pontos de extremidade configurados. Quando você desabilita um perfil do Gerenciador de Tráfego, o próprio perfil e as informações contidas nele permanecem intactos e podem ser editados na interface do Gerenciador de Tráfego.  As referências retomam quando você reabilita o perfil. Quando você cria um perfil do Gerenciador de Tráfego no portal clássico do Azure, ele é automaticamente habilitado. Se você decidir que um perfil não é mais necessário, poderá excluí-lo.

### <a name="to-disable-a-profile"></a>Para desabilitar um perfil

1. Se você estiver usando um nome de domínio personalizado, altere o registro CNAME em seu servidor DNS da Internet para que ele não aponte para o perfil do Gerenciador de Tráfego.
2. O tráfego deixará de ser direcionado para os pontos de extremidade por meio das configurações de perfil do Gerenciador de Tráfego.
3. Selecione o perfil que você deseja desabilitar. Na página do Gerenciador de Tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Observe, clicar no nome do perfil ou na seta ao lado do nome abre a página de configurações do perfil.
4. Depois de selecionar o perfil, clique em **Desabilitar** na parte inferior da página.

### <a name="to-enable-a-profile"></a>Para habilitar um perfil

1. Selecione o perfil que você deseja desabilitar. Na página do Gerenciador de Tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Observe, clicar no nome do perfil ou na seta ao lado do nome abre a página de configurações do perfil.
2. Depois de selecionar o perfil, clique em **Habilitar** na parte inferior da página.
3. Se você estiver usando um nome de domínio personalizado, crie um registro de recurso CNAME em seu servidor DNS da Internet para apontar para o nome de domínio de seu perfil do Gerenciador de Tráfego.
4. O tráfego é novamente direcionado para os pontos de extremidade.

### <a name="to-delete-a-profile"></a>Para excluir um perfil

1. Verifique se o registro de recurso DNS no servidor DNS da Internet não usa mais um registro de recurso CNAME que aponta para o nome de domínio de seu perfil do Gerenciador de Tráfego.
2. Selecione o perfil que você deseja desabilitar. Na página do Gerenciador de Tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Observe, clicar no nome do perfil ou na seta ao lado do nome abre a página de configurações do perfil.
3. Depois de selecionar o perfil, clique em **Excluir** na parte inferior da página.

## <a name="view-traffic-manager-profile-change-history"></a>Exibir histórico de alterações de perfil do Gerenciador de Tráfego

Você pode exibir o histórico de alterações para o perfil do Gerenciador de Tráfego no portal clássico do Azure nos Serviços de Gerenciamento.

### <a name="to-view-your-traffic-manager-change-history"></a>Para exibir o histórico de alterações do Gerenciador de Tráfego

1. No painel esquerdo do portal clássico do Azure, clique em **Serviços de Gerenciamento**.
2. Na página dos Serviços de Gerenciamento, clique em **Logs de Operação**.
3. Na página Logs de Operação, você pode filtrar para exibir o histórico de alterações para o seu perfil do Gerenciador de Tráfego. Depois de selecionar as opções de filtragem, clique na marca de seleção para exibir os resultados.

   * Para exibir as alterações para todos os seus perfis, selecione o intervalo de tempo e a assinatura e, em seguida, selecione **Gerenciador de Tráfego** no menu de atalho **Tipo**.
   * Para filtrar pelo nome do perfil, digite o nome do perfil no campo **Nome do Serviço** ou selecione-o no menu de atalho.
   * Para exibir detalhes de cada alteração individual, selecione a linha com a alteração que você deseja exibir e clique em **Detalhes** na parte inferior da página. Na janela **Detalhes da Operação**, você pode exibir a representação XML do objeto da API que foi criado ou atualizado como parte da operação.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar um ponto de extremidade](traffic-manager-endpoints.md)
* [Configurar o método de roteamento de failover](traffic-manager-configure-failover-routing-method.md)
* [Configurar o método de roteamento de round robin](traffic-manager-configure-round-robin-routing-method.md)
* [Configurar o método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)
* [Apontar um domínio de Internet da empresa para um nome de domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md)
* [Solucionando problemas de estado degradado do Gerenciador de Tráfego](traffic-manager-troubleshooting-degraded.md)



<!--HONumber=Nov16_HO5-->


