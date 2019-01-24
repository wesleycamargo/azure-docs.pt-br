---
title: Tutorial – Personalizar a interface do usuário dos aplicativos no Azure Active Directory B2C | Microsoft Docs
description: Saiba como personalizar a interface do usuário de seus aplicativos no Azure Active Directory B2C usando o portal do Azure.
services: B2C
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5d97a313c347aefbdda14b70e78aa09188da59ef
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855781"
---
# <a name="tutorial-customize-the-user-interface-of-your-applications-in-azure-active-directory-b2c"></a>Tutorial: Personalizar a interface do usuário dos aplicativos no Azure Active Directory B2C

Para experiências do usuário mais comuns, como inscrição, entrada e edição de perfil, você pode usar os [fluxos dos usuários](active-directory-b2c-reference-policies.md) no Azure AD (Azure Active Directory) B2C. As informações neste tutorial ajudam você a aprender a [personalizar a interface do usuário](customize-ui-overview.md) dessas experiências usando seus próprios arquivos HTML e CSS.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar arquivos de personalização da interface do usuário
> * Criar um fluxo de usuário de inscrição e de entrada que use os arquivos
> * Testar a interface do usuário personalizada

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [locatário do Azure AD B2C](tutorial-create-tenant.md), crie um agora. Se você tiver criado um em um tutorial anterior, poderá usar um locatário existente.

## <a name="create-customization-files"></a>Criar arquivos de personalização

Você cria um contêiner e uma conta de Armazenamento do Azure e coloca os arquivos HTML e CSS básicos no contêiner.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Embora você possa armazenar seus arquivos de várias maneiras, para este tutorial, vai armazená-los no [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md).

1. Verifique se você está usando o diretório que contém sua assinatura do Azure. Selecione o **Filtro de diretório e assinatura** no menu superior e escolha o diretório que contém sua assinatura. Esse diretório é diferente daquele que contém o seu locatário B2C do Azure.

    ![Alternar para o diretório de assinatura](./media/tutorial-customize-ui/switch-directories.png)

2. Escolha Todos os serviços no canto superior esquerdo do portal do Azure, então pesquise e selecione **Contas de armazenamento**. 
3. Selecione **Adicionar**.
4. Em **Grupo de recursos**, selecione **Criar novo**, insira um nome para o novo grupo de recursos e, em seguida, clique em **OK**.
5. Insira um nome para a conta de armazenamento. O nome escolhido deverá ser único no Azure, deverá ter entre 3 e 24 caracteres e poderá conter somente números e letras minúsculas.
6. Selecione a localização da conta de armazenamento ou aceite a localização padrão. 
7. Aceite todos os outros valores padrão, selecione **Examinar + Criar** e, em seguida, clique em **Criar**.
8. Depois que a conta de armazenamento tiver sido criada, selecione **Ir para recurso**.

### <a name="create-a-container"></a>Criar um contêiner

1. Na página de visão geral da conta de armazenamento, selecione **Blobs**.
2. Selecione **Contêiner**, insira um nome para o contêiner, escolha **Blob (acesso de leitura anônimo somente para blobs)** e, em seguida, clique em **OK**.

### <a name="enable-cors"></a>Habilitar CORS

 O código do Azure AD B2C em um navegador usa uma abordagem moderna e padrão para carregar conteúdo personalizado de uma URL que você especifica em um fluxo de usuário. CORS (compartilhamento de recurso de origem cruzada) permite que recursos restritos em uma página da Web sejam solicitados em outros domínios.

1. No menu, selecione **CORS**.
2. Para **origens permitidas**, insira `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Você precisa usar todas as letras minúsculas ao digitar o nome do seu locatário.
3. Para **métodos permitidos**, selecione ambos `GET` e `OPTIONS`.
4. Para **cabeçalhos permitidos**, digite um asterisco (*).
5. Para **cabeçalhos expostos**, digite um asterisco (*).
6. Para **Idade máxima de**, insira 200.

    ![Habilitar CORS](./media/tutorial-customize-ui/enable-cors.png)

5. Clique em **Salvar**.

### <a name="create-the-customization-files"></a>Criar os arquivos de personalização

Para personalizar a interface do usuário da experiência de inscrição, você começa criando um arquivo HTML e CSS simples. Você pode configurar seu HTML como quiser, mas deve ter um elemento **div** com um identificador de `api`. Por exemplo, `<div id="api"></div>`. O Azure AD B2C injeta elementos no contêiner `api` quando a página é exibida.

1. Em uma pasta local, crie o seguinte arquivo e altere `your-storage-account` para o nome da conta de armazenamento e `your-container` para o nome do contêiner que você criou. Por exemplo, `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    A página pode ser projetada de qualquer maneira que desejar, mas o elemento div **api** é necessário para qualquer arquivo de personalização de HTML que você criar. 

3. Salve o arquivo como *custom-ui.html*.
4. Crie o seguinte CSS simples que centraliza os todos os elementos na página de inscrição ou entrada, incluindo os elementos que o Azure AD B2C injeta.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Salve o arquivo como *Style.css*.

### <a name="upload-the-customization-files"></a>Fazer upload dos arquivos de personalização

Neste tutorial, você armazena os arquivos criados na conta de armazenamento para que o Azure AD B2C possa acessá-los.

1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, então pesquise e selecione **Contas de armazenamento**.
2. Selecione a conta de armazenamento que você criou, selecione **Blobs** e, em seguida, selecione o contêiner que você criou.
3. Selecione **Fazer Upload**, navegue até o arquivo *custom-ui.html* e selecione-o, então clique em **Fazer Upload**.

    ![Fazer upload dos arquivos de personalização](./media/tutorial-customize-ui/upload-blob.png)

4. Copie a URL para o arquivo que você carregou para usar posteriormente no tutorial.
5. Repita as etapas 3 e 4 para o arquivo *style.css*.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de usuário de inscrição e de entrada

Para concluir as etapas deste tutorial, será necessário criar um aplicativo de teste e um fluxo de usuário de inscrição ou de entrada no Azure AD B2C. Você pode aplicar os princípios descritos neste tutorial para as outras experiências de usuário, como edição de perfil.

### <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com o Azure AD B2C ocorre por meio de um aplicativo que você cria no seu locatário. As seguintes etapas criam um aplicativo que redireciona o token de autorização retornado ao [https://jwt.ms](https://jwt.ms).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo, por exemplo *testapp1*.
6. Para **Aplicativo Web/API Web**, selecione `Yes` e, em seguida, insira `https://jwt.ms` para a **URL de resposta**.
7. Clique em **Criar**.

### <a name="create-the-user-flow"></a>Criar o fluxo de usuário

Para testar os arquivos de personalização, você cria um fluxo de usuário de inscrição ou de entrada interno que usa o aplicativo criado anteriormente.

1. No locatário do Azure AD B2C, selecione **Fluxos dos Usuários** e, em seguida, clique em **Novo fluxo de usuário**.
2. Na guia **Recomendado**, clique em **Inscrever-se e entrar**.
3. Insira um nome para o fluxo de usuário. Por exemplo, *signup_signin*. O prefixo *B2C_1* é adicionado automaticamente ao nome quando o fluxo de usuário é criado.
4. Em **Provedores de identidade**, selecione **Inscrição por email**.
5. Em **Atributos de usuário e declarações**, clique em **Mostrar mais**.
6. Na coluna **Atributo Coletar**, escolha os atributos que você quer coletar do cliente durante a inscrição. Por exemplo, selecione **País/Região**, **Nome de Exibição** e **CEP**.
7. Na coluna **Declaração de retorno**, escolha as declarações que você quer que sejam retornadas nos tokens de autorização enviados de volta ao aplicativo, após uma experiência de inscrição ou de entrada obtida com êxito. Por exemplo, selecione **Nome de Exibição**, **Provedor de Identidade**, **CEP**, **Novo Usuário** e **ID de Objeto do Usuário**.
8. Clique em **OK**.
9. Clique em **Criar**.
10. Em **Personalizar**, selecione **Layouts da página**. Selecione **Página de inscrição ou entrada unificada** e, em seguida, clique em **Sim** para **Usar o conteúdo da página personalizada**.
11. Em **URI da página personalizada**, insira a URL do arquivo *custom-ui.html* que você gravou anteriormente.
12. Na parte superior da página, clique em **Salvar**.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. No locatário do Azure AD B2C, selecione **Fluxos dos Usuários** e selecione o fluxo de usuário que você criou. Por exemplo, *B2C_1_signup_signin*.
2. Na parte superior da página, clique em **Executar fluxo de usuário**.
3. Clique no botão **Executar fluxo de usuário**.

    ![Executar o fluxo de usuário de inscrição ou entrada](./media/tutorial-customize-ui/run-user-flow.png)

    Você deverá ver uma página semelhante ao exemplo a seguir com os elementos centralizados com base no arquivo CSS que você criou:

    ![Resultados do fluxo de usuário](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar arquivos de personalização da interface do usuário
> * Criar um fluxo de usuário de inscrição e de entrada que use os arquivos
> * Testar a interface do usuário personalizada

> [!div class="nextstepaction"]
> [Personalização de idioma no Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)