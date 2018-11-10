---
title: Prática recomendada de gerenciamento de assemblies do U-SQL no pipeline de CI/CD para o Azure Data Lake
description: Aprenda a prática recomendada de gerenciamento de assemblies de C# do U-SQL no pipeline de CI/CD com o Azure DevOps.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0f6f581d90b025dd538eb9b79da1a0addd4259f7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670283"
---
# <a name="best-practice-of-managing-u-sql-assemblies-in-cicd-pipeline"></a>Prática recomendada de gerenciamento de assemblies do U-SQL no pipeline de CI/CD

Neste artigo, você aprenderá como gerenciar o código-fonte do assembly U-SQL com o novo projeto de banco de dados U-SQL introduzido. Você também pode aprender a configurar um pipeline de integração e implantação contínua (CI/CD) para registro de assembly usando o Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Usar o projeto de banco de dados U-SQL para gerenciar o código-fonte do assembly

[O projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) é um tipo de projeto do Visual Studio que ajuda os desenvolvedores a desenvolver, gerenciar e implantar seus bancos de dados U-SQL de forma simples e rápida. Todos os objetos de banco de dados U-SQL (exceto pelas credenciais) podem ser gerenciados com o projeto de banco de dados U-SQL. 

A maneira recomendada de gerenciar o código-fonte de assembly C# scripts U-SQL de registro de assembly é:

* Usar o **projeto de banco de dados U-SQL** para gerenciar scripts U-SQL de registro de assembly.
* Usar a **biblioteca de classes (para aplicativo U-SQL)** para gerenciar o código-fonte C# e as dependências para operadores, funções e agregadores (UDOs/UDFs/UDAGs) definidos pelo usuário.
* Usar o projeto de banco de dados U-SQL para fazer referência ao projeto de biblioteca de classes. 

Um projeto de banco de dados U-SQL pode fazer referência a um projeto de biblioteca de classes (para aplicativo U-SQL). Assemblies registrados no banco de dados U-SQL podem ser criados usando o código-fonte C# referenciado deste projeto de biblioteca de classes (para aplicativo U-SQL).

Você pode seguir as etapas de criação de projetos e adição de referências a seguir:
1. Criar um projeto de biblioteca de classes (para aplicativo U-SQL) em **Arquivo > Novo > Projeto**. O projeto está sob o nó **Azure Data Lake > U-SQL**.
   ![Ferramentas do Data Lake para Visual Studio – criar um projeto de biblioteca de classes C#](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
2. Adicione seu código C# definido pelo usuário no projeto de biblioteca de classes (para aplicativo U-SQL). 
3. Crie um projeto U-SQL em **Arquivo > Novo > Projeto**. O projeto está sob o nó **Azure Data Lake > U-SQL**.
   ![Ferramentas do Data Lake para Visual Studio – criar um projeto de banco de dados U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
4. Adicione uma referência ao projeto de biblioteca de classes C# para o projeto de banco de dados U-SQL.

    ![As Ferramentas do Data Lake para Visual Studio -- adicionar uma referência do projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![As Ferramentas do Data Lake para Visual Studio -- adicionar uma referência do projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)
5. Crie um script de assembly no projeto de banco de dados do U-SQL **clicando com o botão direito do mouse no projeto e selecionando Adicionar Novo Item**.
   ![Ferramentas do Data Lake para Visual Studio – adicionar script de assembly](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)
6. Abra o script de assembly e, no modo de exibição de Design do assembly, escolha o assembly referenciado no menu suspenso **Criar assembly de referência**.

    ![Ferramentas do Data Lake para Visual Studio criam assembly a partir da referência](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Adicione **Dependências Gerenciadas** e **Arquivos Adicionais**, se houver. Ao adicionar arquivos adicionais, a ferramenta usará o caminho relativo para garantir que encontrará os assemblies no computador local e no computador de build. 

**@_DeployTempDirectory** na janela do editor, na parte inferior, é uma variável predefinida que aponta a ferramenta para a pasta de saída de build. Na pasta de saída de compilação, cada assembly possui uma subpasta nomeada com o nome do assembly. Todas as DLLs e arquivos adicionais estão na subpasta. 

## <a name="build-a-u-sql-database-project"></a>Compilar um projeto de banco de dados U-SQL

A saída de build do projeto de banco de dados U-SQL é um pacote de implantação do banco de dados U-SQL nomeado com o sufixo `.usqldbpack`. O pacote `.usqldbpack` é um arquivo zip que inclui todas as instruções DDL em um único script U-SQL na pasta **DDL** e todos os arquivos adicionais e DLLs para assemblies na pasta **Temp**.

## <a name="deploy-a-u-sql-database"></a>Implantar um banco de dados U-SQL

O pacote `.usqldbpack` pode ser implantado na conta local ou na conta do Azure Data Lake Analytics usando o Visual Studio ou o SDK de implantação. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Implantar um banco de dados U-SQL no Visual Studio

Você pode implantar um banco de dados U-SQL por meio de um projeto de banco de dados U-SQL ou um pacote `.usqldbpack` no Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Implantar por meio do projeto de banco de dados U-SQL

1.  Clique com o botão direito do mouse no projeto de banco de dados U-SQL e selecione **Implantar**.
2.  No **assistente Implantar Banco de Dados U-SQL**, escolha a **conta do ADLA** na qual você gostaria de implantar o banco de dados. Há suporte para as contas locais e ADLA.
3.  A **Fonte do Banco de Dados** é preenchida automaticamente apontando para o pacote .usqldbpack na pasta de saída de compilação do projeto.
4.  Insira um nome em **Nome do Banco de Dados** para criar um banco de dados. Se houver um banco de dados com o mesmo nome na conta do Azure Data Lake Analytics de destino, todos os objetos que são definidos no projeto de banco de dados serão criados sem recriar o banco de dados.
5.  Para implantar o banco de dados U-SQL, selecione **Enviar**. Todos os recursos (assemblies e arquivos adicionais) serão carregados e um trabalho do U-SQL que inclui todas as instruções DDL será enviado.

    ![Ferramentas do Data Lake para Visual Studio -- Implantam um projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Ferramentas do Data Lake para Visual Studio -- Implantar um assistente do projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-u-sql-database-in-azure-devops"></a>Implantar banco de dados U-SQL no Azure DevOps

O `PackageDeploymentTool.exe` oferece as interfaces de programação e de linha de comando que ajudam a implantar os bancos de dados U-SQL. O SDK está incluído no [pacote do Nuget do SDK do U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) localizado em `build/runtime/PackageDeploymentTool.exe`.

No Azure DevOps, você pode usar a tarefa de linha de comando e esse SDK para configurar o pipeline de automação para a atualização do banco de dados U-SQL. [Saiba mais sobre o SDK e como configurar o pipeline de CI/CD para implantação do banco de dados U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Próximas etapas

* [Como configurar o pipeline de CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Como testar seu código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Executar um script U-SQL no computador local](data-lake-analytics-data-lake-tools-local-run.md)
