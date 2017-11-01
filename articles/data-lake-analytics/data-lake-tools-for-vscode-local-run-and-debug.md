---
title: "Ferramentas do Azure Data Lake: execução local e depuração local do U-SQL com o Visual Studio Code | Microsoft Docs"
description: "Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio Code na execução local e na depuração local."
Keywords: "VSCode, Ferramentas do Azure Data Lake, Execução local, Depuração local, Depuração Local, visualizar arquivo de armazenamento, carregar no caminho de armazenamento"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.openlocfilehash: 78a5efb19f73192dcc95103abc70c14a3ce2e29a
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="u-sql-local-run-and-local-debug-for-windows-with-visual-studio-code"></a>Execução local e depuração local do U-SQL para Windows com o Visual Studio Code
Neste documento, você aprenderá a executar trabalhos de U-SQL em um computador de desenvolvimento local para acelerar as fases iniciais de codificação ou para depurar o código localmente no Visual Studio Code. Para obter instruções sobre como usar as Ferramentas do Azure Data Lake para Visual Studio Code, confira [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md). 


## <a name="set-up-the-u-sql-local-run-environment"></a>Configurar o ambiente de execução local do U-SQL

1. Selecione Ctrl+Shift+P para abrir a paleta de comandos e, então, insira **ADL: Fazer Download da Dependência de Execução Local** para baixar os pacotes.  

   ![Baixar os pacotes de Dependência de LocalRun do ADL](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Localize os pacotes de dependência do caminho mostrado no painel **Saída** abaixo e então instale o BuildTools e o Win10SDK 10240. Aqui está um caminho de exemplo:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![Localizar os pacotes de dependência](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 Para instalar **BuildTools**, clique em visualcppbuildtools_full.exe na pasta LocalRunDependency e siga as instruções do assistente.   

    ![Instalar BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 Para instalar **Win10SDK 10240**, clique em sdksetup.exe na pasta LocalRunDependency/Win10SDK_10.0.10240_2 e siga as instruções do assistente.  

    ![Instalar Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Defina a variável de ambiente. Defina a variável de ambiente **SCOPE_CPP_SDK** como:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
4. Reinicie o sistema operacional para garantir que as configurações da variável de ambiente entrem em vigor.  

   ![Certifique-se de que a variável de ambiente SCOPE_CPP_SDK esteja instalada](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Inicie o serviço de execução local e envie o trabalho U-SQL para uma conta local 
Usuário novato: use **ADL: Fazer Download de Dependência de Execução Local** para baixar pacotes de execução local, se você ainda não tiver [configurado o ambiente de execução local U-SQL](#set-up-the-u-sql-local-run-environment).

1. Pressione Ctrl+Shift+P para abrir a paleta de comandos e digite **ADL: Iniciar Serviço de Execução Local**.   
2. Selecione **Aceito** para aceitar os termos da Licença de Software da Microsoft pela primeira vez. 

   ![Aceite os Termos de Licença para Software Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. O console cmd é aberto. Aos usuários de primeira viagem, será necessário inserir **3** e insira um caminho de pasta local para os dados de entrada e saída. Para outras opções, basta simplesmente usar os valores padrão. 

   ![Ferramentas do Data Lake para Visual Studio Code cmd de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Select Ctrl+Shift+P para abrir a paleta de comandos, insira **ADL: Enviar Trabalho** e selecione **Local** para enviar o trabalho para sua conta local.

   ![Ferramentas do Data Lake para Visual Studio Code selecionar local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Depois de enviar o trabalho, você poderá exibir os detalhes de envio. Para exibir os detalhes do envio, selecione **jobUrl** na janela **Saída**. Você também pode exibir o status de envio de trabalho do console do cmd. Digite **7** no console do cmd se você quiser saber mais detalhes do trabalho.

   ![Ferramentas do Data Lake para Visual Studio Code saída de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Ferramentas do Data Lake para Visual Studio Code status do cmd de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Iniciar uma depuração local para o trabalho de U-SQL  
Para o usuário novato:

1. Use **ADL: Fazer Download de Dependência de Execução Local** para baixar pacotes de execução local, se você ainda não tiver [configurado o ambiente de execução local U-SQL](#set-up-the-u-sql-local-run-environment).
2. Instale o SDK 2.0 do .NET Core, conforme sugerido na caixa de mensagem, se não estiver instalado.
 
  ![lembrete instala Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Instale o C# para Visual Studio Code, conforme sugerido na caixa de mensagem, se não estiver instalado. Clique em **Instalar** para continuar e, em seguida, reinicie o VSCode.

    ![Lembrete para instalar o C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Siga as etapas abaixo para executar a depuração local:
  
1. Pressione Ctrl+Shift+P para abrir a paleta de comandos e digite **ADL: Iniciar Serviço de Execução Local**. O console cmd é aberto. Verifique se **DataRoot** está definido.
2. Defina um ponto de interrupção no code-behind do C#.
3. Volte ao editor de scripts, clique com o botão direito do mouse e selecione **ADL: Depuração Local**.
    
   ![Ferramentas do Data Lake para Visual Studio Code resultado da depuração local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Próximas etapas
- Para usar as Ferramentas do Azure Data Lake para Visual Studio Code, confira [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- Para obter informações introdutórias sobre o Data Lake Analytics, veja [Tutorial: Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Para saber mais sobre as Ferramentas do Data Lake para Visual Studio, confira [Tutorial: Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obter informações sobre como desenvolver assemblies, confira [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Desenvolver assemblies U-SQL para trabalhos do Azure Data Lake Analytics).
