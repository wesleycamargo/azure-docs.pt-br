<properties
	pageTitle="Visualização do Active Directory B2C do Azure: estrutura extensível de política Microsoft Azure"
	description="Um tópico sobre a estrutura de política extensível do Active Directory B2C do Azure e como criar vários tipos de política"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: estrutura extensível de política

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Noções básicas

A estrutura de política extensível do AD (Active Directory) B2C do Azure é a força principal do serviço. As políticas descrevem totalmente experiências de identidade do consumidor, como inscrição, entrada ou edição de perfil. Por exemplo, uma política de inscrição permite controlar comportamentos definindo as seguintes configurações:

- Tipos de conta (contas sociais, como o Facebook e Google e/ou contas locais, como endereço de email, nome de usuário e senha) que os consumidores podem usam para se inscrever no aplicativo.
- Atributos (por exemplo, nome, CEP, tamanho do calçado, etc.) a serem coletados do consumidor durante a inscrição. 
- Uso de autenticação multifator.
- A aparência e funcionalidade de todas as páginas de inscrição.
- Informações (que se manifestam como declarações em um token) que o aplicativo recebe quando a execução da política é concluída.

Você pode criar várias políticas de tipos diferentes em seu diretório e usá-las em seus aplicativos, conforme necessário. As políticas podem ser reutilizadas entre aplicativos. Isso permite aos desenvolvedores definir e modificar experiências de identidade do consumidor com pouca ou nenhuma alteração no código. Continuaremos a adicionar tipos de política mais sofisticadas ao nosso serviço.

As políticas estão disponíveis para uso por meio de uma interface simples do desenvolvedor. O aplicativo dispara uma política usando uma solicitação de autenticação HTTP padrão (passando um parâmetro de política na solicitação) e recebe um token personalizado como resposta. Por exemplo, a única diferença entre solicitações que invocam uma política de inscrição e uma política de entrada é o nome da política usado no parâmetro de cadeia de caracteres de consulta "p":

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

## Como criar uma política de inscrição

Para habilitar a inscrição de seu aplicativo, você precisará criar uma política de inscrição. Essa política descreve as experiências pelas quais os consumidores passarão durante a inscrição e o conteúdo de tokens que o aplicativo receberá de inscrições bem-sucedidas.

1. [Navegue até a folha de recursos do B2C no portal de visualização do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **Políticas de inscrição**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. O **Nome** determina o nome da política de inscrição usado pelo seu aplicativo. Por exemplo, insira "SiUp".
5. Clique em **Provedores de identidade** e selecione "Endereço de email". Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.
6. Clique em **Atributos de inscrição**. Aqui você escolhe os atributos que você deseja coletar do consumidor durante a inscrição. Por exemplo, selecione "Cidade", "Nome de exibição" e "CEP". Clique em **OK**.
7. Clique em **Declarações do aplicativo**. Aqui você pode escolher as declarações que quer retornar dos tokens de volta ao aplicativo após uma experiência de inscrição bem-sucedida. Por exemplo, selecione "Nome de exibição", "Provedor de identidade", "CEP", "Novo usuário" e "ID de objeto do usuário".
8. Clique em **Criar**. Observe que a política recém-criada aparece como "**B2C\_1\_SiUp**" (o fragmento **B2C\_1\_** torna-se automaticamente pré-pendente) na folha **Políticas de inscrição**.
9. Abra a política clicando em "**B2C\_1\_SiUp**".
10. Selecione "aplicativo Contoso B2C” na lista suspensa **Aplicativos** e `https://localhost:44321/` na lista suspensa **URL de Resposta/URI de redirecionamento**. Clique no botão **Executar agora**. Uma nova guia do navegador se abre e você pode executar a experiência de consumidor de inscrição para o aplicativo.

    > [AZURE.NOTE]É necessário um minuto para que a criação de políticas e as atualizações entrem em vigor.

## Como criar uma política de entrada

Para habilitar a entrada no aplicativo, você precisará criar uma política de entrada. Essa política descreve as experiências pelas quais os consumidores passarão durante a entrada e o conteúdo de tokens que o aplicativo receberá de entradas bem-sucedidas.

1. [Navegue até a folha de recursos do B2C no portal de visualização do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **Políticas de entrada**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. O **Nome** determina o nome da política de entrada usado pelo seu aplicativo. Por exemplo, insira "SiIn".
5. Clique em **Provedores de identidade** e selecione "Endereço de email". Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.
6. Clique em **Declarações do aplicativo**. Aqui você pode escolher as declarações que quer retornar dos tokens de volta ao aplicativo após uma experiência de entrada bem-sucedida. Por exemplo, selecione "Nome de exibição", "Provedor de identidade", "CEP" e "ID de objeto do usuário". Clique em **OK**.
7. Clique em **Criar**. Observe que a política recém-criada aparece como "**B2C\_1\_SiIn**" (o fragmento **B2C\_1\_** torna-se automaticamente pré-pendente) na folha **Políticas de entrada**.
8. Abra a política clicando em "**B2C\_1\_SiIn**".
9. Selecione "aplicativo Contoso B2C” na lista suspensa **Aplicativos** e `https://localhost:44321/` na lista suspensa **URL de Resposta/URI de redirecionamento**. Clique no botão **Executar agora**. Uma nova guia do navegador se abre e você pode executar a experiência de consumidor de entrada no aplicativo.

    > [AZURE.NOTE]É necessário um minuto para que a criação de políticas e as atualizações entrem em vigor.

## Como criar uma política de edição de perfil

Para habilitar a edição de perfil no aplicativo, você precisará criar uma política de edição de perfil. Essa política descreve as experiências pelas quais os consumidores passarão durante a edição do perfil e o conteúdo de tokens que o aplicativo receberá na conclusão bem-sucedida.

1. [Navegue até a folha de recursos do B2C no portal de visualização do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **Políticas de edição de perfil**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. O **Nome** determina o nome da política de edição de perfil usado pelo seu aplicativo. Por exemplo, insira "SiPe".
5. Clique em **Provedores de identidade** e selecione "Endereço de email". Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.
6. Clique em **Atributos do perfil**. Aqui você escolhe os atributos que o consumidor pode exibir e editar. Por exemplo, selecione "Cidade", "Nome de exibição" e "CEP". Clique em **OK**.
7. Clique em **Declarações do aplicativo**. Aqui você pode escolher as declarações que quer retornar dos tokens de volta ao aplicativo após uma experiência de edição de perfil bem-sucedida. Por exemplo, selecione "Nome de exibição" e "CEP".
8. Clique em **Criar**. Observe que a política recém-criada aparece como "**B2C\_1\_SiPe**" (o fragmento **B2C\_1\_** torna-se automaticamente pré-pendente) na folha **Políticas de edição de perfil**.
9. Abra a política clicando em "**B2C\_1\_SiPe**".
10. Selecione "aplicativo Contoso B2C” na lista suspensa **Aplicativos** e `https://localhost:44321/` na lista suspensa **URL de Resposta/URI de redirecionamento**. Clique no botão **Executar agora**. Uma nova guia do navegador se abre e você pode executar a experiência de consumidor de edição de perfil em seu aplicativo.

    > [AZURE.NOTE]É necessário um minuto para que a criação de políticas e as atualizações entrem em vigor.

<!---HONumber=Sept15_HO3-->