---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ae88a6132c79d9d9f930f6445e53b0f0452c9f2a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262600"
---
## <a name="test"></a>Testar a função

Use o cURL para testar a função implantada em um computador Mac ou Linux, ou usando Bash no Windows. Execute o seguinte comando cURL, substituindo o espaço reservado `<app_name>` pelo nome do aplicativo de funções. Acrescente a cadeia de caracteres de consulta `&name=<yourname>` à URL.

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Resposta de função mostrada em um navegador.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Caso não tenha o cURL disponível na linha de comando, digite a mesma URL no endereço do seu navegador da Web. Novamente, substitua o espaço reservado `<app_name>` pelo nome do aplicativo de funções, e acrescente a cadeia de consulta `&name=<yourname>` à URL e execute a solicitação.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Resposta de função mostrada em um navegador.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
