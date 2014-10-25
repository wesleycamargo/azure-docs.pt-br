<properties pageTitle="How to delegate user registration and product subscription" metaKeywords="" description="Learn how to delegate user registration and product subscription to a third party in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to delegate user registration and product subscription in Azure API Management" authors="antonba" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="antonba"></tags>

# Como delegar o registro de usuário e a assinatura do produto

A delegação permite usar seu site existente para gerenciar a entrada/inscrição e assinatura de produtos feitas por desenvolvedores em vez de usar a funcionalidade integrada no portal do desenvolvedor. Isso permite que seu site tenha os dados dos usuários e realize a validação dessas etapas de forma personalizada.

## Neste tópico

-   [Delegando a entrada e inscrição de desenvolvedores][Delegando a entrada e inscrição de desenvolvedores]
-   [Delegando a assinatura de produtos][Delegando a assinatura de produtos]

## <a name="delegate-signin-up"> </a>Delegando a entrada e inscrição de desenvolvedores

Para delegar a entrada e a assinatura do desenvolvedor em seu site existente, você precisará criar um ponto de extremidade de delegação especial em seu site que atue como ponto de entrada para qualquer solicitação desse tipo por meio do portal do desenvolvedor do Gerenciamento de API.

O fluxo de trabalho final será o seguinte:

1.  O desenvolvedor clica no link de assinatura ou entrada no portal do desenvolvedor do Gerenciamento de API
2.  O navegador é redirecionado ao ponto de extremidade de delegação
3.  O ponto de extremidade de delegação, por sua vez, redireciona ou apresenta a IU solicitando o usuário a entrar ou se inscrever
4.  Em caso de êxito, o usuário é redirecionado de volta para o portal do desenvolvedor do Gerenciamento de API onde começou

Para começar, vamos configurar o Gerenciamento de API para encaminhar as solicitações por meio do seu ponto de extremidade de delegação. No portal do editor do Gerenciamento de API, sob o título **Portal do desenvolvedor** no menu à esquerda, clique em **Delegação** e depois clique em **Delegar entrada e inscrição**.

![Página de delegação][Página de delegação]

-   Decida qual será o URL do seu ponto de extremidade de delegação especial e insira-o no campo **URL do ponto de extremidade de delegação**.

-   No campo **Chave de autenticação de delegação**, insira um segredo que será usado para calcular uma assinatura fornecida a você para verificação, para garantir que a solicitação realmente venha do Gerenciamento de API do Azure.

Agora, você precisa criar o **ponto de extremidade de delegação**. Ele precisa realizar uma série de ações:

1.  Receba uma solicitação com a seguinte forma:

    > *<http://www.yourwebsite.com/apimdelegation?operation=SignIn&redirectUrl>={URL da página de origem}&salt={cadeia}&sid={cadeia}*

    Parâmetros de consulta para a entrada/inscrição:

    -   **operation**: identifica o tipo de solicitação de delegação - neste caso, pode ser somente “SignIn”
    -   **redirectUrl**: o URL da página onde o usuário clicou em um link de assinatura ou inscrição
    -   **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
    -   **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado

2.  Confirme que a solicitação está vindo do Gerenciamento de API do Azure (opcional, mas altamente recomendado por segurança)

    -   Calcule um hash HMAC-SHA512 de uma cadeia baseada nos parâmetros de consulta **redirectUrl** e **salt**:

        > HMAC(**redirectUrl** + '\\n' + **salt**)

    -   Compare o hash calculado acima ao valor do parâmetro de consulta **sig**. Se os dois hashes forem correspondentes, prossiga para a próxima etapa. Caso contrário, recuse as solicitações.

3.  Confirme que você está recebendo uma solicitação de entrada/inscrição: o parâmetro de consulta **operation** será definido como "**SignIn**".

4.  Apresente ao usuário a IU para entrar ou se inscrever

5.  Se o usuário estiver se inscrevendo, você precisará criar uma conta correspondente para ele no Gerenciamento de API. [Crie um usuário][Crie um usuário] com a API REST do Gerenciamento de API. Ao fazer isso, certifique-se de definir a ID de usuário como a mesma que está em seu repositório de usuários ou como uma ID que você possa acompanhar.

6.  Quando o usuário for autenticado com sucesso:

    -   [solicite um token de logon único (SSO)][solicite um token de logon único (SSO)] por meio da API REST do Gerenciamento de API

    -   anexe um parâmetro de consulta returnUrl ao URL SSO que você recebeu da chamada à API acima:

        > por exemplo, <https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url>

    -   redirecione o usuário à URL produzida acima

## <a name="delegate-product-subscription"> </a>Delegando a assinatura de produtos

A delegação de uma assinatura de produto funciona de forma semelhante à delegação de uma entrada/inscrição de usuário. O fluxo de trabalho final seria o seguinte:

1.  O desenvolvedor selecione um produto no portal do desenvolvedor do Gerenciamento de API e clica no botão Assinar
2.  O navegador é redirecionado ao ponto de extremidade de delegação
3.  O ponto de extremidade de delegação realiza as etapas de assinatura de produto necessária - isso depende de você e pode envolver o redirecionamento para outra página para solicitar informações de cobrança, fazer perguntas adicionais ou simplesmente armazenar as informações sem precisar de ações do usuário
4.  Em caso de êxito, o usuário é redirecionado de volta para o portal do desenvolvedor do Gerenciamento de API onde começou

Para habilitar a funcionalidade, na página **Delegação** clique em **Delegar assinatura do produto**.

Depois, certifique-se de que o ponto de extremidade de delegação realize as ações a seguir:

1.  Receba uma solicitação com a seguinte forma:

    > *<http://www.yourwebsite.com/apimdelegation?operation>={operação}&productId={produto a ser assinado}&userId={usuário que fez a solicitação}&salt={cadeia}&sid={cadeia}*

    Parâmetros de consulta para a assinatura de produto:

    -   **operation**: identifica o tipo de solicitação de delegação. Para solicitações de assinatura do produto, as opções válidas são:
        -   "Subscribe": uma solicitação para realizar a assinatura do usuário de um determinado produto com uma ID fornecida (veja abaixo)
        -   "Unsubscribe": uma solicitação para cancelar a assinatura do usuário de um produto
        -   "Renew": uma solicitação para renovar uma assinatura (que pode, por exemplo, estar expirando)
    -   **productId**: a ID do produto a que o usuário solicitou assinar
    -   **userId**: a ID do usuário para o qual a solicitação é feita
    -   **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
    -   **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado

2.  Confirme que a solicitação está vindo do Gerenciamento de API do Azure (opcional, mas altamente recomendado por segurança)

    -   Calcule um hash HMAC-SHA512 de uma cadeia baseada nos parâmetros de consulta **productId**, **userId** e **salt**:

        > HMAC(**productId** + '\\n' + **userId** + '\\n' + **salt**)

    -   Compare o hash calculado acima ao valor do parâmetro de consulta **sig**. Se os dois hashes forem correspondentes, prossiga para a próxima etapa. Caso contrário, recuse as solicitações.

3.  Faça o processamento de qualquer assinatura de produto com base no tipo de operação solicitada em **operation** - por exemplo, faturamento, perguntas etc.

4.  Após realizar com êxito a assinatura do produto pelo usuário pela sua parte, assine o usuário do produto do Gerenciamento de API [chamando a API REST para assinatura do produto][chamando a API REST para assinatura do produto].

5.  Redirecione o usuário à **redirectUrl** fornecida no momento do recebimento da solicitação.

  [Delegando a entrada e inscrição de desenvolvedores]: #delegate-signin-up
  [Delegando a assinatura de produtos]: #delegate-product-subscription
  [Página de delegação]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
  [Crie um usuário]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
  [solicite um token de logon único (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
  [chamando a API REST para assinatura do produto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
