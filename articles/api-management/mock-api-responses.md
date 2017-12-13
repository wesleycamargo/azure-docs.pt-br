---
title: Simular respostas de API com o Portal do Azure | Microsoft Docs
description: "Este tutorial mostra como usar o APIM (Gerenciamento de API) para definir uma política em uma API para que ela retorne uma resposta fictícia. Este método permite que os desenvolvedores prossigam com a implementação e teste da instância de Gerenciamento de API no caso de o back-end não estar disponível para enviar respostas reais."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: e485071b026c52eb23532639546ad475fc92cde3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="mock-api-responses"></a>Simular respostas de API

As APIs de back-end podem ser importadas em uma API de APIM ou criadas e gerenciadas manualmente. As etapas neste tutorial mostram como usar o APIM para criar uma API em branco e gerenciá-la manualmente. O tutorial mostra como definir uma política em uma API para que ela retorne uma resposta fictícia. Este método permite que os desenvolvedores prossigam com a implementação e teste da instância de APIM mesmo se o back-end não está disponível para enviar respostas reais. A capacidade de simular respostas pode ser útil em diversos cenários:

+ Quando a fachada da API é criada primeiro e a implementação do back-end ocorre posteriormente. Ou, o back-end está sendo desenvolvido paralelamente.
+ Quando o back-end temporariamente não está operacional ou não pode ser escalado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma API de teste 
> * Adicionar uma operação à API de teste
> * Habilitar a simulação de resposta
> * Testar a API fictícia

![Resposta de operação fictícia](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Pré-requisitos

Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-test-api"></a>Criar uma API de teste 

As etapas nesta seção mostram como criar uma API em branco sem back-end. Elas também mostram como adicionar uma operação à API. Chamar a operação depois de concluir as etapas nesta seção produz um erro. Você não receberá erros após concluir as etapas na seção “Habilitar a simulação de resposta”.

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. No menu à esquerda, selecione **+ Adicionar API**.
3. Selecione **API em Branco** na lista.
4. Insira "*API de Teste*" para **Nome de exibição**.
5. Insira "*Ilimitado*" para **Produtos**.
6. Selecione **Criar**.

## <a name="add-an-operation-to-the-test-api"></a>Adicionar uma operação à API de teste

1. Selecione a API que você criou na etapa anterior.
2. Clique em **+ Adicionar Operação**.

    ![Resposta de operação fictícia](./media/mock-api-responses/mock-api-responses02.png)

    |Configuração|Valor|Descrição|
    |---|---|---|
    |**URL** (verbo HTTP)|GET|Você pode escolher entre um dos verbos HTTP predefinidos.|
    |**URL** |*/test*|Um caminho de URL para a API. |
    |**Nome de exibição**|*Chamada de teste*|O nome exibido no **Portal do desenvolvedor**.|
    |**Descrição**||Forneça uma descrição para a operação que é usada para fornecer a documentação para os desenvolvedores usando essa API no **Portal do desenvolvedor**.|
    |Guia **Consulta**||Você pode adicionar parâmetros de consulta. Além de fornecer um nome e uma descrição, você pode fornecer valores que podem ser atribuídos a esse parâmetro. Um dos valores pode ser marcado como padrão (opcional).|
    |Guia **Solicitação**||Você pode definir esquemas, exemplos e tipos de conteúdo de solicitação. |
    |Guia **Resposta**|Consulte as etapas após essa tabela.|Defina códigos de status de resposta, tipos de conteúdo, exemplos e esquemas.|

3. Selecione a guia **Resposta**, localizada os campos URL, Nome de exibição e Descrição.
4. Clique em **+ Adicionar resposta**.
5. Selecione **200 OK** na lista.
6. Sob o cabeçalho **Representações** à direita, selecione **+ Adicionar representação**.
7. Insira "*application/json*" na caixa de pesquisa e selecione o tipo de conteúdo **application/json**.
8. Na caixa de texto **Exemplo**, digite "*{ 'sampleField' : 'test' }*".
9. Selecione **Salvar**.

## <a name="enable-response-mocking"></a>Habilitar a simulação de resposta

1. Selecione a API que você criou na etapa "Criar uma API de teste".
2. Selecione a operação de teste que você adicionou.
2. Na janela à direita, clique na guia **Design**.
3. Na janela **Processamento de entrada**, clique no ícone de lápis.
4. Na guia **Mocking**, selecione **Respostas estáticas** para **Comportamento do Mocking**.
5. Na caixa de texto **API Management returns the following response: (O Gerenciamento de API retorna a seguinte resposta:)**, digite **200 OK, application/json**. Essa seleção indica que a sua API deve retornar o exemplo de resposta definido na seção anterior.
6. Selecione **Salvar**.

## <a name="test-the-mocked-api"></a>Testar a API fictícia

1. Selecione a API que você criou na etapa "Criar uma API de teste".
2. Abra a guia **Testar**.
3. Certifique-se de que a API **Chamada de teste** esteja selecionada.

    > [!TIP]
    > Uma barra amarela com o texto **Mocking is enabled (A simulação está habilitada)** indica que as respostas retornadas do Gerenciamento de API enviam uma política fictícia e não uma resposta de back-end real.

3. Selecione **Enviar** para fazer uma chamada de teste.
4. A **Resposta HTTP** exibe o JSON fornecido como um exemplo na primeira seção desse tutorial.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]
> 
> 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma API de teste
> * Adicionar uma operação à API de teste
> * Habilitar a simulação de resposta
> * Testar a API fictícia

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)
