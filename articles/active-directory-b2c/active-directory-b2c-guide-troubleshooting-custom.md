---
title: "Azure Active Directory B2C: Solução de problemas de políticas personalizadas | Microsoft Docs"
description: "Um guia para várias abordagens para resolver erros de políticas personalizadas"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0f783062cd674edb0b6b49660f5aee69af4b3adb
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-b2c-custom-policies-and-identity-experience-framework-troubleshooting"></a>Solução de problemas da Identity Experience Framework e de Políticas Personalizadas do Azure Active Directory B2C
## <a name="the-basics"></a>Noções básicas

Os desenvolvedores de identidade usando políticas personalizadas do Azure AD B2C são desafiados a configurar a estrutura de experiência de identidade em seu idioma de política no formato XML.  Este guia é uma lista de ferramentas recomendadas e de dicas para detectar e resolver problemas rapidamente.  Aprender a escrever Políticas Personalizadas é semelhante a aprender uma nova linguagem.  
*Este artigo se concentra na solução de problemas de sua configuração de política personalizada do Azure AD B2C e não o aplicativo de terceira parte confiável ou sua biblioteca de identidade.*



## <a name="xml-editing-inproperly-formatted-xml-is-the-most-common-error"></a>Edição de XML, o XML formatado indevidamente é o erro mais comum.

Um bom editor de XML que exibe XML nativamente, codifica o conteúdo por cor, preenche os termos comuns com antecedência, mantém os elementos XML indexados e pode validar no esquema é quase essencial.  Aqui estão dois de nossos favoritos.

* [Código do VS](https://code.visualstudio.com/)
* [Bloco de Notas++](https://notepad-plus-plus.org/)

Obtenha a definição `TrustFrameworkPolicy_0.3.0.0.xsd` de esquema XML da pasta raiz do starter pack. A validação de esquema XML identifica erros antes do upload.  Examine a documentação do seu editor de XML para `XML tools` e `XML Validation`

Uma rápida revisão das regras do XML pode ser muito valiosa, pois o Azure AD B2C rejeitará quaisquer erros de formatação detectados.  Ocasionalmente, o XML formatado incorretamente pode resultar em falsas mensagens de erro.

## <a name="uploading-policies-and-policy-validation"></a>Upload de políticas e validação de política

 A validação do **upload** é automática e a maioria dos erros resultará na anulação de um upload.  **Tenha em mente que a validação inclui o arquivo de política que você está tentando carregar e inclui a cadeia de arquivos a qual ele se refere.  `RP policy file > Extensions file > Base File` Erros de validação comuns incluem:

Trecho de código de erro: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* O claimType pode ter sido digitado incorretamente ou não existir no Esquema.
* Os claimTypes devem ser definidos em pelo menos um dos arquivos na política.  Por exemplo ` <ClaimType Id="socialIdpUserId">`
* Se ClaimType for definido no arquivo de extensões, mas usado em um TechnichalProfile no arquivo de base, o carregamento do arquivo de base resultará em um erro.

Trecho de código de erro: `...males a reference to a ClaimsTransformation with id...`
* Mesmo que acima.

Trecho de código de erro: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Verifique se a TenantId nos elementos **<TrustFrameworkPolicy>** e **<BasePolicy>** correspondem a seu locatário do B2C de destino.  



## <a name="runtime-troubleshooting"></a>Solução de problemas de tempo de execução

* Use `Run Now` e `https://jwt.io` para testar suas políticas independentemente de seu aplicativo móvel ou Web. Este site funciona como um aplicativo de terceira parte confiável e exibe o conteúdo do token JWT gerado pela sua política do Azure AD B2C.  Para criar um aplicativo de teste na Identity Experience Framework.
    * Nome: TestApp
    * Aplicativo Web/API Web: Não
    * Cliente nativo: Não

* Use o [fiddler](http://www.telerik.com/fiddler) para rastrear a troca de mensagens entre o navegador do cliente e o Azure AD B2C.  Você obterá uma indicação de onde nas etapas de orquestração seu percurso está falhando.

* Use **Application Insights** no **Modo de desenvolvimento** para rastrear o comportamento do seu percurso do usuário da Identity Experience Framework (IEF).  Em **Modo de desenvolvimento**, é possível observar a troca de declarações entre o IEF e os vários provedores de declarações definidos por TechnicalProfiles, como provedores de identidade, serviços baseados em API, o diretório de usuário do Azure AD B2C (diretório do AAD) e outros serviços, como Multi-Factor Authentication.  

## <a name="recommended-practies"></a>Práticas recomendadas

**Mantenha várias versões de seus cenários e agrupe-os em um projeto com o seu aplicativo.** Os arquivos base, de extensões e de terceira parte confiável (RP) são diretamente dependentes uns dos outros, salve-os como um grupo e mantenha versões separadas de trabalho conforme novos recursos forem percebidos em suas políticas.  Prepare-os em seu próprio sistema de arquivos com o código do aplicativo com os quais eles interagem.  Seus aplicativos podem invocar diferentes políticas RP em um locatário e se tornar dependentes nas declarações esperam de suas políticas do Azure AD B2C.

**Desenvolver e testar Perfis Técnicos com percursos do usuário conhecidos.**  Usar políticas de Starter Pack testadas para configurar os perfis técnicas e testá-los separadamente antes de incorporar seus próprios percursos do usuário

**Desenvolver e testar percursos do usuário com perfis técnicas testadas** Altere as etapas de orquestração de um percurso do usuário etapa por etapa, criando progressivamente seus cenários desejados







Vamos começar:

1. Baixar o "active-directory-b2c-custom-policy-starterpack" do GitHub.  [Baixe o zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou execute
### <a name="built-in"></a>Interno



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
8. Clique em **Criar**. Observe que a política recém-criada aparece como "**B2C_1_SiUp**" (o fragmento **B2C\_1\_** é adicionado automaticamente) na folha **Políticas de inscrição**.
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
5. Clique em **Provedores de identidade** e selecione "Entrada na conta local". Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.
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
* [Desabilitar a verificação de email durante a inscrição do consumidor](active-directory-b2c-reference-disable-ev.md)


