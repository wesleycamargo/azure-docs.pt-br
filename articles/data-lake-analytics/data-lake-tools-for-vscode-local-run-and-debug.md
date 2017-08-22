---
title: "Ferramentas do Azure Data Lake – Execução Local e Depuração Local do U-SQL com o Visual Studio Code | Microsoft Docs"
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
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: e5e767a6f31be052c9bae059b49b31d59fdfd894
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---

# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Execução local e depuração local do U-SQL com o Visual Studio Code

## <a name="prerequisites"></a>Pré-requisitos
- Ferramenta do Azure Data Lake para Visual Studio Code. Para obter instruções, consulte [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
- C# para Visual Studio Code (se você quiser executar a Depuração Local do U-SQL).

   ![Ferramentas do Data Lake para Visual Studio Code instalar vscodeCsharp](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > Os recursos de depuração e execução local do U-SQL atualmente dão suporte apenas para usuários do Windows. 


## <a name="set-up-u-sql-local-run-environment"></a>Configurar o ambiente de execução local do U-SQL

1. Abra a paleta de comandos pressionando **CTRL+SHIFT+P** e digite **ADL: Baixar Dependência LocalRun** para baixar os pacotes.  

   ![DownloadLocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Localize os pacotes de dependência do caminho exibido no painel de saída abaixo e, em seguida, instale BuildTools e Win10SDK 10240. Por exemplo:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![LocateDependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

- Instalar BuildTools – siga as instruções do assistente para instalar.   

  ![InstallBuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)
- Instalar Win10SDK 10240 – siga as instruções para instalar.  

  ![InstallWin10SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)
3. Configuração da variável de ambiente – defina a variável de ambiente **SCOPE_CPP_SDK** como:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
Certifique-se de reiniciar o sistema operacional para que as configurações da variável de ambiente entrem em vigor.  

   ![ConfigSCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-local-run-service-and-submit-u-sql-job-to-local-account"></a>Iniciar serviço de execução local e enviar o trabalho do U-SQL para a conta local 
Ao usuário de primeira viagem, será solicitado que escolha a opção **ADL: Baixar Dependência Localrun** para baixar os pacotes, se ainda não o tiver feito.
1. Pressione **CTRL+SHIFT+P** para abrir a paleta de comandos e digite **ADL: Iniciar Serviço de Execução Local**.
2. Aceite o EULA pela primeira vez. 

   ![Aceitar o EULA ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. O console de cmd será exibido. Aos usuários de primeira viagem, será solicitado que digite 3 e, em seguida, insira um caminho de pasta local para os dados de entrada e saída. Para outras opções, basta simplesmente usar o valor padrão. 

   ![Ferramentas do Data Lake para Visual Studio Code cmd de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Pressione CTRL+SHIFT+P para abrir a paleta de comandos, digite **ADL: Enviar Trabalho** e, em seguida, selecione **Local** para enviar o trabalho para sua conta local.

   ![Ferramentas do Data Lake para Visual Studio Code selecionar local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Após o envio do trabalho, você poderá ver os detalhes de envio clicando em jobUrl na janela de saída. Você também pode exibir o status de envio do trabalho no console do CMD. Digite 7 no console do CMD se quiser saber mais detalhes do trabalho.

   ![Ferramentas do Data Lake para Visual Studio Code saída de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Ferramentas do Data Lake para Visual Studio Code status do cmd de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 



## <a name="start-local-debug-for-u-sql-job"></a>Iniciar depuração local no trabalho do U-SQL  
Ao usuário de primeira viagem, será solicitado que escolha a opção **ADL: Baixar Dependência Localrun** para baixar os pacotes, se ainda não o tiver feito.
  
1. Pressione **CTRL+SHIFT+P** para abrir a paleta de comandos e digite **ADL: Iniciar Serviço de Execução Local**. A janela cdm é exibida. Verifique se **DataRoot** está configurado.
3. Defina um ponto de interrupção no code-behind do C#.
4. De volta ao editor de scripts, pressione **CTRL+SHIFT+P** para abrir a paleta de comandos e digite **Depuração local** para iniciar o serviço de depuração local.

![Ferramentas do Data Lake para Visual Studio Code resultado da depuração local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Próximas etapas
- Para usar as Ferramentas do Azure Data Lake para Visual Studio Code, confira [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- Para obter informações introdutórias sobre o Data Lake Analytics, veja [Tutorial: Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Para saber mais sobre como usar as Ferramentas do Data Lake para Visual Studio, confira [Tutorial: Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obter informações sobre como desenvolver assemblies, confira [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Desenvolver assemblies U-SQL para trabalhos do Azure Data Lake Analytics).
