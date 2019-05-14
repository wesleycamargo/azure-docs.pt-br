---
title: Personalizar a interface do usuário do Azure IoT Central | Microsoft Docs
description: Como personalizar os links de Ajuda e o tema do aplicativo do Azure IoT central
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0256396cd228898f3852772b113e6064a0656746
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237651"
---
# <a name="customize-the-azure-iot-central-ui"></a>Personalizar a interface do usuário do Azure IoT Central 

*Este artigo se aplica aos administradores.*

IoT Central permite personalizar a interface do usuário do seu aplicativo aplicando temas personalizados e modificar os links de ajuda para apontar para seus próprios recursos de ajuda personalizada. Captura de tela a seguir mostra uma página usando o tema padrão:

![Tema padrão do IoT Central](./media/howto-customize-ui/standard-ui.png)

Captura de tela a seguir mostra uma página usando uma captura de tela personalizada com os elementos de interface do usuário personalizados realçados:

![Tema Central de IoT personalizado](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Criar um tema

Para criar um tema personalizado, navegue até a **personalizar seu aplicativo** página de **administração** seção:

![Temas de IoT Central](./media/howto-customize-ui/themes.png)

Nessa página, você pode personalizar os seguintes aspectos do seu aplicativo:

### <a name="application-logo"></a>Logotipo do aplicativo

Uma imagem PNG, uma maior do que 1 MB, com um fundo transparente. Esse logotipo é exibido à esquerda na barra de título do aplicativo IoT Central.

Se sua imagem de logotipo inclui o nome do seu aplicativo, você pode ocultar o texto do nome do aplicativo. Para obter mais informações, consulte [gerenciar configurações de aplicativo](./howto-administer.md#manage-application-settings).

### <a name="browser-icon-favicon"></a>Ícone do navegador (favicon)

Uma imagem PNG, de não pode exceder 32 x 32 pixels, com um fundo transparente. Um navegador da web pode usar essa imagem na barra de endereços, histórico, indicadores e guia do navegador.

### <a name="browser-colors"></a>Cores do navegador

Você pode alterar a cor do cabeçalho da página e a cor usada para entre os botões e outros destaques. Use um valor de cor hexadecimal de seis caracteres no formato `##ff6347`. Para obter mais informações sobre **valor HEX** notação de cor, consulte [cores HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Você sempre pode reverter para as opções padrão sobre o **personalizar seu aplicativo** página.

### <a name="changes-for-operators"></a>Alterações para operadores

Se um administrador cria um tema personalizado e, em seguida, operadores e outros usuários do seu aplicativo não podem escolher um tema no **configurações**.

## <a name="replace-help-links"></a>Substitua os links de ajuda

Para fornecer informações de ajuda personalizada para seus operadores e outros usuários, você pode modificar os links no aplicativo **ajudar** menu.

Para modificar os links de Ajuda, navegue até a **Personalizar Ajuda** página de **administração** seção:

![Personalizar os links de ajuda de IoT Central](./media/howto-customize-ui/help-links.png)

Você também pode adicionar novas entradas ao menu Ajuda e remover as entradas padrão:

![Ajuda personalizada de IoT Central](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Você sempre pode reverter para os links de ajuda padrão sobre o **Personalizar Ajuda** página.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como personalizar a interface do usuário em seu aplicativo IoT Central, aqui estão algumas das próximas etapas sugeridas:

> [!div class="nextstepaction"]
> [Administrar o seu aplicativo](./howto-administer.md)
> [configurar o painel do aplicativo](./howto-configure-homepage.md)