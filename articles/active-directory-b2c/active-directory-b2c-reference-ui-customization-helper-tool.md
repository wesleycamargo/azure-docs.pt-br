<properties
	pageTitle="Visualização do Active Directory B2C do Azure: ferramenta auxiliar de personalização de interface do usuário da página | Microsoft Azure"
	description="Uma ferramenta auxiliar usada para demonstrar o recurso de personalização da interface do usuário da página no Active Directory B2C do Azure"
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

# Visualização do Active Directory B2C do Azure: uma Ferramenta Auxiliar usada para demonstrar o recurso de Personalização da Interface do Usuário (IU) da Página

Este artigo é um complemento para o [artigo principal](active-directory-b2c-reference-ui-customization) sobre o recurso de personalização de interface do usuário (IU) da página no Active Directory (AD) B2C do Azure.

Seguir as etapas abaixo permitirá que você pratique o recurso de personalização da interface do usuário da página usando nosso conteúdo de exemplo. Apenas para fins de demonstração, nosso conteúdo de exemplo é HTML5 **estático**.

1. Carregar nosso conteúdo de exemplo para [Armazenamento de Blob do Azure](http://azure.microsoft.com/documentation/services/storage/), torná-lo publicamente acessível via HTTPS e ativar o CORS (Compartilhamento de Recursos Entre Origens) nessas URLs.
2. Modifique as configurações de personalização da interface do usuário da página em sua política de inscrição existente e especifique as URLs acima.

Antes de começar:

- Compile um dos Aplicativos de Início Rápido do AD B2C do Azure listados [aqui](active-directory-b2c-overview.md). Como parte desse procedimento, você criará uma política de inscrição que você modificará aqui.
- Baixe a ferramenta auxiliar [aqui](https://github.com/AzureADSamples/B2C-AzureBlobStorage-Client).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### Etapa 1: carregue nosso conteúdo de exemplo nos locais certos

1. Entre no [Portal de visualização do Azure](https://portal.azure.com/).
2. Clique em **+ Novo** -> **Dados + Armazenamento** -> **Conta de armazenamento**. Você precisará de uma assinatura do Azure para criar uma conta de Armazenamento de Blob do Azure. Você pode se inscrever em uma avaliação gratuita [aqui](https://azure.microsoft.com/pt-BR/pricing/free-trial/).
3. Forneça um **Nome** para a conta de armazenamento (por exemplo, "contoso.core.windows.net") e escolha as seleções apropriadas para **Tipo de preço**, **Grupo de Recursos** e **Assinatura**. Certifique-se de ter a opção **Fixar no Quadro Inicial** marcada. Clique em **Criar**.
4. Volte para o quadro inicial e clique na conta de armazenamento que você acabou de criar.
5. Na seção **Resumo**, clique em **Contêineres** e em **+ Adicionar**.
6. Forneça um **Nome** para o contêiner (por exemplo, "b2c") e selecione **Blob** como o **Tipo de acesso**. Clique em **OK**.
7. O contêiner que você criou aparecerá na lista na folha **Blobs**. Anote a URL do contêiner; por exemplo, ela deve ter a seguinte aparência: `https://contoso.blob.core.windows.net/b2c`. Feche a folha **Blobs**.
8. Na folha do armazenamento de conta, clique em **Chaves** e anote os valores dos campos **Nome da Conta de Armazenamento** e **Chave de Acesso Primária**.

    > [AZURE.NOTE]A **Chave de Acesso Primária** é uma credencial de segurança importante.

9. Execute a ferramenta auxiliar e forneça-a com os valores de **Nome da Conta de Armazenamento** e **Chave de Acesso Primária** copiados na etapa anterior. Isso carregará o nosso conteúdo de exemplo em sua conta de armazenamento.
10. Verifique se o conteúdo foi carregado corretamente, tentando acessar `https://contoso.blob.core.windows.net/b2c/idp.html` em um navegador. Use também [http://test-cors.org/](http://test-cors.org/) para certificar-se de que o conteúdo agora está habilitado para CORS (procure `XHR status: 200` no resultado).

### Etapa 2: modificar as opções de personalização da interface do usuário da página em sua política de inscrição

1. Entre em seu diretório no [portal de visualização do Azure](https://portal.azure.com) e navegue até a folha de recursos B2C.
2. Clique em **Políticas de inscrição** e clique em sua política de inscrição (por exemplo, "B2C\_1\_SiIn").
3. Clique em **Personalização da interface do usuário da página** e **Página de seleção de provedor de identidade**.
4. Alterne o comutador **Usar modelo personalizado** para **Sim**. No campo **URI de página personalizada**, insira a URL apropriada do conteúdo carregado em sua conta de armazenamento. Por exemplo: `https://contoso.blob.core.windows.net/b2c/idp.html`. Clique em **OK**.
5. Clique em **Página de inscrição de conta local**. Alterne o comutador **Usar modelo personalizado** para **Sim**. No campo **URI de página personalizada**, insira a URL apropriada do conteúdo carregado em sua conta de armazenamento. Por exemplo: `https://contoso.blob.core.windows.net/b2c/su.html`. Clique em **OK** duas vezes.
6. Clique em **Salvar**.
7. Clique no comando **Executar agora** na parte superior da folha. Selecione "aplicativo B2C” na lista suspensa **Aplicativos** e `https://localhost:44321/` na lista suspensa **URL de Resposta/URI de redirecionamento**. Clique no botão **Executar agora**.
8. Uma nova guia do navegador se abre e você pode executar a experiência de inscrever-se para o aplicativo com o novo conteúdo já pronto!

> [AZURE.NOTE]Observe que leva até um minuto para que suas alterações de política entrem em vigor.

<!---HONumber=Sept15_HO3-->