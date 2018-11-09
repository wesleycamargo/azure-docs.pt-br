---
title: Noções básicas sobre as práticas recomendadas de segurança do Azure Digital Twins | Microsoft Docs
description: Azure gêmeos Digital práticas recomendadas de segurança
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: a17fe8ed47384ed248b339643be11269b8b9cdc0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092178"
---
# <a name="security-best-practices"></a>Práticas recomendadas de segurança

A segurança do Azure Digital Twins permite o acesso preciso a recursos e ações específicos no seu gráfico de IoT. Ele faz isso por meio de um gerenciamento granular de funções e permissões chamado Controle de acesso baseado em função.

O Azure Digital Twins também aproveita outros recursos de segurança presentes no IoT do Azure, incluindo o Active Directory do Azure. Por esse motivo, configurar seu aplicativo Azure Digital Twins envolve o uso de muitas das mesmas [práticas de segurança do IoT do Azure](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/) recomendadas atualmente.

Este artigo resume as principais práticas recomendadas a serem seguidas.

> [!IMPORTANT]
> Analise os recursos de segurança adicionais (incluindo os fornecedores de dispositivos) para garantir a máxima segurança para o seu espaço de IoT.

## <a name="iot-security-best-practices"></a>Práticas recomendadas de segurança de IoT

Algumas práticas recomendadas de chave para proteger com segurança seus dispositivos IoT incluem:

> [!div class="checklist"]
> * Proteja cada dispositivo conectado ao seu espaço IoT de maneira inviolável.
> * Limite a função de cada dispositivo, sensor e pessoa no espaço da IoT. Se comprometido, o impacto será minimizado.
> * Potencial uso de filtragem de endereço IP do dispositivo e restrição de porta.
> * Limitar largura de banda de e/s e o dispositivo para melhorar o desempenho. A limitação de taxa pode melhorar a segurança, evitando ataques de negação de serviço.
> * Mantenha o firmware do dispositivo atualizado.

Algumas práticas importantes para proteger com segurança um espaço IoT incluem:

> [!div class="checklist"]
> * Criptografe dados persistentes, salvos ou armazenados.
> * Requerer que senhas ou chaves sejam periodicamente alteradas ou atualizadas.
> * Limite cuidadosamente o acesso e as permissões por função (consulte as práticas recomendadas de controle de acesso baseado em função abaixo).
> * Use criptografia avançada. Isso significa exigir senhas longas, usar protocolos seguros e autenticação de dois fatores.

O monitoramento de recursos da IoT para observar outliers, ameaças ou parâmetros de recursos que estão fora do intervalo de operação normal é gerenciado pelo Azure Analytics.

> [!NOTE]
> Para mais informações sobre processamento e monitoramento de eventos, consulte nosso artigo sobre [evento> roteamento](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Práticas recomendadas do Azure Active Directory

O Azure Digital Twins usa o Active Directory do Azure para autenticar usuários e proteger aplicativos. O Active Directory do Azure oferece suporte a autenticação para várias arquiteturas modernas, todas baseadas em protocolos padrão do setor, como OAuth 2.0 ou OpenID Connect. Algumas práticas importantes para proteger o seu espaço IoT para o Active Directory do Azure incluem:

> [!div class="checklist"]
> * Armazene segredos e chaves de aplicativos do Azure Active Directory em um local seguro, como [cofre de chaves](https://azure.microsoft.com/services/key-vault/).
> * Use um certificado emitido por uma [Autoridade de Certificação confiável](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) em vez de segredos de aplicativo para autenticar.
> * Limite o escopo de acesso do OAuth 2.0 para um token.
> * Verifique o período de tempo que um token é válido e se um token permanece válido.
> * Defina períodos de tempo adequados para os tokens serem válidos.
> * Atualize tokens expirados.

## <a name="role-based-access-control-best-practices"></a>Práticas recomendadas de controle de acesso baseado em função

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as práticas recomendadas de IoT do Azure, leia [práticas recomendadas de segurança de IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Para saber mais sobre o controle de acesso baseado em função, leia [controle de acesso baseado em função](./security-role-based-access-control.md).

Para autenticação, leia [autenticar com APIs](./security-authenticating-apis.md).
