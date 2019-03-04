---
title: O que é a Configuração de Aplicativo do Azure | Microsoft Docs
description: Uma visão geral do serviço Configuração de Aplicativo do Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 11dd91039bb352e86800982d0a294f82622a56fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885014"
---
# <a name="what-is-azure-app-configuration"></a>O que é a Configuração de Aplicativo do Azure

A Configuração de Aplicativo do Azure fornece um serviço para gerenciar as configurações de aplicativo centralmente. Programas modernos, especialmente aqueles em execução em uma nuvem, geralmente têm muitos componentes que são distribuídos por natureza. A distribuição das definições de configuração entre esses componentes pode levar a erros difíceis de serem resolvidos durante uma implantação de aplicativo. A Configuração de Aplicativo permite armazenar todas as configurações do aplicativo e proteger seus acessos em um só lugar.

## <a name="why-use-app-configuration"></a>Por que usar a Configuração de Aplicativo

Os aplicativos baseados em nuvem geralmente são executados em várias máquinas virtuais ou contêineres em várias regiões e usam diversos serviços externos. A criação de um aplicativo distribuído desse tipo que seja robusto e escalonável é um verdadeiro desafio. Várias metodologias de programação surgiram para ajudar os desenvolvedores a lidar com o aumento da complexidade da criação desses aplicativos. Por exemplo, o aplicativo de 12 fatores fornece detalhes de muitos padrões de arquitetura bem testados e melhores práticas para uso com aplicativos de nuvem. Uma recomendação básica deste guia é separar a configuração do código. Isso significa que a configuração de um aplicativo, como as configurações, deve ser mantida externamente ao seu executável e lida no ambiente de tempo de execução ou em uma fonte externa.

Embora qualquer aplicativo possa usá-la, estes são bons exemplos dos tipos de aplicativo que devem utilizar a Configuração de Aplicativo:

* Microsserviços baseados no AKS, no Service Fabric ou em outros aplicativos em contêineres implantados em uma ou mais geografias.
* Aplicativos sem servidor, incluindo o Azure Functions, ou outros aplicativos de computação sem estado controlado por evento.
* Pipeline de implantação contínua.

A Configuração de Aplicativo oferece os seguintes benefícios:

* Um serviço totalmente gerenciado que pode ser configurado em minutos.
* Representações e mapeamentos de chave flexíveis.
* Marcação com rótulos.
* Reprodução pontual de configurações.
* Comparação de dois conjuntos de configurações em dimensões com definições personalizadas.
* Segurança aprimorada por meio de identidades gerenciadas pelo Azure.
* Criptografias de dados completas, em repouso ou em trânsito.
* Integração nativa com estruturas populares.

## <a name="how-to-use-app-configuration"></a>Como usar a Configuração de Aplicativo

A maneira mais fácil de adicionar um repositório de configurações de aplicativo ao seu aplicativo é por meio de uma biblioteca de clientes fornecida pela Microsoft. Dependendo da estrutura e da linguagem de programação, estes são os melhores métodos disponíveis para você:

| Estrutura e linguagem de programação | Como conectar-se |
|---|---|
| **.NET Core** e **ASP.NET Core** | Provedor de configuração da Configuração de Aplicativo para .NET Core. |
| **.NET** e **ASP.NET** | Construtor de configuração da Configuração de Aplicativo para .NET. |
| **Java Spring** | Cliente de configuração da Configuração de Aplicativo para Spring Cloud. |
| Outros | API REST da Configuração de Aplicativo. |

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um aplicativo Web ASP.NET](quickstart-aspnet-core-app.md)  
