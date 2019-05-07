---
title: Aplicativo móvel que chamadas de web APIs - chamar uma API web | Plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo móvel que chama as APIs de Web (chamando uma API da Web)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075109"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Aplicativo móvel que chama APIs - web chamar uma API web

Depois que seu aplicativo tem um usuário conectado e recebida tokens, MSAL expõe várias partes de informações sobre o usuário, seu ambiente e os tokens emitidos. Seu aplicativo pode usar esses valores para chamar uma API da web ou exibir uma mensagem de boas-vinda a um usuário.

Primeiro, vamos explorar o resultado da MSAL e, em seguida, como usar um token de acesso do `AuthenticationResult` ou `result` para chamar uma API web protegida.

## <a name="msal-result"></a>Resultado da MSAL

- `AccessToken`: Usado para chamar APIs web protegida em uma solicitação de portador de HTTP.
- `IdToken`: Contém declarações úteis sobre o usuário conectado como seu nome, o locatário inicial e o identificador exclusivo para o armazenamento.
- `ExpiresOn`: o tempo de expiração do token. A MSAL manipula a atualização automática para aplicativos.
- `TenantId`: O identificador do locatário do usuário usado para entrar. Para usuários convidados (Azure AD B2B), esse será o locatário o usuário conectado com não seu locatário inicial.  
- `Scopes`: os escopos que foram concedidos com seu token. Isso pode ser um subconjunto do que é solicitado.

Além disso, a MSAL também fornece uma abstração para um `Account`. Uma conta representa o usuário atual conectado na conta.

- `HomeAccountIdentifier`: O identificador do locatário inicial do usuário.
- `UserName`: Nome de usuário preferencial do usuário. Isso pode estar vazio para que os usuários do Azure AD B2C.
- `AccountIdentifier`: O identificador do usuário conectado. Esse será o mesmo que o `HomeAccountIdentifier` na maioria dos casos, a menos que o usuário é um convidado em outro locatário.

## <a name="calling-an-api"></a>Chamar uma API

Depois que o token de acesso pronto, é simple chamar uma API da web. Seu aplicativo levar isso em token, construir uma solicitação HTTP e executá-lo.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put Access token in HTTP Request
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Fazer várias solicitações de API

Se você precisa chamar a mesma API várias vezes ou várias APIs, há considerações adicionais ao criar o aplicativo:

- ***Consentimento incremental***: Plataforma de identidade da Microsoft permite que os aplicativos obter consentimento do usuário, como permissões são necessárias, em vez de todas as iniciais. Cada vez que seu aplicativo está pronto para chamar uma API, ele deve solicitar apenas os escopos que pretende usar.
- ***Acesso condicional***: Em determinados cenários, você pode obter requisitos adicionais de acesso condicional ao fazer várias solicitações de API. Para manipular esse cenário, certifique-se de capturar erros de solicitações silenciosas e estar preparado para fazer uma solicitação interativa. Isso pode acontecer se a primeira solicitação não tem nenhuma política de acesso condicional aplicada e o aplicativo tenta acessar silenciosamente uma nova API que exige o acesso condicional. Para obter mais informações, consulte [diretrizes para acesso condicional](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para a produção](scenario-mobile-production.md)
