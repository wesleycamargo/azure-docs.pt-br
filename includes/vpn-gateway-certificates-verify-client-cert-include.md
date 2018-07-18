---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9fa18b14b82376a25bb434acd770d340b1ef9262
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197013"
---
Se você estiver tendo problemas para se conectar, verifique os seguintes itens:

- Se você tiver exportado um certificado do cliente, verifique se o exportou como um arquivo .pfx usando o valor padrão 'Incluir todos os certificados no caminho de certificação, se possível'. Quando você exporta usando esse valor, as informações do certificado raiz também são exportadas. Quando o certificado é instalado no computador cliente, o certificado raiz contido no arquivo .pfx também é instalado no computador cliente. O computador cliente deve ter as informações de certificado raiz instaladas. Para verificar, acesse **Gerenciar certificados de usuário** e navegue até **Trusted Root Certification Authorities\Certificates**. Verifique se o certificado raiz está listado. O certificado raiz deve estar presente para que a autenticação funcione.

- Se você estiver usando um certificado emitido com uma solução de AC corporativa e estiver com problemas na autenticação, verifique a ordem de autenticação no certificado do cliente. Você pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado do cliente e acessar **Detalhes > Uso Avançado de Chave**. Verifique se a lista mostra 'Autenticação de cliente' como o primeiro item. Caso contrário, é necessário emitir um certificado de cliente com base no modelo Usuário que tem a Autenticação de cliente como o primeiro item na lista.

- Para saber mais sobre solução de problemas de P2S, confira [Solucionar problemas de conexões P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).