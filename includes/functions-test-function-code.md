---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55147931"
---
## <a name="test"></a>Testar a função

Use o cURL para testar a função implantada em um computador Mac ou Linux ou usando PowerShell no Windows. Execute o seguinte comando cURL, substituindo o espaço reservado `<app_name>` pelo nome do aplicativo de funções. Acrescente a cadeia de caracteres de consulta `&name=<yourname>` à URL.

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Resposta de função mostrada em um navegador.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Caso não tenha `cURL` ou `Invoke-WebRequest` disponível na linha de comando, digite a mesma URL no endereço do seu navegador da Web. Novamente, substitua o espaço reservado `<app_name>` pelo nome do aplicativo de funções, e acrescente a cadeia de consulta `&name=<yourname>` à URL e execute a solicitação.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Resposta de função mostrada em um navegador.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
