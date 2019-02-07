---
title: Administrar um aplicativo Azure IoT Central | Microsoft Docs
description: Como um administrador, saiba como administrar o aplicativo Azure IoT Central
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ebca3060e902c0a45619c2bd64240537cd95308d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811844"
---
# <a name="administer-your-iot-central-application"></a>Administrar seu aplicativo IoT Central

Depois de criar um aplicativo IoT Central, você pode ir para a seção **Administração** para:

- Gerenciar configurações de aplicativo
- Gerenciar usuários
- Gerenciar funções
- Exibir a fatura
- Converter sua avaliação para pago pelo uso
- Exportar dados
- Gerenciar conexão do dispositivo
- Usar tokens de acesso

Para acessar e usar a seção **Administração**, você deve estar na função **Administrador** no aplicativo Azure IoT Central. Se você criar um aplicativo Azure IoT Central, você será automaticamente atribuído à função **Administrador** desse aplicativo. A seção [Gerenciar Usuários](#manage-users) deste artigo explica mais sobre como atribuir a função **Administrador** a outros usuários.

## <a name="manage-application-settings"></a>Gerenciar configurações de aplicativo

### <a name="change-application-name-and-url"></a>Alterar o nome do aplicativo e URL
Na página **Configurações do aplicativo**, você pode alterar o nome e o URL do seu aplicativo e, em seguida, selecionar **Salvar**.

![Página de configurações do aplicativo](media/howto-administer-experimental/image0-a.png)

> [!Note]
> Se você alterar a URL, a URL antiga poderá ser utilizada por outro cliente do Azure IoT Central. Nesse caso, a URL não estará mais disponível para você usar. Ao alterar a URL, a URL antiga não funcionará mais e você deverá notificar os usuários sobre a nova URL a ser utilizada.

### <a name="prepare-and-upload-image"></a>Preparar e carregar a imagem
Para alterar a imagem do aplicativo, consulte [Preparar e carregar imagens no aplicativo Microsoft IoT Central](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

### <a name="copy-an-application"></a>Copiar um aplicativo
É possível criar uma cópia de qualquer aplicativo, menos quaisquer instâncias de dispositivos, histórico de dados do dispositivo e os dados de usuário. A cópia será um aplicativo de Pagamento Conforme o Uso pelo qual você será cobrado. Você não pode criar um aplicativo de avaliação dessa maneira.

Clique no botão **Copiar**. Na caixa de diálogo, insira os detalhes do novo aplicativo de pagamento Conforme o Uso. Em seguida, clique no botão **Copiar** para confirmar que você deseja continuar. Saiba mais sobre os campos deste formulário em [Criar um início rápido de aplicativo](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

![Página de configurações do aplicativo](media/howto-administer-experimental/appCopy2.png)

Depois que a operação de cópia do aplicativo for bem-sucedida, você poderá ir para o novo aplicativo que foi criado copiando seu aplicativo usando o link exibido.

![Página de configurações do aplicativo](media/howto-administer-experimental/appCopy3.png)

> [!Note]
> Copiar um aplicativo também copia a definição de regras e ações. Mas como os usuários que têm acesso ao seu aplicativo original não são copiados para o aplicativo copiado, você precisa adicionar manualmente os usuários a ações como e-mail para os quais os usuários são um pré-requisito. Em geral, é uma boa ideia verificar as regras e ações para se certificar de que estão atualizadas no novo aplicativo.

### <a name="delete-an-application"></a>Excluir um aplicativo

> [!Note]
> Para excluir um aplicativo, você também deve ter as permissões para excluir recursos da assinatura do Azure escolhida ao criar o aplicativo. Para saber mais, confira [Usar o controle de acesso baseado em função para gerenciar o acesso aos recursos de sua assinatura do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Use o botão **Excluir** para excluir permanentemente seu aplicativo IoT Central. Isso excluirá permanentemente todos os dados associados a esse aplicativo.

## <a name="manage-users"></a>Gerenciar usuários

### <a name="add-users"></a>Adicionar usuários

Cada usuário deve ter uma conta de usuário antes de poder entrar e acessar um aplicativo Azure IoT Central. As MSAs (Contas Microsoft) e contas do Azure AD (Azure Active Directory) têm suporte no Azure IoT Central. Atualmente, os grupos do Azure Active Directory não têm suporte no Azure IoT Central.

Para obter mais informações, consulte [Ajuda da conta da Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Início rápido: Adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Para adicionar um usuário a um aplicativo IoT Central, acesse a página **Usuários** na seção **Administração**.

    ![Lista de usuários](media/howto-administer-experimental/image1.png)

1. Na página **Usuários**, escolha **+ Adicionar usuário** para adicionar um usuário.

1. Escolha uma função para o usuário na lista suspensa **Função**. Saiba mais sobre as funções na seção [Gerenciar funções](#manage-roles) deste artigo.

    ![Seleção de função](media/howto-administer-experimental/image3.png)

    > [!NOTE]
    >  Para adicionar usuários em massa, insira as IDs de usuário de todos os usuários que você deseja adicionar separados por ponto e vírgula. Escolha uma função na lista suspensa **Função**. Em seguida, selecione **Salvar**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Editar as funções atribuídas aos usuários

As funções não podem ser alteradas após serem atribuídas. Para alterar a função atribuída a um usuário, exclua o usuário e adicione-o novamente com uma função diferente.

### <a name="delete-users"></a>Excluir usuários

Para excluir usuários, marque uma ou mais caixas de seleção na página **Usuários**. Em seguida, selecione **Excluir**.

## <a name="manage-roles"></a>Gerenciar funções

As funções permitem controlar quem na sua organização pode executar várias tarefas na IoT Central. Existem três funções que você pode atribuir aos usuários do seu aplicativo. 

### <a name="administrator"></a>Administrador

Os usuários na função **administrador** tem acesso a toda a funcionalidade em um aplicativo.

O usuário que cria um aplicativo é automaticamente designado à função **Administrador**. Sempre deverá haver pelo menos um usuário na função **Administrador**.

### <a name="application-builder"></a>Construtor de Aplicativos

Os usuários na função **Application Builder** podem fazer tudo em um aplicativo, exceto administrar o aplicativo. Isso significa que os desenvolvedores podem criar, editar e excluir modelos e dispositivos de dispositivos, gerenciar conjuntos de dispositivos e executar análises e tarefas. Os desenvolvedores não terão acesso à seção **Administração** do aplicativo.

### <a name="application-operator"></a>Operador de Aplicativos

Os usuários na função **Application Operator** não podem fazer alterações nos modelos de dispositivo e não podem administrar o aplicativo. Isso significa que os operadores podem adicionar e excluir dispositivos, gerenciar conjuntos de dispositivos e executar análises e trabalhos. Os operadores não terão acesso às páginas **Application Builder** e **Administration**.


## <a name="view-your-bill"></a>Exibir a fatura

Para exibir a fatura, vá até a página **Cobrança** na seção **Administração**. A página de cobrança do Azure abrirá em uma nova guia e você poderá exibir a fatura de cada um dos aplicativos do Azure IoT Central.

### <a name="convert-your-trial-to-pay-as-you-go"></a>Converter sua avaliação para pago pelo uso

Você pode converter seu aplicativo de teste em um aplicativo de Pagamento Conforme o Uso. Aqui estão as diferenças entre esses tipos de aplicativos.

- **Avaliação** aplicativos são gratuitos por 7 dias antes de expirarem. Eles podem ser convertidos em Pagamento Conforme o Uso em qualquer momento antes de expirarem.
- Os aplicativos **Pagamento Conforme o Uso** são cobrados por dispositivo, com os primeiros 5 dispositivos gratuitos.

Saiba mais sobre preços na [Página de preços da microsoft IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).
    
Para concluir esse processo de autoatendimento, execute estas etapas:

1. Vá para a página **Faturamento** na seção **Administração**. 

    ![Estado de avaliação](media/howto-administer-experimental/freetrialbilling.png)

1. Clique em **Converter Pagamento Conforme o Uso**. 

    ![Converter a versão de avaliação](media/howto-administer-experimental/convert.png)

1. Selecione o Microsoft Azure Active Directory apropriado do Azure e, em seguida, a assinatura do Azure a ser usada em seu aplicativo Pagamento Conforme o Uso.

1. Depois de clicar em **Converter**, seu aplicativo agora é um aplicativo pré-pago e você começa a ser cobrado.

## <a name="export-data"></a>Exportar dados

Você pode habilitar a **Exportação contínua de dados** para exportar dados de medições, dispositivos e modelos de dispositivo para sua conta de armazenamento do Azure Blob. Saiba mais sobre [como exportar seus dados](#howto-export-data).

## <a name="manage-device-connection"></a>Gerenciar conexão do dispositivo

Conecte dispositivos em escala em seu aplicativo usando as chaves e os certificados aqui. Saiba mais sobre [conectar dispositivos](#concepts-connectivity).

## <a name="use-access-tokens"></a>Usar tokens de acesso

Gere tokens de acesso para usá-los nas ferramentas do desenvolvedor. Atualmente, há uma ferramenta de desenvolvedor disponível, que é o explorer IoT Central para monitorar mensagens de dispositivos e alterações em propriedades e configurações. Saiba mais sobre o [Microsoft IoT Central explorer](#howto-use-iotc-explorer). 

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
> [Configurar o modelo de dispositivo](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
