---
title: "Azure Active Directory B2C: Personalização da interface do usuário com políticas personalizadas | Microsoft Docs"
description: "Um tópico sobre personalização da interface do usuário com o uso de políticas personalizadas no Azure AD B2C"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: gsacavdm
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 125f05f5dce5a0e4127348de5b280f06c3491d84
ms.openlocfilehash: 9de6a57671cf65c4aa5d8695d21e0932175b1183
ms.contentlocale: pt-br
ms.lasthandoff: 05/22/2017

---
# <a name="azure-active-directory-b2c-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: Personalização da interface do usuário em uma política personalizada

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois de concluir este artigo, você terá uma política personalizada de inscrição e conexão com sua marca e aparência.  O Azure AD B2C permite um controle quase total do HTML e do CSS apresentados ao usuário final.  Ao usar uma política personalizada, a personalização da interface do usuário é configurada em XML em vez de usar controles no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, conclua as etapas em [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).  Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.

## <a name="the-page-ui-customization-feature"></a>O recurso de personalização da interface do usuário da página

Com o recurso de personalização da interface do usuário da página, você pode personalizar a aparência de qualquer política personalizada.  Mantenha a consistência visual e da marca entre seu aplicativo e o Azure AD B2C.

É assim que ela funciona: o Azure AD B2C executa o código no navegador do consumidor e usa uma abordagem moderna chamada [CORS (Compartilhamento de Recursos entre Origens)](http://www.w3.org/TR/cors/).  Primeiro, especifique uma URL na política personalizada com um conteúdo personalizado em HTML.  O Azure AD B2C mescla os elementos de interface do usuário com o conteúdo em HTML carregado da URL e exibe a página para o consumidor.

## <a name="creating-your-html5-content"></a>Criando o conteúdo em HTML5

Vamos criar o conteúdo em HTML com o nome da marca de seu produto no título.

1. Clique em **Copiar** neste trecho de código HTML.  Ele é um HTML5 bem formado com um elemento vazio chamado `<div id="api"></div>` localizado em algum lugar do `<body>`. Esse elemento marca o local em que o conteúdo do Azure AD B2C é inserido.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Cole em um editor de texto e salve o arquivo como `customize-ui.html`

## <a name="create-a-blob-storage-account"></a>Criar uma conta de armazenamento de blobs

>[!NOTE]
> Neste guia, usamos o Armazenamento de Blobs do Azure para hospedar nosso conteúdo.  Você também pode hospedar o conteúdo em um servidor Web, mas é necessário [habilitar o CORS (Compartilhamento de Recursos entre Origens) no servidor Web](https://enable-cors.org/server.html).

Vamos hospedar esse HTML no armazenamento de blobs do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. No menu Hub, selecione **Novo** -> **Armazenamento** -> **Conta de armazenamento**.
1. Insira um **nome** exclusivo para sua conta de armazenamento.
1. **Modelo de implantação** pode ser deixado como **Resource Manager**.
1. Altere **Tipo de Conta** para **Armazenamento de blobs**.
1. **Desempenho** pode ser deixado como **Padrão**.
1. **Replicação** pode ser deixada como **RA-GRS**.
1. **Camada de acesso** pode ser deixada como **Quente**.
1. **Criptografia do serviço de armazenamento** pode ser deixada como **Desabilitada**.
1. Selecione uma **assinatura** para a conta de armazenamento.
1. Crie um **grupo de recursos** ou selecione um existente.
1. Selecione a **localização geográfica** de sua conta de armazenamento.
1. Clique em **Criar** para criar a conta de armazenamento.
1. Aguarde até que a implantação seja concluída e a folha da conta de armazenamento será aberta automaticamente.

## <a name="create-a-container"></a>Criar um contêiner

Vamos criar um contêiner público no armazenamento de blobs do Azure.

1. Alterne para a guia esquerda chamada **Visão Geral**.
1. Clique em **+ Contêiner**.
1. Em **Nome**, digite `$root`
1. Defina **Tipo de acesso** como **Blob**.
1. Clique em “$root” para abrir o novo contêiner.
1. Clique em **Carregar**.
1. Clique no ícone de pasta ao lado de `Select a file`.
1. Navegue para `customize-ui.html` que criamos na [seção anterior](#the-page-ui-customization-feature).
1. Clique em **Carregar**.
1. Selecione o blob que carregamos chamado `customize-ui.html`.
1. Ao lado da **URL**, clique no botão Copiar.
1. Abra um navegador e navegue para esta URL.  Se ela não estiver acessível, verifique se o tipo de acesso do contêiner está definido como blob.

## <a name="configure-cors"></a>Configurar o CORS

Em seguida, configuramos o CORS (Compartilhamento de Recursos entre Origens) no armazenamento de blobs do Azure.

>[!NOTE]
>Quer experimentar o recurso de personalização da interface do usuário usando nosso conteúdo de HTML e CSS de exemplo?  Fornecemos [uma ferramenta auxiliar simples](active-directory-b2c-reference-ui-customization-helper-tool.md) que carrega e configura nosso conteúdo de exemplo em sua conta de armazenamento de blobs do Azure.  Se você usar a ferramenta, pule para [Modificar a política personalizada de inscrição ou conexão](#modify-your-sign-up-or-sign-in-custom-policy)

1. Na folha de armazenamento em Configurações, abra **CORS**.
1. Clique em **+ Adicionar**.
1. Defina `Allowed origins` como `*`.
1. Na lista suspensa chamada `Allowed verbs`, selecione `GET` e `OPTIONS`.
1. Defina `Allowed headers` como `*`.
1. Defina `Exposed headers` como `*`.
1. Defina `Maximum age (seconds)` como `200`.
1. Clique em **Adicionar**.

## <a name="testing-cors"></a>Testando o CORS

Vamos validar se você está pronto.

1. Navegue para http://test-cors.org/ e cole a URL no campo `Remote URL`.
1. Clique em **Enviar Solicitação**
1. Se você receber um erro, verifique se as [configurações do CORS](#configure-cors) estão corretas.  Você também pode precisar limpar o cache do navegador ou tentar usar uma sessão de navegação particular `CTRL-SHIFT-P`.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Modificar a política personalizada de inscrição ou conexão

1. Na marcação `<TrustFrameworkPolicy>` de nível superior, você deve encontrar a marcação `<BuildingBlocks>`.  Dentro da marcação `<BuildingBlocks>`, adicione uma marcação `ContentDefinitions` copiando este exemplo.  Substitua `{your_storage_account}` com o nome da sua conta de armazenamento.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Carregar a política personalizada atualizada

1. No [portal do Azure](https://portal.azure.com), [alterne para o contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra a folha do Azure AD B2C.
1. Clique em **Todas as Políticas**.
1. Clique em **Carregar Política**
1. Carregue `SignUpOrSignin.xml` com a marcação `ContentDefinitions` que adicionamos.

## <a name="test-the-custom-policy-using-run-now"></a>Testar a política personalizada usando a opção “Executar Agora”

1. Abra a **Folha do Azure AD B2C** e navegue para **Todas as políticas**.
1. Selecione a política personalizada carregada e clique no botão **Executar agora**.
1. Você deverá conseguir se inscrever usando um endereço de email.

## <a name="next-steps"></a>Próximas etapas

Este [guia de referência para personalização da interface do usuário para políticas internas](active-directory-b2c-reference-ui-customization.md) contém informações adicionais sobre quais elementos de interface do usuário podem ser personalizados.  Não há nenhuma diferença na personalização de interface do usuário entre políticas internas e personalizadas.

