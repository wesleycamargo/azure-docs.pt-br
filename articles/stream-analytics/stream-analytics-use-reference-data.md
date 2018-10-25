---
title: Usar dados de referência para pesquisas no Azure Stream Analytics
description: Este artigo descreve como usar dados de referência para pesquisar ou correlacionar dados no design de consulta de um trabalho do Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: fd912885335b41e3d7ca8ee717b6bb1b9c88e729
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984138"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Usar dados de referência para pesquisas no Stream Analytics
Dados de referência (também conhecidos como tabela de pesquisa) são um conjunto finito de dados estáticos ou com alteração lenta por natureza, usados para executar uma pesquisa ou para correlacionar com seu fluxo de dados. O Azure Stream Analytics carrega dados de referência na memória para obter um processamento de fluxo de baixa latência. Para usar dados de referência no seu trabalho do Stream Analytics do Azure geralmente é preciso usar uma [União de dados de referência](https://msdn.microsoft.com/library/azure/dn949258.aspx) em sua Consulta. O Stream Analytics usa o armazenamento de Blobs do Azure como a camada de armazenamento para dados de referência e com os dados de referência do Azure Data Factory podem ser transformados e/ou copiados para o armazenamento de Blobs do Azure, para uso como dados de referência de [qualquer número armazenamentos de dados de nuvem e locais](../data-factory/copy-activity-overview.md). Dados de referência são modelados como uma sequência de blobs (definidos na configuração de entrada) em ordem crescente segundo a data/hora especificada no nome do blob. Ele **somente** dá suporte à adição ao final da sequência usando um valor de data/hora **maior** que aquele especificado pelo último blob na sequência.

O Stream Analytics suporta dados de referência com  **tamanho máximo de 300 MB**. O limite de 300 MB de tamanho máximo de dados de referência é alcançável apenas com consultas simples. À medida que a complexidade da consulta aumenta para incluir o processamento com informações de estado, como agregações em janelas, junções temporais e funções analíticas temporais, espera-se que o tamanho máximo suportado dos dados de referência diminua. Se o Azure Stream Analytics não pode carregar os dados de referência e realize operações complexas, o trabalho executará fora da memória e falhará. Nesses casos, a % de SU Métrica de utilização alcançará 100%    

|**Número de unidades de streaming**  |**Tamanho máximo aprox. suportado (em MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 e além   |300   |

O aumento do número de Unidades de Streaming contínuo de um trabalho além de 6 não aumenta o tamanho máximo suportado dos dados de referência.

O suporte para a compactação não está disponível para os dados de referência. 

## <a name="configuring-reference-data"></a>Configurando os dados de referência
Para configurar os dados de referência, você primeiro precisa criar uma entrada que seja do tipo **Dados de Referência**. A tabela a seguir explica cada propriedade que você precisará fornecer ao criar os entrada de dados de referência com sua descrição:

|**Nome da Propriedade**  |**Descrição**  |
|---------|---------|
|Alias de entrada   | Um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada.   |
|Conta de armazenamento   | O nome da conta de armazenamento onde estão localizados os blobs. Se estiver na mesma assinatura que o trabalho do Stream Analytics, você pode selecioná-lo na lista suspensa.   |
|Chave da conta de armazenamento   | A chave secreta associada à conta de armazenamento. É preenchida automaticamente se a conta de armazenamento estiver na mesma assinatura que o trabalho do Stream Analytics.   |
|Contêiner de armazenamento   | Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob.   |
|Padrão de caminho   | O caminho usado para localizar seus blobs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das duas variáveis a seguir:<BR>{data}, {hora}<BR>Exemplo 1: products/{data}/{hora}/product-list.csv<BR>Exemplo 2: products/{data}/product-list.csv<BR><br> Se o blob não existir no caminho especificado, o trabalho do Stream Analytics aguardará indefinidamente que o blob fique disponível.   |
|Formato de data [opcional]   | Se você tiver usado {data} no padrão de caminho que você especificou, você pode selecionar o formato de data no qual os blobs são organizados na lista suspensa de formatos com suporte.<BR>Exemplo: AAAA/MM/DD, MM/DD/AAAA etc.   |
|Formato de hora [opcional]   | Se você tiver usado {hora} no padrão de caminho que você especificou, você pode selecionar o formato de hora no qual os blobs são organizados na lista suspensa de formatos com suporte.<BR>Exemplo: HH, HH/mm ou HH-mm  |
|Formato de serialização do evento   | Para verificar se as consultas funcionam da maneira esperada, o Stream Analytics precisa saber qual formato de serialização você está usando para os fluxos de dados de entrada. Para dados de referência, os formatos com suporte são CSV e JSON.  |
|Codificação   | UTF-8 é o único formato de codificação com suporte no momento.  |

## <a name="static-reference-data"></a>Dados de referência estática
Se os dados de referência não forem alterados, em seguida, então o suporte para dados são habilitados especificando um caminho estático na configuração de entrada de referência estática. O Azure Stream Analytics pega o blob do caminho especificado. substituição de {data} e {hora} não é necessária. Dados de referência são imutáveis no Stream Analytics. Portanto, não é recomendável substituir um blob de dados de referência estática.

## <a name="generating-reference-data-on-a-schedule"></a>Gerar dados de referência em uma agenda
Se os seus dados de referência são um conjunto de dados de alteração lenta, o suporte para atualização de dados de referência é habilitado especificando, na configuração de entrada, um padrão de caminho usando os tokens de substituição {date} e {time}. O Stream Analytics separa as definições de dados de referência baseadas nesse padrão de caminho. Por exemplo, um padrão de `sample/{date}/{time}/products.csv` com um formato de data de **"AAAA-MM-DD"** e um formato de hora de **"HH-mm"** instrui o Stream Analytics a captar o atualizou o blob `sample/2015-04-16/17-30/products.csv` às 17h30 do dia 16 de abril de 2015, fuso horário UTC.

O Azure Stream Analytics verifica automaticamente os blobs de dados de referência atualizados em um intervalo de um minuto.

> [!NOTE]
> Atualmente os trabalhos do Stream Analytics procuram pela atualização de blob somente quando a hora do computador avança até a hora codificada no nome do blob. Por exemplo o trabalho irá procurar `sample/2015-04-16/17-30/products.csv` assim que possível, mas não antes das 17:30 UTC do dia 16 de abril de 2015. Ele *nunca* procurará um blob com uma hora codificada anterior ao último que foi descoberto.
> 
> Por exemplo Depois que o trabalho localiza o blob `sample/2015-04-16/17-30/products.csv`, ele ignora todos os arquivos com uma data codificada anterior às 17:30 de 16 de abril de 2015. Portanto, se um blob `sample/2015-04-16/17-25/products.csv` atrasado for criado no mesmo contêiner, o trabalho não o usará.
> 
> Da mesma forma, se `sample/2015-04-16/17-30/products.csv` só é produzido às 22:03 do dia 16 de abril de 2015, mas nenhum blob com uma data anterior está presente no contêiner, o trabalho usa esse arquivo começando às 22:03 do dia 16 de abril de 2015 e usando os dados de referência anteriores.
> 
> Uma exceção a isso é iniciada quando o trabalho precisa reprocessar dados de volta no tempo ou quando o trabalho é o primeiro a iniciar. Na hora de início, o trabalho está procurando o blob mais recente produzido antes da hora especificada de início do trabalho. Isso é feito para garantir que haja um conjunto de dados de referência **não vazio** quando o trabalho é iniciado. Se um não for encontrado, o trabalho exibirá o seguinte diagnóstico: `Initializing input without a valid reference data blob for UTC time <start time>`.
> 
> 

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) pode ser utilizada para orquestrar a tarefa de criar os blobs atualizados exigidos pelo Stream Analytics para atualizar as definições de dados de referência. O Data Factory é um serviço de integração de dados baseado em nuvem que automatiza a movimentação e a transformação dos dados. O Data Factory dá suporte [à conexão de um grande número de armazenamentos de dados local baseados em nuvem](../data-factory/copy-activity-overview.md) e mover dados facilmente em um agendamento regular que você especificar. Para obter mais informações e orientações passo a passo sobre como configurar um pipeline de Data Factory para gerar dados de referência para o Stream Analytics que é atualizado em um cronograma predefinido, confira este [exemplo de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Dicas sobre como atualizar seus dados de referência
1. Não substitua os blobs de dados de referência uma vez que são imutáveis.
2. A maneira recomendada para atualizar os dados de referência é:
    * Use {date} / {hora} no padrão de caminho
    * Adicione um novo blob usando o mesmo padrão de contêiner e o caminho definido na entrada do trabalho
    * Use um valor de data/hora **maior** daquele especificado pelo último blob na sequência.
3. Blobs de dados de referência são **não** ordenados pela hora "Última modificação" do blob, mas apenas pela hora e pela data especificadas no nome do blob usando as substituições {date} e {time}.
3. Para evitar a necessidade de listar um grande número de blobs, considere excluir blobs muito antigos para os quais o processamento não será mais feito. Observe que o ASA talvez tenha que reprocessar uma pequena quantidade em alguns cenários, como um reinício.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Início Rápido: criar um trabalho do Stream Analytics usando o Portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
