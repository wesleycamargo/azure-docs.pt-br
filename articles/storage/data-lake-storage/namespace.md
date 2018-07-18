---
title: Espaço de Dados Hierárquico de Pré-visualização do Storage Data Storage Gen2 do Azure
description: Descreve o conceito do namespace hierárquico para a visualização do Azure Data Lake Storage Gen2
services: storage
author: jamesbak
manager: twooley
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: f7c3820624a4ef27e2ece4d902f2c033b6a6f48f
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061213"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Espaço de nomes hierárquico de visualização do Gen2 do Windows Lake Data Storage

Um mecanismo chave que permite que o Pré-visualização do Azure Data Lake Storage Gen2 forneça o desempenho do sistema de arquivos na escala de armazenamento de objetos e preços, é a adição de um **namespace hierárquico**. Isso permite que a coleta de objetos / arquivos em uma conta seja organizada em uma hierarquia de diretórios e subdiretórios aninhados, da mesma forma que o sistema de arquivos do computador é organizado. Com o namespace hierárquico ativado, o Data Lake Storage Gen2 fornece a escalabilidade e a relação custo-benefício do armazenamento de objetos, com semântica do sistema de arquivos que são familiares aos mecanismos e estruturas de análise.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Os benefícios do namespace hierárquico

> [!NOTE]
> Durante a visualização pública do Azure Data Lake Storage Gen2, alguns dos recursos listados abaixo podem variar em sua disponibilidade. À medida que novos recursos e regiões forem lançados durante o programa de visualização, essas informações serão comunicadas por meio do nosso grupo dedicado do Yammer.  

Os benefícios a seguir estão associados a sistemas de arquivos que implementam um namespace hierárquico em dados de blob:

- **Manipulação de Diretório Atômico:** Armazenamentos de objetos aproximam uma hierarquia de diretórios ao adotar uma convenção de barras de incorporação (/) no nome do objeto para denotar segmentos de caminho. Embora essa convenção funcione satisfatoriamente para organizar objetos, a convenção não fornece assistência para ações como mover, renomear ou excluir diretórios. Sem diretórios reais, os aplicativos devem processar potencialmente milhões de blobs individuais para obter tarefas em nível de diretório. Por outro lado, o espaço de nomes hierárquico processa essas tarefas atualizando uma única entrada (o diretório pai). 

    Essa otimização dramática é especialmente significativa para muitas estruturas de análise de big data. Ferramentas como Hive, Spark, etc. geralmente gravam a saída em locais temporários e depois renomeiam a localização na conclusão da tarefa. Sem o namespace hierárquico, essa renomeação pode demorar mais do que o próprio processo de análise. Uma menor latência de trabalho é igual ao custo total de propriedade (TCO) mais baixo para cargas de trabalho de análise.

- **Estilo familiar de interface:** Os sistemas de arquivos são bem compreendidos pelos desenvolvedores e usuários. Não há necessidade de aprender um novo paradigma de armazenamento quando você migra para a nuvem, pois a interface do sistema de arquivos exposta pelo Data Lake Storage Gen2 é o mesmo paradigma usado pelos computadores, grandes e pequenos.

Uma das razões pelas quais os repositórios de objetos não têm suporte histórico a namespaces hierárquicos é que os namespaces hierárquicos limitam a escala. No entanto, o namespace hierárquico do Data Lake Storage Gen2 é dimensionado linearmente e não prejudica a capacidade ou o desempenho dos dados.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Quando habilitar o namespace hierárquico

A ativação do namespace hierárquico é recomendada para cargas de trabalho de armazenamento projetadas para sistemas de arquivos que manipulam diretórios. Isso inclui todas as cargas de trabalho que são principalmente para processamento de análise. Conjuntos de dados que exigem um alto grau de organização também serão beneficiados ao habilitar o namespace hierárquico.

As razões para habilitar o namespace hierárquico são determinadas por uma análise de TCO. De um modo geral, melhorias na latência da carga de trabalho devido à aceleração do armazenamento exigirão recursos de computação por menos tempo. A latência de muitas cargas de trabalho pode ser aprimorada devido à manipulação de diretório atômico que é ativada pelo namespace hierárquico. Em muitas cargas de trabalho, o recurso de computação representa> 85% do custo total e, portanto, até mesmo uma redução modesta na latência da carga de trabalho equivale a uma quantidade significativa de economia de TCO. Mesmo nos casos em que a habilitação do namespace hierárquico aumenta os custos de armazenamento, o TCO ainda é reduzido devido a custos de computação reduzidos.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Quando desabilitar o namespace hierárquico

Algumas cargas de trabalho de armazenamento de objeto não podem obter nenhum benefício ativando o espaço de nomes hierárquico. Exemplos dessas cargas de trabalho incluem backups, armazenamento de imagens e outros aplicativos em que a organização de objetos é armazenada separadamente dos próprios objetos (*,por exemplo,*, em um banco de dados separado).

> [!NOTE]
> Com o release de pré-visualização, se você ativar o namespace hierárquico, não haverá interoperabilidade de dados ou operações entre as APIs REST do Blob e do Data Lake Storage Gen2. Essa funcionalidade será adicionada durante a visualização.

## <a name="next-steps"></a>Próximas etapas

- [Crie uma conta de armazenamento](./quickstart-create-account.md)