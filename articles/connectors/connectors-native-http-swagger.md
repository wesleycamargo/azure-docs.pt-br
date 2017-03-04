
---
title: "Adicionar a ação de HTTP + Swagger em Aplicativos Lógicos | Microsoft Docs"
description: "Visão geral das operações e da ação de HTTP + Swagger"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c0edbe421050ad46f6d31fd6416df4b344b233ad
ms.openlocfilehash: ade380b7fc6adfb929f42c0e6c75b3fa613c45b1
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-http--swagger-action"></a>Introdução à ação de HTTP + Swagger
Com a ação de HTTP + Swagger, você pode criar um conector de primeira classe para qualquer ponto de extremidade REST por meio de um [documento Swagger](https://swagger.io). Você também pode estender um aplicativo lógico para chamar qualquer ponto de extremidade REST com uma experiência de Designer de Aplicativo Lógico de primeira classe.

Para começar com a ação HTTP + Swagger em um aplicativo lógico, confira [Criar um novo aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

- - -
## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Usar HTTP + Swagger como um gatilho ou uma ação
As funções de gatilho e ação HTTP + Swagger funcionam da mesma forma que a [ação HTTP](connectors-native-http.md), porém, proporcionam uma melhor experiência de design, mostrando a forma da API e as saídas no designer dos [metadados do Swagger](https://swagger.io). Além disso, você pode usar HTTP + Swagger como um gatilho. Se você quiser implementar um gatilho de sondagem, ele deverá seguir o padrão de pesquisa descrito em [Criando uma API personalizada para usar com aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

[Saiba mais sobre gatilhos e ações de aplicativo lógico.](connectors-overview.md)

Veja um exemplo de como usar a operação HTTP + Swagger como uma ação em um fluxo de trabalho em um aplicativo lógico.

1. Selecione o botão **Nova Etapa** .
2. Escolha **Adicionar uma ação**.
3. Na caixa de pesquisa de ação, digite **swagger** para listar a ação de HTTP + Swagger.
   
    ![Selecionar ação de HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)
4. Digite a URL para um documento do Swagger:
   
   * Para trabalhar do Designer de Aplicativo Lógico, a URL deverá ser um ponto de extremidade HTTPS e ter CORS habilitado.
   * Se o documento do swagger não atender a este requisito, você poderá usar [o Armazenamento do Azure com CORS habilitado](#hosting-swagger-from-storage) para armazenar o documento.
5. Clique em **Avançar** para leitura e renderização do documento do Swagger.
6. Adicione todos os parâmetros necessários para a chamada HTTP.
   
    ![Concluir a ação HTTP](./media/connectors-native-http-swagger/using-action-2.png)
7. Clique em **Salvar** no canto superior esquerdo da barra de ferramentas e seu aplicativo lógico será salvo e publicado (ativado).

### <a name="host-swagger-from-azure-storage"></a>Hospedar o Swagger do Armazenamento do Azure
Talvez você queira fazer referência a um documento do Swagger que não esteja hospedado ou que não atenda aos requisitos de segurança e entre origens do designer. Para resolver esse problema, você pode armazenar o documento do Swagger no Armazenamento do Azure e habilitar o CORS para fazer referência ao documento.  

Veja as etapas para criar, configurar e armazenar documentos do Swagger no Armazenamento do Azure:

1. [Crie uma conta de armazenamento do Azure com o armazenamento de Blobs do Azure](../storage/storage-create-storage-account.md). (Para fazer isso, defina as permissões como **Acesso Público**).
2. Habilite o CORS no blob. Você pode usar [este script do PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) para definir a configuração automaticamente.
3. Carregue o arquivo do Swagger no blob. Você pode fazer isso no [portal do Azure](https://portal.azure.com) ou em uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/).
4. Faça referência a um link HTTPS para o documento no armazenamento de Blobs do Azure. (O link segue o formato `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`).

## <a name="technical-details"></a>Detalhes técnicos
A seguir, os detalhes dos gatilhos e das ações com suporte deste conector HTTP + Swagger.

## <a name="http--swagger-triggers"></a>Gatilhos de HTTP + Swagger
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos.](connectors-overview.md) O conector HTTP + Swagger tem um gatilho.

| Gatilho | Descrição |
| --- | --- |
| HTTP + Swagger |Faz uma chamada HTTP e retorna o conteúdo da resposta |

## <a name="http--swagger-actions"></a>Ações de HTTP + Swagger
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações.](connectors-overview.md) O conector HTTP + Swagger tem uma ação possível.

| Ação | Descrição |
| --- | --- |
| HTTP + Swagger |Faz uma chamada HTTP e retorna o conteúdo da resposta |

### <a name="action-details"></a>Detalhes da ação
O conector HTTP + Swagger vem uma ação possível. A seguir, as informações sobre cada uma das ações, seus campos de entrada obrigatórios e opcionais, bem como os detalhes da saída correspondente associada ao uso delas.

#### <a name="http--swagger"></a>HTTP + Swagger
Faça uma solicitação de saída HTTP com a assistência dos metadados do Swagger.
Um asterisco (*) significa um campo obrigatório.

| Nome de exibição | Nome da propriedade | Descrição |
| --- | --- | --- |
| Método* |estático |Verbo HTTP a ser usado. |
| URI* |uri |URI da solicitação HTTP. |
| Cabeçalhos |Cabeçalhos |Um objeto JSON de cabeçalhos HTTP a serem incluídos. |
| Corpo |Corpo |O corpo da solicitação HTTP. |
| Autenticação |Autenticação |A autenticação a ser usada para solicitação. [Para obter mais detalhes, consulte HTTP](connectors-native-http.md#authentication). |

**Detalhes de saída**

Resposta HTTP

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| headers |objeto |Cabeçalhos de resposta |
| Corpo |objeto |Objeto de resposta |
| Código de status |int |Código de status HTTP |

### <a name="http-responses"></a>Respostas HTTP
Ao fazer chamadas a várias ações, você pode obter determinadas respostas. A seguir, uma tabela que descreve respostas e descrições correspondentes.

| Name | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceita |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro interno do servidor. Ocorreu um erro desconhecido. |

- - -
## <a name="next-steps"></a>Próximas etapas
Teste a plataforma e [crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) agora mesmo. Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).


