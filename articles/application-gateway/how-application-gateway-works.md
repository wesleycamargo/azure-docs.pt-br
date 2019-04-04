---
title: Funciona como o Azure de Gateway de aplicativo
description: Este artigo fornece informações sobre como para o Gateway de aplicativo funciona
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: bbaf651233d4cebad3f45e5cf3823bcaf6ce38b6
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905776"
---
# <a name="how-application-gateway-works"></a>Como funciona o Gateway de aplicativo

Este artigo explica como o gateway de aplicativo aceita solicitações de entrada e encaminha-as para o back-end.

![como-application-gateway-funciona](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-a-request-is-accepted"></a>Como uma solicitação for aceita

Antes de um cliente envia uma solicitação para o gateway de aplicativo, ele resolve o nome de domínio de gateway de aplicativo usando um servidor de sistema de nome de domínio (DNS). A entrada DNS é controlada pelo Azure, porque seus gateways de aplicativo estão no domínio do azure.com. O DNS do Azure retorna o endereço IP para o cliente, que é o *endereço IP de front-end* do Gateway de aplicativo. O gateway de aplicativo aceita o tráfego de entrada em um ou mais *ouvintes*. Um ouvinte é uma entidade lógica que verifica se há solicitações de conexão. Ele é configurado com um endereço IP de front, protocolo e número da porta para conexões de clientes para o gateway de aplicativo. Se o Firewall de aplicativo da Web (WAF) estiver habilitado, o Gateway de aplicativo verifica os cabeçalhos de solicitação e o corpo (se houver) em relação a *regras de WAF* para determinar se a solicitação é uma solicitação válida - nesse caso, ela será roteada para o back-end - ou uma ameaça à segurança, nesse caso a solicitação será bloqueado.  

Gateway de aplicativo pode ser usado como um balanceador de carga interno do aplicativo ou um balanceador de carga do aplicativo para a Internet. Um gateway de aplicativo para a Internet tem endereços IP públicos. O nome DNS de um gateway de aplicativo para a Internet pode ser publicamente resolvido para seu endereço IP público. Portanto, os gateways de aplicativos para a Internet podem rotear as solicitações de clientes na Internet. Gateways de aplicativo interno têm apenas o endereço IP privado. O nome DNS de um gateway de aplicativo interno pode ser publicamente resolvido para seu endereço IP privado. Portanto, balanceadores de carga interno só podem rotear solicitações de clientes com acesso à rede virtual para o gateway de aplicativo. Gateways de aplicativo para a Internet e internos rotear solicitações para os servidores de back-end usando endereços IP privados. Portanto, os servidores de back-end não é necessário para endereços IP públicos para receber solicitações de interna ou um Gateway de aplicativo para a Internet.

## <a name="how-a-request-is-routed"></a>Como uma solicitação é roteada

Se a solicitação for encontrada para ser válido (ou se o WAF não está habilitado), o *regra de roteamento de solicitação* associado com o *ouvinte* é avaliada para determinar a *pool de back-end* para qual é a solicitação deve ser roteada. As regras são processadas na ordem em que elas são listadas no portal. Com base nas *regra de roteamento de solicitação* configuração, o gateway de aplicativo decide se rotear todas as solicitações no ouvinte para um pool de back-end específico ou roteá-las aos pools de back-end diferente dependendo do caminho da URL ou usando *redirecionar solicitações* para outra porta ou o site externo

Uma vez um *back-end* *pool* foi escolhido, o gateway de aplicativo envia a solicitação para um dos servidores de back-end configurados no pool que esteja íntegro (y.y.y.y). A integridade do servidor é determinada por uma *investigação de integridade*. Se o pool de back-end contém mais de um servidor, o gateway de aplicativo usa o algoritmo de round robin para rotear as solicitações entre os servidores íntegros, portanto, as solicitações nos servidores de balanceamento de carga.

Depois que um servidor de back-end tiver sido determinado, o gateway de aplicativo abre uma nova sessão TCP com o servidor de back-end com base na configuração de *configurações de HTTP*. O *configurações de HTTP* é um componente que especifica o protocolo, porta e outros roteamento relacionados à configuração que é necessário para estabelecer uma nova sessão com o servidor de back-end. A porta e protocolo usado nas configurações de HTTP determinam se o tráfego entre os servidores de gateway e o back-end do aplicativo é criptografado, assim a realização de SSL de ponta a ponta, ou sem criptografia. Ao enviar a solicitação original para o servidor de back-end, o gateway de aplicativo respeita qualquer configuração personalizada feita nas configurações de HTTP relacionadas ao substituir o nome do host, o caminho, o protocolo; afinidade de manutenção baseada em cookies de sessão, conexão descarga, escolher o nome do host do back-end, etc.

Um Gateway de aplicativo interno tem apenas o endereço IP privado. O nome DNS de um Gateway de aplicativo interno é internamente pode ser resolvido para seu endereço IP privado. Portanto, balanceadores de carga interno só podem rotear solicitações de clientes com acesso à rede virtual para o Gateway de aplicativo.

Se seu pool de back-end contém um FQDN resolvível internamente ou um endereço IP privado, o Gateway de aplicativo roteia a solicitação para o servidor de back-end usando seus endereços IP privados de instância. Se seu pool de back-end contém um ponto de extremidade externo ou um FQDN externamente resolvível, o Gateway de aplicativo roteia a solicitação para o servidor de back-end usando seu endereço IP público de front-end. A resolução de DNS se baseia em uma zona DNS privada ou um servidor DNS personalizado se configurado ou ele usa o padrão que DNS do Azure forneceu. Se você não tiver provisionado um endereço IP público de front-end, um é atribuído para a conectividade externa de saída.

### <a name="modifications-to-the-request"></a>Modificações à solicitação

O gateway de aplicativo insere cabeçalhos adicionais 4 para todas as solicitações antes de encaminhar as solicitações para o back-end. Esses cabeçalhos são X-forwarded-for, X-forwarded-proto, X-forwarded-port e X-original-host. O formato do cabeçalho x-forwarded-for é uma lista separada por vírgulas de IP: porta. Os valores válidos para x-forwarded-proto são HTTP ou HTTPS. X-forwarded-port especifica a porta na qual a solicitação chegou ao Gateway de Aplicativo. Cabeçalho X-original-host contém o cabeçalho de host original com o qual a solicitação chegou. Esse cabeçalho é útil em cenários como a integração do site do Azure, onde o cabeçalho de host de entrada é modificado antes do tráfego é roteado para o back-end. Opcionalmente, se a afinidade de sessão é habilitada, um cookie de afinidade gerenciados de gateway é inserido. 

Além disso, você pode configurar o gateway de aplicativo para modificar os cabeçalhos usando [cabeçalhos HTTP reescrever](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) ou modificar o caminho URI usando o caminho de configuração de substituição. Mas, a menos que configurado para fazer isso, todas as solicitações de entrada são colocadas em proxies conforme é o back-end.


## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre o funcionamento do gateway de aplicativo, consulte [componentes do gateway de aplicativo](application-gateway-components.md) entender seus componentes em mais detalhes.
