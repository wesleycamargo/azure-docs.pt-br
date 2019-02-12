---
title: Personalizar os modos de exibição do operador no Azure IoT Central | Microsoft Docs
description: Como um construtor, personalize os modos de exibição do operador em seu aplicativo do Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/29/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 267a619fe32a8d4af0ee9cc8a5001d7a321c3098
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765138"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view-new-ui-design"></a>Tutorial: Personalizar a exibição do operador do Azure IoT Central (design da nova interface do usuário)

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

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os dois tutoriais anteriores:

* [Definir um novo tipo de dispositivo em seu aplicativo do Azure IoT Central](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="configure-your-device-dashboard"></a>Configurar o painel do dispositivo

Como um construtor, você pode definir quais informações são exibidas em um painel do dispositivo. No tutorial [Definir um novo tipo de dispositivo no aplicativo](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), você adicionou um gráfico de linhas e outras informações ao painel do **Ar-condicionado Conectado**.

1. Para editar o modelo de dispositivo **Ar-condicionado Conectado**, escolha **Modelos de Dispositivo** no menu de navegação à esquerda:

    ![Página Modelos de Dispositivo](media/tutorial-customize-operator-experimental/devicetemplates.png)

2. Para personalizar o painel do dispositivo, clique no modelo de dispositivo **Ar-condicionado Conectado (1.0.0)** criado no tutorial [Definir um novo tipo de dispositivo no aplicativo](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

3. Para editar o painel, selecione a guia **Painel**.

4. Para adicionar um bloco de indicador chave de desempenho (KPI) para o painel, escolha **KPI**:

    Para definir o KPI, use as informações da tabela a seguir:

    | Configuração     | Valor |
    | ----------- | ----- |
    | NOME        | Temperatura máxima |
    | Intervalo de tempo  | Passada uma semana |
    | Tipo de Medida | Telemetria |
    | Medida | temperatura |
    | Agregação | Máximo |
    | Visibilidade  | habilitado |

    ![Adicionar KPI](media/tutorial-customize-operator-experimental/addkpi.png)

5. Clique em **Salvar**. Agora você pode ver o bloco KPI no painel:

    ![Bloco KPI](media/tutorial-customize-operator-experimental/temperaturekpi.png)

6. Para mover ou redimensionar um bloco no painel, mova o ponteiro do mouse sobre o bloco. Arraste o bloco para uma nova localização ou redimensione-o.

## <a name="configure-your-settings-layout"></a>Configurar o layout do dispositivo

Como um construtor, você também pode configurar o modo de exibição do operador das configurações do dispositivo. Um operador usa a guia de configurações do dispositivo para configurar um dispositivo. Por exemplo, um operador usa a guia de configurações para definir a temperatura-alvo do ar-condicionado conectado.

1. Para editar o layout de configurações do ar-condicionado conectado, escolha a guia **Configurações**.

2. É possível mover e redimensionar os blocos de configurações:

    ![Editar o layout de configurações](media/tutorial-customize-operator-experimental/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Configurar o layout das propriedades

Além do painel e das configurações, também é possível configurar o modo de exibição do operador das propriedades do dispositivo. Um operador usa a guia de propriedades do dispositivo para gerenciar os metadados do dispositivo. Por exemplo, um operador usa a guia de propriedades para exibir o número de série de um dispositivo ou atualizar detalhes de contato do fabricante.

1. Para editar o layout de propriedades do ar-condicionado conectado, escolha a guia **Propriedades**.

2. Você pode mover e redimensionar os campos de propriedades:

    ![Editar o layout de propriedades](media/tutorial-customize-operator-experimental/propertieslayout.png)

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Como um operador, visualize o dispositivo de ar condicionado conectado

Use a página **Modelos de Dispositivo** para personalizar as guias do painel, de configurações e de propriedades de um operador. Use a página **Device Explorer** para exibir e usar o modelo de dispositivo.

1. Para exibir e usar o modelo de ar-condicionado conectado como um operador, navegue para a página do **Device Explorer** e escolha o dispositivo simulado gerado pelo IoT Central com base no modelo:

    ![Exibir e usar o modelo de dispositivo](media/tutorial-customize-operator-experimental/usetemplate.png)

2. Para atualizar a Localização desse dispositivo, escolha **Propriedades** e edite o valor no bloco Localização. Em seguida, clique em **Salvar**:

    ![Editar um valor de propriedade](media/tutorial-customize-operator-experimental/editproperty.png)

3. Para enviar uma configuração ao ar condicionado conectado, escolha **Configurações**, altere o valor de uma configuração em um bloco e escolha **Atualizar**:

    ![Enviar uma configuração para o dispositivo](media/tutorial-customize-operator-experimental/sendsetting.png)

    Quando o dispositivo reconhecer o novo valor de configuração, a configuração é exibida como **sincronizada** no bloco.

4. Como um operador, você pode exibir o painel do dispositivo conforme configurado pelo construtor:

    ![Modo de exibição do operador do painel do dispositivo](media/tutorial-customize-operator-experimental/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Configurar a página inicial padrão

Quando um construtor ou um operador entra em um aplicativo do Azure IoT Central, eles veem uma página inicial. Como um construtor, você pode configurar o conteúdo desta página inicial para incluir o conteúdo mais útil e relevante para um operador.

1. Para personalizar a home page padrão, navegue para a página **Inicial** e edite **Editar** no canto superior direito da página. Um painel deslizará vindo da esquerda com uma lista de objetos que podem ser adicionados à página **Inicial**:

    ![Página Construtor do aplicativo](media/tutorial-customize-operator-experimental/builderhome.png)

2. Para personalizar a página **Inicial**, adicione blocos da **Biblioteca**. Escolha **Link** e adicione detalhes do site da sua organização. Em seguida, clique em **Salvar**:

    ![Adicionar link à página inicial](media/tutorial-customize-operator-experimental/addlink.png)

    > [!NOTE]
    > Também é possível adicionar links para páginas dentro do seu aplicativo do Azure IoT Central. Por exemplo, é possível adicionar um link para um painel do dispositivo ou uma página de configurações.

3. Como opção, escolha **Imagem** e carregue uma imagem a ser exibida na sua página inicial. Uma imagem pode conter uma URL para a qual você é destinado ao clicar nela:

    ![Adicionar imagem à página inicial](media/tutorial-customize-operator-experimental/addimage.png)

    Para saber mais, consulte [Como preparar e carregar imagens para o aplicativo Azure IoT Central](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="preview-the-default-home-page-as-an-operator"></a>Visualizar a página inicial padrão como operador

Para ver a home page como um operador, clique em **Concluído** no canto superior direito da página:

![Alternar o Modo de Design](media/tutorial-customize-operator-experimental/operatorviewhome.png)

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

* [Monitorar seus dispositivos (como um operador)](tutorial-monitor-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Adicionar um novo dispositivo ao seu aplicativo (como um operador e desenvolvedor do dispositivo)](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
