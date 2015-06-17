<properties
   pageTitle="Gerenciar perfis do Gerenciador de Tráfego"
   description="Este artigo ajudará você criar, desabilitar, habilitar, excluir e exibir o histórico de um perfil do Gerenciador de Tráfego."
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Gerenciar um perfil do Gerenciador de Tráfego

Você utiliza um perfil do Gerenciador de Tráfego para especificar quais pontos de extremidade de sites ou de serviços de nuvem serão monitorados pelo Gerenciador de Tráfego, e qual método de balanceamento de carga você deseja usar para distribuir conexões para tais pontos de extremidade. 

## Criar um perfil do Gerenciador de Tráfego usando Criação Rápida

Você pode criar rapidamente um perfil do Gerenciador de Tráfego usando **Criação Rápida** no Portal de Gerenciamento. Criação Rápida permite criar perfis com configurações básicas. No entanto, você não pode usar a Criação Rápida para configurações como do conjunto de pontos de extremidade (serviços de nuvem e sites), da ordem de failover para o método de balanceamento de carga de failover ou configurações de monitoramento. Depois de criar seu perfil, você pode definir essas configurações no Portal de Gerenciamento. Consulte [tarefas de configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx) para obter uma lista de procedimentos de configuração.

### Para criar um novo perfil do Gerenciador de Tráfego

1. **Implante seus serviços de nuvem e sites em seu ambiente de produção.** Para obter mais informações sobre serviços de nuvem, consulte [Serviços de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obter informações sobre serviços de nuvem, consulte [Práticas recomendadas](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile). Para obter mais informações sobre sites, consulte [Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Faça logon no Portal de Gerenciamento.** Para criar um novo perfil do Gerenciador de Tráfego, clique em **Novo** no canto inferior esquerdo do portal, clique em **Serviços de Rede**, clique em **Gerenciador de Tráfego**; em seguida, clique em **Criação Rápida** para começar a configurar seu perfil.
3. **Configure o prefixo DNS.** Dê a seu perfil do Gerenciador de Tráfego um nome de prefixo DNS exclusivo. Você pode especificar apenas o prefixo para um nome de domínio do Gerenciador de Tráfego.
4. **Selecione a assinatura.** Selecione a assinatura adequada do Azure. Cada perfil está associado a uma única assinatura. Se você tem apenas uma assinatura, esta opção não aparece.
5. **Selecione o método de balanceamento de carga.** Selecione o método de balanceamento de carga em **Política de Balanceamento de Carga**. Para obter mais informações sobre os métodos de balanceamento de carga, consulte [Sobre métodos de balanceamento de carga do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md).
6. **Clique em "Criar" para criar seu novo perfil**. Quando a configuração do perfil for concluída, você poderá localizar seu perfil no painel do Gerenciador de Tráfego, no Portal de Gerenciamento.
7. **Configure pontos de extremidade, monitoramento e configurações adicionais no Portal de Gerenciamento.** Já que você só pode configurar configurações básicas usando a Criação Rápida, é necessário definir configurações adicionais, como a lista de pontos de extremidade e a ordem de failover do ponto de extremidade, para concluir a configuração desejada. Consulte [Tarefas de configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx) para obter uma lista de procedimentos para ajudá-lo a concluir a configuração.

## Habilitar, desabilitar ou excluir um perfil

Você pode desabilitar um perfil existente do Gerenciador de Tráfego para que ele não faça referência a solicitações do usuário para pontos de extremidade configurados nesse perfil. Quando você desabilita um perfil do Gerenciador de Tráfego, o próprio perfil e as informações contidas nele permanecerão intactos e poderão ser editados na interface do Gerenciador de Tráfego. Quando você quiser reabilitar o perfil, você pode fazer isso facilmente no Portal de Gerenciamento e as referências serão retomadas. Quando você cria um perfil do Gerenciador de Tráfego no Portal de Gerenciamento, ele é habilitado automaticamente. Se você decidir que um perfil não é mais necessário, você poderá excluí-lo. 

### Para desabilitar um perfil

1. Modifique o registro de recurso DNS no servidor DNS da Internet para usar o tipo de registro apropriado e um apontador para outro nome ou para o endereço IP de um local específico na Internet. Em outras palavras, altere o registro de recurso DNS no servidor DNS da Internet para que ele não use mais um registro de recurso CNAME que aponte para o nome de domínio do seu perfil do Gerenciador de Tráfego.
2. O tráfego vai deixar de ser direcionado para os pontos de extremidade por meio das configurações de perfil do Gerenciador de Tráfego.
3. Selecione o perfil que você deseja desabilitar. Para selecionar o perfil, na página do Gerenciador de Tráfego e realce o perfil, clicando na coluna ao lado do nome do perfil. Não clique no nome do perfil nem na seta ao lado do nome, já que isso levaria você à página de configurações do perfil.
4. Após selecionar o perfil, clique em **Desabilitar**, na parte inferior da página.

### Para habilitar um perfil

1. Selecione o perfil que você deseja habilitar. Para selecionar o perfil, na página do Gerenciador de Tráfego e realce o perfil, clicando na coluna ao lado do nome do perfil. Não clique no nome do perfil nem na seta ao lado do nome, já que isso levaria você à página de configurações do perfil.
2. Após selecionar o perfil, clique em **Habilitar**, na parte inferior da página.
3. Modifique o registro de recurso DNS em seu servidor DNS da Internet para usar o tipo de registro CNAME, que mapeia o seu nome de domínio de empresa para o nome de domínio do seu perfil do Gerenciador de Tráfego. Para obter mais informações, consulte [Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md).
4. O tráfego começará a ser direcionado novamente para os pontos de extremidade.

### Para excluir um perfil

1. Certifique-se de que o registro de recurso DNS no seu servidor DNS da Internet não use mais um registro de recurso CNAME que aponte para o nome de domínio do seu perfil do Gerenciador de Tráfego.
2. Selecione o perfil que você deseja excluir. Para selecionar o perfil, na página do Gerenciador de Tráfego e realce o perfil, clicando na coluna ao lado do perfil. Não clique no nome do perfil nem na seta ao lado do nome, já que isso levaria você à página de configurações do perfil.
4. Após selecionar o perfil, clique em **Excluir**, na parte inferior da página.

## Exibir histórico de alterações de perfil do Gerenciador de Tráfego

Você pode exibir o histórico de alterações para o perfil do Gerenciador de Tráfego no Portal de Gerenciamento nos Serviços de Gerenciamento.

### Para exibir o histórico de alterações do Gerenciador de Tráfego

1. No painel esquerdo do Portal de Gerenciamento, clique em **Serviços de Gerenciamento**.
2. Na página dos Serviços de Gerenciamento, clique em **Logs de Operação**.
3. Na página Logs de Operação, você pode filtrar para exibir o histórico de alterações para o seu perfil do Gerenciador de Tráfego. Depois de selecionar as opções de filtragem, clique na marca de seleção para exibir os resultados.
   - Para exibir alterações do perfil para todos os perfis, selecione o intervalo de tempo e de assinatura e selecione **Gerenciador de Tráfego** na lista suspensa **Tipo**.
   - Para filtrar pelo nome do perfil, digite o nome do perfil no campo **Nome do Serviço** ou selecione-o na lista suspensa.
   - Para exibir detalhes de cada alteração individual, selecione a linha com a alteração que você deseja exibir e clique em **Detalhes** na parte inferior da página. Na janela **Detalhes da Operação**, você pode exibir a representação XML do objeto da API que foi criado ou atualizado como parte da operação e copiar o código XML para a área de transferência.


## Consulte também

[Visão geral do Gerenciador de Tráfego](traffic-manager-overview.md)

[Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md)

[Sobre o monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md)

[Tarefas de configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Serviços de Nuvem](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

<!--HONumber=49--> 