---
title: Executar script U-SQL do Azure Data Lake em seu computador local | Microsoft Docs
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio para executar trabalhos de U-SQL em seu computador local.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888959"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>Executar script U-SQL no computador local

Ao desenvolver um script U-SQL, é comum executar o script U-SQL localmente, pois isso economiza tempo e dinheiro. As Ferramentas do Azure Data Lake para Visual Studio dão suporte à execução de scripts de U-SQL em seu computador local. 

## <a name="basic-concepts-for-local-run"></a>Conceitos básicos da execução local

O gráfico abaixo mostra os componentes para a execução local, e como esses componentes são mapeados para execução na nuvem.

|Componente|Execução local|Execução de nuvem|
|---------|---------|---------|
|Armazenamento|Pasta Raiz de Dados local|Conta padrão do Azure Data Lake Store|
|Computação|Mecanismo de execução local de U-SQL|Serviço de Azure Data Lake Analytics|
|Ambiente de execução|Diretório de trabalho no computador local|Cluster do Azure Data Lake Analytics|

Mais explicação para componentes de Execução Local:

### <a name="local-data-root-folder"></a>Pasta Raiz de Dados local

Pasta Raiz de Dados local é um "repositório local" para a conta de computador local. Qualquer pasta no sistema de arquivos locais em seu computador local pode ser uma pasta Raiz de Dados local. Ela é igual à conta padrão do Azure Data Lake Store de uma conta do Data Lake Analytics. Alternar para uma pasta raiz de dados diferente é como alternar para uma conta de repositório padrão diferente. 

A pasta Raiz de Dados é usada para:
- Armazenar metadados, como bancos de dados, tabelas, funções com valor de tabela e assemblies.
- Pesquisar os caminhos de entrada e saída que são definidos como caminhos relativos no script U-SQL. O uso de caminhos relativos facilita a implantação de seus scripts U-SQL no Azure.

### <a name="u-sql-local-run-engine"></a>Mecanismo de execução local de U-SQL

O mecanismo de execução local U-SQL é uma "conta de computação local" para trabalhos de U-SQL. Os usuários podem executar trabalhos de U-SQL localmente usando as Ferramentas do Azure Data Lake para Visual Studio. Também há suporte para execução local por meio das interfaces de programação e linha de comando do SDK de U-SQL do Azure Data Lake. [Saiba mais sobre o SDK do U-SQL do Azure Data Lake](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directory"></a>Diretório de trabalho

Ao executar um script U-SQL, é necessário ter um diretório de trabalho para armazenar em cache os resultados da compilação, os logs de execução e assim por diante. Nas Ferramentas do Azure Data Lake para Visual Studio, o diretório de trabalho é o diretório de trabalho do projeto de U-SQL (normalmente localizado em `<U-SQL project root path>/bin/debug>`). O diretório de trabalho será limpo sempre que uma nova execução for disparada.

## <a name="local-run-in-visual-studio"></a>Execução local no Visual Studio

As Ferramentas do Azure Data Lake para Visual Studio têm um mecanismo de execução local interno e o revela como uma conta de computação local. Para executar um script U-SQL localmente, selecione a conta (Computador Local) ou (Projeto local) na lista suspensa da margem do editor de script e clique em **Enviar**.

![As Ferramentas do Data Lake para Visual Studio enviam o script para a conta local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>Execução local com a conta (Computador Local)

Conta (Computador local) é uma conta de computação com local compartilhado com uma única pasta raiz de dados local como a conta de armazenamento local. A pasta raiz de dados está localizada por padrão em "C:\Usuários\<nome do usuário > \AppData\Local\USQLDataRoot", e também é configurável por meio de **Ferramentas > Data Lake > Opções e Configurações**.

![Ferramentas de Data Lake para Visual Studio configuram raiz de dados local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Um projeto de U-SQL é necessário para execução local. O diretório de trabalho do projeto de U-SQL é usado para o diretório de trabalho de execução local de U-SQL. Resultados da compilação, logs de execução e outros arquivos relacionados à execução do trabalho são gerados e armazenados na pasta do diretório de trabalho durante a execução local. Observe que, sempre que você executar novamente o script, todos esses arquivos no diretório de trabalho serão limpos e regenerados.

## <a name="local-run-with-local-project-account"></a>Execução local com a conta (Projeto local)

Conta (Projeto local) é uma conta de computação local isolada do projeto para cada projeto com uma pasta Raiz de Dados local isolada. Cada projeto de U-SQL ativo aberto no Gerenciador de Soluções tem uma conta `(Local-project: <project name>)` correspondente listada no Gerenciador de Servidores e margem de editor de script U-SQL. 

A conta (Projeto Local) fornece um ambiente de desenvolvimento limpo e isolado para desenvolvedores. Ao contrário de conta (Computador local) que tem uma pasta Raiz de Dados local compartilhada armazenando metadados e dados de entrada/saída para todos os trabalhos locais, a conta (Projeto local) cria uma pasta Raiz de Dados local temporária no diretório de trabalho do projeto de U-SQL sempre que um script U-SQL for executado. Essa pasta Raiz de Dados temporária será limpa quando ocorrer a recompilação ou nova execução. 

O projeto de U-SQL fornece uma boa experiência para gerenciar esse ambiente de execução local isolado por meio da propriedade e referência de projeto. Você configurar tanto as fontes de dados de entrada para scripts U-SQL no projeto, como os ambientes de banco de dados referenciados.

### <a name="manage-input-data-source-for-local-project-account"></a>Gerenciar fonte de dados de entrada para conta (Projeto local)

O projeto U-SQL se encarrega da criação da pasta Raiz de Dados local e da configuração de dados da conta (Projeto local). Uma pasta temporária da Raiz de Dados é limpa e recriada no diretório de trabalho do projeto de U-SQL sempre que a execução local e a recompilação ocorrerem. Todas as fontes de dados configuradas pelo projeto U-SQL são copiadas nessa pasta de Raiz de Dados local temporária antes da execução de trabalho local. 

Você pode configurar a pasta raiz de suas fontes de dados **clicando com botão direito no projeto U-SQL > Propriedades > Testar Fonte de Dados**. Ao executar o script U-SQL na conta (Projeto local), todos os arquivos e subpastas (incluindo arquivos em subpastas) na pasta **Fonte de Dados de Teste** são copiados na pasta Raiz de Dados local temporária. Após a conclusão da execução de trabalho local, os resultados de saída também podem ser encontrados na pasta Raiz de Dados local temporária no diretório de trabalho do projeto. Observe que todas essas saídas serão excluídas e limpas quando o projeto for recompilado e limpo. 

![Ferramentas do Data Lake para Visual Studio configuram a fonte de dados de teste do projeto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>Gerenciar o ambiente de banco de dados referenciado para a conta (Projeto local) 

Se uma consulta U-SQL usa ou consulta com objetos de banco de dados U-SQL, você deve tornar os ambientes de banco de dados prontos localmente antes de executar esse script U-SQL localmente. Para a conta (Projeto local), as dependências de banco de dados U-SQL podem ser gerenciadas por referências de projeto de U-SQL. Você pode adicionar referências de projeto de banco de dados U-SQL ao seu projeto de U-SQL. Antes de executar scripts U-SQL na conta (Projeto local), todos os bancos de dados referenciados são implantados na pasta Raiz de Dados local temporária. E para cada execução, a pasta Raiz de Dados temporária será limpa como um ambiente novamente isolado.

Artigos relacionados:
* [Saiba como gerenciar a definição de banco de dados U-SQL por meio do projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [Saiba como gerenciar a referência de banco de dados U-SQL no projeto de U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>Diferença entre a conta (Computador Local) e a conta (Projeto local)

A conta (Computador local) tem como objetivo simular uma conta do Azure Data Lake Analytics no computador local dos usuários. Ela compartilha a mesma experiência com a conta do Azure Data Lake Analytics. (Projeto local) tem como objetivo fornecer um ambiente de desenvolvimento local fácil de usar que ajuda os usuários a implantar referências de bancos de dados e dados de entrada antes de executar o script localmente. A conta (Computador local) fornece um ambiente compartilhado permanente, que pode ser acessado por meio de todos os projetos. A conta (Projeto local) fornece um ambiente de desenvolvimento isolado para cada projeto, e é atualizada em cada execução. Com base no indicado acima, a conta (Projeto local) oferece uma experiência de desenvolvimento mais rápida ao aplicar as novas alterações rapidamente.

Confira mais diferenças entre a conta de (Computador Local) e (Projeto Local) no gráfico a seguir:

|Ângulo de diferença|(Computador local)|(Projeto local)|
|----------------|---------------|---------------|
|Acesso local|Pode ser acessado por todos os projetos|Somente o projeto correspondente pode acessar essa conta|
|Pasta Raiz de Dados local|Uma pasta local permanente. Configurado por meio de **Ferramentas > Data Lake > Opções e Configurações**|Uma pasta temporária criada para cada execução local no diretório de trabalho do projeto de U-SQL. A pasta é limpa quando ocorrer a recompilação ou nova execução|
|Dados de entrada para o script U-SQL|Caminho relativo sob a pasta Raiz de Dados local permanente|Definido por meio de **Propriedade do projeto U-SQL > Testar Fonte de Dados**. Todos os arquivos e subpastas são copiados para a pasta Raiz de Dados temporária antes da execução local|
|Dados de saída para o script U-SQL|Caminho relativo sob a pasta Raiz de Dados local permanente|Saída para a pasta Raiz de Dados temporária. Os resultados são limpos ao recompilar ou executar novamente.|
|Implantação de banco de dados de referência|Os bancos de dados de referência não são implantados automaticamente durante a execução na conta (Computador local). O mesmo vale para enviar para a conta do Azure Data Lake Analytics.|Os bancos de dados de referência são implantados automaticamente na conta (Projeto local) antes da execução local. Todos os ambientes de banco de dados são limpos e reimplantados quando ocorre a recompilação ou nova execução.|

## <a name="local-run-with-u-sql-sdk"></a>Execução local com o SDK de U-SQL

Além de executar scripts U-SQL localmente no Visual Studio, você também pode usar o SDK de U-SQL do Azure Data Lake para executá-los localmente com interfaces de programação e de linha de comando. Por meio dessas interfaces, você pode automatizar a execução e o teste local do U-SQL.

Saiba mais sobre o [SDK do U-SQL do Azure Data Lake](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Próximas etapas

- [SDK do U-SQL do Azure Data Lake](data-lake-analytics-u-sql-sdk.md)
- [Como configurar o pipeline de CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Usar o projeto de banco de dados U-SQL para desenvolver o banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
- [Como testar seu código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
