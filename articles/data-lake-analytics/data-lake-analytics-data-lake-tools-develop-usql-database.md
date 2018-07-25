---
title: Usar o projeto de banco de dados U-SQL para desenvolver o banco de dados U-SQL para o Azure Data Lake | Microsoft Docs
description: Saiba como desenvolver um banco de dados U-SQL usando as Ferramentas do Azure Data Lake para Visual Studio.
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
ms.openlocfilehash: a8099a8c9bcec8ed5a7bb480dae58d2c077b8fe0
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890628"
---
# <a name="use-u-sql-database-project-to-develop-u-sql-database-for-azure-data-lake"></a>Usar o projeto de banco de dados U-SQL para desenvolver o banco de dados U-SQL para o Azure Data Lake

O banco de dados U-SQL oferece visualizações estruturadas sobre os dados não estruturados, gerencia dados estruturados em tabelas e fornece um sistema geral de catálogo de metadados para organizar seus dados estruturados e código personalizado. O banco de dados é o conceito que agrupa esses objetos relacionados.

Saiba mais sobre o [banco de dados U-SQL e a Linguagem de Definição de Dados (DDL)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

O projeto de banco de dados U-SQL é um tipo de projeto do Visual Studio que ajuda os desenvolvedores a desenvolver, gerenciar e implantar seus bancos de dados U-SQL de forma simples e rápida.

## <a name="create-a-u-sql-database-project"></a>Criar um projeto de banco de dados U-SQL

As Ferramentas do Azure Data Lake para Visual Studio adicionaram um novo modelo de projeto chamado projeto de banco de dados U-SQL após a versão 2.3.3000.0. Para criar um projeto U-SQL, percorra **Arquivo > Novo > Projeto**, o projeto de banco de dados U-SQL pode ser encontrado em **Azure Data Lake > Nó U-SQL**.

![Ferramentas do Data Lake para Visual Studio criam um projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-using-database-project"></a>Desenvolver objetos de banco de dados U-SQL usando o projeto banco de dados

Clique com o botão direito do mouse no projeto de banco de dados U-SQL, clique em **Adicionar > Novo Item**, todos os tipos de objetos suportados podem ser encontrados no Assistente para Adicionar Novo Item. 

1.  No caso do objeto que não é assembly, por exemplo, a função com valor de tabela, um novo script U-SQL é criado após a adição de um novo item. Você pode começar a desenvolver a instrução DDL para esse objeto no editor.
2.  No objeto assembly, a ferramenta fornece um editor de interface do usuário amigável que ajuda você a registrar o assembly e implantar arquivos .dll e arquivos adicionais. Siga as etapas abaixo para adicionar uma definição de objeto assembly ao projeto de banco de dados U-SQL:

1.  Adicione referências do projeto C# que inclui o UDO/UDAG/UDF para o projeto de banco de dados U-SQL.

    ![As Ferramentas do Data Lake para Visual Studio adicionam uma referência do projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![As Ferramentas do Data Lake para Visual Studio adicionam uma referência do projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  No modo de exibição de design do assembly, escolha o assembly referenciado na lista suspensa **Criar assembly de referência**.

    ![Ferramentas do Data Lake para Visual Studio criam assembly a partir da referência](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Adicione **Dependências Gerenciadas** e **Arquivos Adicionais**, caso eles existam. Ao adicionar arquivos adicionais, a ferramenta usará o caminho relativo para garantir que ele posteriormente conseguirá encontrar os assemblies no computador local e no computador de build. @_DeployTempDirectory é uma variável predefinida que aponta a ferramenta para a pasta de saída de build. Na saída de compilação, cada assembly tem uma subpasta nomeada como o nome do assembly, todas as dlls e arquivos adicionais podem ser encontrados na subpasta. 
 
## <a name="build-u-sql-database-project"></a>Compilar o projeto de banco de dados U-SQL

A saída de build do projeto de banco de dados U-SQL é um pacote de implantação do banco de dados U-SQL nomeado com o sufixo `.usqldbpack`. O pacote `.usqldbpack` é um arquivo zip que inclui todas as instruções DDL em um único script U-SQL na pasta **DDL** e todas as dlls e arquivos adicionais para assemblies na pasta **Temp**.

Saiba mais sobre [como criar um projeto de banco de dados U-SQL com a linha de comando do MSBuild e tarefas de compilação do Visual Studio Team Service](data-lake-analytics-cicd-overview.md#build-u-sql-database-project).

## <a name="deploy-u-sql-database"></a>Implantar o banco de dados U-SQL

O pacote .usqldbpack pode ser implantado na conta local ou na conta do Azure Data Lake Analytics usando o Visual Studio ou o SDK de implantação. 

### <a name="deploy-u-sql-database-in-visual-studio"></a>Implantar banco de dados U-SQL no Visual Studio

Você pode implantar um banco de dados U-SQL por meio de um projeto de banco de dados U-SQL ou um pacote .usqldbpack no Visual Studio.

#### <a name="deploy-through-u-sql-database-project"></a>Implantar por meio do projeto de banco de dados U-SQL

1.  Clique com o botão direito do mouse no projeto de banco de dados U-SQL e escolha **Implantar**.
2.  No assistente de Implantar Banco de Dados U-SQL, escolha a **conta do ADLA** na qual você gostaria de implantar o banco de dados. Há suporte para a conta (Local) e a conta do ADLA.
3.  A **Fonte do Banco de Dados** é preenchida automaticamente apontando para o pacote .usqldbpack na pasta de saída de compilação do projeto
4.  Insira **Nome do banco de dados** para criar um banco de dados. Se houver um banco de dados com o mesmo na conta do Azure Data Lake Analytics de destino, todos os objetos definidos no projeto de banco de dados serão criados sem recriar o banco de dados.
5.  Clique em **Enviar** para implantar o banco de dados U-SQL. Todos os recursos (assemblies e arquivos adicionais) serão carregados e um trabalho do U-SQL que inclui todas as instruções DDL será enviado.

    ![As Ferramentas do Data Lake para Visual Studio implantam um projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![As Ferramentas do Data Lake para Visual Studio implantam um assistente do projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-u-sql-database-deployment-package"></a>Implantar por meio do pacote de implantação do banco de dados U-SQL

1.  Abra o **Gerenciador de Servidores**, expanda a **conta do Azure Data Lake Analytics** na qual você gostaria de implantar o banco de dados.
2.  Clique com o botão direito do mouse nos Bancos de Dados U-SQL e escolha **Implantar Banco de Dados**.
3.  Defina a **Fonte do Banco de Dados** para o caminho (arquivo .usqldbpack) do pacote de implantação do banco de dados U-SQL.
4.  Insira o **Nome do banco de dados** para criar um banco de dados. Se houver um banco de dados com o mesmo na conta do Azure Data Lake Analytics de destino, todos os objetos que são definidos no projeto de banco de dados serão criados sem recriar o banco de dados.

    ![As Ferramentas do Data Lake para Visual Studio implantam um pacote do banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![As Ferramentas do Data Lake para Visual Studio implantam um assistente de pacote do banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-using-sdk"></a>Implantar um banco de dados U-SQL usando SDK

O `PackageDeploymentTool.exe` oferece as interfaces de programação e de linha de comando que ajudam a implantar os bancos de dados U-SQL. O SDK está incluído no [pacote do Nuget do SDK do U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) localizando em `build/runtime/PackageDeploymentTool.exe`.

[Saiba mais sobre o SDK e como configurar o pipeline de CI/CD para implantação do banco de dados U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service).

## <a name="reference-a-u-sql-database-project"></a>Fazer referência a um projeto de banco de dados U-SQL

Um projeto U-SQL pode fazer referência a um projeto de banco de dados U-SQL. A referência afeta duas cargas de trabalho:

- Compilação do projeto: os ambientes do banco de dados referenciado são configurados antes de compilar os scripts U-SQL. 
- A execução local na conta (projeto Local): os ambientes de banco de dados referenciados são implantados na conta (projeto Local) antes da execução do script U-SQL. [Saiba mais sobre a execução local e a diferença entre a conta de (computador Local) e (projeto Local) aqui](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-u-sql-database-reference"></a>Como adicionar uma referência de banco de dados U-SQL

1. Clique com o botão direito do mouse no projeto U-SQL no **Gerenciador de Soluções** e escolha **Adicionar Referência de Banco de Dados U-SQL...**.

    ![Ferramentas do Data Lake para Visual Studio adicionam uma referência do projeto de banco de dados](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Configure a referência de banco de dados de um projeto de banco de dados U-SQL na solução atual ou um arquivo de pacote do banco de dados U-SQL.
3. Forneça o nome do banco de dados.

    ![Ferramentas do Data Lake para Visual Studio adicionam um assistente de referência do projeto de banco de dados](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Próximas etapas

- [Como configurar o pipeline de CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Como testar seu código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Executar um script U-SQL no computador local](data-lake-analytics-data-lake-tools-local-run.md)
