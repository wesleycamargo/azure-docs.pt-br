---
title: Compilar um classificador com o Serviço de Visão Personalizada – Serviços Cognitivos do Azure | Microsoft Docs
description: Saiba como usar o Serviço de Visão Personalizada para criar um classificador que consiga discernir objetos em fotografias.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: c5183078d2f9d5eb16abef4f5df240f77eea6b8b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223362"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Como compilar um classificador com Visão Personalizada

Para usar o Serviço de Visão Personalizada, primeiro você deve compilar um classificador. Neste documento, saiba como compilar um classificador por meio de seu navegador da Web.

## <a name="prerequisites"></a>Pré-requisitos

Para compilar um classificador, primeiro você deve ter:

- Uma [conta Microsoft](https://account.microsoft.com/account) válida ou uma OrgID do Azure Active Directory (“conta corporativa ou de estudante”) para que você possa entrar no customvision.ai e começar.

    > [!IMPORTANT] 
    > Atualmente, não há suporte para logon da OrgID para usuários do Azure Active Directory (Azure AD) das [nuvens nacionais](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud).

- Uma série de imagens para treinar seu classificador (com um mínimo de 30 imagens por marca).

- Algumas imagens para testar seu classificador depois que ele for treinado.

- Opcional: uma assinatura do Azure associada à sua conta Microsoft ou OrgID. Se você não tiver uma assinatura do Azure, pode criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

    > [!IMPORTANT]
    > Sem uma assinatura do Azure, você só poderá criar projetos de __avaliação limitada__. Se tiver uma assinatura do Azure, você será solicitado a criar recursos de treinamento e previsão do Serviço de Visão Personalizada no [portal do Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) durante a criação do projeto.   

## <a name="create-a-new-project"></a>Criar um novo projeto

Para criar um novo projeto, use as etapas a seguir:

1. No navegador da Web, navegue até a [página da Web da Visão Personalizada](https://customvision.ai). Selecione __Entrar__ para começar a usar o serviço.

    ![Imagem da página de entrada](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > Depois de entrar no Serviço de Visão Personalizada, é apresentada uma lista de projetos. Fora dos dois projetos de “avaliação limitada” para teste, os projetos são associados um recurso do Azure. Se você for um usuário do Azure, verá todos os projetos associados aos [recursos do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) aos quais tem acesso. 

2. Para criar seu primeiro projeto, selecione **Novo Projeto**. No primeiro projeto, você será solicitado a aceitar os Termos de serviço. Marque a caixa de seleção e selecione o botão **Concordo**. A caixa de diálogo **Novo Projeto** aparecerá.

    ![A caixa de diálogo do novo projeto tem campos para nome, descrição e domínios.](./media/getting-started-build-a-classifier/new-project.png)

3. Insira um nome e uma descrição para o projeto. Depois selecione um dos domínios disponíveis. Cada domínio otimiza o classificador para tipos específicos de imagens, conforme descrito na tabela a seguir:

    |Domínio|Finalidade|
    |---|---|
    |__Genérico__| Otimizado para uma ampla gama de tarefas de classificação de imagens. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio Genérico. |
    |__Alimentos__|Otimizado para fotografias de pratos como você os veria no menu de um restaurante. Caso queira classificar fotografias de frutas ou legumes individuais, use o domínio Alimentos.|
    |__Pontos de referência__|Otimizado para pontos de referência reconhecidos, tanto naturais quanto artificiais. Este domínio funciona melhor quando o ponto de referência está claramente visível na fotografia. Este domínio funciona mesmo quando o ponto de referência está um pouco obstruído devido a pessoas na frente dele.|
    |__Varejo__|Otimizado para imagens encontradas em um catálogo ou site de compras. Caso deseje uma classificação de alta precisão entre vestidos, calças e camisas, use esse domínio.|
    |__Adulto__|Otimizado para oferecer uma melhor definição entre conteúdo adulto e não adulto. Por exemplo, se você quiser bloquear imagens de pessoas em trajes de banho, esse domínio permite que você crie um classificador personalizado para fazer isso.|
    |__Domínios compactos__| Otimizados para as restrições de classificação em tempo real em dispositivos móveis. Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente.|

    Você pode alterar o domínio posteriormente se desejar.

4. Selecione um Grupo de recursos. O menu suspenso Grupo de Recursos mostra todos os Grupos de Recursos do Azure, que incluem um Recurso do Serviço de Visão Personalizada. Você também pode criar selecionando __avaliação limitada__. A entrada de avaliação limitada é o único grupo de recursos que um não usuário do Azure poderá escolher.

    Para criar o projeto, selecione __Criar projeto__.

## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

1. Para adicionar imagens ao classificador, use o botão __Adicionar imagens__, depois selecione __Procurar arquivos locais__. Selecione __Abrir__ para mover para a marcação.

    > [!TIP]
    > Depois de selecionar as imagens, você deve marcá-las. A marca é aplicada ao grupo de imagens que você selecionou para ser carregado, portanto, pode ser mais fácil carregar imagens com marcas que você planeja usar. Você também pode alterar a marca das imagens selecionadas depois que elas forem marcadas e carregadas.

    > [!TIP]
    > Carregue imagens com diferentes ângulos de câmera, iluminação, plano de fundo, tipos, estilos, grupos, tamanhos, etc. Use uma variedade de tipos de foto para garantir que o classificador não seja desviado e possa generalizar bem.

    O Serviço de Visão Personalizada aceita imagens de treinamento em formato .jpg, .png e .bmp, até 6 MB por imagem. (Imagens de previsão podem ter até 4 MB por imagem.) É recomendável que imagens tenham 256 pixels na borda mais curta. Qualquer imagem de menos de 256 pixels na borda menor é escalada verticalmente pelo Serviço de Visão Personalizada.

    ![O controle de adição de imagens é mostrado no canto superior esquerdo e como um botão na parte inferior central.](./media/getting-started-build-a-classifier/add-images01.png)

    >[!NOTE] 
    > A API REST pode ser usada para carregar as imagens de treinamento de URLs.

2. Para definir a marca, insira o texto no campo __Minhas Marcas__, depois use o botão __+__. Para carregar as imagens e marcá-las, use o botão __Carregar [número] arquivos__. Você pode adicionar mais de uma marca às imagens. 

    > [!NOTE]
    > O tempo de carregamento varia de acordo com o número e o tamanho das imagens selecionadas.

    ![Imagem da página de marca e upload](./media/getting-started-build-a-classifier/add-images03.png)

3. Selecione __Concluído__ depois que as imagens tiverem sido carregadas.

    ![A barra de progresso mostra todas as tarefas concluídas.](./media/getting-started-build-a-classifier/add-images04.png)

4. Para carregar outro conjunto de imagens, retorne à Etapa 1. Por exemplo, caso deseje diferenciar cachorros e pôneis, carregue e marque as imagens de pôneis.

## <a name="train-and-evaluate-the-classifier"></a>Treinar e avaliar o classificador

Para treinar o classificador, selecione o botão **Treinar**.

![O botão de treinamento fica perto da parte superior direita da janela do navegador.](./media/getting-started-build-a-classifier/train01.png)

Leva apenas alguns minutos para treinar o classificador. Durante esse tempo, as informações sobre o processo de treinamento são exibidas.

![O botão de treinamento fica perto da parte superior direita da janela do navegador.](./media/getting-started-build-a-classifier/train02.png)

Depois do treinamento, o __Desempenho__ é exibido. Os indicadores de precisão e recuperação informam a você a qualidade do classificador com base nos testes automáticos. O Serviço de Visão Personalizada usa as imagens que você enviou para treinamento para calcular esses números por meio de um processo chamado [validação cruzada k vezes](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![Os resultados de treinamento mostram a precisão e recuperação geral e de cada marca no classificador.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Cada vez que você selecionar o botão **Treinar**, você cria uma nova iteração do seu classificador. Você pode exibir todas as iterações antigas na guia **Desempenho** e pode excluir as que podem estar obsoletas. Ao excluir uma iteração, você acaba excluindo imagens associadas exclusivamente a ela.

O classificador usa todas as imagens para criar um modelo que identifica cada marca. Para testar a qualidade do modelo, o classificador testa cada imagem no modelo para ver o que o modelo localiza.

As qualidades do classificador de resultados são exibidas.

|Termo|Definição|
|---|---|
|__Precisão__|Quando você classifica uma imagem, qual é a probabilidade de seu classificador classificar corretamente a imagem? De todas as imagens usadas para treinar o classificador (cachorros e pôneis), qual porcentagem o modelo identificou corretamente? 99 marcas corretas em 100 imagens oferece uma precisão de 99%.|
|__Recuperação__|De todas as imagens que deveriam ter sido classificadas corretamente, quantas delas seu classificador identificou corretamente? Uma recuperação de 100% significa que, se houver 38 imagens de cachorro nas imagens usadas para treinar o classificador, o classificador encontrou 38 cachorros.|

## <a name="next-steps"></a>Próximas etapas

[Testar e retreinar o modelo](test-your-model.md)

