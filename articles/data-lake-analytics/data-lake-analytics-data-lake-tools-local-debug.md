---
title: Depurar o código do Azure Data Lake Analytics localmente | Microsoft Docs
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio para depurar trabalhos do U-SQL em sua estação de trabalho local.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890626"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Depurar o código do Azure Data Lake Analytics localmente

Você pode usar Ferramentas do Azure Data Lake para Visual Studio para executar e depurar código do Azure Data Lake Analytics em sua estação de trabalho, assim como no serviço do Azure Data Lake.

Saiba [como executar um script U-SQL no computador local](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Depurar scripts e assemblies do C# localmente

É possível depurar assemblies do C# sem os enviar nem os registrar para o Serviço Azure Data Lake Analytics. Você pode definir pontos de interrupção no arquivo code-behind e em um projeto do C# referenciado.

### <a name="to-debug-local-code-in-code-behind-file"></a>Para depurar o código local no arquivo code-behind

1. Definir pontos de interrupção no arquivo code-behind.
2. Pressione F5 para depurar o script localmente.

> [!NOTE]
   > O procedimento a seguir só funciona no Visual Studio 2015. No Visual Studio mais antigo, talvez seja necessário adicionar manualmente os arquivos pdb.  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Para depurar o código local em um projeto do C# referenciado

1. Crie um projeto de Assembly do C# e compile-o para gerar o dll de saída.
2. Registre o dll usando uma instrução U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Definir pontos de interrupção no código C#.
4. Pressione F5 para depurar o script referenciando a dll do C# localmente.


## <a name="next-steps"></a>Próximas etapas

- Para ver uma consulta mais complexa, consulte [Analisar logs de site usando o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Para ver detalhes do trabalho, confira [Usar o Navegador de Trabalhos e o Modo de Exibição de Trabalho para trabalhos do Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Para usar o modo de exibição de execução de vértice, veja [Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
