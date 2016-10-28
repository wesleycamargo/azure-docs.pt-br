<properties
   pageTitle="Gerenciar pontos de extremidade no Gerenciador de Tráfego do Azure | Microsoft Azure"
   description="Este artigo o ajudará a adicionar, remover, habilitar e desabilitar pontos de extremidade do Gerenciador de Tráfego do Azure."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# Adicionar, desabilitar, habilitar ou excluir pontos de extremidade

O recurso de aplicativos Web no Serviço de Aplicativo do Azure já fornecem failover e funcionalidade de roteamento de tráfego de round robin para sites em um datacenter, independentemente do modo do site. O Gerenciador de Tráfego do Azure permite que você especifique o failover e o roteamento de tráfego para sites e serviços de nuvem em datacenters diferentes. A primeira etapa necessária fornecer essa funcionalidade é adicionar o serviço de nuvem ou ponto de extremidade de site ao Gerenciador de Tráfego.

>[AZURE.NOTE] Você não pode adicionar locais externos ou perfis do Gerenciador de Tráfego como pontos de extremidade usando o portal clássico do Azure. Você deve usar a API REST [Criar definição](http://go.microsoft.com/fwlink/p/?LinkId=400772) ou o Windows PowerShell [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774).

Você também pode desabilitar pontos de extremidade individuais que fazem parte de um perfil do Gerenciador de Tráfego. Os pontos de extremidade incluem serviços de nuvem e sites. A desabilitação de um ponto de extremidade o mantém como parte do perfil, mas o perfil age como se o ponto de extremidade não estivesse incluído nele. Essa ação é muito útil para remover temporariamente um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Depois que o ponto de extremidade estiver funcionando novamente, ele poderá ser habilitado

>[AZURE.NOTE] A desabilitação de um ponto de extremidade não tem nada a ver com seu estado de implantação no Azure. Um ponto de extremidade íntegro permanecerá ativo e capaz de receber tráfego mesmo quando desabilitado no Gerenciador de Tráfego. Além disso, a desabilitação de um ponto de extremidade em um perfil não afeta seu status em outro perfil.

## Para adicionar um serviço de nuvem ou ponto de extremidade de site


1. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
2. Na parte superior da página, clique em **Pontos de extremidade** para exibir os pontos de extremidade que já fazem parte de sua configuração.
3. Na parte inferior da página, clique em **Adicionar** para acessar a página **Adicionar Pontos de Extremidade de Serviço**. Por padrão, a página lista os serviços de nuvem em **Pontos de Extremidade de Serviço**.
4. Para serviços de nuvem, selecione os serviços de nuvem na lista para habilitá-los como pontos de extremidade para esse perfil. Limpar o nome do serviço de nuvem o remove da lista de pontos de extremidade.
5. Para sites, clique na lista suspensa **Tipo de Serviço** e selecione **Aplicativo Web**.
6. Selecione os sites na lista para adicioná-los como pontos de extremidade a esse perfil. Limpar o nome do site o remove da lista de pontos de extremidade. Observe que você só pode selecionar um único site por data center do Azure (também conhecido como região). Se você selecionar um site em um data center que hospeda vários sites, quando selecionar o primeiro site, os outros no mesmo data center ficarão indisponíveis para seleção. Observe também que apenas sites padrão são listados.
7. Depois que você selecionar os pontos de extremidade para esse perfil, clique na marca de seleção no canto inferior direito para salvar suas alterações.

>[AZURE.NOTE] Se você estiver usando o método de roteamento de tráfego de *Failover*, depois de adicionar ou remover um ponto de extremidade, não deixe de ajustar a Lista de Prioridade de Failover na página Configuração para refletir a ordem de failover que você deseja para sua configuração. Para obter mais informações, consulte [Configurar roteamento de tráfego de Failover](traffic-manager-configure-failover-routing-method.md).

## Para desabilitar um ponto de extremidade

1. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
2. Na parte superior da página, clique em **Pontos de Extremidade** para exibir os pontos de extremidade incluídos em sua configuração.
3. Clique no ponto de extremidade que você deseja desabilitar e, em seguida, clique em **Desabilitar** na parte inferior da página.
4. O tráfego deixará de fluir para o ponto de extremidade com base no TTL (Vida Útil) DNS configurado para o nome de domínio do Gerenciador de Tráfego. Você pode alterar a vida útil na página Configuração do perfil do Gerenciador de Tráfego.

## Para habilitar um ponto de extremidade

1. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
2. Na parte superior da página, clique em **Pontos de Extremidade** para exibir os pontos de extremidade incluídos em sua configuração.
3. Clique no ponto de extremidade que você deseja habilitar e, em seguida, clique em **Habilitar** na parte inferior da página.
4. O tráfego começará a fluir para o serviço novamente, conforme orientado pelo perfil.

## Para excluir um serviço de nuvem ou ponto de extremidade de site


1. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
2. Na parte superior da página, clique em **Pontos de extremidade** para exibir os pontos de extremidade que já fazem parte de sua configuração.
3. Na página Pontos de Extremidade, clique no nome do ponto de extremidade que você deseja excluir do perfil.
4. Na parte inferior da página, clique em **Excluir**.

>[AZURE.NOTE] Você não pode excluir locais externos ou perfis do Gerenciador de Tráfego, como pontos de extremidade usando o portal clássico do Azure. Você deve usar o Windows PowerShell. Para obter mais informações, consulte [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## Próximas etapas


[Configurar o método de roteamento de failover](traffic-manager-configure-failover-routing-method.md)

[Configurar o método de roteamento de round robin](traffic-manager-configure-round-robin-routing-method.md)

[Configurar o método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)

[Solucionando problemas de estado degradado do Gerenciador de Tráfego](traffic-manager-troubleshooting-degraded.md)

[Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

<!---HONumber=AcomDC_0824_2016-->