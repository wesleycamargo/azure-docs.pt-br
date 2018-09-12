---
title: 'Pesquisa Personalizada do Bing: Criar uma página da Web de pesquisa personalizada | Microsoft Docs'
description: Descreve como configurar uma instância de pesquisa personalizada e integrá-la a uma página da Web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: 1f9b689ac6127bc2f7d1e810356ae9a23b8e0996
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162386"
---
# <a name="build-a-custom-search-web-page"></a>Compilar uma página da Web de pesquisa personalizada
A Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada para os tópicos importantes para você. Por exemplo, se você possuir um site de artes marciais que fornece uma experiência de pesquisa, será possível especificar os domínios, sites e as páginas da Web que o Bing pesquisará. Os usuários veem os resultados da pesquisa personalizados ao conteúdo importante para eles, em vez de terem que acessar os resultados gerais da pesquisa que podem conter conteúdo irrelevante. 

Este tutorial demonstra como configurar uma instância de pesquisa personalizada e integrá-la a uma nova página da web.

As tarefas abordadas são:

> [!div class="checklist"]
> - Criar uma instância de pesquisa personalizada
> - Adicionar entradas ativas
> - Adicionar entradas bloqueadas
> - Adicionar entradas fixadas
> - Integrar pesquisa personalizada em uma página da Web

## <a name="prerequisites"></a>Pré-requisitos
- Para acompanhar o tutorial, você precisa de uma chave de assinatura para a API de Pesquisa Personalizada do Bing.  Para obter uma chave, confira [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/).

## <a name="create-a-custom-search-instance"></a>Criar uma instância de pesquisa personalizada
Para criar uma instância de Pesquisa Personalizada do Bing:

1.  Abra um navegador da Internet.
2.  Navegue até o [portal](https://customsearch.ai) da pesquisa personalizada.
3.  Entre no portal usando uma conta Microsoft (MSA). Se você não tiver uma MSA, clique em **Criar uma conta Microsoft**. Se for a primeira vez que estiver acessando o portal, serão solicitadas permissões para acessar os dados. Clique em **Sim**.
4.  Após entrar, clique em **Nova pesquisa personalizada**. Na janela **Criar uma nova instância de pesquisa personalizada**, insira um nome que seja significativo e descreva o tipo de conteúdo que a pesquisa retornará. É possível alterar o nome a qualquer momento.
 
    ![Captura de tela da caixa Criar uma nova instância de pesquisa personalizada](../media/newCustomSrch.png)

5.  Clique em OK, especifique uma URL e se quer incluir subpáginas da página de base:

    ![Captura de tela da página de definição de URL](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Adicionar entradas ativas
Para incluir resultados de sites ou URLs específicos, adicione-os à guia **Ativo**.

1.  No **Editor de Definição**, clique na guia **Ativo** e insira a URL de um ou mais sites que você quer incluir na pesquisa.

    ![Captura de tela da guia ativa do Editor de Definição](../media/customSrchEditor.png)

2.  Para confirmar que a sua instância retorna resultados, digite uma consulta no painel de visualização à direita. O Bing só retorna resultados para sites públicos que ele indexou.

## <a name="add-blocked-entries"></a>Adicionar entradas bloqueadas
Para excluir resultados de sites ou URLs específicos, adicione-os à guia **Bloqueado**.

1. No **Editor de Definição**, clique na guia **Bloqueado** e insira a URL de um ou mais sites que você quer excluir da pesquisa.

    ![Captura de tela da guia bloqueada do Editor de Definição](../media/blockedCustomSrch.png)


2. Para confirmar que a instância não retorna resultados dos sites bloqueados, insira uma consulta no painel de visualização à direita. 

## <a name="add-pinned-entries"></a>Adicionar entradas fixadas
Para fixar uma página da Web específica na parte superior dos resultados da pesquisa, adicione a página da Web e o termo de consulta à guia **Fixado**.  A guia **Fixado** contém uma lista de pares de termos de páginas da Web e consultas que especificam a página da Web que aparece como o resultado principal de uma consulta específica. O termo de consulta do usuário deve corresponder exatamente ao termo da consulta fixada da página da Web a ser fixado na parte superior.

1. No **Editor de Definição**, clique em **Fixado** e insira a página da Web e o termo de consulta da página da Web que devem retornar como o resultado principal.

    ![Captura de tela da guia fixada do Editor de Definição](../media/pinnedCustomSrch.png)

2. Para confirmar que a instância retorna a página da Web especificada como o resultado principal, insira o termo da consulta que você fixou no painel de visualização à direita.

## <a name="configure-hosted-ui"></a>Configurar a interface do usuário hospedada
A Pesquisa personalizada fornece uma interface do usuário hospedada para renderizar a resposta JSON da instância de pesquisa personalizada.  Para definir a experiência de interface do usuário:

1. Clique na guia **Interface do Usuário Hospedada**.
2. Selecione um layout.

    ![Captura de tela da etapa de seleção do layout da Interface do Usuário Hospedada](./media/custom-search-hosted-ui-select-layout.png)

3. Selecione um tema de cor.

    ![Captura de tela da etapa de seleção do layout da Interface do Usuário Hospedada](./media/custom-search-hosted-ui-select-color-theme.png)

4. Especifique opções de configuração adicionais.

    ![Captura de tela da etapa de configuração adicional da Interface do Usuário Hospedada](./media/custom-search-hosted-ui-additional-configurations.png)

5. Cole a **Chave da assinatura**. Consulte [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Captura de tela da etapa de configuração adicional da Interface do Usuário Hospedada](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Consumir Interface do Usuário Hospedada
Há duas maneiras de consumir a interface do usuário hospedada.  

- Opção 1: integre o trecho de código do JavaScript fornecido ao aplicativo.
- Opção 2: use o ponto de extremidade de HTML fornecido.

O restante deste tutorial ilustra a **Opção 1: trecho de código do Javascript**.  

## <a name="set-up-your-visual-studio-solution"></a>Configure sua solução do Visual Studio
1. Abra o **Visual Studio** no seu computador.
2. No menu **Arquivo**, selecione **Novo** e depois **Projeto**.
3. Na janela **Novo Projeto**, selecione **Aplicativo Web Visual C#/Web/ASP.NET Core**, nomeie o projeto e clique em **OK**.
   
    ![Captura de tela da janela do novo projeto](./media/custom-search-new-project.png)

4. Na janela, **Aplicativo Web New ASP.NET Core**, selecione **Aplicativo Web** e clique em **OK**.
    
    ![Captura de tela da janela do novo projeto](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Editar index.cshtml
1. No **Gerenciador de Soluções**, expanda **Páginas** e clique duas vezes em **index.cshtml** para abrir o arquivo.

    ![Captura de tela do Gerenciador de Soluções com páginas expandidas e index.cshtml selecionado](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. Em index.cshtml, exclua tudo a partir da linha 7 e abaixo.
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. Adicione um elemento de quebra de linha e um div para atuar como um contêiner.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. Na guia **Interface do Usuário Hospedada**, role para baixo até a seção intitulada**Consumir a Interface do Usuário**. Copie o trecho de código do JavaScript.

    ![Captura de tela do botão Salvar Interface do Usuário Hospedada](./media/custom-search-hosted-ui-consuming-ui.png)

5. Cole o elemento de script no contêiner que você adicionou.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch">
        <script type="text/javascript"
                id="bcs_js_snippet"
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">
        </script>
    </div>
    ```

6. No **Gerenciador de Soluções**, clicar com o botão direito do mouse em **wwwroot** e clique em **Exibir no Navegador**.

    ![Captura de tela do gerenciador de soluções selecionando Exibir no Navegador no menu de contexto wwwroot](./media/custom-search-webapp-view-in-browser.png)

A nova página da Web de pesquisa personalizada deve ser semelhante a esta:

![Captura de tela da página da Web de pesquisa personalizada](./media/custom-search-webapp-browse-index.png)

Executar uma pesquisa renderiza resultados como este.

![Captura de tela de resultados da pesquisa personalizada](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> - Criou uma instância de pesquisa personalizada
> - Adicionou entradas ativas
> - Adicionou entradas bloqueadas
> - Adicionou entradas fixadas
> - Integrou pesquisa personalizada em uma página da Web

Agora, é possível continuar chamando os pontos de extremidade da Pesquisa Personalizada do Bing por meio de programação.

> [!div class="nextstepaction"]
> [Chamar ponto de extremidade da Pesquisa Personalizada do Bing (C#)](../call-endpoint-csharp.md)