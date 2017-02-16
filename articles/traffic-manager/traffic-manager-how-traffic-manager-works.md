---
title: "Como funciona o Gerenciador de Tráfego | Microsoft Docs"
description: "Este artigo explica como funciona o Gerenciador de Tráfego do Azure"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: a6c9370d-e60d-440f-aa82-b6d3fa5416b0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 6ba1732840d094497a9e2cb7a7ea7825a7f4bc89

---

# <a name="how-traffic-manager-works"></a>Como funciona o Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure permite que você controle a distribuição do tráfego entre os pontos de extremidade do aplicativo. Um ponto de extremidade é qualquer serviço para a Internet hospedado dentro ou fora do Azure.

O Gerenciador de Tráfego oferece dois benefícios principais:

1. Distribuição do tráfego de acordo com um dos vários [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)
2. [Monitoramento contínuo de integridade do ponto de extremidade](traffic-manager-monitoring.md) e failover automático quando os pontos de extremidade falharem

Quando um cliente tenta se conectar a um serviço, ele primeiro deve resolver o nome DNS do serviço para um endereço IP. O cliente se conecta a esse endereço IP para acessar o serviço.

**O ponto mais importante para entender é que o Gerenciador de Tráfego funciona no nível de DNS.**  O Gerenciador de Tráfego usa o DNS para direcionar clientes para pontos de extremidade de serviço específicos com base nas regras do método de roteamento de tráfego. Os clientes se conectam **diretamente** ao ponto de extremidade selecionado. O Gerenciador de Tráfego não é um proxy nem um gateway. O Gerenciador de Tráfego não vê o tráfego que passa entre o cliente e o serviço.

## <a name="traffic-manager-example"></a>Exemplo de Gerenciador de Tráfego

A Contoso Corp desenvolveu um novo portal de parceiros. A URL para este portal é https://partners.contoso.com/login.aspx. O aplicativo é hospedado em três regiões do Azure. Para melhorar a disponibilidade e maximizar o desempenho global, elas usam o Gerenciador de Tráfego para distribuir o tráfego do cliente para o ponto de extremidade mais próximo disponível.

Para alcançar essa configuração:

* Elas implantam três instâncias de seu serviço. Os nomes DNS dessas implantações são “contoso-us.cloudapp.net”, “contoso-eu.cloudapp.net” e “contoso-asia.cloudapp.net”.
* Em seguida, elas criam um perfil do Gerenciador de Tráfego, chamado “contoso.trafficmanager.net”, que o configuram para usar o método de roteamento de tráfego por “Desempenho” nos três pontos de extremidade.
* Por fim, elas configuram seu nome de domínio intuitivo “partners.contoso.com” para apontar para “contoso.trafficmanager.net” usando um registro DNS CNAME.

![Configuração DNS do Gerenciador de Tráfego][1]

> [!NOTE]
> Ao usar um domínio personalizado com o Gerenciador de Tráfego do Azure, você deve usar um CNAME para indicar seu nome de domínio intuitivo para seu nome de domínio do Gerenciador de Tráfego. Os padrões do DNS não permitem que você crie um CNAME no “apex” (ou raiz) de um domínio. Portanto, não é possível criar um CNAME para “contoso.com” (às vezes chamado de domínio raiz). Você só pode criar um CNAME para um domínio em “contoso.com”, como “www.contoso.com”. Para solucionar esse problema, recomendamos o uso um redirecionamento HTTP simples para redirecionar as solicitações de “contoso.com” para um nome alternativo como “www.contoso.com”.

## <a name="how-clients-connect-using-traffic-manager"></a>Como os clientes se conectam usando o Gerenciador de Tráfego

Continuando o exemplo anterior, quando um cliente solicita a página https://partners.contoso.com/login.aspx, ele realiza as seguintes etapas para resolver o nome DNS e estabelecer uma conexão:

![Estabelecimento de conexão usando o Gerenciador de Tráfego][2]

1. O cliente envia uma consulta DNS para seu serviço DNS recursivo configurado para resolver o nome “partners.contoso.com”. Um serviço DNS recursivo, às vezes chamado de serviço “DNS local”, não hospeda domínios DNS diretamente. Em vez disso, o cliente descarrega o trabalho de entrar em contato com os diversos serviços DNS autoritativos pela Internet necessários para resolver um nome DNS.
2. Para resolver o nome DNS, o serviço DNS recursivo localiza os servidores de nomes para o domínio “contoso.com”. Ele então entra em contato com os servidores de nomes para solicitar o registro DNS de “partners.contoso.com”. Os servidores DNS de contoso.com retornam o registro CNAME que aponta para contoso.trafficmanager.net.
3. Em seguida, o serviço DNS recursivo localiza os servidores de nomes para o domínio “trafficmanager.net”, que são fornecidos pelo serviço do Gerenciador de Tráfego do Azure. Em seguida, ele envia uma solicitação para o registro DNS de “contoso.trafficmanager.net” para os servidores DNS.
4. Os servidores de nomes do Gerenciador de Tráfego recebem a solicitação. Eles escolhem um ponto de extremidade que se baseia:

    - No estado configurado de cada ponto de extremidade (pontos de extremidade desabilitados não são retornados)
    - A integridade atual de cada ponto de extremidade, conforme determinado pela integridade Gerenciador de Tráfego. Para obter mais informações, consulte [Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md).
    - O método de roteamento de tráfego escolhido. Para obter mais informações, consulte [Métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md).

5. O ponto de extremidade escolhido é retornado como outro registro DNS CNAME. Nesse caso, vamos supor que contoso-us.cloudapp.net seja retornado.
6. Em seguida, o serviço DNS recursivo localiza os servidores de nomes para o domínio “cloudapp.net”. Ele entra em contato com os servidores de nomes para solicitar o registro DNS de “contoso-us.cloudapp.net”. Um registro DNS “A” que contém o endereço IP do ponto de extremidade de serviço dos EUA é retornado.
7. O serviço DNS recursivo consolida os resultados e retorna uma única resposta DNS para o cliente.
8. O cliente recebe os resultados DNS e conecta-se ao endereço IP fornecido. O cliente se conecta ao ponto de extremidade de serviço do aplicativo diretamente, não pelo Gerenciador de Tráfego. Como é um ponto de extremidade HTTPS, o cliente executa o handshake SSL/TLS necessário e faz uma solicitação HTTP GET para a página “/login.aspx”.

O serviço DNS recursivo armazena em cache as respostas DNS recebidas. O resolvedor DNS no dispositivo do cliente também armazena em cache o resultado. O cache permite que as próximas consultas DNS sejam respondidas mais rapidamente usando dados do cache em vez de consultar outros servidores de nomes. A duração do cache é determinada pela propriedade TTL (“vida útil”) de cada registro DNS. Valores menores resultam na expiração de cache mais rápida e, portanto, mais viagens de ida e volta para os servidores de nomes do Gerenciador de Tráfego. Valores maiores significam que pode levar mais tempo para direcionar o tráfego para fora de um ponto de extremidade com falha. O Gerenciador de Tráfego permite configurar o TTL usado nas respostas DNS do Gerenciador de Tráfego, permitindo que você escolha o valor que melhor equilibra as necessidades do seu aplicativo.

## <a name="faq"></a>Perguntas frequentes

### <a name="what-ip-address-does-traffic-manager-use"></a>Qual endereço IP o Gerenciador de Tráfego usa?

Conforme explicado em Como funciona o Gerenciador de Tráfego, o Gerenciador de Tráfego funciona no nível do DNS. Ele envia as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego.

Portanto, o Gerenciador de Tráfego não fornece um ponto de extremidade ou o endereço IP para que os clientes se conectem. Portanto, se você desejar um endereço IP estático para o serviço, isso deverá ser configurado no serviço, não no Gerenciador de Tráfego.

### <a name="does-traffic-manager-support-sticky-sessions"></a>O Gerenciador de Tráfego dá suporte a sessões “temporárias”?

Conforme explicado [anteriormente](#how-clients-connect-using-traffic-manager), o Gerenciador de Tráfego funciona no DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego. Portanto, o Gerenciador de Tráfego não vê o tráfego HTTP entre o cliente e o servidor.

Além disso, o endereço IP de origem da consulta DNS recebida pelo Gerenciador de Tráfego pertence ao serviço DNS recursivo, não ao cliente. Portanto, o Gerenciador de Tráfego não consegue acompanhar clientes individuais e não pode implementar sessões “temporárias”. Essa limitação é comum a todos os sistemas de gerenciamento de tráfego baseados em DNS e não é específica ao Gerenciador de Tráfego.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Por que vejo um erro de HTTP ao usar o Gerenciador de Tráfego?

Conforme explicado [anteriormente](#how-clients-connect-using-traffic-manager), o Gerenciador de Tráfego funciona no DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego. O Gerenciador de Tráfego não vê o tráfego HTTP entre o cliente e o servidor. Portanto, qualquer erro HTTP visto deve ser proveniente do aplicativo. Para que o cliente se conecte ao aplicativo, todas as etapas de resolução DNS são concluídas. Isso inclui qualquer interação que o Gerenciador de Tráfego tem no fluxo de tráfego do aplicativo.

Portanto, as investigações adicionais devem se concentrar no aplicativo.

O cabeçalho de host HTTP enviado do navegador do cliente é a fonte mais comum de problemas. Verifique se o aplicativo está configurado para aceitar o cabeçalho de host correto para o nome de domínio que está sendo usado. Para pontos de extremidade que usam o Serviço de Aplicativo do Azure, consulte [Configurando um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure usando o Gerenciador de Tráfego](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual é o impacto no desempenho de usar o Gerenciador de Tráfego?

Conforme explicado [anteriormente](#how-clients-connect-using-traffic-manager), o Gerenciador de Tráfego funciona no DNS. Como os clientes se conectam diretamente ao pontos de extremidade de seu serviço, não há qualquer impacto no desempenho ao usar o Gerenciador de Tráfego quando a conexão é estabelecida.

Como o Gerenciador de Tráfego é integrado aos aplicativos no nível de DNS, ele requer uma pesquisa de DNS adicional para ser inserido na cadeia de resolução de DNS (consulte [Exemplos do Gerenciador de Tráfego](#traffic-manager-example)). O impacto do Gerenciador de Tráfego no tempo de resolução de DNS é mínimo. O Gerenciador de Tráfego usa uma rede global de servidores de nomes, bem como a rede [anycast](https://en.wikipedia.org/wiki/Anycast) para garantir que as consultas DNS são sempre encaminhadas para o servidor de nomes mais próximo disponível. Além disso, o armazenamento em cache de respostas DNS significa que a latência DNS adicional gerada pelo uso do Gerenciador de Tráfego só se aplica a uma fração de sessões.

O método por Desempenho encaminha o tráfego para o ponto de extremidade mais próximo disponível. O resultado final é que o impacto de desempenho geral associado a esse método deve ser mínimo. Qualquer aumento na latência DNS deve ser deslocado pela menor latência de rede para o ponto de extremidade.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quais protocolos de aplicativo posso usar com o Gerenciador de Tráfego?

Conforme explicado [anteriormente](#how-clients-connect-using-traffic-manager), o Gerenciador de Tráfego funciona no DNS. Após a conclusão da pesquisa de DNS, os clientes se conectam diretamente ao ponto de extremidade do aplicativo, não pelo Gerenciador de Tráfego. Portanto, a conexão pode usar qualquer protocolo de aplicativo. No entanto, as verificações de integridade do ponto de extremidade do Gerenciador de Tráfego exigem um ponto de extremidade HTTP ou HTTPS. O ponto de extremidade de uma verificação de integridade pode ser diferente do ponto de extremidade do aplicativo ao qual os clientes se conectam.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Posso usar o Gerenciador de Tráfego com um nome de domínio raiz?

Nº Os padrões de DNS não permitem que CNAMEs coexistam com outros registros DNS do mesmo nome. O apex (ou raiz) de uma zona DNS sempre contém dois registros DNS já existentes: a SOA e os registros NS autoritativos. Isso significa que um registro CNAME não pode ser criado no apex da zona sem violar os padrões do DNS.

Como explicamos no [exemplo sobre o Gerenciador de Tráfego](#traffic-manager-example), o Gerenciador de Tráfego exige um registro DNS CNAME para mapear o nome DNS intuitivo. Por exemplo, você mapeia www.contoso.com para o nome DNS do perfil do Gerenciador de Tráfego contoso.trafficmanager.net. Além disso, o perfil do Gerenciador de Tráfego retorna um segundo DNS CNAME para indicar a qual ponto de extremidade o cliente deve se conectar.

Para solucionar esse problema, recomendamos o uso de um redirecionamento HTTP para direcionar o tráfego do nome de domínio raiz para uma URL diferente, que então poderá usar o Gerenciador de Tráfego. Por exemplo, o domínio raiz “contoso.com” pode redirecionar os usuários para o CNAME “www.contoso.com” que aponta para o nome DNS do Gerenciador de Tráfego.

O suporte completo para domínios naked no Gerenciador de Tráfego é controlado em nossa lista de pendências de recurso. Você pode registrar seu suporte para essa solicitação de recurso [votando em nosso site de comentários da comunidade](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [failover automático e monitoramento do ponto de extremidade](traffic-manager-monitoring.md)do Gerenciador de Tráfego.

Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Gerenciador de Tráfego.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png



<!--HONumber=Nov16_HO5-->


