<properties
   pageTitle="Como o Gerenciador de Tráfego funciona | Microsoft Azure"
   description="Este artigo ajudará a entender o que é o Gerenciador de Tráfego do Azure e como ele funciona"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="jonatul"/>

# Como funciona o Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure permite que você controle como o tráfego é distribuído entre seus pontos de extremidade do aplicativo. Um ponto de extremidade pode ser qualquer ponto de extremidade para a Internet, hospedado no Azure ou fora do Azure.

O Gerenciador de Tráfego oferece dois benefícios principais:

1. Distribuição do tráfego de acordo com um dos vários [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)
2. [Monitoramento contínuo de integridade do ponto de extremidade](traffic-manager-monitoring.md) e failover automático quando os pontos de extremidade falharem

Quando um usuário final tenta se conectar a um ponto de extremidade de serviço, seu cliente (PC, telefone, etc.) deve primeiro resolver o nome DNS no ponto de extremidade para um endereço IP. O cliente se conecta a esse endereço IP para acessar o serviço.

**O ponto mais importante para entender é que o Gerenciador de Tráfego funciona no nível de DNS.** O Gerenciador de Tráfego usa o DNS para direcionar os usuários finais a pontos de extremidade de serviço específicos, com base no método de roteamento de tráfego escolhido e na integridade atual do ponto de extremidade. Os clientes se conectam a esses pontos de extremidade selecionados **diretamente**. O Gerenciador de Tráfego não é um proxy e não vê o tráfego que passa entre o cliente e o serviço.

## Exemplo de Gerenciador de Tráfego

A Contoso Corp desenvolveu um novo portal de parceiros. A URL para este portal será https://partners.contoso.com/login.aspx. O aplicativo é hospedado no Azure e para melhorar a disponibilidade e maximizar o desempenho global, eles desejam implantar o aplicativo em 3 regiões no mundo todo e usar o Gerenciador de Tráfego para distribuir seu ponto de extremidade mais próximo disponível aos usuários finais.

Para alcançar essa configuração:

- Eles implantam 3 instâncias do seu serviço. Os nomes DNS dessas implantações são “contoso us.cloudapp .net”, “contoso eu.cloudapp .net” e “contoso asia.cloudapp .net”.
- Em seguida, eles criam um perfil do Gerenciador de Tráfego chamado “contoso.trafficmanager.net”, que está configurado para usar o método de roteamento de tráfego "Desempenho" pelos pontos de 3 extremidade citados acima.
- Por fim, eles configuram seu domínio intuitivo “partners.contoso.com” para apontar para “contoso.trafficmanager.net” usando um registro DNS CNAME.

![Configuração DNS do Gerenciador de Tráfego][1]

> [AZURE.NOTE] Ao usar um domínio personalizado com o Gerenciador de Tráfego do Azure, você deve usar um CNAME para indicar seu nome de domínio intuitivo para seu nome de domínio do Gerenciador de Tráfego.

> Devido a uma restrição dos padrões DNS, um CNAME não pode ser criado no "apex" (ou raiz) de um domínio. Portanto, não é possível criar um CNAME para “contoso.com” (às vezes chamado de domínio “nu”). Você só pode criar um CNAME para um domínio "contoso.com", como "www.contoso.com".

> Assim, você não pode usar o Gerenciador de Tráfego diretamente com um domínio nu. Para solucionar esse problema, recomendamos o uso um simples HTTP para redirecionar para solicitações diretas de “contoso.com” para um nome alternativo como "www.contoso.com".

## Como os clientes se conectam usando o Gerenciador de Tráfego

Quando um usuário final solicita a página https://partners.contoso.com/login.aspx (conforme descrito no exemplo acima), o cliente executa as seguintes etapas para resolver o nome DNS e estabelecer uma conexão.

![Estabelecimento de conexão usando o Gerenciador de Tráfego][2]

1.	O cliente (PC, telefone, etc) faz uma consulta DNS para “partners.contoso.com” para seu serviço DNS recursivo configurado. (Um serviço DNS recursivo, às vezes chamado de serviço “DNS local”, não hospeda domínios DNS diretamente. Em vez disso, ele é usado pelo cliente para descarregar o trabalho de entrar em contato com os diversos serviços DNS de autoridade através da Internet necessários para resolver um nome DNS.)
2.	O serviço DNS recursivo agora resolve o nome DNS de “partners.contoso.com”. Em primeiro lugar, o serviço DNS recursivo localiza os servidores de nomes para o domínio "contoso.com". Ele então contata os servidores de nomes para solicitar o registro de DNS de “partners.contoso.com”. O CNAME contoso.trafficmanager.net é retornado.
3.	O serviço DNS recursivo agora localiza os servidores de nomes para o domínio “trafficmanager,net”, que são fornecidos pelo serviço do Gerenciador de Tráfego do Azure. Ele contata os servidores de nomes para solicitar o registro de DNS de “contoso.trafficmanager.net”.
4.	Os servidores de nomes do Gerenciador de Tráfego recebem a solicitação. Em seguida, eles escolhem qual ponto de extremidade deve ser retornado, com base em: a. O estado habilitado/desabilitado de cada ponto de extremidade (pontos de extremidade desabilitados não são retornados) b. A integridade atual de cada ponto de extremidade, conforme determinado pela integridade Gerenciador de Tráfego. Para obter mais informações, consulte Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego. c. O método de roteamento de tráfego escolhido. Para obter mais informações, consulte Métodos de roteamento de tráfego do Gerenciador de Tráfego.
5.	O ponto de extremidade escolhido é retornado como outro registro de DNS CNAME, nesse caso, vamos supor que contoso us.cloudapp.net é retornado.
6.	O serviço DNS recursivo localiza os servidores de nomes para o domínio "cloudapp.net". Ele contata os servidores de nomes para solicitar o registro de DNS de “contoso-us.cloudapp.net”. Um registro DNS “A” que contém o endereço IP do ponto de extremidade de serviço dos EUA é retornado.
7.	O serviço DNS recursivo retorna os resultados consolidados da sequência acima de resoluções de nome para o cliente.
8.	O cliente recebe os resultados DNS do serviço DNS recursivo e conecta-se ao endereço IP fornecido. Observe que se ele se conecta ao ponto de extremidade de serviço do aplicativo diretamente, não pelo Gerenciador de Tráfego. Como é um ponto de extremidade HTTPS, ele executa o handshake SSL/TLS necessário e faz uma solicitação HTTP GET para a página "/login.aspx".

Observe que o serviço DNS recursivo armazenará em cache as respostas DNS recebidas, assim como o cliente DNS no dispositivo do usuário final. Isso permite que as consultas DNS subsequentes sejam respondidas mais rapidamente usando dados do cache em vez de consultar outros servidores de nomes. A duração do cache é determinada pela propriedade TTL (“time-to-live”) de cada registro DNS. Valores menores resultam em expiração de cache mais rápida e, portanto, mais viagens para os servidores de nome do Gerenciador de Tráfego; valores médios maiores podem demorar mais para direcionar o tráfego para fora de um ponto de extremidade com falha. O Gerenciador de Tráfego permite configurar o TTL usado nas respostas DNS do Gerenciador de Tráfego, permitindo que você escolha o valor que melhor equilibra as necessidades do seu aplicativo.

## Próximas etapas

Saiba mais sobre o [failover automático e monitoramento do ponto de extremidade](traffic-manager-monitoring.md) do Gerenciador de Tráfego.

Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) do Gerenciador de Tráfego.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0525_2016-->