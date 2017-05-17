---
title: "Explicando o Banco de Dados do Azure para Unidades de Computação e Unidades de Armazenamento PostgreSQL | Microsoft Docs"
description: "Banco de Dados do Azure para PostgreSQL: Explica Unidade de Computação e Unidade de Armazenamento e o que acontece quando você atinge o máximo de Unidade de Computação ou Unidade de Armazenamento."
services: postgresql
author: 
ms.author: 
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0cf1f0cf3d93dac49e75f558dcba8af002a492da
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="explaining-compute-unit-and-storage-unit"></a>Explicação da Unidade de Computação e Unidade de Armazenamento
Este artigo explica os conceitos de Unidade de Computação e Unidade de Armazenamento e o que acontece quando você atinge o máximo de Unidade de Computação ou Unidade de Armazenamento.

## <a name="what-are-compute-units"></a>O que são unidades de computação?
Unidades de computação são uma medida de taxa de transferência de processamento da CPU que possuem disponibilidade garantida para um único Banco de Dados do Azure para o servidor PostgreSQL. Uma unidade de computação é uma medida combinada de recursos de CPU e memória. Em geral, 50 unidades de computação serão equivalentes à metade de um núcleo, 100 unidades de computação serão iguais a um núcleo e 2.000 unidades de computação serão iguais a vinte núcleos de taxa de transferência de processamento de disponibilidade garantida para o servidor. 

A quantidade de memória por unidade de computação é otimizada para as camadas de serviço Basic, Standard e Premium. Dobrar as unidades de computação aumentando o nível de desempenho equivale a dobrar o conjunto de recursos disponíveis para esse Banco de Dados do Azure único para PostgreSQL. 

Por exemplo, uma camada Standard de 2.000 unidades de computação fornece 20 vezes mais taxa de transferência de CPU e memória que uma Standard configurada com 100 unidades de computação. No entanto, apesar das 100 unidades de computação da camada Standard fornecerem a mesma taxa de transferência de CPU em comparação com 100 unidades de computação da camada Basic, a quantidade de memória que é pré-configurada na camada Standard é o dobro da quantidade de memória configurada para a camada Basic e, portanto, fornece melhor latência de desempenho e de transação da carga de trabalho.

>[!IMPORTANT]
>Para uma taxa de transferência de desempenho de carga de trabalho previsível e uma alta simultaneidade de usuário, é altamente recomendável que você escolha a camada de serviço Standard.

Você pode alterar [as camadas de serviço](concepts-service-tiers.md) a qualquer momento sem praticamente nenhum tempo de inatividade do aplicativo. Para muitas empresas e aplicativos, ser capaz de criar um ou vários bancos de dados dentro de cada Banco de Dados do Azure único para o servidor PostgreSQL e aumentar ou diminuir o desempenho sob demanda oferece a flexibilidade necessária para gerenciar os custos.

>[!IMPORTANT]
>Atualmente, suportamos o aumento e a redução dos níveis de desempenho dentro de uma camada de serviço. Por exemplo, você pode aumentar de 100 unidades de computação da camada Standard para 400 unidades de computação da camada Standard. Por outro lado, você pode diminuir de 400 unidades de computação da camada Standard para 100 unidades de computação da camada Standard. O recurso para ser capaz de dimensionar entre camadas de serviço, por exemplo, entre as camadas Basic e Standard estará disponível no futuro.

## <a name="what-are-storage-units"></a>O que são unidades de armazenamento?
Unidades de armazenamento são uma medida de capacidade de armazenamento provisionado que possui disponibilidade garantida para um único Banco de Dados do Azure para o servidor PostgreSQL. Cada camada de serviço tem uma quantidade fixa de armazenamento provisionado, que está incluída no preço da camada de serviço selecionada.

Opcionalmente, as unidades de armazenamento podem ser dimensionadas independentemente das unidades de computação, em incrementos de 125 GB, até o máximo permitido de armazenamento conforme descrito na tabela a seguir.
| **Recursos de camada de serviço** | **Básico** | **Standard** | **Premium\*** |
|---------------------------|-----------|--------------|---------------|
| Unidades de armazenamento incluídas | 50 GB | 125 GB | 250 GB |
| Armazenamento total máximo | 1050 GB | 10000 GB | 4000 GB |
| Garantia IOPS de armazenamento | N/D  | Sim | Sim |
| IOPS de armazenamento máximo | N/D  | 30.000 | 40.000 |
As camadas de serviço Standard e Premium também fornecem uma garantia de IOPS provisionado. A quantidade de IOPS provisionado disponível depende da camada de serviço e da capacidade de armazenamento configurada. Para servidores implantados na camada Standard, o IOPS possui o tamanho fixo de 3 vezes o armazenamento provisionado. 

Por exemplo, se você tiver 125 GB de armazenamento provisionado, terá 375 IOPS provisionados disponíveis para o seu servidor. A camada Premium fornece uma latência muito baixa e um alto armazenamento de IOPS. Para servidores implantados na camada Premium, a o IOPS de baixa latência provisionado pode dimensionar entre cinco a dez vezes o armazenamento provisionado.
>[!IMPORTANT]
>Se sua carga de trabalho estiver afunilada pelas unidades de computação configuradas, pode ser que você não consiga aproveitar a capacidade total de IOPS provisionado. É recomendável que você monitore as unidades de computação e considere o dimensionamento das unidades de computação se não for possível aproveitar ao máximo o IOPS provisionado disponível.

Dimensionar as unidades de armazenamento aumentando o armazenamento provisionado equivale a aumentar proporcionalmente o IOPS provisionado para as camadas Standard e Premium.

>[!IMPORTANT]
>A camada Basic não oferece garantia de IOPS.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Como posso determinar o número de unidades de computação necessárias para a minha carga de trabalho?
Se você estiver pretendendo migrar o PostgreSQL local ou em execução em uma máquina virtual, você pode determinar o número de unidades de computação estimando quantos núcleos de taxa de transferência de processamento são necessários para a sua carga de trabalho. 

Se a máquina local ou virtual estiver utilizando 4 núcleos (sem contar o hyperthread da CPU), você pode começar configurando 400 unidades de computação para o Banco de Dados do Azure para PostgreSQL. É possível aumentar ou diminuir as unidades de computação dinamicamente conforme as suas necessidades de carga de trabalho sem praticamente nenhum tempo de inatividade do aplicativo. Você também pode monitorar o consumo de unidades de computação por meio do Portal ou da CLI para dimensionar corretamente os recursos do servidor PostgreSQL.

## <a name="how-can-i-determine-the-number-of-storage-units-needed-for-my-workload"></a>Como posso determinar o número de unidades de armazenamento necessárias para a minha carga de trabalho?
Você pode estimar a quantidade de unidades de armazenamento necessárias determinando primeiro a quantidade de capacidade de armazenamento necessária. As camadas Basic, Standard e Premium vêm com armazenamento incluído integrado na SKU. 

O segundo fator importante é determinar o IOPS necessário. A camada Basic fornece IOPS variável sem garantia. A camada Standard é dimensionada a uma taxa fixa de três IOPS por GB de armazenamento provisionado e fornece garantia de IOPS. Você também pode monitorar o consumo de IOPS provisionado por meio do Portal ou da CLI para determinar o uso.

>[!IMPORTANT]
>Após provisionadas as unidades de armazenamento não podem ser reduzidas dinamicamente.

## <a name="what-happens-when-i-hit-my-maximum-compute-units-andor-storage-units"></a>O que acontece quando eu atingir o máximo unidades de computação e/ou unidades de armazenamento?
Os níveis de desempenho são calibrados e controlados para fornecer os recursos necessários para executar sua carga de trabalho de banco de dados até os limites máximos permitidos para a sua camada de serviço e nível de desempenho selecionados. 

Se a sua carga de trabalho está atingindo os limites em um dos limites de unidades de computação/IOPS provisionado, você continuará a receber os recursos no nível máximo permitido, mas provavelmente você observará latências maiores para as suas consultas. Esses limites não resultam em erros, mas apenas em uma lentidão na carga de trabalho, a menos que a lentidão se torne tão grave que as consultas comecem a não serem executadas a tempo. 

Se você está atingindo os limites de número máximo de conexões permitido, você verá erros explícitos. Consulte [Limites de recurso do Banco de Dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits) para obter mais informações sobre os limites de recursos. <Need to write about the behavior if a user reaches the storage capacity limits>

## <a name="next-steps"></a>Próximas etapas
- Consulte [Camadas de serviço do Banco de Dados do Azure para PostgreSQL](./concepts-service-tiers.md) para obter informações sobre as unidades de computação e unidades de armazenamento disponíveis para servidores individuais.


