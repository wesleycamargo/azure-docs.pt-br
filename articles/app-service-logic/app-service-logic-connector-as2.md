<properties 
   pageTitle="Usando o Conector do AS2 em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure" 
   description="Como criar e configurar o conector do AS2 ou aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# Comece a usar o Conector do AS2 e adicione-o a seu Aplicativo Lógico

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Use o Conector do AS2 para receber e enviar mensagens pelo protocolo de transporte AS2 (Applicability Statement 2) em comunicações comerciais. Os dados são transportados de maneira segura e confiável pela Internet. A segurança é obtida usando criptografia e certificados digitais.

Você pode adicionar o Conector do AS2 a seu fluxo de trabalho comercial e processar dados como parte de um fluxo de trabalho comercial dentro de um Aplicativo Lógico.

## Gatilhos e Ações
Um Gatilho inicia uma nova instância com base em um evento específico, como a chegada de uma mensagem AS2 de um parceiro. Uma Ação é o resultado, como após receber uma mensagem AS2 e depois enviar a mensagem usando AS2.

O Conector do AS2 pode ser usado como um gatilho ou uma ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML. O Conector do AS2 tem os seguintes gatilhos e ações disponíveis:

Gatilhos | Ações
--- | ---
Receber e decodificar | Codificar e enviar

## Requisitos para começar
Os seguintes itens devem ser criados por você antes que possam ser usados pelo conector do AS2:

Requisito | Descrição
--- | ---
Aplicativo de API do TPM | Antes de criar um conector do AS2, você precisa criar um [Conector de Gerenciamento de Parceiros Comerciais do BizTalk][1]. <br/><br/>**Observação** Saiba o nome do Aplicativo de API de TPM. 
Banco de Dados SQL do Azure | Armazena itens B2B incluindo esquemas, parceiros, certificados e contratos. Cada um dos aplicativos de API B2B requer seu próprio banco de dados SQL do Azure. <br/><br/>* * Observação * * Copie a cadeia de conexão para esse banco de dados.<br/><br/>[Criar um banco de dados SQL do Azure](../sql-database/sql-database-get-started.md)
Contêiner do Armazenamento de Blob do Azure | Armazena propriedades das mensagens quando o arquivamento AS2 está habilitado. Se você não precisar de arquivamento de mensagens AS2, não será necessário um contêiner de armazenamento. <br/><br/>* * Observação * * Se você estiver habilitando o arquivamento, copie a cadeia de conexão para este Armazenamento de Blob.<br/><br/>[Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

## Criar o conector do AS2

Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Conector do AS2", selecione-o e selecione **Criar**.
3. Digite o Nome, o Plano do Serviço de Aplicativo e outras propriedades.
4. Insira as seguintes configurações de pacote:

	Propriedade | Descrição
--- | --- 
Cadeia de conexão de banco de dados | Insira a cadeia de conexão ADO.NET ao Banco de Dados SQL do Azure que você criou. Quando você copia a cadeia de conexão, a senha não é adicionada a ela. Certifique-se de inserir a senha na cadeia de conexão antes de colar.
Habilitar arquivamento de mensagens de entrada | Opcional. Habilite esta propriedade para armazenar as propriedades das mensagens AS2 de entrada recebidas de parceiros. 
Cadeia de Conexão do Armazenamento de Blob do Azure | Insira a cadeia de conexão no contêiner do Armazenamento de Blob do Azure criado por você. Quando o Arquivamento está habilitado, as mensagens codificadas e decodificadas são armazenadas neste contêiner de armazenamento.
Nome da instância do TPM | Insira o nome do Aplicativo de API do **Gerenciamento de Parceiros Comerciais do BizTalk** criado anteriormente. Quando você cria o conector de AS2, esse conector executa somente os contratos de AS2 dentro dessa instância específica do TPM.

5. Selecione **Criar**.

Parceiros comerciais são as entidades envolvidas em comunicações B2B (business-to-business). Quando dois parceiros estabelecem uma relação, isso é chamado de um Contrato. O acordo definido se baseia na comunicação que os dois parceiros desejam atingir e é específica com relação a transporte ou protocolo.

Consulte as etapas para [criar um contrato de parceiro comercial][2].

## Usar o Conector como um Gatilho

1. Ao criar ou editar um aplicativo lógico, selecione o Conector do AS2 criado no painel à direita: ![Configurações do gatilho][3]

2. Clique na seta para a direita →: ![Opções do gatilho][4]

3. O Conector do AS2 expõe um único gatilho. Selecione *Receber e Decodificar*: ![Receber e decodificar dados][5]

4. Esse gatilho não tem entradas. Clique na seta para a direita →: ![Receber e decodificar configurados][6]

Como parte da saída, o conector retorna a carga do AS2, bem como os metadados específicos do AS2.

O Gatilho é disparado quando uma Carga do AS2 é como um POST para https://{Host URL}/decode. Você pode encontrar a URL do Host nas configurações do Aplicativo de API. Talvez você precise alterar o nível de acesso do Aplicativo de API nas configurações do aplicativo para Público (autenticado ou anônimo).

## Usar o Conector como uma Ação
1. Após utilizar o gatilho (ou escolher "executar esta lógica manualmente"), adicione o conector do AS2 criado no painel à direita: ![Configurações de ação][7]

2. Clique na seta para a direita →: ![Lista de ações][8]

3. O conector do AS2 dá suporte a apenas uma ação. Selecione *Codificar e Enviar*: ![Codificar e enviar entrada][9]

4. Insira as entradas para a ação e configure-a: ![Codificar e enviar configurado][10]

	Os parâmetros incluem:

	Parâmetro | Tipo | Descrição
--- | --- | ---
Carga útil | objeto| O conteúdo da carga para codificar e enviar para o ponto de extremidade configurado. A carga deve ser fornecida como um objeto JSON.
AS2 de | string | A identidade AS2 do remetente da mensagem AS2. Esse parâmetro é usado para pesquisar o contrato adequado para enviar a mensagem.
AS2 para | string | A identidade AS2 do receptor da mensagem AS2. Esse parâmetro é usado para pesquisar o contrato adequado para enviar a mensagem.
URL do parceiro | string | O ponto de extremidade do parceiro para o qual a mensagem precisa ser enviada.
Habilitar arquivamento | boolean | Determina se a mensagem de saída deve ser arquivada.

A ação retorna um código de resposta HTTP 200 à conclusão bem-sucedida.

## Faça mais com seu Conector
Você pode [arquivar suas mensagens do AS2](app-service-logic-archive-as2-messages.md).

Mais informações sobre aplicativos lógicos em [O que são aplicativos lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se você quiser começar a usar os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Try Logic Apps](https://tryappservice.azure.com/?appservice=logic) (Experimentar os Aplicativos Lógicos). Você pode criar instantaneamente um aplicativo lógico de início e de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=AcomDC_0420_2016-->