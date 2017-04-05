---
title: "Gerenciador de Tráfego do Azure — perguntas frequentes | Microsoft Docs"
description: "Este artigo fornece respostas a perguntas frequentes sobre o Gerenciador de Tráfego"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8c4c8db3cf57537dd77d33b3ded2dc24167f511f
ms.lasthandoff: 03/25/2017

---

# <a name="traffic-manager-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o Gerenciador de Tráfego

## <a name="traffic-manager-basics"></a>Noções básicas sobre o Gerenciador de Tráfego

### <a name="what-ip-address-does-traffic-manager-use"></a>Qual endereço IP o Gerenciador de Tráfego usa?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), o Gerenciador de Tráfego funciona no nível do DNS. Ele envia as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego.

Portanto, o Gerenciador de Tráfego não fornece um ponto de extremidade ou o endereço IP para que os clientes se conectem. Portanto, se você desejar um endereço IP estático para o serviço, isso deverá ser configurado no serviço, não no Gerenciador de Tráfego.

### <a name="does-traffic-manager-support-sticky-sessions"></a>O Gerenciador de Tráfego dá suporte a sessões “temporárias”?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), o Gerenciador de Tráfego funciona no nível do DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego. Portanto, o Gerenciador de Tráfego não vê o tráfego HTTP entre o cliente e o servidor.

Além disso, o endereço IP de origem da consulta DNS recebida pelo Gerenciador de Tráfego pertence ao serviço DNS recursivo, não ao cliente. Portanto, o Gerenciador de Tráfego não consegue acompanhar clientes individuais e não pode implementar sessões “temporárias”. Essa limitação é comum a todos os sistemas de gerenciamento de tráfego baseados em DNS e não é específica ao Gerenciador de Tráfego.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Por que vejo um erro de HTTP ao usar o Gerenciador de Tráfego?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), o Gerenciador de Tráfego funciona no nível do DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego. O Gerenciador de Tráfego não vê o tráfego HTTP entre o cliente e o servidor. Portanto, qualquer erro HTTP visto deve ser proveniente do aplicativo. Para que o cliente se conecte ao aplicativo, todas as etapas de resolução DNS são concluídas. Isso inclui qualquer interação que o Gerenciador de Tráfego tem no fluxo de tráfego do aplicativo.

Portanto, as investigações adicionais devem se concentrar no aplicativo.

O cabeçalho de host HTTP enviado do navegador do cliente é a fonte mais comum de problemas. Verifique se o aplicativo está configurado para aceitar o cabeçalho de host correto para o nome de domínio que está sendo usado. Para pontos de extremidade que usam o Serviço de Aplicativo do Azure, consulte [Configurando um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure usando o Gerenciador de Tráfego](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual é o impacto no desempenho de usar o Gerenciador de Tráfego?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), o Gerenciador de Tráfego funciona no nível do DNS. Como os clientes se conectam diretamente ao pontos de extremidade de seu serviço, não há qualquer impacto no desempenho ao usar o Gerenciador de Tráfego quando a conexão é estabelecida.

Como o Gerenciador de Tráfego é integrado aos aplicativos no nível de DNS, ele requer uma pesquisa de DNS adicional para ser inserido na cadeia de resolução de DNS. O impacto do Gerenciador de Tráfego no tempo de resolução de DNS é mínimo. O Gerenciador de Tráfego usa uma rede global de servidores de nomes, bem como a rede [anycast](https://en.wikipedia.org/wiki/Anycast) para garantir que as consultas DNS são sempre encaminhadas para o servidor de nomes mais próximo disponível. Além disso, o armazenamento em cache de respostas DNS significa que a latência DNS adicional gerada pelo uso do Gerenciador de Tráfego só se aplica a uma fração de sessões.

O método por Desempenho encaminha o tráfego para o ponto de extremidade mais próximo disponível. O resultado final é que o impacto de desempenho geral associado a esse método deve ser mínimo. Qualquer aumento na latência DNS deve ser deslocado pela menor latência de rede para o ponto de extremidade.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quais protocolos de aplicativo posso usar com o Gerenciador de Tráfego?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), o Gerenciador de Tráfego funciona no nível do DNS. Após a conclusão da pesquisa de DNS, os clientes se conectam diretamente ao ponto de extremidade do aplicativo, não pelo Gerenciador de Tráfego. Portanto, a conexão pode usar qualquer protocolo de aplicativo. No entanto, as verificações de integridade do ponto de extremidade do Gerenciador de Tráfego exigem um ponto de extremidade HTTP ou HTTPS. O ponto de extremidade de uma verificação de integridade pode ser diferente do ponto de extremidade do aplicativo ao qual os clientes se conectam.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Posso usar o Gerenciador de Tráfego com um nome de domínio raiz?

Nº Os padrões de DNS não permitem que CNAMEs coexistam com outros registros DNS do mesmo nome. O apex (ou raiz) de uma zona DNS sempre contém dois registros DNS já existentes: a SOA e os registros NS autoritativos. Isso significa que um registro CNAME não pode ser criado no apex da zona sem violar os padrões do DNS.

O Gerenciador de Tráfego exige um registro CNAME DNS para mapear o nome DNS intuitivo. Por exemplo, você mapeia www.contoso.com para o nome DNS do perfil do Gerenciador de Tráfego contoso.trafficmanager.net. Além disso, o perfil do Gerenciador de Tráfego retorna um segundo DNS CNAME para indicar a qual ponto de extremidade o cliente deve se conectar.

Para solucionar esse problema, recomendamos o uso de um redirecionamento HTTP para direcionar o tráfego do nome de domínio raiz para uma URL diferente, que então poderá usar o Gerenciador de Tráfego. Por exemplo, o domínio raiz “contoso.com” pode redirecionar os usuários para o CNAME “www.contoso.com” que aponta para o nome DNS do Gerenciador de Tráfego.

O suporte completo para domínios naked no Gerenciador de Tráfego é controlado em nossa lista de pendências de recurso. Você pode registrar seu suporte para essa solicitação de recurso [votando em nosso site de comentários da comunidade](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>O Gerenciador de Tráfego considera o endereço de sub-rede do cliente ao manipular consultas DNS? 
Não, no momento o Gerenciador de Tráfego considera apenas o endereço IP de origem da consulta DNS que ele recebe; esse endereço, ao executar pesquisas de métodos de roteamento geográfico e desempenho, na maioria dos casos é o endereço IP do resolvedor DNS.  
Especificamente, o Gerenciador de Tráfego não dá suporte atualmente à [RFC 7871 – Sub-rede de Cliente em Consultas DNS](https://tools.ietf.org/html/rfc7871), que fornece um [Mecanismo de Extensão para DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) que pode passar o endereço de sub-rede do cliente de resolvedores que dão suporte a ele para servidores DNS. Você pode registrar seu suporte para essa solicitação de recurso por meio de nosso [site de comentários da comunidade](https://feedback.azure.com/forums/217313-networking).


## <a name="traffic-manager-geographic-traffic-routing-method"></a>Método de roteamento de tráfego geográfico do Gerenciador de Tráfego

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quais são alguns casos de uso em que o roteamento geográfico é útil? 
O tipo de roteamento geográfico pode ser usado em qualquer cenário em que um cliente do Azure precise diferenciar seus usuários com base nas regiões geográficas. Por exemplo, ao usar o método de roteamento de tráfego geográfico, você proporciona aos usuários de regiões específicas uma experiência de usuário diferente daquelas de outras regiões. Outro exemplo é estar em conformidade com as normas soberanas de dados locais que exigem que os usuários de uma região específica sejam atendidos somente pelos pontos de extremidade dessa região.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quais são as regiões com suporte do Gerenciador de Tráfego para roteamento geográfico? 
A hierarquia de país/região usada pelo Gerenciador de Tráfego pode ser encontrada [aqui](traffic-manager-geographic-regions.md). Embora essa página esteja sempre atualizada com todas as alterações, você também pode recuperar de modo programático as mesmas informações usando a [API REST do Gerenciador de Tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Como o Gerenciador de Tráfego determina de que região um usuário está fazendo uma consulta? 
O Gerenciador de Tráfego analisa o IP de origem da consulta (provavelmente será um resolvedor DNS local fazendo a consulta em nome do usuário) e usa um IP interno do mapa da região para determinar o local. Esse mapa é atualizado continuamente para levar em conta as alterações na Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-will-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Há garantia de que o Gerenciador de Tráfego determinará o local geográfico exato do usuário em todos os casos corretamente?
Não, o Gerenciador de Tráfego não pode assegurar que a região geográfica que podemos deduzir do endereço IP de origem de uma consulta DNS sempre corresponderá ao local do usuário, pelos seguintes motivos: 

- Primeiro, conforme descrito nas perguntas frequentes anteriores, o endereço IP de origem que vemos é aquele de um resolvedor DNS fazendo a pesquisa em nome do usuário. Embora a localização geográfica do resolvedor DNS seja um bom proxy para a localização geográfica do usuário, ela também pode ser diferente dependendo da superfície de serviço de resolvedor DNS e do serviço de resolvedor DNS específico que um cliente tiver optado por usar. Por exemplo, um cliente localizado na Malásia poderia especificar no uso de configurações do seu dispositivo um serviço de resolução DNS cujo servidor DNS em Cingapura pode selecionado para tratar as resoluções de consulta desse usuário/dispositivo. Nesse caso, o Gerenciador de Tráfego verá somente a resolução endereço IP que corresponde ao local Cingapura. Além disso, consulte as Perguntas Frequentes anteriores sobre o suporte de endereço de sub-rede do cliente nesta página.

- Em segundo lugar, o Gerenciador de Tráfego usa um mapa interno para fazer a conversão do endereço IP para a região geográfica. Embora esse mapa seja validado e atualizado regularmente para aumentar a precisão e leve em conta a natureza evolutiva da Internet, ainda há a possibilidade de que nossas informações não sejam uma representação exata da localização geográfica de todos os endereços IP.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Um ponto de extremidade precisa estar fisicamente localizado na mesma região com a qual ele foi configurado para roteamento geográfico? 
Não, o local do ponto de extremidade não impõe restrições sobre quais regiões podem ser mapeadas para ele. Por exemplo, um ponto de extremidade na região Central dos EUA do Azure pode ter todos os usuários da Índia direcionados para ele.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Posso atribuir regiões geográficas aos pontos de extremidade em um perfil que não esteja configurado para fazer roteamento geográfico? 
Sim, se o método de roteamento de um perfil não for geográfico, você poderá usar a [API REST do Gerenciador de Tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/) para atribuir regiões geográficas aos pontos de extremidade nesse perfil. No caso de perfis de tipo de roteamento não geográfico, essa configuração será ignorada. Se você alterar esse perfil para tipo de roteamento geográfico em um momento posterior, o Gerenciador de Tráfego usará esses mapeamentos.


### <a name="when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic-i-am-getting-an-error"></a>Ao tentar alterar o método de roteamento de um perfil existente para geográfico receberei um erro?
Todos os pontos de extremidade em um perfil com roteamento geográfico precisam ter pelo menos uma região mapeada para eles. Para converter um perfil existente em tipo de roteamento geográfico, primeiramente, você precisa associar regiões geográficas a todos os respectivos pontos de extremidade usando a [API REST do Gerenciador de Tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/) antes de alterar o tipo de roteamento para geográfico. Se for usar o portal, você terá que excluir os pontos de extremidade primeiro, alterar o método de roteamento do perfil para geográfico e, por fim, adicionar os pontos de extremidade com o respectivo mapeamento da região geográfica. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Por que é enfaticamente aconselhável que os clientes criem perfis aninhados em vez de pontos de extremidade em um perfil com o roteamento geográfico habilitado? 
Uma região poderá ser atribuída apenas a um ponto de extremidade em um perfil se ela estiver usando tipo de roteamento geográfico. Se esse ponto de extremidade não for um tipo aninhado com um perfil filho conectado a ele, caso esse ponto de extremidade se torne não íntegro, o Gerenciador de Tráfego continuará enviando tráfego a ele, uma vez que a alternativa de não enviar tráfego não é uma opção melhor. O Gerenciador de Tráfego não faz failover para outro ponto de extremidade, mesmo quando a região atribuída for um "pai" da região atribuída ao ponto de extremidade que se tornou não íntegro (por exemplo, se um ponto de extremidade que tenha a região Espanha se tornar não íntegro, não faremos failover para outro ponto de extremidade que tenha a região Europa atribuída a ele). Isso é para garantir que o Gerenciador de Tráfego respeite os limites geográficos que um cliente configurou em seu perfil. Para obter o benefício de fazer failover para outro ponto de extremidade quando um se torna não íntegro, é aconselhável que as regiões geográficas sejam atribuídas a perfis aninhados com vários pontos de extremidade neles, em vez de pontos de extremidade individuais. Dessa forma, se um ponto de extremidade no perfil filho aninhado falhar, o tráfego pode fazer failover para outro ponto de extremidade no mesmo perfil filho aninhado.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existem restrições quanto à versão de API que oferece suporte a esse tipo de roteamento?

Sim, somente a versão 2017-03-01 da API e as mais recentes dão suporte ao tipo de roteamento Geográfico. Qualquer versão de API mais antiga não pode ser usada para criar perfis de tipo de roteamento Geográfico ou atribuir regiões geográficas aos pontos de extremidade. Se uma versão de API mais antiga for usada para recuperar perfis de uma assinatura do Azure, nenhum perfil de tipo de roteamento Geográfico não será retornado. Além disso, ao usar versões de API mais antigas, todos os perfis retornados que tiverem pontos de extremidade com uma atribuição de região geográfica não terão essa atribuição exibida.



## <a name="traffic-manager-endpoints"></a>Pontos de extremidade do Gerenciador de Tráfego

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Posso usar o Gerenciador de Tráfego com pontos de extremidade de várias assinaturas?

Não é possível usar pontos de extremidade de várias assinaturas com Aplicativos Web do Azure. Os Aplicativos Web do Azure exigem que qualquer nome de domínio personalizado usado com Aplicativos Web seja usado somente em uma única assinatura. Não é possível usar Aplicativos Web de várias assinaturas com o mesmo nome de domínio.

Para outros tipos de ponto de extremidade, é possível usar o Gerenciador de Tráfego com pontos de extremidade de mais de uma assinatura. O modo de configurar o Gerenciador de Tráfego depende de se você está usando o modelo de implantação clássico ou a experiência do Resource Manager.

* No Gerenciador de Recursos, os pontos de extremidade de qualquer assinatura podem ser adicionados ao Gerenciador de Tráfego, desde que a pessoa que configura o perfil do Gerenciador de Tráfego tenha o acesso de leitura ao ponto de extremidade. Essas permissões podem ser concedidas usando o [RBAC (controle de acesso baseado em função) do Azure Resource Manager](../active-directory/role-based-access-control-configure.md).
* Na interface do modelo de implantação clássico, o Gerenciador de Tráfego requer que os Serviços de Nuvem ou os Aplicativos Web configurados como pontos de extremidade do Azure residam na mesma assinatura que o perfil do Gerenciador de Tráfego. Pontos de extremidade de Serviço de Nuvem em outras assinaturas podem ser adicionados ao Gerenciador de Tráfego como pontos de extremidade “externos”. Esses pontos de extremidade externos são cobrados como pontos de extremidade do Azure, em vez de à taxa externa.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Posso usar o Gerenciador de Tráfego com os slots de “Preparo” do Serviço de Nuvem?

Sim. Os slots de “preparo” do Serviço de Nuvem podem ser configurados no Gerenciador de Tráfego como pontos de extremidade Externos. Verificações de integridade ainda são cobradas à taxa de Pontos de Extremidade do Azure. Como o tipo do ponto de extremidade externo está em uso, as alterações no serviço subjacente não serão selecionadas automaticamente. Com pontos de extremidade externos, o Gerenciador de Tráfego não consegue detectar quando o Serviço de Nuvem é parado ou excluído. Portanto, o Gerenciador de Tráfego continua cobrando por verificações de integridade até que o ponto de extremidade seja desabilitado ou excluído.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>O Gerenciador de Tráfego dá suporte aos pontos de extremidade IPv6?

No momento, o Gerenciador de Tráfego não oferece servidores de nome endereçáveis por IPv6. No entanto, o Gerenciador de Tráfego ainda pode ser usado por clientes IPv6 que se conectem a pontos de extremidade IPv6. Um cliente não faz solicitações DNS diretamente ao Gerenciador de tráfego. Em vez disso, o cliente usa um serviço DNS recursivo. Um cliente somente IPv6 envia solicitações para o serviço DNS recursivo via IPv6. Então o serviço recursivo deve conseguir contatar os servidores de nome do Gerenciador de Tráfego usando IPv4.

O Gerenciador de Tráfego responde com o nome DNS do ponto de extremidade. Para dar suporte a um ponto de extremidade IPv6, deve haver um registro DNS AAAA apontando o nome DNS do ponto de extremidade para o endereço IPv6. Verificações de integridade do Gerenciador de Tráfego dão suporte apenas a endereços IPv4. O serviço deve expor um ponto de extremidade IPv4 no mesmo nome DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Posso usar o Gerenciador de Tráfego com mais de um Aplicativo Web na mesma região?

Normalmente, o Gerenciador de Tráfego é usado para direcionar o tráfego para os aplicativos implantados em regiões diferentes. No entanto, ele também pode ser usado onde um aplicativo tem mais de uma implantação na mesma região. Os pontos de extremidade do Gerenciador de Tráfego do Azure não permitem adicionar mais de um ponto de extremidade do Aplicativo Web da mesma região do Azure ao mesmo perfil do Gerenciador de Tráfego.

As etapas a seguir fornecem uma solução alternativa para essa restrição:

1. Verifique se seus pontos de extremidade estão em diferentes “unidades de escala” do aplicativo Web'. Um nome de domínio deve mapear para um único site em uma unidade de escala. Portanto, dois aplicativos Web na mesma unidade de escala não podem compartilhar um perfil do Gerenciador de Tráfego.
2. Adicione seu nome de domínio intuitivo como um nome de host personalizado a cada Aplicativo Web. Cada Aplicativo Web deve estar em uma unidade de escala diferente. Todos os Aplicativos Web devem pertencer à mesma assinatura.
3. Adicione um (e somente um) ponto de extremidade do Aplicativo Web ao perfil do Gerenciador de Tráfego, como um ponto de extremidade do Azure.
4. Adicione cada ponto de extremidade do Aplicativo Web extra ao seu perfil do Gerenciador de Tráfego como um ponto de extremidade externo. Pontos de extremidade externos só podem ser adicionados usando o modelo de implantação do Gerenciador de Recursos.
5. Crie um registro DNS CNAME em seu domínio intuitivo que aponte para o seu nome DNS de perfil do Gerenciador de Tráfego (<...>.trafficmanager.net).
6. Acesse seu site usando o nome de domínio intuitivo, não o nome DNS do perfil do Gerenciador de Tráfego.

##  <a name="traffic-manager-endpoint-monitoring"></a>Monitoramento de ponto de extremidade do Gerenciador de Tráfego

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>O Gerenciador de Tráfego é resistente a falhas de região do Azure?

O Gerenciador de Tráfego é um componente fundamental da entrega de aplicativos altamente disponíveis no Azure.
Para entregar alta disponibilidade, o Gerenciador de Tráfego deve ter um nível muito alto de disponibilidade e ser resiliente a falhas de região.

Por design, os componentes do Gerenciador de Tráfego são resilientes a uma falha completa de qualquer região do Azure. Essa resiliência se aplica a todos os componentes do Gerenciador de Tráfego: os servidores de nome DNS, a API, a camada de armazenamento e o serviço de monitoramento de ponto de extremidade.

No caso improvável de uma falha de uma região do Azure inteira, Gerenciador de Tráfego deve continuar a funcionar normalmente. Os aplicativos implantados em várias regiões do Azure podem contar com o Gerenciador de Tráfego para direcionar o tráfego para uma instância disponível de seu aplicativo.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Como a escolha da localização do grupo de recursos afeta o Gerenciador de Tráfego?

O Gerenciador de Tráfego é um serviço global único. Não é regional. A escolha do local do grupo de recursos não faz diferença para perfis do Gerenciador de Tráfego implantados nesse grupo de recursos.

O Azure Resource Manager exige que todos os grupos de recursos especifiquem uma localização, que determina a localização padrão para os recursos implantados nesse grupo de recursos. Quando você cria um perfil do Gerenciador de Tráfego, ele é criado em um grupo de recursos. Todos os perfis do Gerenciador de Tráfego usam **global** como a localização, substituindo o padrão do grupo de recursos.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Como determinar a integridade atual de cada ponto de extremidade?

O status de monitoramento atual de cada ponto de extremidade, bem como o perfil geral, é exibido no portal do Azure. Essas informações também estão disponíveis por meio da [API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) do Traffic Monitor, dos [cmdlets do PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) e da [CLI do Azure entre plataformas](../cli-install-nodejs.md).

O Azure não fornece informações históricas sobre dados passados referentes à integridade do ponto de extremidade ou à capacidade de emitir alertas sobre alterações na integridade do ponto de extremidade.

### <a name="can-i-monitor-https-endpoints"></a>Posso monitorar os pontos de extremidade HTTPS?

Sim. O Gerenciador de Tráfego oferece suporte à investigação por HTTPS. Configure **HTTPS** como o protocolo na configuração de monitoramento.

O Gerenciador de Tráfego não pode fornecer nenhuma validação de certificado, incluindo:

* Certificados no lado do servidor não estão validados
* Certificados no lado do servidor SNI não estão validados
* Não há suporte para certificados de cliente

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Qual cabeçalho host as verificações de integridade do ponto de extremidade usam?

O Gerenciador de Tráfego usa cabeçalhos de host em verificações de integridade HTTP e HTTPS. O cabeçalho de host usado pelo Gerenciador de Tráfego é o nome do ponto de extremidade de destino configurado no perfil. O valor usado no cabeçalho do host não pode ser especificado separadamente da propriedade “target”.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quais são os endereços IP dos quais as verificações de integridade se originam?

A lista a seguir contém os endereços IP dos quais as verificações de integridade do Gerenciador de Tráfego podem se originar. Você pode usar esta lista para garantir que as conexões de entrada desses endereços IP sejam permitidas nos pontos de extremidades para verificar o status de integridade.

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203

## <a name="traffic-manager-nested-profiles"></a>Perfis aninhados do Gerenciador de Tráfego

### <a name="how-do-i-configure-nested-profiles"></a>Como posso configurar perfis aninhados?

Os perfis aninhados do Gerenciador de Tráfego podem ser configurados usando o Azure Resource Manager e as APIs REST clássicas do Azure, cmdlets do Azure PowerShell e comandos da CLI do Azure de plataforma cruzada. Também há suporte para eles no novo portal do Azure. Não há suporte para eles no portal clássico.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>A quantas camadas de aninhamento o Gerenciador de Tráfego dá suporte?

Você pode aninhar perfis em até 10 níveis de profundidade. “Loops” não são permitidos.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Posso combinar outros tipos de ponto de extremidade com perfis filho aninhados no mesmo perfil do Gerenciador de Tráfego?

Sim. Não há nenhuma restrição sobre como combinar os pontos de extremidade de diferentes tipos em um perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Como o modelo de cobrança se aplica a perfis aninhados?

Há não impacto negativo sobre os preços ao usar perfis aninhados.

A cobrança do Gerenciador de Tráfego tem dois componentes: verificações de integridade do ponto de extremidade e milhões de consultas DNS

* Verificações de integridade de ponto de extremidade: não há nenhum encargo para um perfil filho quando configurado como um ponto de extremidade em um perfil pai. O monitoramento dos pontos de extremidade no perfil filho é cobrado como de costume.
* Consultas DNS: cada consulta é contada apenas uma vez. Uma consulta em um perfil pai que retorna um ponto de extremidade de um perfil filho é contada apenas no perfil pai.

Para obter detalhes completos, consulte a [página de preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Há impacto no desempenho para perfis aninhados?

Nº Não há nenhum impacto no desempenho ao usar perfis aninhados.

Os servidores de nomes do Gerenciador de Tráfego atravessam a hierarquia de perfil internamente durante o processamento de cada consulta DNS. Uma consulta DNS a um perfil pai pode receber uma resposta DNS com um ponto de extremidade de um perfil filho. Um único registro CNAME é usado se você está usando um único perfil ou perfis aninhados. Não é necessário criar um registro CNAME para cada perfil na hierarquia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Como o Gerenciador de Tráfego calcula a integridade de um ponto de extremidade aninhado em um perfil pai?

O perfil pai não executa verificações de integridade no filho diretamente. Em vez disso, a integridade dos pontos de extremidade do perfil filho é usada para calcular a integridade geral do perfil filho. Essas informações são propagadas até a hierarquia do perfil aninhado para determinar a integridade do ponto de extremidade aninhado. O perfil pai usa essa integridade agregada para determinar se o tráfego pode ser direcionado para o filho.

A tabela a seguir descreve o comportamento das verificações de integridade do Gerenciador de Tráfego de um ponto de extremidade aninhado.

| Status do Monitor de perfil filho | Status do monitor de ponto de extremidade pai | Observações |
| --- | --- | --- |
| Desabilitado. O perfil filho foi desabilitado. |Parada |O estado do ponto de extremidade pai é Parado, não Desabilitado. O estado Desabilitado é reservado para indicar que você desabilitou o ponto de extremidade no perfil pai. |
| Degradado. Pelo menos um ponto de extremidade do perfil filho está no estado Degradado. |Online: o número de pontos de extremidade Online no perfil filho é pelo menos o valor de MinChildEndpoints.<BR>CheckingEndpoint: o número de pontos de extremidade Online mais CheckingEndpoint no perfil filho é pelo menos o valor de MinChildEndpoints.<BR>Degradado: caso contrário. |O tráfego é roteado para um ponto de extremidade do status CheckingEndpoint. Se MinChildEndpoints estiver definido com um valor muito alto, o ponto de extremidade estará sempre degradado. |
| Online. Pelo menos, um ponto de extremidade do perfil filho está em um estado Online. Nenhum ponto de extremidade está no estado Degradado. |Veja acima. | |
| CheckingEndpoints. Pelo menos, um ponto de extremidade do perfil filho é um 'CheckingEndpoint'. Nenhum ponto de extremidade está ‘Online’ ou ‘Degradado’ |Mesmo que acima. | |
| Inativo. Todos os pontos de extremidade de perfil filho estão com status Desabilitado ou Parado ou esse é um perfil que não tem nenhum ponto de extremidade. |Parada | |

## <a name="next-steps"></a>Próximas etapas:
- Saiba mais sobre o [failover automático e monitoramento do ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md)do Gerenciador de Tráfego.
- Saiba mais sobre os [métodos de roteamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md)do Gerenciador de Tráfego.
