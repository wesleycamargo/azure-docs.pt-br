<properties
   pageTitle="Gerenciar pontos de extremidade no Gerenciador de Tráfego"
   description="Este artigo o ajudará a adicionar, remover, habilitar e desabilitar pontos de extremidade do Gerenciador de Tráfego."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# Adicionar, desabilitar, habilitar ou excluir pontos de extremidade

Os sites do Azure já fornecem failover e funcionalidade de balanceamento de carga de round-robin para sites em um data center, independentemente do modo do site. O Gerenciador de Tráfego permite que você especifique o failover e o balanceamento de carga round-robin para sites e serviços de nuvem em data centers diferentes. A primeira etapa necessária fornecer essa funcionalidade é adicionar o serviço de nuvem ou ponto de extremidade de site ao Gerenciador de Tráfego.

>[AZURE.NOTE]Você não pode adicionar locais externos ou perfis do Gerenciador de Tráfego como pontos de extremidade usando o Portal de Gerenciamento. Você deve usar a API REST [Criar definição](http://go.microsoft.com/fwlink/p/?LinkId=400772) ou o Windows PowerShell [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774).

Você também pode desabilitar pontos de extremidade individuais que fazem parte de um perfil do Gerenciador de Tráfego. Os pontos de extremidade incluem serviços de nuvem e sites. A desabilitação de um ponto de extremidade o mantém como parte do perfil, mas o perfil age como se o ponto de extremidade não estivesse incluído nele. Essa ação é muito útil para remover temporariamente um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Depois que o ponto de extremidade estiver funcionando novamente, ele poderá ser habilitado

>[AZURE.NOTE]A desabilitação de um ponto de extremidade não tem nada a ver com seu estado de implantação no Azure. Um ponto de extremidade íntegro permanecerá ativo e capaz de receber tráfego mesmo quando desabilitado no Gerenciador de Tráfego. Além disso, a desabilitação de um ponto de extremidade em um perfil não afeta seu status em outro perfil.

## Para adicionar um serviço de nuvem ou ponto de extremidade de site


1. No painel do Gerenciador de Tráfego no Portal de Gerenciamento, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
2. Na parte superior da página, clique em **Pontos de extremidade** para exibir os pontos de extremidade que já fazem parte de sua configuração.
3. Na parte inferior da página, clique em **Adicionar** para acessar a página **Adicionar Pontos de Extremidade de Serviço**. Por padrão, a página lista os serviços de nuvem em **Pontos de Extremidade de Serviço**.
4. Para serviços de nuvem, selecione os serviços de nuvem na lista para habilitá-los como pontos de extremidade para esse perfil. Limpar o nome do serviço de nuvem o remove da lista de pontos de extremidade.
5. Para sites, clique no menu suspenso **Tipo de Serviço** e selecione **Site**.
6. Selecione os sites na lista para adicioná-los como pontos de extremidade a esse perfil. Limpar o nome do site o remove da lista de pontos de extremidade. Observe que você só pode selecionar um único site por data center do Azure (também conhecido como região). Se você selecionar um site em um data center que hospeda vários sites, quando selecionar o primeiro site, os outros no mesmo data center ficarão indisponíveis para seleção. Observe também que apenas sites padrão são listados.
7. Depois que você selecionar os pontos de extremidade para esse perfil, clique na marca de seleção no canto inferior direito para salvar suas alterações.

>[AZURE.NOTE]Se você estiver usando o método de balanceamento de carga de *Failover*, depois de adicionar ou remover um ponto de extremidade, não deixe de ajustar a Lista de Prioridade de Failover na página Configuração para refletir a ordem de failover que você deseja para sua configuração. Para obter mais informações, consulte [Configurar o balanceamento de carga de failover](traffic-manager-configure-failover-load-balancing.md).

## Para desabilitar um ponto de extremidade

1. No painel do Gerenciador de Tráfego no Portal de Gerenciamento, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
2. Na parte superior da página, clique em **Pontos de Extremidade** para exibir os pontos de extremidade incluídos em sua configuração.
3. Clique no ponto de extremidade que você deseja desabilitar e, em seguida, clique em **Desabilitar** na parte inferior da página.
4. O tráfego deixará de fluir para o ponto de extremidade com base no TTL (Vida Útil) DNS configurado para o nome de domínio do Gerenciador de Tráfego. Você pode alterar a vida útil na página Configuração do perfil do Gerenciador de Tráfego.

## Para habilitar um ponto de extremidade

1. No painel do Gerenciador de Tráfego no Portal de Gerenciamento, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
2. Na parte superior da página, clique em **Pontos de Extremidade** para exibir os pontos de extremidade incluídos em sua configuração.
3. Clique no ponto de extremidade que você deseja habilitar e, em seguida, clique em **Habilitar** na parte inferior da página.
4. O tráfego começará a fluir para o serviço novamente, conforme orientado pelo perfil.

## Para excluir um serviço de nuvem ou ponto de extremidade de site


1. No painel do Gerenciador de Tráfego no Portal de Gerenciamento, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
2. Na parte superior da página, clique em **Pontos de extremidade** para exibir os pontos de extremidade que já fazem parte de sua configuração.
3. Na página Pontos de Extremidade, clique no nome do ponto de extremidade que você deseja excluir do perfil.
4. Na parte inferior da página, clique em **Excluir**.

>[AZURE.NOTE]Você não pode excluir locais externos ou perfis do Gerenciador de Tráfego como pontos de extremidade usando o Portal de Gerenciamento. Você deve usar o Windows PowerShell. Para obter mais informações, consulte [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## Consulte também

[Visão geral do Gerenciador de Tráfego](traffic-manager-overview.md)

[Sobre o monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md)

[Tarefas de Configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Serviços de Nuvem](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327)
 

<!---HONumber=July15_HO2-->