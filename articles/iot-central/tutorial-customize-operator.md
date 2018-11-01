---
title: Personalizar os modos de exibição do operador no Azure IoT Central | Microsoft Docs
description: Como um construtor, personalize os modos de exibição do operador em seu aplicativo do Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 070a6ee4bc712b2dbec598cdb9be5c324895c033
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154974"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Tutorial: Personalizar o modo de exibição do operador do Azure IoT Central

Este tutorial mostra a você, como um construtor, como personalizar o modo de exibição do operador do seu aplicativo. Ao fazer uma alteração no aplicativo como um construtor, você pode visualizar o modo de exibição do operador no aplicativo do Microsoft Azure IoT Central.

Neste tutorial, você personaliza o aplicativo para exibir informações relevantes sobre o dispositivo de ar condicionado conectado a um operador. Suas personalizações habilitam o operador gerenciar os dispositivos de ar condicionado conectados ao aplicativo.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o painel do dispositivo
> * Configurar o layout das configurações do dispositivo
> * Configurar o layout das propriedades do dispositivo
> * Visualizar o dispositivo como operador
> * Configurar sua página inicial padrão
> * Visualizar a página inicial padrão como operador

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os dois tutoriais anteriores:

* [Definir um novo tipo de dispositivo em seu aplicativo do Azure IoT Central](tutorial-define-device-type.md).
* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Configurar o painel do dispositivo

Como um construtor, você pode definir quais informações são exibidas em um painel do dispositivo. No tutorial [Definir um novo tipo de dispositivo em seu aplicativo](tutorial-define-device-type.md), você adicionou um gráfico de linhas e outras informações ao painel **Ar condicionado-1 conectado**.

1. Para editar o modelo do dispositivo de **Ar condicionado conectado**, escolha **Explorer** no menu de navegação à esquerda:

    ![Página Explorer](media/tutorial-customize-operator/explorer.png)

2. Para começar a personalizar o painel do dispositivo de ar condicionado conectado, selecione o modelo do dispositivo de **Ar condicionado conectado (1.0.0)**. Escolha o dispositivo **Ar condicionado-1 conectado** criado no tutorial [Definir um novo tipo de dispositivo em seu aplicativo](tutorial-define-device-type.md):

    ![Selecione o dispositivo de ar condicionado conectado](media/tutorial-customize-operator/selectdevice.png)

    Quando estiver em um dispositivo, como **Ar condicionado-1 conectado**, você pode selecionar **Editar modelo** para fazer uma alteração no modelo subjacente. Para saber mais sobre como [Criar uma nova versão de modelo de dispositivo](howto-version-devicetemplate.md).

3. Para editar o painel, escolha **Painel**, e selecione **Editar modelo**:

    ![Página Painel de modelo do dispositivo](media/tutorial-customize-operator/dashboard.png)

4. Para adicionar um bloco de indicador chave de desempenho (KPI) para o painel, escolha **KPI**:

    ![Adicionar KPI](media/tutorial-customize-operator/addkpi.png)

    Para definir o KPI, use as informações da tabela a seguir:

    | Configuração     | Valor |
    | ----------- | ----- |
    | NOME        | Temperatura máxima |
    | Medida | temperatura |
    | Agregação | Máximo |
    | Intervalo de tempo  | Passada uma semana |

5. Escolha **Salvar**. Agora você pode ver o bloco KPI no painel:

    ![Bloco KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Para mover ou redimensionar um bloco no painel, mova o ponteiro do mouse sobre o bloco. Você pode arrastar o bloco para um novo local ou redimensioná-lo:

    ![Editar layout do painel](media/tutorial-customize-operator/dashboardlayout.png)

7. Clique em **Concluído** quando terminar de fazer alterações.

## <a name="configure-your-settings-layout"></a>Configurar o layout do dispositivo

Como um construtor, você também pode configurar o modo de exibição do operador das configurações do dispositivo. Um operador usa a página de configurações do dispositivo para configurar um dispositivo. Por exemplo, um operador usa a página de configurações para definir a temperatura-alvo do ar-condicionado conectado.

1. Para editar o layout de configurações do ar condicionado conectado, escolha **Configurações** e selecione **Editar modelo**:

    ![Página Configurações](media/tutorial-customize-operator/settings.png)

2. É possível mover e redimensionar os blocos de configurações:

    ![Editar o layout de configurações](media/tutorial-customize-operator/settingslayout.png)

3. Clique em **Concluído** quando terminar de fazer alterações.

> [!NOTE]
> No modo **Editar modelo**, não é possível editar os valores das configurações.

## <a name="configure-your-properties-layout"></a>Configurar o layout das propriedades

Além do painel e das configurações, também é possível configurar o modo de exibição do operador das propriedades do dispositivo. Um operador usa a página de propriedades do dispositivo para gerenciar os metadados do dispositivo. Por exemplo, um operador usa a página de propriedades para visualizar um número de série do dispositivo ou atualizar detalhes de contato do fabricante.

1. Para editar o layout de propriedades do ar condicionado conectado, escolha **Propriedades**, e selecione **Editar modelo**:

    ![Página Propriedades](media/tutorial-customize-operator/properties.png)

2. Você pode mover e redimensionar os campos de propriedades:

    ![Editar o layout de propriedades](media/tutorial-customize-operator/propertieslayout.png)

3. Clique em **Concluído** quando terminar de fazer alterações.

> [!NOTE]
> No modo **Editar modelo**, não é possível editar os valores das propriedades.

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Como um operador, visualize o dispositivo de ar condicionado conectado

No modo **Editar modelo**, você pode personalizar as páginas do painel, das configurações e das propriedades para um operador. Se você não estiver no modo **Editar modelo**, você pode exibir o aplicativo como um operador.

1. Para exibir o dispositivo de ar-condicionado conectado como um operador, clique em **Concluído** para interromper a edição do modelo e retornar para a exibição do dispositivo do operador.

2. Para atualizar a Localização desse dispositivo, edite o valor no bloco de Localização e escolha **Salvar**:

    ![Editar um valor de propriedade](media/tutorial-customize-operator/editproperty.png)

3. Para enviar uma configuração ao ar condicionado conectado, escolha **Configurações**, altere o valor de uma configuração em um bloco e escolha **Atualizar**:

    ![Enviar uma configuração para o dispositivo](media/tutorial-customize-operator/sendsetting.png)

    Quando o dispositivo reconhecer o novo valor de configuração, a configuração é exibida como **sincronizada** no bloco.

4. Como um operador, você pode exibir o painel do dispositivo conforme configurado pelo construtor:

    ![Modo de exibição do operador do painel do dispositivo](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Configurar a página inicial padrão

Quando um construtor ou um operador entra em um aplicativo do Azure IoT Central, eles veem uma página inicial. Como um construtor, você pode configurar o conteúdo desta página inicial para incluir o conteúdo mais útil e relevante para um operador.

1. Para personalizar a página inicial padrão, navegue até a página **Inicial** e selecione **Editar** no canto superior direito da página. Ao selecionar **Editar**, um painel deslizará da direita com uma lista de objetos que você pode adicionar à sua página inicial.

    ![Página Construtor do aplicativo](media/tutorial-customize-operator/builderhome.png)

2. Para personalizar a página inicial, adicione blocos da **Biblioteca**. Escolha **Link** e adicione detalhes do site da sua organização. Depois escolha **Salvar**:

    ![Adicionar link à página inicial](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Também é possível adicionar links para páginas dentro do seu aplicativo do Azure IoT Central. Por exemplo, é possível adicionar um link para um painel do dispositivo ou uma página de configurações.

3. Como opção, escolha **Imagem** e carregue uma imagem a ser exibida na sua página inicial. Uma imagem pode conter uma URL para a qual você é destinado ao clicar nela:

    ![Adicionar imagem à página inicial](media/tutorial-customize-operator/addimage.png)

    Para saber mais, consulte [Como preparar e carregar imagens para o aplicativo Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-default-home-page-as-an-operator"></a>Visualizar a página inicial padrão como operador

Para visualizar a página inicial como um operador e parar de editar, selecione **Concluído** na parte superior direita da página

![Alternar o Modo de Design](media/tutorial-customize-operator/operatorviewhome.png)

É possível clicar nos blocos de link e de imagem para navegar até as URLs que você definir como um construtor.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como personalizar o modo de exibição do operador do aplicativo.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Configurar o painel do dispositivo
> * Configurar o layout das configurações do dispositivo
> * Configurar o layout das propriedades do dispositivo
> * Visualizar o dispositivo como operador
> * Configurar sua página inicial padrão
> * Visualizar a página inicial padrão como operador

Agora que você aprendeu como personalizar o modo de exibição do aplicativo do operador, as próximas etapas sugeridas são:

* [Monitorar seus dispositivos (como um operador)](tutorial-monitor-devices.md)
* [Adicionar um novo dispositivo ao seu aplicativo (como um operador e desenvolvedor do dispositivo)](tutorial-add-device.md)
