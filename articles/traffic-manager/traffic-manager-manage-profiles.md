<properties
   pageTitle="Gerenciar perfis do Gerenciador de Tráfego do Azure | Microsoft Azure"
   description="Este artigo ajudará você a criar, desabilitar, habilitar, excluir e exibir o histórico de um perfil do Gerenciador de Tráfego do Azure."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma" />

# Gerenciar um perfil de Gerenciador de tráfego do Azure

Você utiliza um perfil do Gerenciador de Tráfego para especificar quais pontos de extremidade de sites ou de serviços de nuvem serão monitorados pelo Gerenciador de Tráfego, e qual método de balanceamento de carga você deseja usar para distribuir conexões para estes pontos de extremidade.

## Criar um perfil do Gerenciador de Tráfego usando a Criação Rápida

Você pode criar rapidamente um perfil do Gerenciador de Tráfego usando a Criação Rápida no portal do Azure. Criação Rápida permite criar perfis com configurações básicas. No entanto, você não pode usar a Criação Rápida para configurações como do conjunto de pontos de extremidade (serviços de nuvem e sites), da ordem de failover para o método de balanceamento de carga de failover ou configurações de monitoramento. Depois de criar seu perfil, você pode definir essas configurações no Portal do Azure. O Gerenciador de Tráfego dá suporte a até 200 pontos de extremidade por perfil. No entanto, a maioria dos cenários de uso exigem apenas um pequeno número de pontos de extremidade. Consulte [Tarefas de configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx) para obter uma lista de procedimentos de configuração.

### Para criar um novo perfil do Gerenciador de Tráfego

1. **Implante seus serviços de nuvem e sites no ambiente de produção.** Para obter mais informações sobre serviços de nuvem, consulte [Serviços de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obter mais informações sobre serviços de nuvem, consulte [Melhores práticas](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile). Para obter mais informações sobre sites, consulte [Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Faça logon no Portal do Azure.** Para criar um novo perfil do Gerenciador de Tráfego, clique em **Novo** no canto inferior esquerdo do portal, clique em **Serviços de Rede > Gerenciador de Tráfego** e depois clique em **Criação Rápida** para começar a configurar seu perfil.
3. **Configure o prefixo DNS.** Dê ao seu perfil do gerenciador de tráfego um nome do prefixo DNS único. Você pode especificar apenas o prefixo para um nome de domínio do Gerenciador de Tráfego.
4. **Selecione a assinatura.** Selecione a assinatura do Azure apropriada. Cada perfil está associado uma única assinatura. Se você tiver apenas uma assinatura, esta opção não aparecerá.
5. **Selecione o método de balanceamento de carga.** Selecione o método de balanceamento de carga na **Política de balanceamento de carga**. Para obter informações sobre métodos de balanceamento de carga, consulte [Sobre métodos de balanceamento de carga do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md).
6. **Clique em "Criar" para criar seu novo perfil**. Quando a configuração do perfil for concluída, você poderá localizar seu perfil no painel do Gerenciador de Tráfego, no Portal do Azure.
7. **Configure pontos de extremidade, monitoramento e configurações adicionais no Portal do Azure.** Como você só pode definir configurações básicas usando a Criação Rápida, é necessário definir configurações adicionais, como a lista de pontos de extremidade e a ordem de failover do ponto de extremidade, para concluir a configuração desejada. Consulte [Tarefas de configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx) para obter uma lista de procedimentos, para ajudá-lo a concluir a configuração.

## Desabilitar, habilitar ou excluir um perfil

Você pode desabilitar um perfil existente do Gerenciador de Tráfego para que ele não referencie solicitações de usuários para seus pontos de extremidade configurados. Quando você desabilita um perfil do Gerenciador de Tráfego, o próprio perfil e as informações contidas nele permanecem intactos e podem ser editados na interface do Gerenciador de Tráfego. Quando quiser habilitar o perfil novamente, você poderá fazer isso facilmente no Portal do Azure, e as referências serão retomadas. Quando você cria um perfil do Gerenciador de Tráfego no Portal do Azure, ele é automaticamente habilitado. Se você decidir que um perfil não é mais necessário, você poderá excluí-lo.

### Para desabilitar um perfil

1. Modifique o registro de recurso DNS no servidor DNS da Internet para usar o tipo de registro e ponteiro apropriados para outro nome ou o endereço IP de um local específico na Internet. Em outras palavras, altere o registro de recurso DNS no servidor DNS da Internet para que ele não use um registro de recurso CNAME que aponte para o nome de domínio de seu perfil do Gerenciador de Tráfego.
2. O tráfego deixará de ser direcionado para os pontos de extremidade por meio das configurações de perfil do Gerenciador de Tráfego.
3. Selecione o perfil que você deseja desabilitar. Para selecionar o perfil, na página do Gerenciador de Tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Não clique no nome do perfil ou na seta ao lado do nome, pois você será levado para a página de configurações do perfil.
4. Depois de selecionar o perfil, clique em **Desabilitar** na parte inferior da página.

### Para habilitar um perfil

1. Selecione o perfil que você deseja habilitar. Para selecionar o perfil, na página do Gerenciador de Tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Não clique no nome do perfil ou na seta ao lado do nome, pois você será levado para a página de configurações do perfil.
2. Depois de selecionar o perfil, clique em **Habilitar** na parte inferior da página.
3. Modifique o registro de recurso DNS no servidor DNS da Internet para usar o tipo de registro CNAME, que mapeia o nome de domínio da empresa para o nome de domínio de seu perfil do Gerenciador de Tráfego. Para obter mais informações, consulte [Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md).
4. O tráfego começará a ser direcionado para os pontos de extremidade novamente.

### Para excluir um perfil

1. Verifique se o registro de recurso DNS no servidor DNS da Internet não usa mais um registro de recurso CNAME que aponta para o nome de domínio de seu perfil do Gerenciador de Tráfego.
2. Selecione o perfil que você deseja excluir. Para selecionar o perfil, na página do Gerenciador de Tráfego e realce o perfil, clicando na coluna ao lado do perfil. Não clique no nome do perfil ou na seta ao lado do nome, pois você será levado para a página de configurações do perfil.
4. Depois de selecionar o perfil, clique em **Excluir** na parte inferior da página.

## Exibir histórico de alterações de perfil do Gerenciador de Tráfego

Você pode exibir o histórico de alterações para o perfil do Gerenciador de Tráfego no Portal do azure nos Serviços de Gerenciamento.

### Para exibir o histórico de alterações do Gerenciador de Tráfego

1. No painel esquerdo do Portal do Azure, clique em **Serviços de Gerenciamento**.
2. Na página dos Serviços de Gerenciamento, clique em **Logs de Operação**.
3. Na página Logs de Operação, você pode filtrar para exibir o histórico de alterações para o seu perfil do Gerenciador de Tráfego. Depois de selecionar as opções de filtragem, clique na marca de seleção para exibir os resultados.
   - Para exibir alterações do perfil para todos os perfis, selecione o intervalo de tempo e a assinatura e selecione **Gerenciador de Tráfego** no menu de atalho **Tipo**.
   - Para filtrar pelo nome do perfil, digite o nome do perfil no campo **Nome do Serviço** ou selecione-o no menu de atalho.
   - Para exibir detalhes de cada alteração individual, selecione a linha com a alteração que você deseja exibir e clique em **Detalhes** na parte inferior da página. Na janela **Detalhes da Operação**, você pode exibir a representação XML do objeto de API que foi criado ou atualizado como parte da operação e copiar o código XML na área de transferência.


## Recursos adicionais

[Visão geral do Gerenciador de Tráfego](traffic-manager-overview.md)

[Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md)

[Sobre o monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md)

[Tarefas de Configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Serviços de Nuvem](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

<!---HONumber=August15_HO7-->