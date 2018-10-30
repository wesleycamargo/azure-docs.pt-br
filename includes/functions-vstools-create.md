---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a8be74558fb71ceb1c28c27004b27a5a5f477a8f
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650618"
---
O modelo de projeto do Azure Functions no Visual Studio cria um projeto que pode ser publicado em um aplicativo de funções no Azure. Um aplicativo de funções permite a você agrupar funções como uma unidade lógica para o gerenciamento, implantação e compartilhamento de recursos.

1. No Visual Studio, selecione **Novo** > **Projeto** no menu **Arquivo**.

2. No diálogo **Novo Projeto**, selecione **Instalado**, expanda **Visual C#** > **Cloud**, selecione **Azure Functions**, digite um **Nome** para seu projeto e clique em **OK**. O nome do aplicativo de funções deve ser válido como um namespace do C# e, portanto, não use outros caracteres não alfanuméricos, hífens ou sublinhados.

    ![Diálogo Novo projeto para criar uma função no Visual Studio](./media/functions-vstools-create/functions-vs-new-project.png)

3. Use as configurações conforme especificado na tabela que acompanha a imagem.

    ![Diálogo de nova função no Visual Studio](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Configuração      | Valor sugerido  | DESCRIÇÃO                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versão** | Azure Functions 2.x <br />(.NET Core) | Isso cria um projeto de função que usa o tempo de execução versão 2.x do Azure Functions, o qual dá suporte a .NET Core. O Azure Functions 1.x dá suporte ao .NET Framework. Para obter mais informações, consulte [Como direcionar para versões de tempo de execução do Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Modelo** | Gatilho HTTP | Isso cria uma função acionada por uma solicitação HTTP. |
    | **Conta de armazenamento**  | Emulador de Armazenamento | Um gatilho HTTP não usa a conexão da conta de armazenamento. Todos os outros tipos de gatilho exigem uma cadeia de conexão de conta de armazenamento válida. |
    | **Direitos de acesso** | Anônima | A função criada pode ser acionada por qualquer cliente sem fornecer uma chave. Essa configuração de autorização torna fácil testar a nova função. Para obter mais informações sobre chaves e autorização, consulte [Chaves de autorização](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) no [associações HTTP e webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
4. Clique em **OK** para criar a função de projeto e função acionada por HTTP.
