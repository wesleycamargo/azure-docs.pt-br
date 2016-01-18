<properties
	pageTitle="Visualização do Active Directory B2C do Azure: ferramenta auxiliar de personalização de interface do usuário da página | Microsoft Azure"
	description="Uma ferramenta auxiliar usada para demonstrar o recurso de personalização da interface do usuário da página no Active Directory B2C do Azure"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: uma Ferramenta Auxiliar usada para demonstrar o recurso de Personalização da Interface do Usuário (IU) da Página

Este artigo é um complemento do [artigo principal sobre personalização da interface do usuário](active-directory-b2c-reference-ui-customization.md) no Active Directory B2C do Azure. As etapas abaixo descrevem como usufruir do recurso de personalização da interface do usuário da página usando conteúdo de exemplo HTML e CSS que fornecemos.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Obter um locatário do Azure AD B2C

Para personalizar algo, você precisará [obter um locatário do AD B2C do Azure](active-directory-b2c-get-started.md), caso ainda não tenha um.

## Criar uma política de inscrição

O conteúdo de exemplo que fornecemos personaliza duas páginas em uma [política de inscrição](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy): a [Página de Seleção de IDP](active-directory-b2c-reference-ui-customization.md#identity-provider-selection-page) e a [Página de Inscrição da Conta Local](active-directory-b2c-reference-ui-customization.md#local-account-sign-up-page). Ao [criar a política de inscrição](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy), adicione uma Conta Local (Endereço de email), Facebook e Google+ como **provedores de identidade**. Esses são os únicos IDPs que nosso conteúdo HTML de exemplo aceitará.

## Registrar um aplicativo

Você precisará [registrar um aplicativo](active-directory-b2c-app-registration.md) no locatário B2C que possa ser usado para executar a política. Depois de registrar o aplicativo, você terá algumas opções que poderão ser usadas para executar de fato a política de inscrição:

- Compile um dos aplicativos de Início Rápido do Azure AD B2C listados [aqui](active-directory-b2c-overview.md#getting-started).
- Use o aplicativo predefinido [Playground do AD B2C do Azure](https://aadb2cplayground.azurewebsites.net). Caso opte por usar o playground, você deverá registrar um aplicativo no locatário B2C usando o **URI de redirecionamento** `https://aadb2cplayground.azurewebsites.net/`
- Na política, use o botão **Executar Agora** no [Portal do Azure](https://portal.azure.com).

## Personalizar a política

Para personalizar a aparência das políticas, primeiramente, você precisa criar arquivos HTML e CSS usando as convenções específicas do AD B2C do Azure. Em seguida, carregue o conteúdo estático em um local publicamente disponível para que o Azure AD B2C possa acessá-lo. Esse local pode ser seu próprio servidor Web dedicado, o Armazenamento de Blob do Azure, a CDN do Azure ou qualquer outro recurso estático que esteja hospedando o provedor. Os únicos requisitos são que o conteúdo esteja disponível por HTTPS e possa ser acessado usando CORS. Depois de expor o conteúdo estático na Web, é possível editar a política para apontar para esse local e apresentar esse conteúdo aos usuários finais. O [artigo principal sobre personalização da interface do usuário](active-directory-b2c-reference-ui-customization.md) descreve detalhadamente como funciona o recurso de personalização do Azure AD B2C.

Para os fins deste tutorial, já criamos o conteúdo de exemplo e o hospedamos no Armazenamento de Blob do Azure. O conteúdo de exemplo é uma personalização bastante básica no tema de nossa empresa fictícia, "Contoso B2C". Para testá-lo em sua própria política, siga estas etapas:

1. Entre em seu locatário no [Portal do Azure](https://portal.azure.com) e navegue até a folha de recursos do B2C.
2. Clique em **Políticas de inscrição** e clique em sua política de inscrição (por exemplo, "b2c\_1\_sign\_up").
3. Clique em **Personalização da interface do usuário da página** e em **Página de seleção do provedor de identidade**.
4. Alterne a opção **Usar modelo personalizado** para **Sim**. No campo **URI da página personalizada**, digite `https://contosob2c.blob.core.windows.net/static/Index.html`. Clique em **OK**.
5. Clique em **Página de inscrição da conta local**. Alterne a opção **Usar modelo personalizado** para **Sim**. No campo **URI da página personalizada**, digite `https://contosob2c.blob.core.windows.net/static/EmailVerification.html`. Clique em **OK** duas vezes para fechar as folhas de personalização da interface do usuário.
6. Clique em **Salvar**.

Agora, você pode testar a política personalizada. Você pode usar seu próprio aplicativo ou o playground do AAD B2C se desejar, mas também pode simplesmente clicar no comando **Executar Agora** na folha da política. Selecione o aplicativo no menu suspenso e o URI de redirecionamento apropriado. Clique no botão **Executar agora**. Uma nova guia do navegador é aberta e você pode passar pela experiência de usuário de se inscrever no aplicativo com o novo conteúdo definido!

## Carregar o conteúdo de exemplo no Armazenamento de Blob do Azure

Se quiser usar o Armazenamento de Blob do Azure para hospedar o conteúdo da página, você poderá criar sua própria conta de armazenamento e usar nossa ferramenta auxiliar do B2C para carregar os arquivos.

#### Criar uma conta de armazenamento

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **+ Novo** -> **Dados + Armazenamento** -> **Conta de armazenamento**. Você precisará de uma assinatura do Azure para criar uma conta de Armazenamento de Blob do Azure. Você pode se inscrever em uma versão de avaliação gratuita [aqui](https://azure.microsoft.com/pricing/free-trial/).
3. Forneça um **Nome** para a conta de armazenamento (por exemplo, "contoso") e escolha as seleções apropriadas para **Tipo de preço**, **Grupo de Recursos** e **Assinatura**. Verifique se a opção **Fixar no Quadro Inicial** está marcada. Clique em **Criar**.
4. Volte para o quadro inicial e clique na conta de armazenamento que você acabou de criar.
5. Na seção **Resumo**, clique em **Contêineres** e em **+ Adicionar**.
6. Forneça um **Nome** para o contêiner (por exemplo, "b2c") e selecione **Blob** como o **Tipo de acesso**. Clique em **OK**.
7. O contêiner que você criou aparecerá na lista da folha **Blobs**. Anote a URL do contêiner; por exemplo, ela deve ter a seguinte aparência: `https://contoso.blob.core.windows.net/b2c`. Feche a folha **Blobs**.
8. Na folha do armazenamento de conta, clique em **Chaves** e anote os valores dos campos **Nome da Conta de Armazenamento** e **Chave de Acesso Primária**.

> [AZURE.NOTE]A **Tecla de Acesso Primária** é uma credencial de segurança importante.

#### Baixar a ferramenta auxiliar e os arquivos de exemplo

Você pode baixar a [ferramenta auxiliar Armazenamento de Blob do Azure e os arquivos de exemplo como um .zip aqui](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) ou cloná-los do GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Esse repositório contém um diretório `sample_templates\contoso`, que inclui o exemplo HTML, CSS e as imagens. Para que esses modelos façam referência à sua própria conta de Armazenamento de Blob do Azure, você precisará editar os arquivos HTML. Abra `Index.htnml` e `EmailValidation.html` e substitua todas as instâncias de `https://localhost` pela URL do seu próprio contêiner que você copiou nas etapas acima. É necessário usar o caminho absoluto dos arquivos HTML, pois nesse caso, o HTML será atendido pelo Azure AD, sob o domínio `https://login.microsoftonline.com`.

#### Carregar os arquivos de exemplo

No mesmo repositório, descompacte `B2CAzureStorageClient.zip` e execute o arquivo `B2CAzureStorageClient.exe`. Esse programa simplesmente carregará todos os arquivos no diretório que você especifica para sua conta de armazenamento e habilita o acesso CORS para esses arquivos. Se você seguiu as etapas acima, os arquivos HTML e CSS agora estarão apontando para a conta de armazenamento. Observe que o nome da sua conta de armazenamento é a parte que precede `blob.core.windows.net`, por exemplo, `contoso`. Você pode verificar se o conteúdo foi carregado corretamente, tentando acessar `https://{storage-account-name}.blob.core.windows.net/{container-name}/Index.html` em um navegador. Use também [http://test-cors.org/](http://test-cors.org/) para certificar-se de que o conteúdo agora está habilitado para CORS (procure o status XHR: 200 no resultado).

#### Personalizar a política, novamente

Agora que você carregou o conteúdo de exemplo em sua própria conta de armazenamento, é preciso editar a política de inscrição para fazer referência a ela. Repita as etapas da seção acima ["Personalizar a política"](#customize-your-policy), desta vez usando as URLs da sua própria conta de armazenamento. Por exemplo, o local do arquivo `Index.html` será `<url-of-your-container>/Index.html`.
        
Agora, você pode usar o botão **Executar Agora** ou seu próprio aplicativo para executar a política novamente. O resultado deve ser quase o mesmo — você usou o mesmo HTML e CSS de exemplo em ambos os casos. No entanto, as políticas agora estão fazendo referência à sua própria instância do Armazenamento de Blob do Azure, e você é livre para editar e recarregar os arquivos como quiser. Para saber mais sobre como personalizar o HTML e CSS, consulte o [artigo principal sobre personalização da interface do usuário](active-directory-b2c-reference-ui-customization.md).

<!---HONumber=AcomDC_0107_2016-->