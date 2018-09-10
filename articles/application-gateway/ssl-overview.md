---
title: Habilitando o SSL de ponta a ponta no Gateway de Aplicativo Azure AD
description: Esta artigo oferece uma visão geral do suporte a SSL de ponta a ponta do Gateway de Aplicativo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: amsriva
ms.openlocfilehash: 4575bed18697a5661d58dc350c24a9497f7c46ff
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578806"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Visão geral do SSL de ponta a ponta com um Gateway de Aplicativo

O Gateway de Aplicativo dá suporte a terminação SSL no gateway, pelo qual o tráfego flui geralmente descriptografado até os servidores de back-end. Esse recurso permite que os servidores Web fiquem livres da sobrecarga da criptografia e descriptografia dispendiosa. No entanto, para alguns clientes, a comunicação descriptografada com os servidores de back-end não é uma opção aceitável. Essa comunicação não criptografada pode ocorrer devido a requisitos de segurança e conformidade ou o aplicativo só pode aceitar uma conexão segura. Para tais aplicativos, o gateway de aplicativo dá suporte à criptografia SSL de ponta a ponta.

O SSL de ponta a ponta permite transmitir com segurança dados confidenciais para o back-end criptografado aproveitando as vantagens dos recursos de balanceamento de carga da Camada 7 que o gateway de aplicativo fornece. Alguns desses recursos têm uma afinidade de sessão baseada em cookies, um roteamento baseado em URL, suporte para o roteamento com base em sites ou a capacidade de injetar cabeçalhos X-Forwarded-*.

Quando configurado com o modo de comunicação SSL de ponta a ponta, o gateway de aplicativo encerra as sessões SSL no gateway e descriptografa o tráfego do usuário. Ele aplica as regras configuradas para selecionar uma instância de pool de back-end apropriada para rotear o tráfego. Depois, o gateway de aplicativo inicia uma nova conexão SSL com o servidor de back-end e criptografa novamente os dados usando o certificado de chave pública do servidor de back-end antes de transmitir a solicitação ao back-end. O SSL de ponta a ponta é habilitado definindo a configuração de protocolo em **BackendHTTPSetting** para HTTPS, que é então aplicado a um pool de back-end. Cada servidor de back-end no pool de back-end com SSL de ponta a ponta habilitado deve ser configurado com um certificado para permitir uma comunicação segura.

![cenário do ssl de ponta a ponta][1]

Neste exemplo, as solicitações que usam o TLS1.2 são roteadas para os servidores de back-end no Pool1 usando o SSL de ponta a ponta.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL de ponta a ponta e lista de exceções de certificados

O gateway de aplicativo se comunica somente com as instâncias de back-end conhecidas que têm o certificado na lista de exceções do gateway. Para habilitar a lista de exceções dos certificados, você precisa carregar a chave pública dos certificados do servidor de back-end no gateway de aplicativo (não no certificado-raiz). Somente as conexões com os back-ends conhecidos e na lista de exceções são permitidas. O back-ends restantes resultam em um erro de gateway. Os certificados autoassinados servem somente para teste e não são recomendados para cargas de trabalho de produção. Esses certificados devem estar na lista de exceções do gateway de aplicativo, conforme descrito nas etapas acima, antes de poder ser usados.

> [!NOTE]
> A configuração do certificado de autenticação não é necessária para serviços do Azure confiáveis, como aplicativos Web do Azure.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre SSL de ponta a ponta, vá para [Configurar um gateway de aplicativo com terminação SSL usando o portal do Azure](create-ssl-portal.md) para criar um gateway de aplicativo usando o SSL de ponta a ponta.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
