---
title: Resolução de problemas da extensão do Painel de Acesso do Azure para IE | Microsoft Docs
description: Como usar a política de grupo para implantar o complemento do Internet Explorer para o portal de meus aplicativos.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2018
ms.author: celested
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace2482e51454458977452f0aa610dd43a94e8a7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211182"
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Resolução de problemas da extensão do painel de acesso do Internet Explorer
Este artigo ajuda você a solucionar os problemas a seguir:

* Você não consegue acessar seus aplicativos por meio do portal de meus aplicativos ao usar o Internet Explorer.
* Consulte a mensagem "Instalação de Software", mesmo que você já tenha instalado o software.

Se você é um administrador, confira também: [Como implantar a extensão do painel de acesso para o Internet Explorer usando a Política de Grupo](deploy-access-panel-browser-extension.md)

## <a name="run-the-diagnostic-tool"></a>Execute a ferramenta de diagnóstico
Você pode diagnosticar problemas de instalação com a extensão do painel de acesso fazendo download e executando a ferramenta de diagnóstico do painel de acesso:

1. [Clique aqui para baixar a ferramenta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Abra o arquivo e pressione o botão **Extrair todos** .
   
    ![Pressione Extrair Tudo](./media/manage-access-panel-browser-extension/extract1.png)
3. Em seguida, pressione o botão **Extrair** para continuar.
   
    ![Pressione Extrair](./media/manage-access-panel-browser-extension/extract2.png)
4. Para executar a ferramenta, clique com o botão direito do mouse no arquivo denominado **AccessPanelExtensionDiagnosticTool**, em seguida, selecione **Abrir com > Microsoft Windows com base em Script Host**.
   
    ![Abrir com > Microsoft Windows com base em Script Host](./media/manage-access-panel-browser-extension/open_tool.png)
5. Você verá a janela diagnóstico a seguir, que descreve o que pode estar errado com a sua instalação.
   
    ![Um exemplo da janela de diagnóstico](./media/manage-access-panel-browser-extension/tool_preview.png)
6. Clique em "**Sim**" para permitir que o programa corrija os problemas encontrados.
7. Para salvar essas alterações, feche todas as janelas do Internet Explorer e abra novamente o Internet Explorer.<br />Se você não puder acessar seus aplicativos, tente as etapas abaixo.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verifique se a extensão do painel de acesso está habilitada
Para verificar se a extensão do painel de acesso está habilitada no Internet Explorer:

1. No Internet Explorer, clique no **ícone de engrenagem** no canto superior direito da janela. Em seguida, selecione **Opções da Internet**.<br />(Em versões mais antigas do Internet Explorer pode ser encontrada em **Ferramentas > Opções da Internet**.
   
    ![Vá para Ferramentas > Opções da Internet](./media/manage-access-panel-browser-extension/internetoptions.png)
2. Clique na guia **Programas** e clique no botão **Gerenciar Complementos**.
   
    ![Clique em Gerenciar complementos](./media/manage-access-panel-browser-extension/internetoptions_programs.png)
3. Nesta caixa de diálogo, selecione **extensão do painel de acesso** e, em seguida, clique no botão **Habilitar**.
   
    ![Clique em Habilitar](./media/manage-access-panel-browser-extension/enableaddon.png)
4. Para salvar essas alterações, feche todas as janelas do Internet Explorer e abra novamente o Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Habilitar extensões para a navegação InPrivate
Se você estiver usando o modo de Navegação InPrivate:

1. No Internet Explorer, clique no **ícone de engrenagem** no canto superior direito da janela. Em seguida, selecione **Opções da Internet**.<br />(Em versões mais antigas do Internet Explorer pode ser encontrada em **Ferramentas > Opções da Internet**.
   
    ![Um exemplo da janela de diagnóstico](./media/manage-access-panel-browser-extension/inprivateoptions.png)
2. Vá para a guia **Privacidade**, em seguida, **desmarque** a caixa de seleção **Desabilitar barras de ferramentas e extensões quando a Navegação InPrivate se iniciar**</p>
   
    ![Desmarque a opção de desabilitar as barras de ferramentas e extensões quando começar a navegação InPrivate](./media/manage-access-panel-browser-extension/enabletoolbars.png)
3. Para salvar essas alterações, feche todas as janelas do Internet Explorer e abra novamente o Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Desinstalar a extensão do painel de acesso
Para desinstalar a extensão do painel de acesso do computador:

1. No teclado, pressione a **tecla Windows** para abrir o menu Iniciar. Quando o menu estiver aberto, você pode digitar qualquer coisa para fazer uma pesquisa. Digite "Control Panel" e, em seguida, abra o **Painel de controle** quando ele aparecer nos resultados da pesquisa.
   
    ![Pesquise Painel de Controle](./media/manage-access-panel-browser-extension/search_sm.png)
2. No canto superior direito do painel de controle, altere a opção **Exibir para** **ícones grandes**. Em seguida, localize e clique no botão **Programas e Recursos**.
   
    ![Altere a exibição para mostrar ícones grandes](./media/manage-access-panel-browser-extension/control_panel.png)
3. Na lista, selecione **Extensão do Painel de Acesso** e clique no botão **Desinstalar**.
   
    ![Clique em Desinstalar](./media/manage-access-panel-browser-extension/uninstall.png)
4. Você pode tentar instalar a extensão novamente para ver se o problema foi resolvido.

Se você encontrar problemas ao desinstalar a extensão, também pode removê-lo usando a ferramenta [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Artigos relacionados
* [Acesso a aplicativos e logon único com o Active Directory do Azure](what-is-single-sign-on.md)
* [Como implantar a extensão do painel de acesso para o Internet Explorer usando a Política de Grupo](deploy-access-panel-browser-extension.md)

