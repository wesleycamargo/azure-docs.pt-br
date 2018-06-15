---
title: Administrar um aplicativo Azure IoT Central | Microsoft Docs
description: Como um administrador, saiba como administrar o aplicativo Azure IoT Central
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0bdd798f9bc8fdaef54abd721a986c607c6323a5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628548"
---
# <a name="how-to-administer-your-application"></a>Como administrar o aplicativo

Após criar um aplicativo Microsoft Azure IoT Centra, será possível usar a seção **Administração** da interface do usuário do Azure IoT Central para administrá-lo. Para navegar até a seção **Administração**, escolha **Administração** no menu de navegação esquerdo.

A seção **Administração** permite a você:

- Gerenciar usuários

- Gerenciar funções

- Exibir informações de cobrança

- Gerenciar configurações de aplicativo

- Estender uma avaliação gratuita

Na seção **Administração**, há um menu de navegação secundário com links para as várias tarefas de administração.

Para acessar e usar a seção **Administração**, você deve estar na função **Administrador** no aplicativo Azure IoT Central. Se você criar um aplicativo Azure IoT Central, você será automaticamente atribuído à função **Administrador** desse aplicativo. A seção *Gerenciando Usuários* deste artigo explica mais sobre como atribuir a função **Administrador** a outros usuários.

## <a name="change-application-name"></a>Alterar o nome do aplicativo

Para alterar o nome do aplicativo, use o menu de navegação secundário para navegar até a página **Configurações de Aplicativo** na seção **Administração**.

Na página **Configurações de Aplicativo**, insira um nome de sua escolha no campo **Nome do Aplicativo** e, em seguida, escolha **Salvar**.

## <a name="change-the-application-url"></a>Alterar a URL do aplicativo

Para alterar a URL do aplicativo, use o menu de navegação secundário para navegar até a página **Configurações de Aplicativo** na seção **Administração**.

![Página Configurações de Aplicativo](media\howto-administer\image0-a.png)

Na página **Configurações de Aplicativo**, insira a URL de sua escolha no campo **URL** e, em seguida, escolha **Salvar**. A URL pode ter no máximo 200 caracteres. Se a URL não estiver disponível, um erro de validação será exibido

> [!Note]
> Se você alterar a URL, a URL antiga poderá ser utilizada por outro cliente do Azure IoT Central. Nesse caso, a URL não estará mais disponível para você usar. Ao alterar a URL, a URL antiga não funcionará mais e você deverá notificar os usuários sobre a nova URL a ser utilizada.

## <a name="change-the-application-image"></a>Alterar a imagem do aplicativo

Para obter mais informações sobre o uso de imagens em um aplicativo do Azure IoT Central, consulte [Preparar e carregar imagens no aplicativo Azure IoT Central](howto-prepare-images.md).

## <a name="delete-an-application"></a>Excluir um aplicativo

Para excluir o aplicativo, use o menu de navegação secundário para navegar até a página **Configurações do Aplicativo** na seção **Administração**.

Clique em **Excluir**.

> [!Note]
> A exclusão de um aplicativo exclui irrecuperavelmente todos os dados associados ao aplicativo. Para excluir um aplicativo, você também deve ter os direitos para excluir recursos da assinatura do Azure escolhida ao criar o aplicativo. Para saber mais, consulte [Usar o Controle de Acesso Baseado em Função para gerenciar o acesso aos recursos de sua assinatura do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Funções no Azure IoT Central

As funções permitem controlar quem, dentro da organização, pode executar várias tarefas do Azure IoT Central. O Azure IoT Central tem três funções que podem ser atribuídas aos usuários do aplicativo. As funções são atribuídas por cada aplicativo. O mesmo usuário pode ter diferentes funções em diferentes aplicativos. É possível atribuir o mesmo usuário a várias funções em um aplicativo.

### <a name="administrator"></a>Administrador

Usuários na função **Administrador** têm acesso a todas as funcionalidades em um aplicativo Azure IoT Central.

O usuário que cria um aplicativo é automaticamente designado à função **Administrador**. Sempre deverá haver pelo menos um usuário na função **Administrador**.

### <a name="application-builder"></a>Construtor de Aplicativos

Os usuários na função **Construtor de Aplicativos** podem fazer tudo em um aplicativo Azure IoT Central, exceto administrar o aplicativo.

### <a name="application-operator"></a>Operador de Aplicativos

Os usuários na função **Operador de Aplicativos** não têm acesso à página do **Construtor de Aplicativos**. Eles não podem administrar o aplicativo.

## <a name="manage-users"></a>Gerenciar usuários

Os administradores de aplicativos podem atribuir usuários às funções no aplicativo.

### <a name="add-users"></a>Adicionar usuários

Cada usuário deve ter uma conta de usuário antes de poder entrar e acessar um aplicativo Azure IoT Central. As MSAs (Contas Microsoft) e contas do AAD (Azure Active Directory) têm suporte no Azure IoT Central. Atualmente, os grupos do Azure Active Directory não têm suporte no Azure IoT Central.

Para saber mais, consulte a [Ajuda da Conta Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e o [Início Rápido: Adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Para adicionar uma conta de usuário a um aplicativo Azure IoT Central, use o menu de navegação secundário para navegar até a página **Usuários** na seção **Administração**.

    ![Lista de usuários](media\howto-administer\image1.png)

1. Na página **Usuários**, escolha **+ Adicionar usuário** para adicionar um usuário.

    ![Adicionar usuário](media\howto-administer\image2.png)

1. Ao adicionar um usuário ao aplicativo Azure IoT Central, escolha uma função para o usuário na lista suspensa **Função**. Saiba mais sobre funções na seção *Funções no Azure IoT Central* deste artigo.

    ![Seleção da função](media\howto-administer\image3.png)

    > [!NOTE]
    >  Para adicionar usuários em massa, insira as IDs de usuário de todos os usuários que você deseja adicionar separados por ponto e vírgula. Escolha uma função na lista suspensa **Função** e escolha **Salvar**.

1. Após adicionar um usuário, uma entrada para esse usuário será exibida na página **Usuários**.

    ![Lista de Usuários](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>Editar as funções atribuídas aos usuários

As funções não poderão ser alteradas após serem atribuídas. Para alterar a função atribuída a um usuário, exclua o usuário e adicione o usuário novamente com uma função diferente.

### <a name="delete-users"></a>Excluir usuários

Para excluir usuários, marque uma ou mais caixas de seleção na página **Usuários** e, em seguida, escolha **Excluir**.

## <a name="view-your-bill"></a>Exibir a fatura

Para exibir a fatura, navegue até a página **Cobrança** na seção **Administração** e escolha **Cobrança**. A página de cobrança do Azure abrirá em uma nova guia e você poderá exibir a fatura de cada um dos aplicativos do Azure IoT Central.

## <a name="convert-your-trial-to-a-paid-application"></a>Converter a versão de avaliação para um aplicativo pago

Após avaliar o IoT Central, você poderá converter a versão de avaliação para um aplicativo pago. Para concluir esse processo de autoatendimento, execute estas etapas:

1. Use o menu de navegação secundário para navegar até a página **Cobrança** na seção **Administração**. Caso não tenha estendido a versão de avaliação, a página será semelhante à seguinte:

    ![Estado de avaliação gratuita](media/howto-administer/freetrial.png)

2. Clique em **Converter para Pago**. Caso não tenha estendido a versão de avaliação, o item pop-up será semelhante ao seguinte:

    No item pop-up, selecione o locatário apropriado do Azure Active Directory e, em seguida, a Assinatura do Azure que você deseja usar para o aplicativo IoT Central.

    ![Estender a avaliação gratuita](media/howto-administer/extend.png)

3. Após clicar em **Converter**, a versão de avaliação será convertida em um aplicativo pago e você começará a ser cobrado.

## <a name="extend-your-free-trial"></a>Estender a avaliação gratuita

Por padrão, todas as avaliações gratuitas estão disponíveis por 7 dias. Se você quiser ampliar a versão de avaliação para 30 dias, execute estas etapas:

1. Use o menu de navegação secundário para navegar até a página **Cobrança** na seção **Administração**:

1. Clique em **Estender Avaliação**. No item pop-up, selecione o locatário apropriado do Azure Active Directory e, em seguida, a Assinatura do Azure a ser usada para o aplicativo IoT Central:

1. Em seguida, clique em **Estender**. A avaliação agora é válida por 30 dias.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como administrar o aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Configurar o modelo de dispositivo](howto-set-up-template.md)