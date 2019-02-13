---
title: Visão geral dos serviços do Machine Learning (com R) no Banco de Dados SQL do Azure (versão prévia)
description: Este tópico descreve os Serviços do Machine Learning do Banco de Dados SQL do Azure (com R) e explica como eles funcionam.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: e2159e7cc59830c3d0d10f1c5b9697ab5b45b666
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824778"
---
# <a name="machine-learning-services-with-r-in-azure-sql-database-preview"></a>Serviços do Machine Learning (com R) no Banco de Dados SQL do Azure (versão prévia)

Os Serviços do Machine Learning são um recurso do Banco de Dados SQL do Azure, usado para executar scripts do R no banco de dados. O recurso inclui pacotes R da Microsoft para análise preditiva de alto desempenho e machine learning. Os dados relacionais podem ser usados em scripts R por meio de procedimentos armazenados, do script T-SQL que contém instruções do R ou do código R que contém o T-SQL.

> [!NOTE]
> No momento, os Serviços do Machine Learning (com o R) no Banco de Dados SQL do Azure estão em versão prévia pública. [Inscreva-se na versão prévia](#signup) abaixo.

## <a name="what-you-can-do-with-r"></a>O que você pode fazer com o R

Use o poder da linguagem R para fornecer análise avançada e machine learning no banco de dados. Essa capacidade leva os cálculos e o processamento para o local em que os dados residem, eliminando a necessidade de efetuar pull de dados na rede. Além disso, aproveite o poder dos pacotes empresariais do R para fornecer análise avançada em escala.

Os Serviços do Machine Learning incluem uma distribuição base do R, sobreposta com pacotes empresariais do R da Microsoft. As funções e os algoritmos do R da Microsoft foram desenvolvidos para escala e utilidade, fornecendo análise preditiva, modelagem estatística, visualizações de dados e algoritmos de machine learning de ponta.

### <a name="r-packages"></a>Pacotes R

Os pacotes R de software livre mais comuns são pré-instalados nos Serviços do Machine Learning. Os seguintes pacotes R da Microsoft também estão incluídos:

| Pacote R | DESCRIÇÃO|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | O Microsoft R Open é a distribuição aprimorada de R da Microsoft. É uma plataforma de software livre completa para análise estatística e ciência de dados. É baseada no R e totalmente compatível com ele, além de incluir outras funcionalidades para um melhor desempenho e capacidade de reprodução. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | O RevoScaleR é a biblioteca principal para o R escalonável. As funções nessa biblioteca estão entre mais amplamente usadas. As transformações e a manipulação de dados, resumo estatístico, visualização e muitas formas de modelagem e de análises são encontrados nessas bibliotecas. Além disso, as funções nessas bibliotecas distribuem automaticamente as cargas de trabalho entre os núcleos disponíveis para processamento paralelo, com a capacidade de trabalhar em partes de dados coordenadas e gerenciadas pelo mecanismo de cálculo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | O MicrosoftML adiciona algoritmos de machine learning para criar modelos personalizados para análise de texto, análise de imagem e de sentimento. |

Além dos pacotes pré-instalados, é possível [instalar pacotes de adição](sql-database-connect-query-r.md#add-package).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Inscrever-se para obter a visualização

Para se inscrever na versão prévia pública, siga estas etapas:

1. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

2. Envie um email para a Microsoft pelo endereço [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) a fim de se inscrever na versão prévia pública. A versão prévia pública dos Serviços de Machine Learning (com R) no Banco de Dados SQL não está habilitada por padrão.

Após se inscrever no programa, a Microsoft integrará você à versão prévia pública e habilitará o R para seu banco de dados novo ou existente.

Os Serviços do Machine Learning (com R) no Banco de Dados SQL estão disponíveis atualmente apenas no modelo de compra baseado em vCore das camadas de serviço **Uso Geral** e **Comercialmente Crítico** para opções de implantação autônomas e de pool elástico. Nesta versão prévia pública inicial, não há suporte para a camada de serviço **Hiperescala** e a opção de implantação **Instância Gerenciada**.

Atualmente, R é a única linguagem com suporte. No momento, não há suporte para Python. A versão prévia está inicialmente disponível nas seguintes regiões: Europa Ocidental, Europa Setentrional, Oeste dos EUA 2, Leste dos EUA, Centro-Sul dos EUA, Centro-Norte dos EUA, Canadá Central, Sudeste Asiático, Sul da Índia e Sudeste da Austrália. Serão adicionadas mais regiões posteriormente.

Não use os Serviços do Machine Learning com R para cargas de trabalho de produção durante a versão prévia pública.

## <a name="next-steps"></a>Próximas etapas

- Confira as [principais diferenças dos serviços do Machine Learning do SQL Server](sql-database-machine-learning-services-differences.md)
- Para saber como usar os Serviços do Microsoft Machine Learning (com R) no Banco de Dados SQL do Azure, confira [Guia de início rápido](sql-database-connect-query-r.md).
- Saiba mais com os [tutoriais da linguagem R do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)