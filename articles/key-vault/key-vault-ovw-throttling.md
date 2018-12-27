---
title: Diretrizes de limitação do Azure Key Vault
description: A limitação do Azure Key Vault limita o número de chamadas simultâneas para evitar o uso excessivo de recursos.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: f119e4a5b5c5f97848c588636a3a707428abbd5b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082519"
---
# <a name="azure-key-vault-throttling-guidance"></a>Diretrizes de limitação do Azure Key Vault

A limitação é o processo que você iniciar para limitar o número de chamadas simultâneas para o serviço do Azure a fim de evitar o uso excessivo de recursos. O AKV (Azure Key Vault) foi projetado para lidar com um grande volume de solicitações. No caso de ocorrer um grande número de solicitações, limitar as solicitações do seu cliente ajuda a manter o desempenho ideal e a confiabilidade do serviço AKV.

Limites de limitação variam de acordo com o cenário. Por exemplo, se você estiver executando um grande volume de gravação, a possibilidade de limitação será maior do que se você estiver apenas executando leituras.

## <a name="how-does-key-vault-handle-its-limits"></a>Como o Key Vault trata os próprios limites?

Os limites de serviço no Key Vault existem para impedir o uso incorreto de recursos e assegurar a qualidade de serviço para todos os clientes do Key Vault. Quando um limite de serviço é excedido, o Key Vault limita quaisquer solicitações adicionais desse cliente por um período de tempo. Quando isso acontece, o Key Vault retorna o código de status HTTP 429 (Número excessivo de solicitações) e as solicitações falham. Além disso, solicitações com falha que retornam um 429 contam para as restrições acompanhadas pelo Key Vault. 

Se você tiver um caso comercial válido para restrições mais altas, entre em contato conosco.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Como restringir o seu aplicativo em resposta aos limites de serviço

A seguir estão as **práticas recomendadas** para limitar seu aplicativo:
- Reduza o número de operações por solicitação.
- Reduza a frequência de solicitações.
- Evite tentativas imediatas. 
    - Todas as solicitações se acumulam em relação a seus limites de uso.

Quando você implementa o tratamento de erro do aplicativo, use o código de erro HTTP 429 para detectar a necessidade de limitação do lado do cliente. Se a solicitação falha novamente com um código de erro HTTP 429, você ainda está encontrando um limite de serviço do Azure. Continue a usar o método de limitação do lado do cliente recomendado, tentando realizar a solicitação novamente até que ela tenha êxito.

O código que implementa a retirada exponencial é mostrado abaixo. 
```
     public async Task OnGetAsync()
     {
         Message = "Your application description page.";
         int retries = 0;
         bool retry = false;
         try
         {
             AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
             KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
             var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                     .ConfigureAwait(false);
             Message = secret.Value;

             /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
             do
             {
                 long waitTime = Math.Min(getWaitTime(retries), 2000000);
                 secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                     .ConfigureAwait(false);
                 retry = false;
             } 
             while(retry && (retries++ < 10));
         }
         /// <exception cref="KeyVaultErrorException">
         /// Thrown when the operation returned an invalid status code
         /// </exception>
         catch (KeyVaultErrorException keyVaultException)
         {
             Message = keyVaultException.Message;
             if((int)keyVaultException.Response.StatusCode == 429)
                 retry = true;
         }
     }

     // This method implements exponential backoff incase of 429 errors from Azure Key Vault
     private static long getWaitTime(int retryCount)
     {
         long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
         return waitTime;
     }
```

### <a name="recommended-client-side-throttling-method"></a>Método recomendado de limitação do lado do cliente

No código de erro HTTP 429, inicie a limitação do cliente usando uma abordagem de retirada exponencial:

1. Aguarde 1 segundo, tente solicitar novamente
2. Se ainda estiver limitado, aguarde 2 segundos e tente a solicitação novamente
3. Se ainda estiver limitado, aguarde 4 segundos e tente a solicitação novamente
4. Se ainda estiver limitado, aguarde 8 segundos e tente a solicitação novamente
5. Se ainda estiver limitado, aguarde 16 segundos e tente a solicitação novamente

Neste ponto, você não deve estar obtendo códigos de resposta HTTP 429.

## <a name="see-also"></a>Consulte também

Para obter uma orientação mais profunda de limitação no Microsoft Cloud, consulte [Padrão de Limitação](https://docs.microsoft.com/azure/architecture/patterns/throttling).

