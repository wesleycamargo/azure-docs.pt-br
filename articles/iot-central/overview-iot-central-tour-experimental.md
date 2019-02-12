---
title: Faça um tour da interface do usuário do Azure IoT Central | Microsoft Docs
description: Como um construtor, familiarize-se com as áreas principais da interface do usuário do Azure IoT Central que você usa para criar uma solução de IoT.
author: dominicbetts
ms.author: dobett
ms.date: 01/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 80fe2fb2998ed129098a99f004da9c9e5e88e474
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815023"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-new-ui-design"></a>Fazer um tour pela interface do usuário do Azure IoT Central (design da nova interface do usuário)

Este artigo apresenta a interface do usuário do Microsoft Azure IoT Central. Você pode usar a interface do usuário para criar, gerenciar e usar uma solução do Azure IoT Central e seus dispositivos conectados.

Como um _construtor_, use a interface do usuário do Azure IoT Central para definir sua solução o Azure IoT Central. Você pode usar a interface do usuário para:

- Definir os tipos de dispositivos que se conectam à sua solução.
- Configurar as regras e ações para seus dispositivos.
- Personalizar a interface do usuário para um _operador_ que usa sua solução.

Como um _operador_, você pode usar a interface do usuário do Azure IoT Central para gerenciar sua solução do Azure IoT Central. Você pode usar a interface do usuário para:

- Monitorar seus dispositivos.
- Configurar seus dispositivos.
- Solucionar e corrigir problemas com dispositivos.
- Provisione novos dispositivos.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="use-the-left-navigation-menu"></a>Use o menu de navegação à esquerda

Use o menu de navegação à esquerda para acessar as diferentes áreas do aplicativo:

| Menu | DESCRIÇÃO |
| ---- | ----------- |
| ![Menu de navegação à esquerda](media/overview-iot-central-tour-experimental/navigationbar.png) | <ul><li>O botão **Início** exibe a home page do seu aplicativo. Como um construtor, você pode personalizar essa página inicial para seus operadores.</li><li>O botão **Device Explorer** lista os dispositivos simulados e reais associados a cada modelo de dispositivo no aplicativo. Como um operador, use o **Device Explorer** para gerenciar os dispositivos conectados.</li><li>O botão **Definições do Dispositivo** permite exibir e criar conjuntos de dispositivos. Como um operador, você pode criar conjuntos de dispositivos como uma coleção lógica de dispositivos especificados por uma consulta.</li><li>O botão **Análise** mostra a análise derivada da telemetria do dispositivo para dispositivos e conjuntos de dispositivos. Como um operador, você pode criar exibições personalizadas sobre dados do dispositivo para derivar insights de seu aplicativo.</li><li>O botão **Trabalhos** habilita o gerenciamento de dispositivos em massa fazendo você criar e executar trabalhos para realizar atualizações em escala.</li><li>O botão **Modelos de Dispositivo** mostra as ferramentas usadas por um construtor para criar e gerenciar modelos de dispositivo.</li><li>O botão **Exportação Contínua de Dados** permite que um administrador configure uma exportação contínua para outros serviços do Azure como armazenamento e filas.</li><li>O botão **Administração** mostra as páginas de administração do aplicativo em que um administrador pode gerenciar as configurações do aplicativo, usuários e funções.</li></ul> |

## <a name="search-help-and-support"></a>Pesquisa, ajuda e suporte

O menu superior aparece em cada página:

![Barra de ferramentas](media/overview-iot-central-tour-experimental/toolbar.png)

- Para procurar dispositivos e modelos de dispositivo, escolha o ícone **Pesquisar**.
- Para alterar o idioma da interface do usuário, escolha o ícone **Idioma**.
- Para obter ajuda e suporte, escolha o menu suspenso da **Ajuda** para ver uma lista de recursos.
- Para alterar o tema da interface do usuário, saia do aplicativo e escolha o ícone **Conta**.

Você pode escolher entre um tema claro ou escuro para a interface do usuário:

![Escolher um tema para a interface do usuário](media/overview-iot-central-tour-experimental/themes.png)

## <a name="home-page"></a>Página inicial

![Página inicial](media/overview-iot-central-tour-experimental/homepage.png)

A home page é a primeira página que você vê quando entra no seu aplicativo do Azure IoT Central. Como um construtor, você pode adicionar blocos para personalizar a home page de outros usuários do aplicativo. Para obter mais informações, consulte o tutorial [Personalizar modo de exibição do operador do Azure IoT Central](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="device-explorer"></a>Gerenciador de dispositivos

![Página Explorer](media/overview-iot-central-tour-experimental/explorer.png)

A página Explorer mostra os _dispositivos_ no aplicativo do Azure IoT Central agrupados por _modelo de dispositivo_.

* Um modelo do dispositivo define um tipo de dispositivo que pode se conectar ao seu aplicativo. Para saber mais, consulte [Definir um novo tipo de dispositivo em seu aplicativo do Azure IoT Central](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* Um dispositivo representa um dispositivo real ou simulado no seu aplicativo. Para saber mais, consulte [Adicionar um novo dispositivo ao seu aplicativo do Azure IoT Central](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="device-sets"></a>Conjuntos de dispositivos

![Pagina Conjuntos de dispositivos](media/overview-iot-central-tour-experimental/devicesets.png)

A página _Conjuntos de dispositivos_ mostra conjuntos de dispositivos criados pelo construtor. Um conjunto de dispositivos é uma coleção de dispositivos relacionados. Um construtor define uma consulta para identificar os dispositivos que estão incluídos em um conjunto de dispositivos. Ao personalizar a análise em seu aplicativo, você usa conjuntos de dispositivos. Para saber mais, consulte o artigo [Usar conjuntos de dispositivos em seu aplicativo do Azure IoT Central](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="analytics"></a>Análise

![Página Análise](media/overview-iot-central-tour-experimental/analytics.png)

A página Análise mostra gráficos que ajudam você a entender como os dispositivos conectados ao seu aplicativo estão se comportando. Um operador usa essa página para monitorar e investigar problemas com dispositivos conectados. O construtor pode definir os gráficos mostrados nessa página. Para saber mais, consulte o artigo [Criar análises personalizadas para seu aplicativo do Azure IoT Central](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="jobs"></a>Trabalhos

![Página Trabalhos](media/overview-iot-central-tour-experimental/jobs.png)

A página de trabalhos permite que você execute operações de gerenciamento de dispositivos em massa em seus dispositivos. O construtor usa essa página para atualizar propriedades do dispositivo, configurações e comandos. Para saber mais, consulte o artigo [Executar um trabalho](howto-run-a-job-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="device-templates"></a>Modelos de Dispositivo

![Página Modelos de Dispositivo](media/overview-iot-central-tour-experimental/templates.png)

A página Modelos de Dispositivo é o local em que um construtor cria e gerencia os modelos de dispositivo no aplicativo. Para saber mais, consulte o tutorial [Definir um novo tipo de dispositivo em seu aplicativo do Azure IoT Central](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="continuous-data-export"></a>Exportação Contínua de Dados

![Página Exportação Contínua de Dados](media/overview-iot-central-tour-experimental/export.png)

A página Exportação Contínua de Dados é o local em que o administrador define como exportar dados, como a telemetria, por meio do aplicativo. Outros serviços podem armazenar os dados exportados ou usá-los para análise. Para saber mais, confira o artigo [Exportar seus dados no Azure IoT Central](howto-export-data-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="administration"></a>Administração

![Página Administração](media/overview-iot-central-tour-experimental/administration.png)

A página Administração contém links para as ferramentas que um administrador usa para definir usuários e funções no aplicativo. Para saber mais, consulte o artigo [Administrar seu aplicativo do Azure IoT Central](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do Azure IoT Central e está familiarizado com o layout da interface do usuário, a próxima etapa sugerida é concluir o início rápido [Criar um aplicativo do Azure IoT Central](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).