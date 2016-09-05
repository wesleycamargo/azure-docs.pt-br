<properties
   pageTitle="Como o Gerenciador de Tráfego funciona | Microsoft Azure"
   description="Este artigo ajudará a entender o que é o Gerenciador de Tráfego do Azure e como ele funciona"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="sewhee"/>

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
3.	O serviço DNS recursivo agora localiza os servidores de nomes para o domínio 'trafficmanager.net', que são fornecidos pelo serviço Gerenciador de Tráfego do Azure. Ele contata os servidores de nomes para solicitar o registro de DNS de “contoso.trafficmanager.net”.
4.	Os servidores de nomes do Gerenciador de Tráfego recebem a solicitação. Em seguida, eles escolhem qual ponto de extremidade deve ser retornado, com base em: a. O estado habilitado/desabilitado de cada ponto de extremidade (pontos de extremidade desabilitados não são retornados) b. A integridade atual de cada ponto de extremidade, conforme determinado pela integridade Gerenciador de Tráfego. Para obter mais informações, consulte Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego. c. O método de roteamento de tráfego escolhido. Para obter mais informações, consulte Métodos de roteamento de tráfego do Gerenciador de Tráfego.
5.	O ponto de extremidade escolhido é retornado como outro registro de DNS CNAME, nesse caso, vamos supor que contoso us.cloudapp.net é retornado.
6.	O serviço DNS recursivo localiza os servidores de nomes para o domínio "cloudapp.net". Ele contata os servidores de nomes para solicitar o registro de DNS de “contoso-us.cloudapp.net”. Um registro DNS “A” que contém o endereço IP do ponto de extremidade de serviço dos EUA é retornado.
7.	O serviço DNS recursivo retorna os resultados consolidados da sequência acima de resoluções de nome para o cliente.
8.	O cliente recebe os resultados DNS do serviço DNS recursivo e conecta-se ao endereço IP fornecido. Observe que se ele se conecta ao ponto de extremidade de serviço do aplicativo diretamente, não pelo Gerenciador de Tráfego. Como é um ponto de extremidade HTTPS, ele executa o handshake SSL/TLS necessário e faz uma solicitação HTTP GET para a página "/login.aspx".

Observe que o serviço DNS recursivo armazenará em cache as respostas DNS recebidas, assim como o cliente DNS no dispositivo do usuário final. Isso permite que as consultas DNS subsequentes sejam respondidas mais rapidamente usando dados do cache em vez de consultar outros servidores de nomes. A duração do cache é determinada pela propriedade TTL (“time-to-live”) de cada registro DNS. Valores menores resultam em expiração de cache mais rápida e, portanto, mais viagens para os servidores de nome do Gerenciador de Tráfego; valores médios maiores podem demorar mais para direcionar o tráfego para fora de um ponto de extremidade com falha. O Gerenciador de Tráfego permite configurar o TTL usado nas respostas DNS do Gerenciador de Tráfego, permitindo que você escolha o valor que melhor equilibra as necessidades do seu aplicativo.

## Perguntas frequentes

### Qual endereço IP o Gerenciador de Tráfego usa?

Conforme explicado em Como funciona o Gerenciador de Tráfego, o Gerenciador de Tráfego funciona no nível do DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego.

Portanto, o Gerenciador de Tráfego não fornece um ponto de extremidade ou o endereço IP para que os clientes se conectem. Então se, por exemplo, um endereço IP estático for necessário, ele deverá ser configurado no serviço, não no Gerenciador de Tráfego.

### O Gerenciador de Tráfego dá suporte a sessões “temporárias”?

Conforme explicado [acima](#how-clients-connect-using-traffic-manager), o Gerenciador de Tráfego funciona no nível do DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego. Portanto, o Gerenciador de Tráfego não vê o tráfego HTTP entre cliente e o servidor, incluindo cookies.

Além disso, observe que o endereço IP de origem da consulta DNS recebida pelo Gerenciador de Tráfego é o endereço IP do serviço DNS recursivo, não o endereço IP do cliente.

Portanto, o Gerenciador de Tráfego não consegue identificar ou rastrear os clientes individuais e, portanto, não é possível implementar sessões “temporárias”. Isso é comum a todos os sistemas de gerenciamento de tráfego com base em DNS, e não é uma restrição que acompanha o uso do Gerenciador de Tráfego.

### Estou vendo um erro de HTTP ao usar o Gerenciador de Tráfego... por que?

Conforme explicado [acima](#how-clients-connect-using-traffic-manager), o Gerenciador de Tráfego funciona no nível do DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego.

Portanto, o Gerenciador de Tráfego não vê o tráfego HTTP entre cliente e o servidor, e não pode gerar erros no nível HTTP. Qualquer erro HTTP que você veja deve ser proveniente de seu aplicativo. Como o cliente está se conectando ao aplicativo, isso também significa que resolução de DNS, incluindo a função do Gerenciador de Tráfego, deve ter sido concluída.

Portanto, as investigações adicionais devem se concentrar no aplicativo.

Um problema comum é que, ao usar o Gerenciador de Tráfego, o cabeçalho HTTP de “host” passado pelo navegador para o aplicativo mostrará o nome de domínio usado no navegador. Pode ser o nome de domínio do Gerenciador de Tráfego (por exemplo, myprofile.trafficmanager.net) se você estiver usando esse nome de domínio durante o teste, ou pode ser o domínio intuitivo CNAME configurado para apontar para o nome de domínio do Gerenciador de Tráfego. Em qualquer caso, verifique se o aplicativo está configurado para aceitar esse cabeçalho de host.

Se o aplicativo estiver hospedado no Serviço de Aplicativo do Azure, confira [Configuração de um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure usando o Gerenciador de Tráfego](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### Qual é o impacto no desempenho de usar o Gerenciador de Tráfego?

Conforme explicado [acima](#how-clients-connect-using-traffic-manager), o Gerenciador de Tráfego funciona no nível do DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego.

Como os clientes se conectam diretamente ao pontos de extremidade de seu serviço, não há qualquer impacto no desempenho ao usar o Gerenciador de Tráfego quando a conexão é estabelecida.

Como o Gerenciador de Tráfego é integrado aos aplicativos no nível de DNS, ele requer uma pesquisa de DNS adicional para ser inserido na cadeia de resolução de DNS (consulte [Exemplos do Gerenciador de Tráfego](#traffic-manager-example)). O impacto do Gerenciador de Tráfego no tempo de resolução de DNS é mínimo. O Gerenciador de Tráfego usa uma rede global de servidores de nome, e usa a rede de difusão para garantir que as consultas DNS sejam sempre roteadas para o servidor de nomes mais próximo disponível. Além disso, o armazenamento em cache de respostas DNS significa que a latência DNS adicional gerada pelo uso do Gerenciador de Tráfego só se aplica a uma fração de sessões.

O resultado é que o impacto no desempenho geral associado à incorporação do Gerenciador de Tráfego em seu aplicativo deve ser mínimo.

Além disso, quando o [método de roteamento de tráfego "Desempenho"](traffic-manager-routing-methods.md#performance-traffic-routing-method) do Gerenciador de Tráfego é usado, o aumento na latência de DNS deve ser muito menor do que o aprimoramento no desempenho obtido pelo roteamento de usuários finais para seu ponto de extremidade mais próximo disponível.

### Quais protocolos de aplicativo posso usar com o Gerenciador de Tráfego?
Conforme explicado [acima](#how-clients-connect-using-traffic-manager), o Gerenciador de Tráfego funciona no nível do DNS. Após a conclusão da pesquisa de DNS, os clientes se conectam diretamente ao ponto de extremidade do aplicativo, não pelo Gerenciador de Tráfego. Esta conexão, portanto, pode usar qualquer protocolo de aplicativo.

No entanto, as verificações de integridade do ponto de extremidade do Gerenciador de Tráfego exigem um ponto de extremidade HTTP ou HTTPS. Isso pode ser separado do ponto de extremidade do aplicativo aos quais os clientes se conectam, especificando uma porta TCP diferente ou um caminho de URI nas configurações da verificação de integridade do perfil do Gerenciador de Tráfego.

### Posso usar o Gerenciador de Tráfego com um nome de domínio sem www (“naked”)?

Não atualmente.

O tipo de registro de DNS CNAME é usado para criar um mapeamento de um nome DNS para outro nome. Conforme explicado no [Exemplo do Gerenciador de Tráfego](#traffic-manager-example), o Gerenciador de Tráfego exige um registro DNS CNAME para mapear o nome DNS intuitivo (por exemplo, www.contoso.com) para o nome DNS do perfil do Gerenciador de Tráfego (por exemplo, contoso.trafficmanager.net). Além disso, o próprio perfil do Gerenciador de Tráfego retorna um segundo DNS CNAME para indicar a qual ponto de extremidade o cliente deve se conectar.

Os padrões de DNS não permitem que CNAMEs coexistam com outros registros de DNS do mesmo tipo. Como o ápice (ou raiz) de uma zona DNS sempre contém dois registros DNS pré-existentes (SOA e os registros NS autoritativos), isso significa que um registro CNAME não pode ser criado no ápice da zona sem violar os padrões de DNS.

Para solucionar esse problema, recomendamos que os serviços que usem um domínio sem www (naked) e que desejam usar o Gerenciador de Tráfego devem usar um redirecionamento de HTTP para direcionar o tráfego do domínio naked para uma URL diferente, que poderá usar o Gerenciador de Tráfego. Por exemplo, o domínio naked "contoso.com" pode redirecionar usuários para "www.contoso.com", que pode usar o Gerenciador de Tráfego.

O suporte completo para domínios naked no Gerenciador de Tráfego é controlado em nossa lista de pendências de recurso. Se você estiver interessado nesse recurso, registre seu suporte [votando por ele em nosso site de comentários da comunidade](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

## Próximas etapas

Saiba mais sobre o [failover automático e monitoramento do ponto de extremidade](traffic-manager-monitoring.md) do Gerenciador de Tráfego.

Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) do Gerenciador de Tráfego.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0824_2016-->