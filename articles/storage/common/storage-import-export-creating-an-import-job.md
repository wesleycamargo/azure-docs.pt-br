---
title: Criar um trabalho de importação para a Importação/Exportação do Azure | Microsoft Docs
description: Saiba como criar uma importação para o serviço de Importação/Exportação do Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fa76f4fb5d4da5fd00bb9fa4ed862c6977a47e90
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102172"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Criando um trabalho de importação para o serviço de Importação/Exportação do Azure

Criar um trabalho de importação para o serviço de importação/exportação do Microsoft Azure usando a API REST envolve as seguintes etapas:

- Preparar unidades com a Ferramenta de Importação/Exportação do Azure.

- Obter o local para o qual enviar a unidade.

- Criar o trabalho de importação.

- Enviar as unidades à Microsoft por meio de um serviço de transporte compatível.

- Atualizar o trabalho de importação com os detalhes do envio.

  Veja [Usar o serviço de importação/exportação do Microsoft Azure para transferir dados ao armazenamento de blobs](storage-import-export-service.md) para obter uma visão geral do serviço de importação/exportação e um tutorial que demonstra como usar o [Portal do Azure](https://portal.azure.com/) para criar e gerenciar tarefas de importação e exportação.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Preparar unidades com a ferramenta de importação/exportação do Azure

As etapas para preparar unidades para um trabalho de importação são as mesmas se você cria o trabalho por meio do portal ou por meio da API REST.

Abaixo apresentamos uma breve visão geral da preparação de unidades. Veja a [Referência de Import-ExportTool do Azure](storage-import-export-tool-how-to-v1.md) para saber mais. Você pode baixar a Ferramenta de Importação/Exportação do Azure [aqui](https://go.microsoft.com/fwlink/?LinkID=301900).

Para preparar a unidade:

- Identifique os dados a importar.

- Identifique os blobs de destino no armazenamento do Windows Azure.

- Use a Ferramenta de Importação/Exportação do Azure para copiar seus dados para um ou mais discos rígidos.

  A Ferramenta de Importação/Exportação do Azure também gerará um arquivo de manifesto para cada unidade assim que for preparada. Um arquivo de manifesto contém:

- Uma enumeração de todos os arquivos a carregar e os mapeamentos desses arquivos para blobs.

- Somas de verificação dos segmentos de cada arquivo.

- Informações sobre metadados e propriedades para associar a cada blob.

- Lista de ação a ser tomada se um blob que está sendo carregado tem o mesmo nome de um blob existente no contêiner. As opções possíveis são: a) substituir o blob com o arquivo, b) manter o blob existente e ignorar o carregamento do arquivo, c) acrescentar um sufixo ao nome para que não haja conflito com outros arquivos.

## <a name="obtaining-your-shipping-location"></a>Obtendo o local de envio

Antes de criar um trabalho de importação, você precisa obter um nome e o endereço de um local de envio chamando a operação [List Locations](https://docs.microsoft.com/rest/api/storageimportexport/locations/list). `List Locations` retorna uma lista de locais e seus endereços de correspondência. É possível selecionar uma localização na lista retornada e enviar os discos rígidos para esse endereço. Você também pode usar a operação `Get Location` para obter o endereço para entrega de uma localização específica diretamente.

 Siga as etapas abaixo para obter a localização de envio:

-   Identifique o nome da localização de sua conta de armazenamento. Esse valor pode ser encontrado no campo **Local** do **Painel** da conta de armazenamento no portal do Azure ou consultado usando a operação [Get Storage Account Properties](/rest/api/storagerp/storageaccounts) da API de gerenciamento de serviços.

-   Recupere o local que está disponível para processar esta conta de armazenamento chamando a operação `Get Location`.

-   Se a propriedade `AlternateLocations` do local contiver o próprio local, então é seguro usar este local. Caso contrário, chame a operação `Get Location` novamente com uma das localizações alternativas. O local original pode estar fechado temporariamente para manutenção.

## <a name="creating-the-import-job"></a>Criando o trabalho de importação
Para criar o trabalho de importação, chame a operação [Put Job](/rest/api/storageimportexport/jobs). Primeiro, forneça as seguintes informações:

-   Um nome para o trabalho.

-   O nome da conta de armazenamento.

-   O nome do local de envio, obtido na etapa anterior.

-   Um tipo de trabalho (importação).

-   O endereço de retorno para o qual as unidades devem ser enviadas depois que o trabalho de importação for concluído.

-   A lista de unidades no trabalho. Para cada unidade, você deve incluir as seguintes informações que foram obtidas durante a etapa de preparação de unidade:

    -   A Id da unidade

    -   A chave do BitLocker

    -   O caminho relativo do arquivo de manifesto no disco rígido

    -   O hash MD5 do arquivo de manifesto codificado para Base16

## <a name="shipping-your-drives"></a>Enviando suas unidades
Você deve enviar suas unidades para o endereço obtido na etapa anterior, e deve fornecer ao serviço de importação/exportação o número de rastreamento do pacote.

> [!NOTE]
>  Você deve enviar suas unidades por meio de um serviço de transporte compatível, que fornecerá um número de rastreamento para seu pacote.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Atualizando o trabalho de importação com as informações do envio
Depois que tiver o número de rastreamento, chame a operação [Update Job Properties](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Update) para atualizar o nome da operadora, o número de rastreamento do trabalho e o número da conta da operadora para retorno. Opcionalmente, você também pode especificar a quantidade de unidades e a data de envio.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)
