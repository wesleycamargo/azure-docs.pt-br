---
title: "Azure Active Directory B2C: estrutura de política extensível | Microsoft Docs"
description: "Um tópico sobre a estrutura de política extensível do Active Directory B2C do Azure e como criar vários tipos de política"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 351149296a6d7dfa801b295ec21fc04215c7b051
ms.openlocfilehash: 5db03c2ba594963f6c5a2f091f83b9de240fe2ba


---
# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure Active Directory B2C: estrutura de política extensível
## <a name="the-basics"></a>Noções básicas
A estrutura de política extensível do Azure AD (Azure Active Directory) B2C é o principal ponto forte do serviço. As políticas descrevem totalmente as experiências de identidade do consumidor, como inscrição, credenciais ou edição de perfil. Por exemplo, uma política de inscrição permite controlar comportamentos definindo as seguintes configurações:

* Tipos de conta (contas sociais, como o Facebook, ou contas locais como um endereço de email) que os consumidores podem usar para se inscrever no aplicativo.
* Atributos (como nome, CEP e tamanho do calçado, por exemplo) que serão coletados junto ao consumidor durante a inscrição.
* Uso da Multi-Factor Authentication.
* A aparência e funcionalidade de todas as páginas de inscrição.
* Informações (que se manifestam como declarações em um token) que o aplicativo recebe quando a execução da política é concluída.

Você pode criar várias políticas de tipos diferentes em seu locatário e usá-las em seus aplicativos, conforme necessário. As políticas podem ser reutilizadas entre os aplicativos. Isso permite aos desenvolvedores definir e modificar experiências de identidade do consumidor com pouca ou nenhuma alteração no código.

As políticas estão disponíveis para uso por meio de uma interface simples do desenvolvedor. O aplicativo dispara uma política usando uma solicitação de autenticação HTTP padrão (passando um parâmetro de política na solicitação) e recebe um token personalizado como resposta. Por exemplo, a única diferença entre solicitações que invocam uma política de inscrição e aquelas que invocam uma política de credenciais é o nome da política usado no parâmetro de cadeia de caracteres de consulta "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Para obter mais detalhes sobre a estrutura de políticas, confira esta [postagem do blog](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Criar uma política de inscrição
Para habilitar a inscrição no seu aplicativo, você precisará criar uma política de inscrição. Essa política descreve as experiências pelas quais os consumidores passarão durante a inscrição e o conteúdo dos tokens que o aplicativo receberá de inscrições bem-sucedidas.

1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Clique em **Políticas de inscrição**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. O **Nome** determina o nome da política de inscrição usado pelo seu aplicativo. Por exemplo, insira "SiUp".
5. Clique em **Provedores de identidade** e selecione "Inscrição de email". Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.
6. Clique em **Atributos de inscrição**. Escolha aqui os atributos do consumidor que você deseja coletar durante a inscrição. Por exemplo, selecione "Cidade/região", "Nome de exibição" e "CEP". Clique em **OK**.
7. Clique em **Declarações do aplicativo**. Escolha aqui as declarações que você deseja que sejam retornadas nos tokens enviados ao aplicativo após uma experiência de inscrição bem-sucedida. Por exemplo, selecione "Nome de exibição", "Provedor de identidade", "CEP", "Novo usuário" e "ID de objeto do usuário".
8. Clique em **Criar**. Observe que a política recém-criada aparece como "**B2C_1_SiUp**" (o fragmento** B2C\_1\_** é adicionado automaticamente) na folha **Políticas de inscrição**.
9. Abra a política clicando em "**B2C_1_SiUp**".
10. Selecione "aplicativo Contoso B2C” na lista suspensa **Aplicativos** e `https://localhost:44321/` na lista suspensa **URL de Resposta/URI de redirecionamento**.
11. Clique em **Executar agora**. Uma nova guia do navegador se abre e você poderá percorrer a experiência do consumidor de inscrição no aplicativo.
    
    > [!NOTE]
    > Leva até um minuto para a criação de políticas e as atualizações entrem em vigor.
    > 
    > 

## <a name="create-a-sign-in-policy"></a>Usar uma política de entrada
Para habilitar a entrada no aplicativo, você precisará criar uma política de entrada. Essa política descreve as experiências pelas quais os consumidores passarão durante a entrada e o conteúdo de tokens que o aplicativo receberá de entradas bem-sucedidas.

1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Clique em **Políticas de entrada**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. O **Nome** determina o nome da política de entrada usado pelo seu aplicativo. Por exemplo, insira "SiIn".
5. Clique em **Provedores de identidade** e selecione "Entrada na conta local". Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.
6. Clique em **Declarações do aplicativo**. Escolha aqui as declarações que você deseja que sejam retornadas dos tokens enviados ao aplicativo após uma experiência de entrada bem-sucedida. Por exemplo, selecione "Nome de exibição", "Provedor de identidade", "CEP" e "ID de objeto do usuário". Clique em **OK**.
7. Clique em **Criar**. Observe que a política recém-criada aparece como "**B2C_1_SiIn**" (o fragmento **B2C\_1\_** torna-se automaticamente pré-pendente) na folha **Políticas de entrada**.
8. Abra a política clicando em "**B2C_1_SiIn**".
9. Selecione "aplicativo Contoso B2C” na lista suspensa **Aplicativos** e `https://localhost:44321/` na lista suspensa **URL de Resposta/URI de redirecionamento**.
10. Clique em **Executar agora**. Uma nova guia do navegador se abre e você poderá percorrer a experiência do consumidor de entrada no aplicativo.
    
    > [!NOTE]
    > Leva até um minuto para a criação de políticas e as atualizações entrem em vigor.
    > 
    > 

## <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou credenciais
Esta política controla as duas experiências de inscrição e credenciais do consumidor com uma única configuração. Os consumidores são conduzidos para o caminho certo (inscrição ou credenciais), dependendo do contexto. Ele também descreve o conteúdo de tokens que o aplicativo receberá mediante inscrições ou entradas bem-sucedidas.  Há um exemplo de código para a política de inscrição ou de entrada [disponível aqui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Clique em **Políticas de inscrição ou de entrada**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. O **Nome** determina o nome da política de inscrição usado pelo seu aplicativo. Por exemplo, insira "SiUpIn".
5. Clique em **Provedores de identidade** e selecione "Inscrição de email". Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.
6. Clique em **Atributos de inscrição**. Escolha aqui os atributos do consumidor que você deseja coletar durante a inscrição. Por exemplo, selecione "Cidade/região", "Nome de exibição" e "CEP". Clique em **OK**.
7. Clique em **Declarações do aplicativo**. Escolha aqui as declarações que você deseja que sejam retornadas dos tokens enviados ao aplicativo após uma experiência de inscrição ou credenciais bem-sucedida. Por exemplo, selecione "Nome de exibição", "Provedor de identidade", "CEP", "Novo usuário" e "ID de objeto do usuário".
8. Clique em **Criar**. Observe que a política recém-criada aparece como "**B2C_1_SiUpIn**" (o fragmento **B2C\_1\_** é automaticamente adicionado) na folha **Políticas de inscrição ou de entrada**.
9. Abra a política clicando em "**B2C_1_SiUpIn**".
10. Selecione "aplicativo Contoso B2C” na lista suspensa **Aplicativos** e `https://localhost:44321/` na lista suspensa **URL de Resposta/URI de redirecionamento**.
11. Clique em **Executar agora**. Uma nova guia do navegador se abre e você poderá percorrer a experiência do consumidor de inscrição ou credenciais, conforme configurado.
    
    > [!NOTE]
    > Leva até um minuto para a criação de políticas e as atualizações entrem em vigor.
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>Criar uma política de edição de perfil
Para habilitar a edição de perfil no aplicativo, você precisará criar uma política de edição de perfil. Essa política descreve as experiências pelas quais os consumidores passarão durante a edição do perfil e o conteúdo de tokens que o aplicativo receberá na conclusão bem-sucedida.

1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Clique em **Políticas de edição de perfil**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. O **Nome** determina o nome da política de edição de perfil usado pelo seu aplicativo. Por exemplo, insira "SiPe".
5. Clique em **Provedores de identidade** e selecione "Endereço de email". Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.
6. Clique em **Atributos do perfil**. Aqui você escolhe os atributos que o consumidor pode exibir e editar. Por exemplo, selecione "País/Região", "Nome de Exibição" e "CEP". Clique em **OK**.
7. Clique em **Declarações do aplicativo**. Aqui você escolhe as declarações que quer retornar dos tokens de volta ao aplicativo após uma experiência de edição de perfil bem-sucedida. Por exemplo, selecione "Nome de exibição" e "CEP".
8. Clique em **Criar**. Observe que a política recém-criada aparece como "**B2C_1_SiPe**" (o fragmento **B2C\_1\_** é adicionado automaticamente) na folha **Políticas de edição de perfil**.
9. Abra a política clicando em "**B2C_1_SiPe**".
10. Selecione "aplicativo Contoso B2C” na lista suspensa **Aplicativos** e `https://localhost:44321/` na lista suspensa **URL de Resposta/URI de redirecionamento**.
11. Clique em **Executar agora**. Uma nova guia do navegador se abre e você pode percorrer a experiência do consumidor de edição de perfil no aplicativo.
    
    > [!NOTE]
    > Leva até um minuto para a criação de políticas e as atualizações entrem em vigor.
    > 
    > 

## <a name="create-a-password-reset-policy"></a>Criar uma política de redefinição de senha
Para habilitar a redefinição de senha refinada, você precisará criar uma política de redefinição de senha. Observe que a opção de redefinição de senha para todo o locatário especificada [aqui](active-directory-b2c-reference-sspr.md) também é aplicável para as políticas de credenciais. Essa política descreve as experiências pelas quais os consumidores passarão durante a redefinição da senha e o conteúdo de tokens que o aplicativo receberá na conclusão bem-sucedida.

1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Clique em **Políticas de redefinição de senha**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. O **Nome** determina o nome da política de redefinição de senha usada pelo aplicativo. Por exemplo, insira “SSPR”.
5. Clique em **Provedores de identidade** e escolha "Redefinir senha usando endereço de email". Clique em **OK**.
6. Clique em **Declarações do aplicativo**. Aqui você escolhe as declarações que quer retornar dos tokens de volta ao aplicativo após uma experiência de redefinição de senha bem-sucedida. Por exemplo, escolha "ID de Objeto do Usuário".
7. Clique em **Criar**. Observe que a política recém-criada aparece como "**B2C_1_SSPR**" (o fragmento **B2C\_1\_** é adicionado automaticamente) na folha **Políticas de redefinição de senha**.
8. Abra a política clicando em "**B2C_1_SSPR**".
9. Selecione "aplicativo Contoso B2C” na lista suspensa **Aplicativos** e `https://localhost:44321/` na lista suspensa **URL de Resposta/URI de redirecionamento**.
10. Clique em **Executar agora**. Uma nova guia do navegador se abre e você pode percorrer a experiência do consumidor de redefinição de senha no aplicativo.
    
    > [!NOTE]
    > Leva até um minuto para a criação de políticas e as atualizações entrem em vigor.
    > 
    > 

## <a name="how-to-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Como vincular uma política de inscrição ou entrada a uma política de redefinição de senha?
Quando você cria uma política de inscrição ou entrada (com contas locais), o consumidor verá um link "Esqueceu a senha?" na primeira página da experiência. Clicar nesse link não dispara automaticamente uma política de redefinição de senha. Em vez disso, um código de erro específico `AADB2C90118` retorna em seu aplicativo. Seu aplicativo precisa lidar com isso e invocar uma política de redefinição de senha específica. Confira [aqui](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) um exemplo que demonstra essa abordagem de vinculação de políticas.

## <a name="additional-resources"></a>Recursos adicionais
* [Token, sessão e configuração de logon único](active-directory-b2c-token-session-sso.md).




<!--HONumber=Jan17_HO4-->


