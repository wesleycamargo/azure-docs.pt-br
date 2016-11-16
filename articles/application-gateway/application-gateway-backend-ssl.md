---
title: "Habilitando política de SSL e SSL de ponta a ponta com o Gateway de Aplicativo | Microsoft Docs"
description: "Esta página oferece uma visão geral do suporte a SSL de ponta a ponta do Gateway de Aplicativo."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/10/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9642ea16bff4b0cd4bf3e88d7530765d4c7bfbc5


---
# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Habilitando política de SSL e SSL de ponta a ponta no gateway de aplicativo
## <a name="overview"></a>Visão geral
O gateway de aplicativo dá suporte a terminação SSL no gateway, pelo qual o tráfego flui geralmente descriptografado até os servidores de back-end. Isso permite que os servidores Web fiquem livres da sobrecarga dispendiosa de criptografia/descriptografia. No entanto, para alguns clientes, a comunicação descriptografada com os servidores de back-end não é uma opção aceitável. Isso pode ser devido a requisitos de segurança/conformidade ou o aplicativo pode só aceitar conexões seguras. Para tais aplicativos, o gateway de aplicativo agora dá suporte a criptografia SSL de ponta a ponta.

O SSL de ponta a ponta permite transmitir com segurança dados confidenciais para o back-end criptografado aproveitando as vantagens dos recursos de balanceamento de carga da Camada 7 que o gateway de aplicativo fornece, como a afinidade de cookies, roteamento baseado em URL, suporte ao roteamento baseado em sites ou capacidade de injetar cabeçalhos X-Forwarded-*.

Quando configurado com o modo de comunicação SSL de ponta a ponta, o gateway de aplicativo encerra as sessões de SSL de usuário no gateway e descriptografa o tráfego do usuário. Ele aplica as regras configuradas para selecionar uma instância de pool de back-end apropriada para rotear o tráfego. O gateway de aplicativo, em seguida, inicia uma nova conexão SSL com o servidor de back-end e criptografa novamente os dados usando o certificado de chave pública do servidor de back-end antes de transmitir a solicitação para o back-end. O SSL de ponta a ponta SSL é habilitado ao definir a configuração de protocolo no BackendHTTPSetting como Https, que é então aplicado a um pool de back-end. Cada servidor de back-end no pool de back-end com SSL de ponta a ponta habilitado deve ser configurado com um certificado para permitir uma comunicação segura.

![cenário do ssl de ponta a ponta][1]

Neste exemplo, as solicitações que usam o TLS1.2 são roteadas para os servidores de back-end no Pool1 usando o SSL de ponta a ponta.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL de ponta a ponta e lista de exceções de certificados
O gateway de aplicativo se comunica somente com as instâncias de back-end conhecidas que têm o certificado na lista de exceções do gateway. Para habilitar a lista de exceções dos certificados, você precisa carregar a chave pública dos certificados do servidor de back-end no gateway de aplicativo (não no certificado-raiz). Somente as conexões com os back-ends conhecidos e na lista de exceções são permitidas. O back-ends restantes resultam em um erro de gateway. Os certificados autoassinados servem somente para teste e não são recomendados para cargas de trabalho de produção. Esses certificados também devem estar na lista de exceções do gateway de aplicativo, conforme descrito nas etapas acima, antes de poder ser usados.

## <a name="application-gateway-ssl-policy"></a>Política de SSL do Gateway de Aplicativo
O gateway de aplicativo também dá suporte às políticas de negociação de SSL configuráveis, que permitem que os clientes tenham mais controle sobre as conexões SSL no gateway de aplicativo.

1. SSL 2.0 e 3.0 desabilitados por padrão para todos os Gateways do Aplicativo. Eles não são configuráveis.
2. A definição da política SSL dá a opção de desabilitar qualquer um dos três protocolos a seguir: TLSv1\_0, TLSv1\_1, TLSv1\_2.
3. Se nenhuma política SSL for definida, todos os três (TLSv1\_0, TLSv1\_1, TLSv1_2) serão habilitados.

## <a name="next-steps"></a>Próximas etapas
Depois de aprender sobre o SSL de ponta a ponta e a política de SSL, vá para [habilitar o SSL de ponta a ponta no gateway de aplicativo](application-gateway-end-to-end-ssl-powershell.md) para criar um gateway de aplicativo com capacidade de enviar o tráfego para os back-ends de forma criptografada.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png



<!--HONumber=Nov16_HO2-->


