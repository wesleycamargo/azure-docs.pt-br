---
title: Limites de recursos do Banco de Dados SQL do Azure - instância gerenciada | Microsoft Docs
description: Este artigo fornece uma visão geral dos limites de recursos do Banco de Dados SQL do Azure para instâncias gerenciadas.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp
manager: craigg
ms.date: 10/17/2018
ms.openlocfilehash: 97c141b6e0c071a8cea27f9a873f28a6c5113a18
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394847"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Visão geral Limites de recursos da Instância Gerenciada do Banco de Dados SQL do Azure

Este artigo fornece uma visão geral dos limites de recursos da Instância Gerenciada do Banco de Dados SQL do Azure e fornece informações sobre como criar solicitações para aumentar os limites de assinatura regional padrão. 

> [!NOTE]
> Para outras limitações da Instância Gerenciada, consulte [modelo de compra baseado em vCore](sql-database-managed-instance.md#vcore-based-purchasing-model) e [nível de serviço de Instância Gerenciada](sql-database-managed-instance.md#managed-instance-service-tiers). Para diferenças em recursos suportados e instruções T-SQL, consulte [Diferenças de recursos](sql-database-features.md) e [Suporte à instrução T-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Limites de recursos no nível da instância

A Instância Gerenciada tem características e limites de recursos que dependem da infraestrutura e arquitetura subjacentes. Os limites dependem da geração de hardware e da camada de serviço.

### <a name="hardware-generation-characteristics"></a>Características de geração de hardware

A Instância Gerenciada do Banco de Dados SQL do Azure pode ser implantada em duas gerações de hardware (Gen4 e Gen5). As gerações de hardware têm características diferentes que são descritas na tabela a seguir:

|   | **Geração 4** | **Geração 5** |
| --- | --- | --- |
| Hardware | Processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz, SSD ligado vCore = 1 PP (núcleo físico) | Intel E5-2673 v4 (Broadwell) 2,3 GHz, SSD eNVM rápido, vCore = 1 LP (hyper-thread) |
| Computação | 8, 16, 24 vCores | 8, 16, 24, 32, 40, 64, 80 vCores |
| Memória | 7 GB por vCore | 5.1 GB por vCore |
| Armazenamento máximo (comercialmente crítico) | 1 TB | 1 TB, 2 TB ou 4 TB, dependendo do número de núcleos |

### <a name="service-tier-characteristics"></a>Características de camada de serviço

A Instância Gerenciada tem dois níveis de serviço - Propósito Geral e Crítico de Negócios (Visualização Pública). Essas camadas fornecem recursos diferentes, conforme descrito na tabela a seguir:

| **Recurso** | **Uso geral** | **Business Critical (visão geral)** |
| --- | --- | --- |
| Número de vCores\* | Gen4: 8, 16, 24<br/>Gen5: 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 8, 16, 24, 32, 40, 64, 80 |
| Memória | Gen4: 56GB-156GB<br/>Gen5: 44GB-440GB<br/>\*Proporcional ao número de vCores | Gen4: 56GB-156GB <br/> Gen5: 44GB-440GB<br/>\*Proporcional ao número de vCores |
| Tamanho máximo de armazenamento | 8 TB | Gen 4: 1 TB <br/> Geração 5: <br/>- 1 TB for 8, 16 vCores<br/>- 2 TB para 24 vCores<br/>- 4 TB para 32, 40, 64, 80 vCores |
| Armazenamento máximo por banco de dados | Determinado pelo tamanho de armazenamento máximo por instância | Determinado pelo tamanho de armazenamento máximo por instância |
| Número máximo de bancos de dados por instância | 100 | 100 |
| Arquivos de banco de dados máximo por instância | Até 280 | Ilimitado |
| IOPS de armazenamento máximo esperado | 500-5000 ([depende do tamanho do arquivo de dados](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)). | Depende da velocidade SSD subjacente. |

## <a name="supported-regions"></a>Regiões com suporte

A instalação pode ser criada somente em [regiões suportadas](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Se você deseja criar uma Instância Gerenciada na região que atualmente não é suportada, você pode [enviar solicitação de suporte por meio do portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

Atualmente, a instância gerenciada oferece suporte à implantação apenas nos seguintes tipos de inscrição:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pague-conforme-o-uso](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provedor de serviços de nuvem (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)

> [!NOTE]
> Essa limitação é temporária. Novos tipos de assinatura serão ativados no futuro.

## <a name="regional-resource-limitations"></a>Limitações de recursos regionais

Os tipos de assinatura suportados podem conter um número limitado de recursos por região. A Instância Gerenciada tem dois limites padrão por região do Azure, dependendo do tipo de assinatura:

- **Limite de sub-rede**: o número máximo de sub-redes nas quais as instâncias gerenciadas são implantadas em uma única região.
- **Limite do número de instâncias**: o número máximo de instâncias que podem ser implantadas em uma única região.

Na tabela a seguir são mostrados limites regionais padrão para assinaturas suportadas:

|Tipo de assinatura| Número máximo de sub-redes da instância gerenciada | Número máximo de instâncias |Número máximo de GP gerenciados instâncias *|Número máximo de continuidade de negócios gerenciados instâncias *|
| :---| :--- | :--- |:--- |:--- |
|Pago conforme o uso|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* Você pode implantar 1 ou 4 instâncias de GP em uma sub-rede, de modo que o número total de "instâncias" na sub-rede nunca exceda 4.

** Número máximo de instâncias em uma camada de serviço se aplica se não houver instâncias em outra camada de serviço. Caso planeje misturar instâncias GP e BC na mesma sub-rede, use a seção a seguir como referência para combinações permitidas. Como uma regra simples, o número total de sub-redes não pode exceder 3 e o número total de unidades de instância não pode exceder 12.

Esses limites podem ser aumentados criando uma solicitação de suporte [especial no portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance) se você precisar de mais Instâncias Gerenciadas na região atual. Como alternativa, você pode criar novas Instâncias Gerenciadas em outra região do Azure sem enviar solicitações de suporte.

> [!IMPORTANT]
> Ao planejar suas implantações, considere que uma instância Business Critical (BC) (devido à redundância adicionada) geralmente consome 4x mais capacidade do que uma instância General Purpose (GP). Assim, para seus cálculos, 1 instância de GP = 1 unidade de instância e 1 instância de BC = 4 unidades de instância. Para simplificar sua análise de consumo em relação aos limites padrão, resuma as unidades da instância em todas as sub-redes da região onde as Instâncias Gerenciadas são implantadas e compare os resultados com os limites da unidade da instância para o seu tipo de assinatura.

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>Estratégias para implementar instâncias mistas de uso geral e crítico de negócios

As assinaturas do [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) podem ter combinações de instâncias de GP e BC. No entanto, há algumas restrições sobre o posicionamento das instâncias nas sub-redes.

> [!Note] 
> [Pague-conforme-o-uso](https://azure.microsoft.com/offers/ms-azr-0003p/) e [Provedor de Serviços de Nuvem (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) podem ter uma Business Critical ou até 4 instâncias de Propósito Geral.

Os exemplos a seguir abrangem casos de implantação com sub-redes não vazias e camadas de serviço GP e BC mistas.

|Número de sub-redes|Subrede 1|Subrede 2|Subrede 3|
|:---|:---|:---|:---|
|1|1 BC e até 8 GP<br>2 BC e até 4 GP|N/D| N/D|
|2|0 BC, até 4 GP|1 BC, até 4 GP<br>2 BC, 0 GP|N/D|
|2|1 BC, 0 GP|0 BC, até 8 GP<br>1 BC, até 4 GP|N/D|
|2|2 BC, 0 GP|0 BC, até 4 GP|N/D|
|3|1 BC, 0 GP|1 BC, 0 GP|0 BC, até 4 GP|
|3|1 BC, 0 GP|0 BC, até 4 GP|0 BC, até 4 GP|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Obtaining a larger quota for SQL Managed Instance

Se você precisar de mais instâncias gerenciadas nas regiões atuais, poderá enviar a solicitação de suporte para estender a cota usando o portal do Azure. Para iniciar o processo de obtenção de uma cota maior:

1. Abra **ajuda + suporte**e clique em **nova solicitação de suporte**. 

   ![Ajuda e Suporte](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na guia Básico da nova solicitação de suporte:
   - Para **tipo de problema**, selecione **Limites de serviço e assinatura (cotas)**.
   - Em **Assinatura**, selecione sua assinatura.
   - Para **tipo de cota**, selecione **instância gerenciada do banco de dados do SQL**.
   - Para **plano de suporte**, selecione o plano de suporte.

     ![Cota de tipo de problema](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Clique em **Próximo**.
4. Na guia Problema para a nova solicitação de suporte:
   - Para **Gravidade**, selecione o nível de gravidade do problema.
   - Para **Detalhes**, forneça informações adicionais sobre o seu problema, incluindo mensagens de erro.
   - Para **Upload de arquivo**, anexe um arquivo com mais informações (até 4 MB).

     ![Detalhes do problema](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Uma solicitação válida deve incluir:
     > - Região na qual assinatura limite precisa ser aumentado
     > - Número necessário de instâncias, por nível de serviço, em sub-redes existentes após o aumento da cota (se alguma das sub-redes existentes precisar ser expandida
     > - Número necessário de novas sub-redes e número total de instâncias por camada de serviço nas novas sub-redes (se você precisar implantar instâncias gerenciadas em novas sub-redes).
     
5. Clique em **Próximo**.
6. Na guia Informações de contato da nova solicitação de suporte, insira o método de contato preferencial (email ou telefone) e os detalhes de contato.
7. Clique em **Criar**.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a Instância Gerenciada, consulte [O que é uma Instância Gerenciada?](sql-database-managed-instance.md). 
- Para saber mais sobre preços, consulte [Preços do Banco de Dados SQL gerenciados](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Para saber como criar sua primeira Instância Gerenciada, consulte o [Guia de início rápido](sql-database-managed-instance-get-started.md).
