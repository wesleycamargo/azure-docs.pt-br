---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: e2c8b4e74933df593dbc023dcb886dff80554a1a
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977324"
---
As configurações do contêiner são hierárquicas e todos os contêineres no computador host usam uma hierarquia compartilhada.

Você pode usar uma das seguintes opções para especificar as configurações:

* [Variáveis de ambiente](#environment-variable-settings)
* [Argumentos de linha de comando](#command-line-argument-settings)

Os valores de variáveis de ambiente substituem os valores do argumento da linha de comandos, os quais, por sua vez, substituem os valores padrão da imagem do contêiner. Se você especificar valores diferentes em uma variável de ambiente e um argumento de linha de comando para a mesma configuração, o valor na variável de ambiente será usado pelo contêiner instanciado.

|Precedência|Local da configuração|
|--|--|
|1|Variável de ambiente| 
|2|Linha de comando|
|3|Valor padrão de imagem de contêiner|

### <a name="environment-variable-settings"></a>Configurações da variável de ambiente

Os benefícios de se usar variáveis de ambiente são:

* Várias configurações podem ser definidas.
* Vários contêineres podem usar as mesmas configurações.

### <a name="command-line-argument-settings"></a>Configurações do argumento de linha de comando

A vantagem de se usar argumentos de linha de comando é que cada contêiner pode usar configurações diferentes.
