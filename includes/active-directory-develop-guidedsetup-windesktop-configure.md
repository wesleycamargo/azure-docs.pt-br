---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: b26b88d0e089217fa9915bdbdcb8f913731bcc67
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988172"
---
## <a name="register-your-application"></a>Registre seu aplicativo

Você pode registrar seu aplicativo de duas maneiras.

### <a name="option-1-express-mode"></a>Opção 1: modo Expresso

Registre rapidamente seu aplicativo fazendo o seguinte:
1. Vá até o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Selecione **Adicionar um aplicativo**.

3. No campo **Nome do Aplicativo**, insira um nome para o seu aplicativo.

4. Marque a caixa de seleção **Instalação Interativa** e, depois, selecione **Criar**.

5. Siga as instruções para obter a ID do aplicativo e cole-a em seu código.

### <a name="option-2-advanced-mode"></a>Opção 2: modo Avançado

Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, faça o seguinte:
1. Caso ainda não tenha registrado seu aplicativo, acesse o [Portal de Registro de Aplicativo da Microsoft](https://apps.dev.microsoft.com/portal/register-app).

2. Selecione **Adicionar um aplicativo**.

3. No campo **Nome do Aplicativo**, insira um nome para o seu aplicativo.

4. Desmarque a caixa de seleção **Instalação Interativa** e, depois, selecione **Criar**.

5. Selecione **Adicionar Plataforma**, selecione **Aplicativo Nativo** e, em seguida, selecione **Salvar**.

6. Na caixa **ID do Aplicativo**, copie o GUID.

7. Vá para o Visual Studio, abra o arquivo *App.xaml.cs* e, depois, substitua `your_client_id_here` pela ID do aplicativo que você acabou de registrar e copiar.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
