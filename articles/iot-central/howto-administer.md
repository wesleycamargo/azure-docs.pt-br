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
ms.openlocfilehash: 25b4777be4257933b84d58d0f10cf12571de9590
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155313"
---
# <a name="administer-your-iot-central-application"></a>Administrar o aplicativo IOT Central

Após criar um aplicativo Microsoft Azure IoT Centra, será possível usar a seção **Administração** da interface do usuário do Azure IoT Central para administrá-lo. Para ir até a seção **Administração**, escolha **Administração** no menu de navegação esquerdo.

A seção **Administração** permite a você:

- Gerenciar usuários

- Gerenciar funções

- Exibir informações de cobrança

- Gerenciar configurações de aplicativo

- Oferecer uma avaliação gratuita

Na seção **Administração**, há um menu de navegação secundário com links para as várias tarefas de administração.

Para acessar e usar a seção **Administração**, você deve estar na função **Administrador** no aplicativo Azure IoT Central. Se você criar um aplicativo Azure IoT Central, você será automaticamente atribuído à função **Administrador** desse aplicativo. A seção [Gerenciando Usuários](#manage-users) deste artigo explica mais sobre como atribuir a função **Administrador** a outros usuários.

## <a name="change-application-name"></a>Alterar o nome do aplicativo

Para alterar o nome do aplicativo, use o menu de navegação secundário para ir até a página **Configurações de Aplicativo** na seção **Administração**.

Na página **Configurações de Aplicativo**, insira um nome de sua escolha no campo **Nome do Aplicativo**. Em seguida, selecione **Salvar**.

## <a name="change-the-application-url"></a>Alterar a URL do aplicativo

Para alterar a URL do aplicativo, use o menu de navegação secundário para navegar até a página **Configurações de Aplicativo** na seção **Administração**.

![Página de configurações do aplicativo](media\howto-administer\image0-a.png)

Na página **Configurações de Aplicativo**, insira a URL de sua escolha no campo **URL** e, em seguida, escolha **Salvar**. A URL pode ter no máximo 200 caracteres. Se a URL não estiver disponível, um erro de validação será exibido.

> [!Note]
> Se você alterar a URL, a URL antiga poderá ser utilizada por outro cliente do Azure IoT Central. Nesse caso, a URL não estará mais disponível para você usar. Ao alterar a URL, a URL antiga não funcionará mais e você deverá notificar os usuários sobre a nova URL a ser utilizada.

## <a name="change-the-application-image"></a>Alterar a imagem do aplicativo

Para obter mais informações sobre o uso de imagens em um aplicativo do Azure IoT Central, consulte [Preparar e carregar imagens no aplicativo Azure IoT Central](howto-prepare-images.md).

## <a name="copy-an-application"></a>Copiar um aplicativo

É possível criar uma cópia de qualquer aplicativo, menos quaisquer instâncias de dispositivos, histórico de dados do dispositivo e os dados de usuário. A cópia será um aplicativo pago pelo qual você será cobrado. Não é possível criar um aplicativo de avaliação por meio da cópia de outro aplicativo.

Para copiar um aplicativo, vá até a página **Configurações do Aplicativo**. Em seguida, escolha o botão **Copiar**.

![Página de configurações do aplicativo](media\howto-administer\appCopy1.png)

Escolher no botão **Copiar** abrirá uma caixa de diálogo na qual é possível escolher um nome de URL, diretório do Azure AD, assinatura e região do Azure para o novo aplicativo que será criado por meio da cópia do aplicativo. Escolha valores para cada um desses campos. Em seguida, escolha o botão **Copiar** para confirmar que você quer continuar. Você pode aprender mais sobre o que inserir esses valores no artigo sobre [como criar um aplicativo](howto-create-application.md).

![Página de configurações do aplicativo](media\howto-administer\appCopy2.png)

Após a conclusão correta da operação de cópia do aplicativo, acesse o novo aplicativo que foi criado copiando seu aplicativo. Para acessar o aplicativo, escolha o link exibido na página **Configurações do Aplicativo**.

![Página de configurações do aplicativo](media\howto-administer\appCopy3.png)

> [!Note]
> Ao copiar um aplicativo, a definição das regras ou ações também será copiada. No entanto, como os usuários que têm acesso ao seu aplicativo original não são copiados para o aplicativo copiado, será necessário adicionar manualmente os usuários a ações, como o email para o qual os usuários são um pré-requisito.

## <a name="delete-an-application"></a>Excluir um aplicativo

Para excluir o aplicativo, use o menu de navegação secundário para navegar até a página **Configurações do Aplicativo** na seção **Administração**.

Clique em **Excluir**.

> [!Note]
> A exclusão de um aplicativo exclui permanentemente todos os dados associados ao aplicativo.  Para excluir um aplicativo, você também deve ter as permissões para excluir recursos da assinatura do Azure escolhida ao criar o aplicativo. Para saber mais, confira [Usar o controle de acesso baseado em função para gerenciar o acesso aos recursos de sua assinatura do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

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

Cada usuário deve ter uma conta de usuário antes de poder entrar e acessar um aplicativo Azure IoT Central. As MSAs (Contas Microsoft) e contas do Azure AD (Azure Active Directory) têm suporte no Azure IoT Central. Atualmente, os grupos do Azure Active Directory não têm suporte no Azure IoT Central.

Para saber mais, confira a [Ajuda da Conta Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e o [Início Rápido: Adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Para adicionar uma conta de usuário a um aplicativo Azure IoT Central, use o menu de navegação secundário para ir até a página **Usuários** na seção **Administração**.

    ![Lista de usuários](media\howto-administer\image1.png)

1. Na página **Usuários**, escolha **+ Adicionar usuário** para adicionar um usuário.

    ![Adicionar usuário](media\howto-administer\image2.png)

1. Escolha uma função para o usuário na lista suspensa **Função**. Saiba mais sobre funções na seção *Funções no Azure IoT Central* deste artigo.

    ![Seleção de função](media\howto-administer\image3.png)

    > [!NOTE]
    >  Para adicionar usuários em massa, insira as IDs de usuário de todos os usuários que você deseja adicionar separados por ponto e vírgula. Escolha uma função na lista suspensa **Função**. Em seguida, selecione **Salvar**.

1. Após adicionar um usuário, uma entrada para esse usuário será exibida na página **Usuários**.

    ![Lista de Usuários](media\howto-administer\image4.png)

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Editar as funções atribuídas aos usuários

As funções não podem ser alteradas após serem atribuídas. Para alterar a função atribuída a um usuário, exclua o usuário e adicione-o novamente com uma função diferente.

### <a name="delete-users"></a>Excluir usuários

Para excluir usuários, marque uma ou mais caixas de seleção na página **Usuários**. Em seguida, selecione **Excluir**.

## <a name="view-your-bill"></a>Exibir a fatura

Para exibir a fatura, vá até a página **Cobrança** na seção **Administração**. Em seguida, escolha **Cobrança**. A página de cobrança do Azure abrirá em uma nova guia e você poderá exibir a fatura de cada um dos aplicativos do Azure IoT Central.

## <a name="convert-your-trial-to-a-paid-application"></a>Converter a versão de avaliação para um aplicativo pago

Após avaliar o IoT Central, você poderá converter a versão de avaliação para um aplicativo pago. Para concluir esse processo de autoatendimento, execute estas etapas:

1. Use o menu de navegação secundário para ir até a página **Cobrança** na seção **Administração**. Caso não tenha estendido a versão de avaliação, a página será semelhante à seguinte captura de tela:

    ![Estado de avaliação gratuita](media/howto-administer/freetrial.png)

2. Escolha **Converter para Pago**. Caso não tenha estendido a versão de avaliação, a janela pop-up será semelhante à seguinte captura de tela:

    ![Estender a avaliação gratuita](media/howto-administer/extend.png)

3. Na janela pop-up, escolha o locatário apropriado do Azure Active Directory e, em seguida, a assinatura do Azure a ser usada para o aplicativo IoT Central.

3. Após escolher **Converter**, a versão de avaliação será convertida em um aplicativo pago e você começará a ser cobrado.

## <a name="extend-your-free-trial"></a>Estender a avaliação gratuita

Por padrão, todas as avaliações gratuitas estão disponíveis por sete dias. Se você quiser ampliar a versão de avaliação para 30 dias, execute estas etapas:

1. Use o menu de navegação secundário para ir até a página **Cobrança** na seção **Administração**.

1. Escolha **Estender Avaliação**. Na janela pop-up, escolha o locatário apropriado do Azure Active Directory e, em seguida, a assinatura do Azure a ser usada para o aplicativo IoT Central.

1. Em seguida, escolha **Estender**. A avaliação agora é válida por 30 dias.

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>Usar SDKs do Azure em operações do plano de controle

Os pacotes do SDK do IoT Central Azure Resource Manager estão disponíveis para Node, Python, C#, Ruby, Java e Go. Essas bibliotecas suportam operações de plano de controle para o IoT Central e permitem criar, listar, atualizar ou excluir aplicativos do IoT Central. Eles também oferecem auxílio para tratar da autenticação e do tratamento de erros específicos de cada idioma. 

Veja exemplos de como usar os SDKs do Azure Resource Manager em [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Para saber mais, dê uma olhada nestes pacotes no GitHub.

| Linguagem | Repositório | Pacote |
| ---------| ---------- | ------- |
| Nó | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como administrar o aplicativo Azure IoT Central, veja a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Configurar o modelo de dispositivo](howto-set-up-template.md)
