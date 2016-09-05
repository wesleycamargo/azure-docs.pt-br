<properties
   pageTitle="Considerações sobre o desempenho do Gerenciador de Tráfego do Azure | Microsoft Azure"
   description="Compreenda o desempenho no Gerenciador de Tráfego e como testar o desempenho de seu site ao usar o Gerenciador de Tráfego"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/10/2016"
   ms.author="sewhee" />


# Considerações de desempenho sobre Gerenciador de Tráfego

Esta página explica as considerações de desempenho usando o Gerenciador de Tráfego. Cenários, como: você tem um site na região dos EUA e na Ásia e uma delas tem falhas na verificação de integridade para testes do Gerenciador de Tráfego, todos os usuários serão direcionados à região íntegra e o comportamento poderá ser exibido como problema de desempenho, mas seria um comportamento esperado com base na distância para a solicitação do usuário.

  

## Observação importante sobre o funcionamento do Gerenciador de Tráfego

- Essencialmente, o Gerenciador de Tráfego faz apenas uma coisa – resolução de DNS. Isso significa que o único impacto de desempenho que o Gerenciador de Tráfego pode ter sobre seu site é na pesquisa inicial de DNS.
- Esclarecimento sobre a pesquisa de DNS do Gerenciador de Tráfego. O Gerenciador de Tráfego preenche, e atualiza regularmente, os servidores raiz de DNS normais da Microsoft com base em sua política e nos resultados de teste. Portanto, mesmo durante a pesquisa inicial de DNS não há qualquer envolvimento do Gerenciador de Tráfego, uma vez que a solicitação de DNS é tratada pelos servidores raiz de DNS normais da Microsoft. Se o Gerenciador de Tráfego 'cair' (ou seja, se ocorrer uma falha nas VMs que realizam a investigação de política e atualização de DNS), não haverá qualquer impacto em seu nome DNS do Gerenciador de Tráfego, uma vez que as entradas nos servidores DNS da Microsoft ainda serão preservadas. O único impacto será a não realização da investigação e da atualização com base na política (ou seja, se o site primário ficar inativo, o Gerenciador de Tráfego não poderá atualizar o DNS a fim de apontar para seu site de failover).
- O tráfego NÃO flui pelo Gerenciador de Tráfego. Não há qualquer servidor do Gerenciador de Tráfego atuando como intermediário entre seus clientes e o serviço hospedado do Azure. Após a conclusão da pesquisa de DNS, o Gerenciador de Tráfego é completamente removido da comunicação entre o cliente e o servidor.
- A pesquisa de DNS é muito rápida e é armazenado em cache. A pesquisa inicial de DNS dependerá do cliente e de seus servidores DNS configurados. Normalmente, um cliente pode fazer uma pesquisa de DNS em cerca de 50 ms (consulte http://www.solvedns.com/dns-comparison/). Após a conclusão da primeira pesquisa os resultados serão armazenados em cache durante o tempo de vida do DNS, cujo padrão para o Gerenciador de Tráfego é de 300 segundos.
- A política do Gerenciador de Tráfego que você escolher (desempenho, failover e round robin) não tem qualquer influência sobre o desempenho do DNS. Sua política de desempenho pode afetar negativamente a experiência do usuário, por exemplo, se você envia usuários dos Estados Unidos a um serviço hospedado na Ásia. Porém, esse problema de desempenho não é causado pelo Gerenciador de Tráfego.

  

## Testando o desempenho do Gerenciador de Tráfego

Há alguns sites disponíveis publicamente que você pode usar para determinar o desempenho e o comportamento do Gerenciador de Tráfego. Esses sites são úteis para determinar a latência do DNS e os serviços hospedados para os quais os usuários em todo o mundo estão sendo direcionados. Lembre-se de que a maioria dessas ferramentas não armazena em cache os resultados de DNS, portanto, executar os testes várias vezes mostrará a pesquisa completa de DNS, enquanto os clientes que se conectam ao ponto de extremidade do Gerenciador de Tráfego só verão o impacto no desempenho da pesquisa total DNS uma vez durante o tempo de vida.


## Exemplo de ferramentas para medir o desempenho


Uma das ferramentas mais simples é o WebSitePulse. Insira a URL e você verá as estatísticas como o tempo de resolução do DNS, Primeiro Byte, Último Byte e outras estatísticas de desempenho. Você pode escolher entre três locais diferentes para testar seu site. Neste exemplo, você verá que a primeira execução mostra que o tempo da primeira pesquisa de DNS é de 0,204 s. Na segunda execução desse teste no mesmo ponto de extremidade do Gerenciador de Tráfego, o tempo de pesquisa de DNS foi de 0,002 segundos, uma vez que os resultados já estão armazenados em cache.

http://www.websitepulse.com/help/tools.php


![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

Tempo de DNS quando armazenado em cache:


![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)



Outro ferramenta realmente útil para obter simultaneamente o tempo de resolução de DNS de várias regiões geográficas é a ferramenta Check Website da Watchmouse. Insira a URL e você verá o tempo de resolução de DNS, o tempo de conexão e a velocidade de vários locais. Isso também é útil para testar a Política de desempenho do Gerenciador de Tráfego e ver para qual serviço hospedado os usuários em todo o mundo estão sendo enviados.

http://www.watchmouse.com/en/checkit.php


![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

http://tools.pingdom.com/ – isso testará um site e fornecerá estatísticas de desempenho para cada elemento na página em um gráfico visual. Se você alternar para a guia Análise da Página, poderá ver a porcentagem de tempo gasto fazendo a pesquisa de DNS.

 

http://www.whatsmydns.net/ – esse site realizará uma pesquisa de DNS de 20 locais geograficamente diferentes e exibirá os resultados em um mapa. Essa é uma ótima representação visual para ajudar a determinar a qual serviço hospedado seus clientes se conectarão.

 

http://www.digwebinterface.com – semelhante ao site watchmouse, mas este mostra informações de DNS mais detalhadas, incluindo CNAMEs e registros A. Marque ‘Colorize output’ e ‘Stats’ nas opções e selecione 'All'em Nameservers.

## Próximas etapas


[Sobre os métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md)

[Teste as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md)

[Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=AcomDC_0824_2016-->