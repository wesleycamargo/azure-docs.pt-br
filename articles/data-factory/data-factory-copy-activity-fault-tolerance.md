---
title: "Tolerância a falhas da Atividade de Cópia do Azure Data Factory – ignorar linhas incompatíveis | Microsoft Docs"
description: "Saiba mais sobre a tolerância a falhas, ignorando as linhas compatíveis durante a cópia com o Azure Data Factory"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: d613537657af3bbe379a53e92532bf6b184d762b
ms.contentlocale: pt-br
ms.lasthandoff: 07/20/2017

---
# <a name="copy-activity-fault-tolerance---skip-incompatible-rows"></a>Tolerância a falhas da Atividade de Cópia – ignorar linhas incompatíveis

Com a [Atividade de Cópia](data-factory-data-movement-activities.md), você tem diferentes opções para lidar com linhas incompatíveis ao copiar dados entre os armazenamentos de dados de origem e do coletor. Escolha entre anular e falhar a atividade de cópia ao encontrar dados incompatíveis (comportamento padrão) ou continuar a cópia de todos os dados, ignorando as linhas incompatíveis. Além disso, você também tem a opção de registrar as linhas incompatíveis no Blob do Azure, de modo que você possa examinar a causa da falha, corrigir os dados na fonte de dados e repetir a operação.

## <a name="supported-scenarios"></a>Cenários com suporte
Atualmente, a atividade de cópia dá suporte à detecção, à omissão e ao log da seguinte situação incompatível durante a cópia:

- **Incompatibilidade de tipo de dados entre tipos nativos da origem e do coletor**

    Exemplo: copiar de um arquivo CSV no Blob do Azure para o Banco de Dados SQL do Azure e o esquema definido no Banco de Dados SQL do Azure tem três colunas do tipo *INT*. Portanto, as linhas com os dados numéricos (por exemplo, `123,456,789`) no arquivo CSV de origem são copiadas com êxito, enquanto as linhas que contêm um valor não numérico (por exemplo, `123,456,abc`) são ignoradas como linhas incompatíveis.

- **Número de incompatibilidade de colunas entre a origem e o coletor**

    Exemplo: copiar de um arquivo CSV no Blob do Azure para o Banco de Dados SQL do Azure e o esquema definido no SQL do Azure tem seis colunas. Portanto, as linhas que contêm seis colunas no arquivo CSV de origem são copiadas com êxito, enquanto as linhas com outras quantidades de colunas são ignoradas como linhas incompatíveis.

- **Violação de chave primária ao gravar no banco de dados relacional**

    Exemplo: para copiar do SQL Server para o Banco de Dados SQL do Azure, há uma Chave Primária definida no Banco de Dados SQL do Azure do coletor, mas não há uma Chave Primária definida no SQL Server de origem. As linhas duplicadas que podem existir na fonte não têm permissão durante a gravação no coletor. A atividade de cópia copia apenas a primeira linha para o coletor e ignora a segunda ou mais linhas com um valor duplicado de chave primária da origem para o coletor.

## <a name="configuration"></a>Configuração
O seguinte exemplo fornece a definição de JSON sobre como configurar a omissão das linhas incompatíveis na Atividade de Cópia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| enableSkipIncompatibleRow | Habilite ou não a omissão das linhas incompatíveis durante a cópia. | True <br/>False (padrão) | Não |
| redirectIncompatibleRowSettings | Um grupo de propriedades que poderá ser especificado quando você desejar registrar as linhas incompatíveis. | &nbsp; | Não |
| linkedServiceName | O serviço vinculado do Armazenamento do Azure para armazenar o log que contém todas as linhas ignoradas. | Especifique o nome de um serviço vinculado do [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou do [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service), que se refere à instância do Armazenamento usada para armazenar o arquivo de log. | Não |
| path | O caminho do arquivo de log que contém todas as linhas ignoradas. | Especifique o caminho de armazenamento de Blobs no qual você deseja registrar os dados incompatíveis. Se você não fornecer um caminho, o serviço criará um contêiner para você. | Não |

## <a name="monitoring"></a>Monitoramento
Após a conclusão da execução da atividade de cópia, você poderá ver o número de linhas ignoradas na seção de monitoramento da seguinte maneira:

![Ignorar monitoramento de linhas incompatíveis](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Se você configurar para registrar as linhas incompatíveis, para descobrir o que foi ignorado e qual é a causa raiz da incompatibilidade, encontre o arquivo de log neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`.

Os dados originais e o erro correspondente são registrados no arquivo. Um exemplo do conteúdo do arquivo de log é o seguinte:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).,
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Atividade de Cópia do Azure Data Factory, consulte [Mover dados usando a Atividade de Cópia](data-factory-data-movement-activities.md).
