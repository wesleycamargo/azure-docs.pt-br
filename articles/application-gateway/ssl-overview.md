---
title: Habilitando o SSL de ponta a ponta no Gateway de Aplicativo Azure AD
description: Esta artigo oferece uma visão geral do suporte a SSL de ponta a ponta do Gateway de Aplicativo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2018
ms.author: amsriva
ms.openlocfilehash: fcb49f532d5dfcd340baf017bd55c69d4e81e0e6
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630675"
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
> A configuração do certificado de autenticação não é necessária para serviços do Azure confiáveis, como aplicativos Serviço de Aplicativo do Azure.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL de ponta a ponta com a v2 do SKU

Os Certificados de Autenticação foram reprovados e substituídos por Certificados raiz confiáveis no SKU do Gateway de Aplicativo v2. Eles funcionam da mesma forma para Certificados de Autenticação com algumas diferenças importantes:

- Os certificados assinados por autoridades de certificação conhecidas cujo CN corresponde ao nome do host nas configurações de back-end de HTTP não exigem etapas adicionais para que o SSL de ponta a ponta funcione. 

   Por exemplo, se os certificados de back-end forem emitidos por uma autoridade de certificação bem conhecida e tiverem um CN da contoso.com, e o campo de host da configuração http de back-end também estiver definido como contoso.com, nenhuma etapa adicional será necessária. Você pode definir o protocolo de configuração http de back-end como HTTPS e, tanto a investigação de integridade quanto o caminho de dados, serão habilitados para SSL. Se você estiver usando o Serviço de Aplicativo do Azure ou outros serviços Web do Azure como seu back-end, eles também serão implicitamente confiáveis e nenhuma etapa adicional será necessária para o SSL de ponta a ponta.
- Se o certificado for autoassinado ou assinado por intermediários desconhecidos, para ativar o SSL de ponta a ponta na SKU v2, um certificado raiz confiável deverá ser definido. O Gateway de Aplicativo só se comunicará com back-ends cujo certificado raiz do certificado do servidor corresponda a um da lista de certificados raiz confiáveis na configuração http de back-end associada ao pool.
- Além da correspondência do certificado raiz, o Gateway de Aplicativo também validará se a configuração do Host especificada na configuração http de back-end corresponde à configuração do nome comum (CN) apresentado pelo certificado SSL do servidor de back-end. Ao tentar estabelecer uma conexão SSL com o back-end, o Gateway de Aplicativo definirá a extensão de Indicação de Nome de Servidor (SNI) para o Host especificado na configuração http de back-end.
- Se **escolher o nome do host do endereço de back-end** for escolhido em vez do campo Host na configuração http de back-end, o cabeçalho SNI será sempre definido como o FQDN do pool de back-end e o CN no certificado SSL do servidor de back-end deverá corresponder ao FQDN. Os membros do pool de back-end com IPs não têm suporte neste cenário.
- O certificado raiz é um certificado raiz codificado em base64 a partir dos certificados do servidor de back-end.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre SSL de ponta a ponta, vá para [Configurar um SSL de ponta a ponta usando o gateway de aplicativo com o PowerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um Gateway de Aplicativo usando o SSL de ponta a ponta.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
