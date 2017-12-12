---
title: Copiar dados de maneira incremental usando o Azure Data Factory | Microsoft Docs
description: 'Estes tutoriais mostram como copiar incrementalmente os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. O primeiro deles copia dados de uma tabela. '
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/05/2017
ms.author: shlo
ms.openlocfilehash: 2ae6cb42685dfb227bd75f83e73dfdf646ab909f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Carregar dados incrementalmente de um armazenamento de dados de origem para um armazenamento de dados de destino

Em uma solução de integração de dados, o carregamento incremental (ou delta) de dados depois de uma carga inicial completa de dados é um cenário amplamente usado. Os tutoriais nesta seção mostram maneiras diferentes de carregamento incremental de dados com o uso do Azure Data Factory versão 2.

## <a name="delta-data-loading-using-a-watermark"></a>Carregamento de dados delta usando uma marca d'água
Nesse caso, você define uma marca d'água em seu banco de dados de origem. Uma marca d'água é uma coluna que tem o último carimbo de data/hora atualizado ou uma chave de incremento. A solução de carregamento delta carrega os dados alterados entre uma marca d'água antiga e uma nova marca d'água. O fluxo de trabalho para essa abordagem é ilustrado no diagrama a seguir: 

![Fluxo de trabalho para o uso de uma marca d'água](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Para obter instruções passo a passo, veja os seguintes tutoriais: 

- [Copiar incrementalmente os dados de uma tabela no Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
- [Copiar dados incrementalmente de várias tabelas em um SQL Server local para o Banco de Dados SQL do Azure](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-using-the-change-tracking-technology"></a>Carregamento de dados delta usando a tecnologia Controle de Alterações
A tecnologia Controle de alterações é uma solução leve no SQL Server e no Banco de Dados SQL do Azure que fornece um mecanismo eficiente de controle de alterações para aplicativos. Ele permite que um aplicativo identifique facilmente os dados inseridos, atualizados ou excluídos. 

O fluxo de trabalho para essa abordagem é ilustrado no diagrama a seguir:

![Fluxo de trabalho para usar o Controle de Alterações](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
[Copiar incrementalmente os dados do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure usando a tecnologia Controle de Alterações](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>Próximas etapas
Vá para o tutorial a seguir: 

> [!div class="nextstepaction"]
>[Copiar incrementalmente os dados de uma tabela no Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
