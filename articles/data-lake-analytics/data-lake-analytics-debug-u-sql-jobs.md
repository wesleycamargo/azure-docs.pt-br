---
title: Depurar trabalhos do U-SQL | Microsoft Docs
description: "Saiba como depurar vértice com falha do U-SQL usando o Visual Studio. "
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: yanacai
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: aac455520ab62d69b406a254a54b0f000ea2e5bc
ms.contentlocale: pt-br
ms.lasthandoff: 06/26/2017


---
<a id="debug-user-defined-c-code-for-failed-u-sql-jobs" class="xliff"></a>

# Depurar o código C# definido pelo usuário para trabalhos U-SQL com falha

O U-SQL oferece um modelo de extensibilidade por meio de C#, os usuários podem escrever código C# definido, como um extrator, redutor, etc. definido pelo usuário para obter mais extensibilidade (saiba mais sobre [Código U-SQL definido pelo usuário](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#user-defined-functions---udf)). No entanto, nenhum desenvolvedor pode criar código sem erros e a depuração em sistemas de big data é difícil, pois muitos sistemas fornecem apenas informações de depuração de execução limitadas, como logs, etc. 

As Ferramentas ADL para Visual Studio oferecem um recurso chamado **Falha na depuração de vértice**, que você pode usar para clonar facilmente o ambiente em falha (incluindo os dados de entrada intermediários e código de usuário, etc.) a partir da nuvem em sua máquina local para rastrear e depurar os trabalhos em falha com a mesma execução e dados de entrada exatos da nuvem.

O vídeo a seguir demonstra a **Falha na depuração de vértice** nas Ferramentas de ADL para Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> O Visual Studio pode travar ou falhar se você não tiver estas duas atualizações de janelas: [Atualização 2 Redistribuível do Microsoft Visual C++ 2015](https://www.microsoft.com/download/details.aspx?id=51682), [Tempo de Execução do Universal C para Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).
> 

<a id="download-failed-vertex-to-local" class="xliff"></a>

## Baixe a falha de vértice para o local

Ao abrir um trabalho em falha nas Ferramentas do ADL para Visual Studio, você receberá um alerta. As informações de erro detalhadas serão exibidas na guia de erros e na barra de alerta amarela na parte superior da janela.

1. Clique em **Download** para baixar todos os recursos e fluxos de entrada necessários. Clique em **Repetir** em caso de falha no download. 

2. Clique em **Abrir** após concluir o download para gerar um ambiente de depuração local. Uma nova instância do Visual Studio com uma solução de depuração será criada e aberta automaticamente. 

3. As etapas de depuração são um pouco diferentes entre trabalhos em falha com o code-behind e assemblies.

    - [Depurar trabalho em falha com code-behind](#debug-job-failed-with-code-behind)
    - [Depurar trabalho em falha com assemblies](#debug-job-failed-with-assemblies)

![Vértice para download do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

<a id="debug-job-failed-with-code-behind" class="xliff"></a>

## Depurar trabalho em falha com code-behind

Se você criou um código definido pelo usuário no arquivo de code-behind (geralmente chamado de "Script.usql.cs" no projeto do U-SQL) e o trabalho do U-SQL falhou, o código fonte será importado automaticamente para a solução de depuração gerada, e você pode usar a experiência de depuração baseada em Visual Studio (observação, variáveis, etc.) para solucionar o problema. 

Antes da depuração, verifique se você marcou **Exceções de Common Language Runtime** na janela Configurações de Exceção (**Ctrl + Alt + E**).

![Configuração do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Pressione **F5**, o código code-behind será executado automaticamente até que seja interrompido por uma exceção.

2. Abra **ADLTool_Codebehind.usql.cs** no projeto, defina pontos de interrupção e pressione F5 para depurar o código passo a passo.

    ![Exceção de depuração do U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

<a id="debug-job-failed-with-assemblies" class="xliff"></a>

## Depurar trabalho em falha com assemblies

Se você usa assemblies registrados no script U-SQL, o sistema não consegue obter o código-fonte automaticamente. Você precisa fazer algumas configurações antes de depurar o código definido pelo usuário, ou seja, deve adicionar o código-fonte dos assemblies à solução gerada automaticamente.

<a id="configure-the-solution" class="xliff"></a>

### Configurar a solução

1. Clique com o botão direito do mouse em **Solução 'VertexDebug'** > **Adicionar** > **Projeto Existente...** para encontrar o código-fonte dos assemblies e adicionar o projeto à solução de depuração.

    ![Adicionar projeto de depuração do U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Clique com botão direito do mouse em **LocalVertexHost** > **Propriedades** na solução e copie o caminho **Diretório de trabalho**.

3. Clique com botão direito do mouse em **projeto de código-fonte do assembly** > **Propriedades**, selecione a guia **Compilação** à esquerda e cole o caminho copiado na etapa 2 em **Saída** > **Caminho de saída**.  

    ![Definir caminho pdb da depuração do U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Pressione **Ctrl+Alt+E** e marque **Exceções de Common Language Runtime** na janela Configurações de exceção.

<a id="start-debug" class="xliff"></a>

### Iniciar depuração

1. Clique com botão direito do mouse em **projeto de código-fonte do assembly** > **Recompilação** para gerar arquivos pdb no diretório de trabalho LocalVertexHost.

2. Pressione **F5**, o projeto será executado automaticamente até que seja interrompido por uma exceção. Você pode ver a seguinte mensagem de erro pela primeira vez, a qual pode ignorar. Pode levar um minuto para que a tela de depuração seja exibida.

    ![Aviso do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Abra o código-fonte e defina pontos de interrupção, em seguida, pressione **F5** para depurar o código passo a passo.

Também é possível usar outras experiências de depuração baseadas no Visual Studio (observação, variáveis, etc.) para solucionar o problema. 

**Observe que** você precisa recompilar o projeto de código-fonte do assembly sempre que modificar o código para que os novos arquivos pdb entrem em vigor.

Depois que a depuração tiver sido concluída com êxito, a janela de saída mostrará a seguinte mensagem:

    The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).

![Depuração do U-SQL do Azure Data Lake Analytics bem sucedida](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

<a id="resubmit-the-job" class="xliff"></a>

## Reenviar o trabalho
Depois de concluir a depuração, você pode reenviar o trabalho que falhou.

1. Registre novos assemblies .dll no seu banco de dados ADLA.

    1. No Gerenciador de Servidores/Gerenciador de Nuvem, expanda o nó **Conta da ADLA** > **Bancos de dados**.
    2. Clique com o botão direito do mouse em **Assemblies** para Registrar assemblies. 
    3. Registre os novos assemblies .dll no seu banco de dados ADLA.
    ![Registrar assembly da depuração do U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
2. Ou copie seu código C# de volta para o arquivo de code-behind script.usql.cs--C#.
3. Reenvie o trabalho.

<a id="next-steps" class="xliff"></a>

## Próximas etapas

* [Guia de programação do U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
* [Desenvolver operadores do U-SQL definidos pelo usuário para trabalhos da Análise Azure Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* [Tutorial: desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)



