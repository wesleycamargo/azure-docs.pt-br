---
title: "Criar fluxos de trabalho para processar emails e anexos - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Este tutorial mostra como criar fluxos de trabalho automatizados para processamento de emails e anexos com os Aplicativos Lógicos do Azure, o Armazenamento do Azure e o Azure Functions"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 210731ce2e792452650b7a92cfc542c78a0e8014
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="process-emails-and-attachments-with-a-logic-app"></a>Processar emails e anexos com um aplicativo lógico

Os Aplicativos Lógicos do Azure ajudam a automatizar fluxos de trabalho e a integrar os dados entre os serviços do Azure, os serviços da Microsoft, outros aplicativos SaaS (software como serviço) e sistemas locais. Este tutorial mostra como você pode criar um [aplicativo lógico](../logic-apps/logic-apps-overview.md) que trata a emails recebidos e anexos. Esse aplicativo lógico processa esse conteúdo, salva-o no armazenamento do Azure e envia notificações para revisão do conteúdo. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o [Armazenamento do Azure](../storage/common/storage-introduction.md) e o Gerenciador de Armazenamento para verificar emails e anexos.
> * Criar uma [função do Azure](../azure-functions/functions-overview.md) que remova o HTML dos emails. Este tutorial inclui o código que você pode usar para essa função.
> * Criar um aplicativo lógico em branco.
> * Adicionar um gatilho que monitora anexos nos emails.
> * Adicionar uma condição que verifica se os emails têm anexos.
> * Adicionar uma ação que chama a função do Azure quando um email tem anexos.
> * Adicionar uma ação que cria blobs de armazenamento para emails e anexos.
> * Adicionar uma ação que envia notificações por email.

Quando terminar, o aplicativo lógico ficará parecido com este fluxo de trabalho em alto nível:

![Aplicativo lógico concluído em alto nível](./media/tutorial-process-email-attachments-workflow/overview.png)

Caso você não tenha uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">crie uma conta gratuita do Azure</a> antes de começar. 

## <a name="prerequisites"></a>pré-requisitos

* Uma conta de email de qualquer provedor de email compatível com os Aplicativos Lógicos, como o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros provedores, [revise a lista de conectores aqui](https://docs.microsoft.com/connectors/).

  Esso aplicativo lógico usa uma conta do Outlook do Office 365. 
  Se você usar uma conta de email diferente, as etapas gerais serão as mesmas, mas a interface do usuário poderá parecer um pouco diferente.

* Baixe e instale o <a href="http://storageexplorer.com/" target="_blank">Gerenciador de Armazenamento do Microsoft Azure</a> gratuito. Essa ferramenta ajuda a verificar se o contêiner de armazenamento está configurado corretamente.

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure

Entre no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com suas credenciais da conta do Azure.

## <a name="set-up-storage-to-save-attachments"></a>Configurar armazenamento para salvar anexos

Você pode salvar emails recebidos e anexos como blobs em um [contêiner de armazenamento do Azure](../storage/common/storage-introduction.md). 

1. Antes de criar um contêiner de armazenamento, [crie uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) com estas configurações:

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **Nome** | attachmentstorageacct | O nome da conta de armazenamento | 
   | **Modelo de implantação** | Gerenciador de recursos | O [modelo de implantação](../azure-resource-manager/resource-manager-deployment-model.md) para gerenciar a implantação de recursos | 
   | **Tipo de conta** | Propósito geral | O [tipo de conta de armazenamento](../storage/common/storage-introduction.md#types-of-storage-accounts) | 
   | **Desempenho** | Standard | Essa configuração especifica os tipos de dados com suporte e a mídia para armazenar dados. Confira os [Tipos de contas de armazenamento](../storage/common/storage-introduction.md#types-of-storage-accounts). | 
   | **Replicação** | Armazenamento com redundância local (LRS) | Essa configuração especifica como os dados são copiados, armazenados, gerenciados e sincronizados. Confira [Replicação](../storage/common/storage-introduction.md#replication). | 
   | **Transferência segura é necessária** | Desabilitado | Essa configuração especifica a segurança necessária para solicitações de conexões. Confira [Requer transferência segura](../storage/common/storage-require-secure-transfer.md). | 
   | **Assinatura** | <*nome-da-sua-assinatura-do-Azure*> | O nome e a ID da assinatura do Azure | 
   | **Grupo de recursos** | LA-Tutorial-RG | O nome do [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usado para organizar e gerenciar os recursos relacionados. <p>**Observação:** um grupo de recursos reside dentro de uma região específica. Embora os itens neste tutorial possam não estar disponíveis em todas as regiões, tente usar a mesma região sempre que possível. | 
   | **Localidade** | Leste dos EUA 2 | A região na qual armazenar informações sobre sua conta de armazenamento | 
   | **Configurar redes virtuais** | Desabilitado | Para este tutorial, mantenha a configuração **Desabilitada**. | 
   |||| 

   Você também pode usar o [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) ou a [CLI do Azure](../storage/common/storage-quickstart-create-storage-account-cli.md).
  
2. Depois que o Azure implanta sua conta de armazenamento, obtenha a chave de acesso da sua conta de armazenamento:

   1. No menu da sua conta de armazenamento em **Configurações**, escolha **Chaves de acesso**. 
   2. Localize **key1** nas **Chaves padrão** e o nome da sua conta de armazenamento.

      ![Copie e salve a chave e o nome da conta de armazenamento](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Você também pode usar o [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) ou a [CLI do Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az_storage_account_keys_list). 

3. Crie um contêiner de armazenamento para os anexos de email.
   
   1. No menu da conta de armazenamento, no painel **Visão Geral**, escolha **Blobs** em **Serviços** e escolha **+ Contêiner**.

   2. Insira "anexos" como o nome do contêiner. Em **Nível de acesso público**, selecione **Contêiner (acesso de leitura anônimo para contêineres e blobs)** e escolha **OK**.

   Você também pode usar o [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) ou a [CLI do Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_create). 
   Quando estiver pronto, você pode encontrar seu contêiner de armazenamento na conta de armazenamento no portal do Azure:

   ![Contêiner de armazenamento concluído](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

Em seguida, conecte o Gerenciador de Armazenamento à conta de armazenamento.

## <a name="set-up-storage-explorer"></a>Configurar o Gerenciador de Armazenamento

Agora, conecte o Gerenciador de Armazenamento à conta de armazenamento para que você possa confirmar que seu aplicativo lógico está salvando anexos como blobs corretamente em seu contêiner de armazenamento.

1. Abra o Gerenciador de Armazenamento do Microsoft Azure. Quando o Gerenciador de Armazenamento solicita uma conexão com o Armazenamento do Azure, escolha **Usar um nome e de conta de armazenamento e uma chave** > **Avançar**.
Se nenhum aviso é exibido, escolha **Adicionar conta** na barra de ferramentas do gerenciador.

2. Em **Conectar usando o nome e a chave**, digite o nome da conta de armazenamento e a chave de acesso que você salvou anteriormente. Escolha **Avançar** > **Conectar**.

3. Verifique se sua conta de armazenamento e o aparecem corretamente no Gerenciador de Armazenamento:

   1. Em **Gerenciador**, expanda **(Local e Conectado)** > 
    **Contas de Armazenamento** > **attachmentstorageaccount** > 
    **Contêineres de Blob**.

   2. Confirme se o contêiner "anexos" é exibido agora. 
   Por exemplo: 

      ![Gerenciador de Armazenamento - confirmar contêiner de armazenamento](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Em seguida, crie uma [função do Azure](../azure-functions/functions-overview.md) que remova HTML do email de entrada.

## <a name="create-a-function-to-clean-html"></a>Criar uma função para limpar HTML

Agora, use o trecho de código fornecido por estas etapas para criar uma função do Azure que remova HTML de cada email recebido. Dessa forma, o conteúdo do email fica mais limpo e fácil de processar. Em seguida, você pode chamar essa função do seu aplicativo lógico.

1. Antes de criar uma função, [crie um aplicativo de funções](../azure-functions/functions-create-function-app-portal.md) com estas configurações:

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **Nome do aplicativo** | CleanTextFunctionApp | Um nome globalmente exclusivo e descritivo para o aplicativo de funções | 
   | **Assinatura** | <*nome-da-sua-assinatura-do-Azure*> | A mesma assinatura do Azure que você usou anteriormente | 
   | **Grupo de recursos** | LA-Tutorial-RG | O mesmo grupo de recursos do Azure que você usou anteriormente | 
   | **Plano de hospedagem** | Plano de consumo | Essa configuração determina como alocar e dimensionar recursos, como capacidade de computação, para executar seu aplicativo de funções. Confira [Comparação entre planos de hospedagem](../azure-functions/functions-scale.md). | 
   | **Localidade** | Leste dos EUA 2 | A mesma região que você usou anteriormente | 
   | **Armazenamento** | cleantextfunctionstorageacct | Crie uma conta de armazenamento para seu aplicativo de funções. Use apenas letras minúsculas e números. <p>**Observação:** a conta de armazenamento contém seus aplicativos de funções e é diferente da sua conta de armazenamento criada anteriormente para anexos de email. | 
   | **Application Insights** | Desativar | Ativa o monitoramento de aplicativos com o [Application Insights](../application-insights/app-insights-overview.md), mas, para este tutorial, mantenha a configuração **Desligada**. | 
   |||| 

   Se o seu aplicativo de funções não abrir automaticamente após a implantação, localize o aplicativo no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>. No menu principal do Azure, escolha **Serviços de Aplicativos** e selecione o aplicativo de funções.

   ![Aplicativo de funções criado](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Se o **Serviços de Aplicativos** não aparecer no menu do Azure, vá para **Mais serviços**. Na caixa Pesquisar, localize e selecione **Aplicativos de Funções**. Para saber mais, confira [Criar sua função](../azure-functions/functions-create-first-azure-function.md).

   Você também pode usar a [CLI do Azure](../azure-functions/functions-create-first-azure-function-azure-cli.md) ou os [modelos do PowerShell e do Gerenciador de Recursos](../azure-resource-manager/resource-group-template-deploy.md).

2. Em **Aplicativos de Funções**, expanda **CleanTextFunctionApp**e selecione **Funções**. Na barra de ferramentas de funções, escolha **+ Nova função**.

   ![Criar nova função](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. Em **Escolher um modelo abaixo ou ir para o início rápido**, selecione o modelo de função **HttpTrigger - C#**.

   ![Selecionar o modelo de função](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

4. Em **Nomear sua função**, digite ```RemoveHTMLFunction```. Em **Gatilho HTTP** > **Nível de autorização**, mantenha o valor padrão **Função** valor e escolha **Criar**.

   ![Nomear sua função](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. Depois de abrir o editor, substitua o código de modelo por esse código, que remove o HTML e retorna resultados para o chamador:

   ``` CSharp
   using System.Net;
   using System.Text.RegularExpressions;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await req.Content.ReadAsStringAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return req.CreateResponse(HttpStatusCode.OK, new { updatedBody });

   }
   ```

6. Quando terminar, escolha **Salvar**. Para testar sua função, escolha **Testar** no ícone de seta (**<**) na extremidade direita do editor. 

   ![Abra o painel “Testar”](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. No painel **Testar**, em **Corpo da Solicitação**, digite essa linha e escolha **Executar**.

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![Testar a função](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   A janela **saída** mostra esse resultado da função:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Depois de verificar que sua função funciona, crie o aplicativo lógico. Embora este tutorial mostre como criar uma função que remove o HTML de emails, os Aplicativos Lógicos também têm um conector **HTML para Texto**.

## <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

1. No menu principal do Azure, escolha **Novo** > **Enterprise Integration** > **Aplicativo Lógico**.

   ![Criar aplicativo lógico](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. Em **Criar aplicativo lógico**, forneça essas informações sobre seu aplicativo lógico, como mostrado e descrito. Quando tiver concluído, escolha **Fixar no painel** > **Criar**.

   ![Fornecer informações de aplicativo lógico](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **Nome** | LA-ProcessAttachment | O nome do seu aplicativo lógico | 
   | **Assinatura** | <*nome-da-sua-assinatura-do-Azure*> | A mesma assinatura do Azure que você usou anteriormente | 
   | **Grupo de recursos** | LA-Tutorial-RG | O mesmo grupo de recursos do Azure que você usou anteriormente |
   | **Localidade** | Leste dos EUA 2 | A mesma região que você usou anteriormente | 
   | **Log Analytics** | Desativar | Para este tutorial, mantenha a configuração **Desligada**. | 
   |||| 

3. Depois que o Azure implanta o aplicativo, o Designer de Aplicativos Lógicos é exibido e mostra uma página com um vídeo introdutório e modelos de padrões de aplicativo lógico comuns. Em **Modelos**, escolha **Aplicativo lógico em branco**.

   ![Escolha o modelo de aplicativo lógico em branco](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Em seguida, adicione um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) que escuta emails recebidos com anexos. Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando novos dados atendem a uma condição específica. Para saber mais, confira [Criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Monitorar o recebimento de emails

1. No designer, digite "quando o email chega" na caixa de pesquisa. Selecione esse disparador para seu provedor de email:  **< *seu-provedor-de-email*> - Quando um novo email é recebido**, por exemplo:

   ![Selecionar este disparador para o provedor de email: "Quando um novo email é recebido"](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Para as contas corporativas ou de estudante do Azure, selecione o Outlook do Office 365. 
   * Para contas pessoais da Microsoft, selecione Outlook.com. 

2. Se forem solicitadas credenciais, entre na sua conta de email para que os Aplicativos Lógicos possam se conectar à sua conta de email.

3. Agora forneça os critérios que o gatilho usa para filtrar novos emails.

   1. Especifique a pasta, o intervalo e a frequência de verificação de emails.

      ![Especificar a pasta, intervalo e frequência de verificação de emails](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Configuração | Valor | DESCRIÇÃO | 
      | ------- | ----- | ----------- | 
      | **Pasta** | Caixa de Entrada | A pasta de email a ser verificada | 
      | **Intervalo** | 1 | O número de intervalos de espera entre as verificações | 
      | **Frequência** | Minuto | A unidade de tempo para cada intervalo entre verificações | 
      |  |  |  | 
  
   2. Escolha **Mostrar opções avançadas** e especificar estas configurações:

      | Configuração | Valor | DESCRIÇÃO | 
      | ------- | ----- | ----------- | 
      | **Tem Anexo** | sim | Obter somente emails com anexos. <p>**Observação:** o gatilho não remove emails da sua conta; ele verifica apenas novas mensagens e processa somente os emails que correspondem ao filtro de assunto. | 
      | **Incluir Anexos** | sim | Obter os anexos como entrada para o fluxo de trabalho, em vez de simplesmente procurar anexos. | 
      | **Filtro de Assunto** | ```Business Analyst 2 #423501``` | O texto a ser localizado no assunto do email | 
      |  |  |  | 

4. Por enquanto, para ocultar os detalhes do gatilho, clique na barra de título do gatilho.

   ![Recolher forma para ocultar detalhes](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

   Seu aplicativo lógico agora está ativo, mas ele não faz nada além de verificar seus emails. 
   Em seguida, adicione uma condição que especifica os critérios para continuar o fluxo de trabalho.

## <a name="check-for-attachments"></a>Verificar anexos

1. No gatilho, escolha **+ Nova etapa** > **Adicionar uma condição**.

   Quando a forma de condição aparece, por padrão, a lista de parâmetros ou a lista de conteúdo dinâmico é exibida e mostra os parâmetros da etapa anterior que você pode incluir como entradas de fluxo de trabalho. 
   A largura do navegador determina a lista exibida.

2. Renomeie a condição com uma descrição melhor.

   1. Na barra de título da condição, escolha o botão de **reticências** (**...** ) > **Renomear**.

      Por exemplo, se seu navegador estiver na exibição estreita:

      ![Renomear condição](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

      Se seu navegador estiver na exibição ampla e a lista de conteúdo dinâmico bloquear o acesso ao botão de reticências, feche a lista escolhendo **Adicionar conteúdo dinâmico** dentro da condição. 
      
      ![Fechar lista de conteúdo dinâmico](./media/tutorial-process-email-attachments-workflow/close-dynamic-content-list.png)

   2. Renomeie sua condição com esta descrição:```If email has attachments and key subject phrase```

3. Descreva a condição fornecendo uma expressão. 

   1. Na forma da condição, escolha **Editar no modo avançado**.

      ![Editar condição no modo avançado](./media/tutorial-process-email-attachments-workflow/edit-advanced-mode.png)

   2. Na caixa de texto, insira esta expressão:

      ```@equals(triggerBody()?['HasAttachment'], bool('true'))```

      Esta expressão compara o valor da propriedade **HasAttachment** do corpo do gatilho, que é o email neste tutorial, com o objeto booliano ```True```. 
      Se os dois valores forem iguais, o email tem pelo menos um anexo, a condição é atendida e o fluxo de trabalho continua.

      Agora, sua condição é semelhante a este exemplo:

      ![Expressão de condição](./media/tutorial-process-email-attachments-workflow/condition-expression.png)

   3. Escolha **Editar no modo básico**. Agora, sua expressão é resolvida como mostrado aqui:

      ![Expressão resolvida](./media/tutorial-process-email-attachments-workflow/condition-expression-resolved.png)

      > [!NOTE]
      > Para criar uma expressão manualmente, você deve trabalhar no modo básico e ter a lista dinâmica aberta para poder trabalhar com o construtor de expressões. Em **Expressão**, você pode selecionar funções. Em **Conteúdo dinâmico**, você pode selecionar campos de parâmetro para usar nessas funções.
      > Posteriormente, este tutorial mostrará como criar expressões manualmente.

4. Salve seu aplicativo lógico.

### <a name="test-your-condition"></a>Testar a condição

Agora, teste se a condição funciona corretamente:

1. Se seu aplicativo lógico já não estiver em execução, escolha **Executar** na barra de ferramentas do designer.

   Esta etapa inicia o aplicativo lógico manualmente sem ter de esperar o intervalo especificado. 
   No entanto, nada acontecerá até que o email de teste chegue na caixa de entrada. 

2. Envie um email que atenda a estes critérios:

   * O assunto do email tem o texto que você especificou no **Filtro de assunto** do gatilho:```Business Analyst 2 #423501```

   * Seu email tem um anexo. 
   Por enquanto, basta criar um arquivo de texto vazio e anexar o arquivo ao seu email.

   Quando o email chega, seu aplicativo lógico verifica anexos e o texto de assunto especificado.
   Se a condição for atendida, o gatilho será acionado e fará com que o mecanismo de aplicativos lógicos crie uma instância do aplicativo lógico e inicie o fluxo de trabalho. 

3. Para verificar se o gatilho foi disparado e se o aplicativo lógico foi executado com êxito, no menu do aplicativo lógico, escolha **Visão Geral**.

   ![Verifique o histórico de execução e gatilhos](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Se seu aplicativo lógico não dispara ou é executado apesar do êxito de um gatilho, confira [Solucionar problemas do seu aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

Em seguida, defina as ações a serem executadas na ramificação **If true**. Para salvar o email junto com os anexos, remova o HTML do corpo do email e crie blobs no contêiner de armazenamento para email e anexos.

> [!NOTE]
> Sua lógica de aplicativo não precisa fazer nada na ramificação **If false** quando um email não tem anexos. Como um exercício extra depois de concluir este tutorial, você pode adicionar uma ação apropriada que deseja executar para a ramificação **If false**.

## <a name="call-the-removehtmlfunction"></a>Chamar o RemoveHTMLFunction

1. No menu de aplicativo lógico, escolha **Designer de Aplicativos Lógicos**. Na ramificação **If true**, escolha **Adicionar uma ação**.

2. Pesquise "funções do azure" e selecione esta ação: **Azure Functions – Escolher uma função do Azure**

   ![Selecionar ação para "Azure Functions - Escolher uma função do Azure"](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. Selecione o aplicativo de funções criado anteriormente: **CleanTextFunctionApp**

   ![Selecione o aplicativo de funções do Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. Agora, selecione a função: **RemoveHTMLFunction**

   ![Selecionar a função do Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. Renomeie a forma da função com esta descrição:```Call RemoveHTMLFunction to clean email body``` 

6. Na forma da função, digite a entrada que a sua função deve processar. Especifique o corpo do email como mostrado e descrito aqui:

   ![Especifique o corpo da solicitação que a função deve esperar](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   1. Em **Corpo da Solicitação**, digite este texto: 
   
      ```{ "emailBody": ``` 

      Até que você conclua essa entrada nas etapas a seguir, um erro sobre JSON inválido será exibido.
      Quando você testou essa função anteriormente, a entrada especificada para a função usava JSON (JavaScript Object Notation). 
      Portanto, o corpo da solicitação deve usar o mesmo formato. 

   2. Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione o campo **Corpo** em **Quando um novo email é recebido**.
   Após o campo **Corpo**, adicione a chave de fechamento:```}```

      ![Especifique o corpo da solicitação para passar para a função](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing2.png)

      Na definição do aplicativo lógico, essa entrada é exibida neste formato:

      ```{ "emailBody": "@triggerBody()?['Body']" }```

7. Salve seu aplicativo lógico.

Em seguida, adicione uma ação que cria um blob em seu contêiner de armazenamento para salvar o corpo do email.

## <a name="create-blob-for-email-body"></a>Criar blob para o corpo do email

1. Na forma da função do Azure, escolha **Adicionar uma ação**. 

2. Em **Escolher uma ação**, procure "blob" e selecione a ação: **Armazenamento de Blobs do Azure – Criar blob**

   ![Adicionar ação para criar um blob para o corpo do email](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. Se você não tiver uma conexão com uma conta de armazenamento do Azure, crie uma conexão com a sua conta de armazenamento com essas configurações, como mostrado e descrito aqui. Quando terminar, escolha **Criar**.

   ![Criar conexão com a conta de armazenamento](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **Nome da Conexão** | AttachmentStorageConnection | Um nome descritivo para a conexão | 
   | **Conta de armazenamento** | attachmentstorageacct | O nome da conta de armazenamento que você criou anteriormente para salvar os anexos | 
   |||| 

4. Renomeie a ação **Criar blob** com esta descrição:```Create blob for email body```

5. Na ação **Criar blob**, forneça essas informações e selecione esses parâmetros para criar o blob, como mostrado e descrito:

   ![Fornecer informações de blob para o corpo do email](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **Caminho da pasta** | /attachments | O nome do contêiner que você criou anteriormente e o caminho até ele. Você também pode procurar e selecionar um contêiner. | 
   | **Nome de blob** | Campo **De** | Transmita o nome de remetente de email como o nome do blob. Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione o campo **De** em **Quando um novo email é recebido**. | 
   | **Conteúdo do blob** | Campo **Conteúdo** | Transmita o corpo do email sem HTML como o conteúdo do blob. Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione **Corpo** em **Chamar RemoveHTMLFunction para limpar o corpo do email**. |
   |||| 

6. Salve seu aplicativo lógico. 

### <a name="check-attachment-handling"></a>Verifique o tratamento de anexos

Agora teste se seu aplicativo lógico trata os emails da maneira especificada:

1. Se seu aplicativo lógico já não estiver em execução, escolha **Executar** na barra de ferramentas do designer.

2. Envie um email que atenda a estes critérios:

   * O assunto do email tem o texto que você especificou no **Filtro de assunto** do gatilho:```Business Analyst 2 #423501```

   * Seu email tem pelo menos um anexo. 
   Por enquanto, basta criar um arquivo de texto vazio e anexar o arquivo ao seu email.

   * Seu email tem conteúdo de teste no corpo, por exemplo: 

     ```
     Testing my logic app
     ```

   Se seu aplicativo lógico não dispara ou é executado apesar do êxito de um gatilho, confira [Solucionar problemas do seu aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

3. Verifique se seu aplicativo lógico salvou o email no contêiner de armazenamento correto. 

   1. No Gerenciador de Armazenamento, expanda as **(Local e Anexado)** > 
    **Contas de Armazenamento** > **attachmentstorageacct (Externa)** > 
    **Contêineres de Blob** > **anexos**.

   2. Verifique o email no contêiner **anexos**. 

      Neste momento, somente o email aparece no contêiner porque o aplicativo lógico ainda não processa os anexos.

      ![Verificar o email salvo no Gerenciador de Armazenamento](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   3. Quando você terminar, exclua o email no Gerenciador de Armazenamento.

4. Opcionalmente, para testar a ramificação **If false**, que não faz nada no momento, você pode enviar um email que não atende aos critérios.

Em seguida, adicione um loop para processar todos os anexos de email.

## <a name="process-attachments"></a>Processar anexos

Este aplicativo lógico usa um loop **for each** para processar cada anexo de email.

1. Na forma **Criar blob para o corpo do email** Formatar, escolha **... Mais**e selecione este comando: **Adicionar um for each**

   ![Adicionar loop "for each"](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

2. Renomeie o loop com esta descrição:```For each email attachment```

3. Agora, especifique os dados para o loop processar. Clique na caixa **Selecionar saída de etapas anteriores**. Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione **Anexos**. 

   ![Selecionar "Anexos"](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   O campo **Anexos** passa uma matriz que contém todos os anexos incluídos em um email. 
   O loop **for each** repete ações em cada item que é transmitido com a matriz.

4. Salve seu aplicativo lógico.

Em seguida, adicione a ação que salva cada anexo como um blob no contêiner de armazenamento **anexos**.

## <a name="create-blobs-for-attachments"></a>Criar blobs para anexos

1. No loop **for each**, escolha **Adicionar uma ação** para que você possa especificar a tarefa a ser executada em cada anexo encontrado.

   ![Adicionar ação para fazer loop](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. Em **Escolher uma ação**, procure "blob" e selecione a ação: **Armazenamento de Blobs do Azure – Criar blob**

   ![Adicionar ação para criar um blob](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. Renomeie a ação **Criar blob 2** com esta descrição:```Create blob for each email attachment```

4. Na ação **Criar blob para cada anexo de email**, forneça essas informações e selecione os parâmetros para criar cada blob como mostrado e descrito:

   ![Fornecer informações de blob](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **Caminho da pasta** | /attachments | O nome do contêiner que você criou anteriormente e o caminho até ele. Você também pode procurar e selecionar um contêiner. | 
   | **Nome de blob** | Campo **Nome** | Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione **Nome** para transmitir o nome do anexo para o nome do blob. | 
   | **Conteúdo do blob** | Campo **Conteúdo** | Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione **Conteúdo** para transmitir o conteúdo do anexo para o conteúdo do blob. |
   |||| 

5. Salve seu aplicativo lógico. 

### <a name="check-attachment-handling"></a>Verifique o tratamento de anexos

Em seguida, teste se seu aplicativo lógico trata os anexos da maneira especificada:

1. Se seu aplicativo lógico já não estiver em execução, escolha **Executar** na barra de ferramentas do designer.

2. Envie um email que atenda a estes critérios:

   * O assunto do email tem o texto que você especificou no **Filtro de assunto** do gatilho:```Business Analyst 2 #423501```

   * Seu email tem pelo menos dois anexos. 
   Por enquanto, basta criar dois arquivos de texto vazios e anexar os arquivos ao email.

   Se seu aplicativo lógico não dispara ou é executado apesar do êxito de um gatilho, confira [Solucionar problemas do seu aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

3. Verifique se seu aplicativo lógico salvou o email e os anexos no contêiner de armazenamento correto. 

   1. No Gerenciador de Armazenamento, expanda as **(Local e Anexado)** > 
    **Contas de Armazenamento** > **attachmentstorageacct (Externa)** > 
    **Contêineres de Blob** > **anexos**.

   2. Verifique o email e os anexos no contêiner **anexos**.

      ![Verificar email e anexos salvos](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   3. Quando você terminar, exclua o email e os anexos no Gerenciador de Armazenamento.

Em seguida, adicione uma ação para que seu aplicativo lógico envie email para revisar os anexos.

## <a name="send-email-notifications"></a>Enviar notificações por email

1. Na ramificação **if true**, no loop **Para cada anexo de email**, escolha **Adicionar uma ação**. 

   ![Adicionar ação no loop "for each"](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. Em **escolher uma ação**, procure "enviar email" e selecione a ação "enviar email" para o provedor de email que você deseja. Para filtrar a lista de ações para um serviço específico, você pode selecionar primeiro o conector em **Conectores**.

   ![Selecionar a ação "enviar email" para o seu provedor de email](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Para as contas corporativas ou de estudante do Azure, selecione o Outlook do Office 365. 
   * Para contas pessoais da Microsoft, selecione Outlook.com. 

3. Se forem solicitadas credenciais, entre na sua conta de email para que os Aplicativos Lógicos criem uma conexão à sua conta de email.

4. Renomeie a ação **Enviar um email** com esta descrição:```Send email for review```

5. Forneça as informações para a ação e selecione os campos que você deseja incluir no email, como mostrado e descrito. Para adicionar linhas em branco em uma caixa de edição, pressione Shift + Enter.  

   Por exemplo, se você estiver trabalhando com a lista de conteúdo dinâmico:

   ![Enviar notificação por email](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Se você não encontrar um campo esperado na lista, selecione **Ver mais** ao lado de **Quando um novo email é recebido** na lista de conteúdo dinâmico ou no final da lista de parâmetros.

   | Configuração | Valor | Observações | 
   | ------- | ----- | ----- | 
   | **Para** | <*recipient-email-address*> | Para fins de teste, você pode usar seu próprio endereço de email. | 
   | **Assunto**  | ```ASAP - Review applicant for position: ``` **Assunto** | O assunto do email que você deseja incluir. Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione o campo **Assunto** em **Quando um novo email é recebido**. | 
   | **Corpo** | ```Please review new applicant:``` <p>```Applicant name: ``` **De** <p>```Application file location: ``` **Caminho** <p>```Application email content: ``` **Corpo** | O conteúdo do corpo do email. Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione estes campos: <p>- O campo **De** em **Quando um novo email é recebido** </br>- O campo **Caminho** em **Criar blob para o corpo do email** </br>- O campo **Corpo** em **Chamar RemoveHTMLFunction para limpar o corpo do email** | 
   |||| 

   Caso você selecione um campo que tenha uma matriz, como **Content**, que é uma matriz que contém anexos, o designer adicionará automaticamente um loop “For each” em torno da ação que faz referência a esse campo. 
   Dessa forma, seu aplicativo lógico pode executar essa ação em cada item da matriz. 
   Para remover o loop, remova o campo da matriz, mova a ação de referência para fora do loop, escolha as reticências (**...** ) na barra de título do loop e escolha **Excluir**.
     
6. Salve seu aplicativo lógico. 

Em seguida, teste seu aplicativo lógico, que agora se parece com este exemplo:

![Aplicativo lógico concluído](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Executar seu aplicativo lógico

1. Envie um email que atenda a estes critérios:

   * O assunto do email tem o texto que você especificou no **Filtro de assunto** do gatilho:```Business Analyst 2 #423501```

   * Seu email tem em um ou mais anexos. 
   Você pode reutilizar um arquivo de texto vazio do teste anterior. 
   Para um cenário mais realista, anexe um arquivo de currículo.

   * O corpo do email tem este texto, que você pode copiar e colar:

     ```
     Name: Jamal Hartnett   
     
     Street address: 12345 Anywhere Road   
     
     City: Any Town   
     
     State or Country: Any State   
     
     Postal code: 00000   
     
     Email address: jamhartnett@outlook.com   
     
     Phone number: 000-000-0000   
     
     Position: Business Analyst 2 #423501   

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook   

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  
     
     Certifications: Six Sigma Green Belt, Lean Project Management   
     
     Language skills: English, Mandarin, Spanish   
     
     Education: Master of Business Administration   
     ```

2. Execute seu aplicativo lógico. Se for bem-sucedido, o aplicativo lógico envia um email que se parece com este exemplo:

   ![Notificação de email enviada pelo aplicativo lógico](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Se você não receber nenhum email, verifique a pasta de Lixo eletrônico. 
   O filtro de lixo de email pode redirecionar esses tipos de mensagens. 
   Caso contrário, se você não tiver certeza de que seu aplicativo lógico foi executado corretamente, confira [Solução de problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

Parabéns, você agora criou e executou um aplicativo lógico que automatiza tarefas em diferentes serviços do Azure e chama um código personalizado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos que contém o aplicativo lógico e os recursos relacionados. No menu principal do Azure, vá para **Grupos de recursos** e selecione o grupo de recursos do seu aplicativo lógico. Selecione **Excluir grupo de recursos**. Insira o nome do grupo de recursos como confirmação e escolha **Excluir**.

![Excluir grupo de recursos do aplicativo lógico](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo lógico que processa e armazena anexos de email integrando serviços do Azure, como o Armazenamento do Azure e o Azure Functions. Agora, saiba mais sobre outros conectores que você pode usar para criar aplicativos lógicos.

> [!div class="nextstepaction"]
> [Saiba mais sobre conectores de Aplicativos Lógicos](../connectors/apis-list.md)