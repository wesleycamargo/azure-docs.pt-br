---
title: Transformação do Coletor de Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação do Coletor de Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 381dc2f9f6d3a074af00ba047472719c086f5811
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408401"
---
# <a name="mapping-data-flow-sink-transformation"></a>Transformação do Coletor de Fluxo de Dados de Mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Opções de coletor](media/data-flow/windows1.png "coletor 1")

Após a conclusão da transformação de fluxo de dados, você pode coletar os dados transformados em um conjunto de dados de destino. Na Transformação do Coletor, você pode escolher a definição de conjunto de dados que deseja usar para os dados de saída de destino. Você pode ter quantas Transformações do Coletor seu fluxo de dados exigir.

Uma prática comum para considerar a alteração dos dados de entrada e o descompasso do esquema é coletar os dados de saída em uma pasta sem um esquema definido no conjunto de dados de saída. Também é possível considerar todas as alterações de coluna em suas fontes selecionando “Permitir Descompasso do Esquema” na fonte e mapear automaticamente todos os campos no coletor.

Você pode optar por substituir, acrescentar ou falhar o fluxo de dados ao coletar em um conjunto de dados.

Também é possível optar por "mapear automaticamente" para coletar todos os campos de entrada. Se você quiser escolher os campos que deseja coletar para o destino ou se quiser alterar os nomes dos campos no destino, escolha "Desativado" para "mapeamento automático" e clique na guia Mapeamento para mapear os campos de saída:

![Opções de coletor](media/data-flow/sink2.png "coletor 2")

## <a name="output-to-one-file"></a>Saída para um arquivo
Para os tipos de coletor de Data Lake ou do Azure Storage Blob, você entregará os dados em uma pasta. O Spark gerará arquivos de dados de saída particionados com base no esquema de particionamento sendo usado na transformação do coletor. Você pode definir o esquema de particionamento clicando na guia "Otimizar". Se você quiser que o ADF mescle sua saída em um único arquivo, clique no botão de opção "Partição Única".

![Opções de coletor](media/data-flow/opt001.png "opções de coletor")

### <a name="output-settings"></a>Configurações de saída

Substituir truncará a tabela se ela existir, então recrie-a e carregue os dados. Acrescentar inserirá novas linhas. Se a tabela do nome da tabela de conjunto de dados não existir no destino de ADW, o Fluxo de Dados criará a tabela e carregará os dados.

Se você desmarcar "Mapeamento Automático", poderá mapear os campos para a tabela de destino manualmente.

![Opções de ADW do coletor](media/data-flow/adw2.png "coletor do ADW")

#### <a name="field-mapping"></a>Mapeamento de campos

Na guia Mapeamento de sua Transformação do Coletor, você pode mapear as colunas de entrada (lado esquerdo) para o destino (lado direito). Quando você coleta fluxos de dados em arquivos, o ADF sempre grava novos arquivos em um pasta. Quando você faz o mapeamento para um conjunto de dados do banco de dados, pode optar por gerar uma nova tabela com esse esquema (defina Salvar Política como "substituir") ou inserir novas linhas em uma tabela existente e mapear os campos para o esquema existente.

Você pode usar a seleção múltipla na tabela de mapeamento para vincular várias colunas com um clique, desvincular várias colunas ou mapear várias linhas para o mesmo nome de coluna.

![Mapeamento de campo](media/data-flow/multi1.png "várias opções")

Se você quiser redefinir os mapeamentos de colunas, pressione o botão "Remapear" para redefinir os mapeamentos.

![Conexões](media/data-flow/maxcon.png "Conexões")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>Atualizações para a Transformação do Coletor da versão de GA do ADF V2

![Opções de coletor](media/data-flow/sink1.png "Coletor Um")

![Opções de coletor](media/data-flow/sink2.png "Coletores")

* Agora as opções Permitir Descompasso de Esquema e Validar Esquema estão disponíveis no Coletor. Isso permitirá que você instrua o ADF para aceitar totalmente as definições de esquema flexível (Descompasso de Esquema) ou falhar o Coletor se o esquema mudar (Validar Esquema).

* Limpe a pasta. O ADF truncará o conteúdo da pasta do coletor antes de gravar os arquivos de destino nessa pasta de destino.

* Opções de nome de arquivo

   * Padrão: Permitir que o Spark nomeie arquivos com base nos padrões de PART
   * Padrão: Insira um nome para seus arquivos de saída
   * Por partição: Insira um nome de arquivo por partição
   * Como os dados na coluna: Configure o arquivo de saída para o valor de uma coluna

> [!NOTE]
> As operações de arquivo serão executadas somente quando você estiver executando a atividade de Execução de Fluxo de Dados, não no modo de Depuração de Fluxo de Dados

Com os tipos de coletor do SQL, você pode definir:

* Truncar tabela
* Recriar tabela (realiza soltar/criar)
* Tamanho do lote de carregamentos de dados grandes. Insira um número de gravações de bucket em partes.

![Mapeamento de campo](media/data-flow/sql001.png "Opções do SQL")

## <a name="next-steps"></a>Próximas etapas

Agora que você criou seu Fluxo de Dados, adicione uma [Atividade de Execução de Fluxo de Dados para seu pipeline](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview).
