<properties linkid="web-sites-traffic-manager" urlDisplayName="Controlando o tráfego de sites do Windows Azure com o Gerenciador de Tráfego do Azure" pageTitle="Controlando o tráfego de sites do Azure com o Gerenciador de Tráfego do Azure" metaKeywords="Sites do Azure, Gerenciador de Tráfego, roteamento da solicitação, round robin, failover, desempenho" description="Este artigo fornece informações resumidas sobre o Gerenciador de Tráfego do Azure em relação a sites do Azure." metaCanonical="" services="web-sites" documentationCenter="" title="Controlando o tráfego de sites do Windows Azure com o Gerenciador de Tráfego do Azure" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

# Controlando o tráfego de sites do Azure com o Gerenciador de Tráfego do Azure

> [WACOM.NOTE] Este artigo fornece informações resumidas sobre o Gerenciador de Tráfego do Microsoft Azure em relação a sites do Azure. Mais informações sobre o próprio Gerenciador de Tráfego do Azure podem ser encontradas visitando-se os links ao final deste artigo.

## Introdução
Você pode usar o Gerenciador de Tráfego do Azure para controlar como as solicitações de clientes Web são distribuídas para sites do Azure. Quando os pontos de extremidade do site do Azure são adicionados a um perfil do Gerenciador de Tráfego do Azure, este acompanha o status de seus sites (em execução, parados ou excluídos) de forma que possa decidir quais desses pontos de extremidade devem receber tráfego.

## Métodos de balanceamento de carga
O Gerenciador de Tráfego do Azure usa três métodos de balanceamento de carga diferentes. Eles estão descritos na lista a seguir desde que pertençam a sites do Azure. 

* **Failover**: se tiver clones de site em regiões diferentes, você poderá usar esse método para configurar um site para todo o tráfego do cliente Web de serviço e configurar outro site em uma região diferente para oferecer esse tráfego caso o primeiro site fique indisponível. 
	
* **Round robin**: se tiver clones de site em regiões diferentes, você poderá usar esse método para distribuir tráfego por igual entre os sites em regiões diferentes. 
	
* **Desempenho**: o método de desempenho distribui o tráfego com base no menor tempo de ida e volta até os clientes. O método de desempenho pode ser usado em sites dentro da mesma região ou em regiões diferentes. 

Para obter informações detalhadas sobre o balanceamento de carga no Gerenciador de Tráfego do Azure, consulte [Sobre métodos de balanceamento de carga do Gerenciador de Tráfego](http://msdn.microsoft.com/pt-br/library/windowsazure/dn339010.aspx).

##Sites do Azure e perfis do Gerenciador de Tráfego 
Para configurar e controlar o tráfego de site, crie um perfil no Gerenciador de Tráfego do Azure que use um dos três métodos de balanceamento de carga descritos anteriormente e, em seguida, adicione os pontos de extremidade (neste caso, sites) para os quais você deseja controlar o tráfego até o perfil. O status do site (em execução, parado ou excluído) é comunicado regularmente ao perfil para que o Gerenciador de Tráfego do Azure possa direcionar o tráfego de acordo.

Ao usar o Gerenciador de Tráfego do Azure com o Azure, tenha em mente os seguintes pontos:

* Para implantações apenas de site dentro da mesma região, os sites do Azure já fornecem uma funcionalidade de failover e round-robin independentemente do modo de site.

* Para implantações na mesma região usando sites do Azure com outro serviço de nuvem do Azure, você pode combinar ambos os tipos de pontos de extremidade para habilitar cenários híbridos.

* Você só pode especificar um ponto de extremidade de site por região em um perfil. Quando você seleciona um site como um ponto de extremidade para uma região, os sites restantes nessa região ficam disponíveis para seleção desse perfil.

* Os pontos de extremidade de site especificados em um perfil do Gerenciador de Tráfego do Azure serão exibido na seção **Nomes de Domínio** da página Configurar dos sites no perfil, mas não serão configuráveis lá.

* Depois que você adicionar um site a um perfil, o **URL do Site** no painel da página de portal do site exibirá a URL do domínio personalizado do site se você tiver definido uma. Do contrário, ele exibirá a URL do perfil do Gerenciador de Tráfego (por exemplo, `contoso.trafficmgr.com`). O nome de domínio direto do site e a URL do Gerenciador de Tráfego estarão visíveis na página Configurar do site na seção **Nomes de Domínio**.

* Os nomes de domínio personalizados funcionarão conforme esperado, mas além de adicioná-los a seus sites, você deve configurar o mapa DNS a fim de apontar para a URL do Gerenciador de Tráfego. Para obter informações sobre como configurar um domínio personalizado para um site do Azure, consulte [Configurando um nome de domínio personalizado para um site do Azure](https://www.windowsazure.com/pt-br/documentation/articles/web-sites-custom-domain-name/).

* Você só pode adicionar sites que estejam no modo Padrão para um perfil do Gerenciador de Tráfego do Azure.

## Próximas etapas

Para obter uma visão geral conceitual e técnica do Gerenciador de Tráfego do Azure, consulte [Visão geral do Gerenciador de Tráfego](http://msdn.microsoft.com/pt-br/library/windowsazure/hh744833.aspx). 

Para obter informações sobre como configurar o Gerenciador de Tráfego do Azure, inclusive para usar sites do Azure, consulte [Tarefas de configuração do Gerenciador de Tráfego](http://msdn.microsoft.com/pt-br/library/windowsazure/hh744830.aspx).

Para obter informações detalhadas sobre o balanceamento de carga no Gerenciador de Tráfego do Azure, consulte [Sobre métodos de balanceamento de carga do Gerenciador de Tráfego](http://msdn.microsoft.com/pt-br/library/windowsazure/dn339010.aspx).


