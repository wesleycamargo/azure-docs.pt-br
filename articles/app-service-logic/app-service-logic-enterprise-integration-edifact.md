<properties 
	pageTitle="Integração corporativa com o EDIFACT | Microsoft Azure" 
	description="Saiba como usar contratos EDIFACT para criar Aplicativos lógicos" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="jeffhollan" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2016" 
	ms.author="jonfan"/>

# Integração corporativa com o EDIFACT 

> [AZURE.NOTE] Esta página cobre os recursos de EDIFACT dos Aplicativos Lógicos. Para obter informações sobre X12 clique [aqui](app-service-logic-enterprise-integration-x12.md).

## Criar um contrato EDIFACT 
Para poder trocar mensagens EDIFACT, você precisará criar um contrato EDIFACT e armazená-lo em sua conta de integração. As etapas a seguir orientarão você pelo processo de criação de um contrato EDIFACT.

### Veja o que você precisa antes de começar
- Um [conta de integração](./app-service-logic-enterprise-integration-accounts.md) definida na sua assinatura do Azure
- Pelo menos dois [parceiros](./app-service-logic-enterprise-integration-partners.md) já definidos na sua conta de integração

>[AZURE.NOTE]Ao criar um contrato, o conteúdo das mensagens trocadas com o parceiro deve corresponder ao tipo de contrato.


Depois de você [criar uma conta de integração](./app-service-logic-enterprise-integration-accounts.md) e [adicionar os parceiros](./app-service-logic-enterprise-integration-partners.md), poderá criar um contrato EDIFACT executando estas etapas:

### Na página inicial do Portal do Azure

Depois de fazer logon no [Portal do Azure](http://portal.azure.com "Portal do Azure"):
1. Selecione **Procurar** no menu à esquerda.

>[AZURE.TIP]Se você não vir o link **Procurar**, talvez seja necessário expandir o menu primeiro. Faça isso selecionando o link **Mostrar menu** que está localizado na parte superior esquerda do menu recolhido.

![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-0.png)
2. Digite *integração* na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-3.png)
3. Na folha **Contas de Integração** que é aberta, selecione a conta de integração na qual você criará o contrato. Se você não vir nenhuma lista de contas de integração, [crie uma primeiro](./app-service-logic-enterprise-integration-accounts.md "Tudo sobre contas de integração"). ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-4.png)
4.  Selecione o bloco **Contratos**. Se você não vir o bloco de contratos, adicione-o primeiro. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-5.png)
5. Selecione o botão **Adicionar** na folha Contratos que se abre. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-agreement-2.png)
6. Insira um **Nome** para seu contrato, selecione **Tipo de contrato** para EDIFACT, **Parceiro Host**, **Identidade do Host** e **Parceiro Convidado**, **Identidade do Convidado** na folha Contratos que se abre. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1.png)
7. Depois de definir as propriedades do contrato, selecione **Configurações de Recebimento** para configurar como as mensagens recebidas por este contrato devem ser manipuladas.
8. O controle de Configurações de Recebimento é dividido nas seções a seguir, incluindo Identificadores, Confirmação, Esquemas, Números de Controle, Validações, Configurações Internas e Processamento em lote. Configure essas propriedades com base no seu contrato com o parceiro com o qual você trocará mensagens. Veja a seguir uma exibição desses controles e configure-os com base em como você deseja que este contrato identifique e manipule as mensagens recebidas: ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-2.png)
9. Selecione o botão **OK** para salvar suas configurações.

### Identificadores

|Propriedade|Descrição |
|---|---|
|UNB 6.1 (Senha de Referência do Destinatário)|Insira um valor alfanumérico entre 1 e 14 caracteres.|
|UNB 6.2 (Qualificador de Referência do Destinatário)|Insira um valor alfanumérico com no mínimo um caractere e no máximo dois caracteres.|

### Agradecimentos 

|Propriedade|Descrição |
|----|----|
|Recebimento de Mensagem (CONTRL)|Marque essa caixa de seleção para retornar uma confirmação técnica (CONTRL) ao remetente do intercâmbio. A confirmação é enviada ao remetente do intercâmbio com base nas Configurações de Envio do contrato.|
|Confirmação (CONTRL)|Marque essa caixa de seleção para retornar uma confirmação funcional (CONTRL) ao emissor de intercâmbio. A confirmação é enviada ao emissor de intercâmbio com base nas Configurações de Envio do contrato.|

### Esquemas

|Propriedade|Descrição |
|----|----|
|UNH2.1 (TIPO)|Selecione um tipo de conjunto de transação.|
|UNH2.2 (VERSÃO)|Insira o número de versão da mensagem. (No mínimo um caractere; no máximo três caracteres).|
|UNH2.3 (LIBERAÇÃO)|Insira o número de liberação da mensagem. (No mínimo um caractere; no máximo três caracteres).|
|UNH2.5 (CÓDIGO ATRIBUÍDO ASSOCIADO)|Insira o código atribuído. (No máximo seis caracteres. Deve ser alfanuméricos).|
|UNG 2.1 (ID DE REMETENTE DO APLICATIVO)|Insira um valor alfanumérico com no mínimo um caractere e no máximo 35 caracteres.|
|UNG 2.2 (QUALIFICADOR DE CÓDIGO DO APLICATIVO REMETENTE)|Insira um valor alfanumérico, com no máximo quatro caracteres.|
|ESQUEMA|Selecione o esquema carregado anteriormente que você deseja usar de sua Conta de Integração associada.|

### Números de Controle

|Propriedade|Descrição |
|----|----|
|Recusar duplicatas de Números de Controle de Intercâmbio|Marque essa caixa de seleção para bloquear intercâmbios duplicados. Se essa opção for selecionada, a Ação de Decodificação do EDIFACT verificará se o número de controle de intercâmbio (UNB5) do intercâmbio recebido não corresponde ao número de controle de intercâmbio processado anteriormente. Se houver uma correspondência, o intercâmbio não será processado.
|Verificar UNB5 duplicado a cada (dias)|Se você optou por não permitir números de controle de intercâmbio duplicados, poderá especificar o número de dias em que a verificação é executada, fornecendo o valor apropriado para **Verificar UNB5 duplicados a cada (dias)**.|
|Recusar duplicatas de Números de controle de grupo|Marque essa caixa de seleção para bloquear intercâmbios com números de controle de grupo duplicados (UNG5).|
|Recusar duplicatas de Números de controle de Conjuntos de transações|Marque essa caixa de seleção para bloquear intercâmbios com números de controle de conjunto de transação duplicados (UNH1).|
|Número de controle da confirmação EDIFACT|Para designar os números de referência do conjunto de transação a serem usados em uma confirmação, insira um valor para o prefixo, um intervalo de números de referência e um sufixo.|

### Validações

|Propriedade|Descrição |
|----|----|
|Tipo de Mensagem|Especifique o tipo de mensagem. À medida que cada linha de validação é concluída, outra é adicionada automaticamente. Se nenhuma regra for especificada, a linha marcada como padrão será usada para validação.|
|Validação de EDI|Marque essa caixa de seleção para executar a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita.|
|Validação Estendida|Marque essa caixa de seleção para permitir a validação estendida (XSD) de intercâmbios recebidos do remetente de intercâmbio. Isso inclui validação de comprimento do campo, campos opcionais e contagem repetida, além da validação de tipo de dados XSD.|
|Permitir Zeros à Esquerda/Direita|Selecione **Permitir** para permitir zeros à direita/à esquerda; **Não Permitir** para não permitir zeros à direita/à esquerda, ou **Cortar** para cortar os zeros à esquerda/à direita.|
|Cortar Zeros à Esquerda/Direita|Marque esta caixa de seleção para cortar quaisquer zeros à esquerda ou à direita|
|Política de Separador à Direita|Marque **Não Permitido** se você não quiser permitir delimitadores e separadores à direita em um intercâmbio recebido do emissor de intercâmbio. Se o intercâmbio tiver delimitadores e separadores à direita, ele será declarado inválido. Escolha **Opcional** para aceitar intercâmbios com ou sem delimitadores e separadores à direita. Escolha **Obrigatório** se o intercâmbio recebido precisar conter delimitadores e separadores à direita.|

### Configurações Internas

|Propriedade|Descrição |
|----|----|
|Criar marcas XML vazias se forem permitidos separadores à direita|Marque essa caixa de seleção para que o remetente do intercâmbio inclua marcas XML vazias para separadores à direita.|
|Processamento em lote de entrada|As opções incluem:</br></br>**Dividir o intercâmbio em Conjuntos de Transações – suspender Conjuntos de Transações com Erro**: analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado ao conjunto de transação. Com essa opção, se um ou mais conjuntos de transações do intercâmbio falharem na validação, somente esses conjuntos de transação serão suspensos. Dividir o Intercâmbio em Conjuntos de Transações – suspender Intercâmbios com Erro: analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado. Com essa opção, se um ou mais conjuntos de transações do intercâmbio falharem na validação, o intercâmbio inteiro será suspenso.</br></br>**Preservar Intercâmbio – suspender Conjuntos de Transação com Erro**: deixa o intercâmbio intacto, criando um documento XML para o intercâmbio em lote inteiro. Com essa opção, se um ou mais conjuntos de transações do intercâmbio falharem na validação, somente esses conjuntos de transação serão suspensos, continuando o processamento dos demais.</br></br>**Preservar Intercâmbio - suspender Intercâmbios com Erro**: deixa o intercâmbio intacto, criando um documento XML para o intercâmbio em lote inteiro. Com essa opção, se um ou mais conjuntos de transações do intercâmbio falharem na validação, o intercâmbio inteiro será suspenso.|

O contrato está pronto para manipular mensagens recebidas que estão em conformidade com as configurações selecionadas.

Para definir as configurações que manipulam as mensagens enviadas aos parceiros:
10. Selecione **Configurações de Envio** para configurar como as mensagens enviadas por este contrato devem ser manipuladas.

O controle de Configurações de Envio é dividido nas seções a seguir, incluindo Identificadores, Confirmação, Esquemas, Envelopes, Conjuntos de Caracteres e Separadores, Números de Controle e Validação.

Veja a seguir uma exibição desses controles. Faça as seleções com base em como você deseja manipular as mensagens enviadas aos parceiros por meio deste contrato: ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-3.png)
11. Selecione o botão **OK** para salvar suas configurações.

### Identificadores
|Propriedade|Descrição |
|----|----|
|UNB1.2 (Versão de sintaxe)|Selecione um valor entre **1** e **4**.|
|UNB 2.3 (Endereço de Roteamento do Remetente Inverso)|Insira um valor alfanumérico com no mínimo um caractere e no máximo 14 caracteres.|
|UNB3.3 (Endereço de Roteamento do Destinatário Inverso)|Insira um valor alfanumérico com no mínimo um caractere e no máximo 14 caracteres.|
|UNB 6.1 (Senha de Referência do Destinatário)|Insira um valor alfanumérico com no mínimo um e no máximo 14 caracteres.|
|UNB 6.2 (Qualificador de Referência do Destinatário)|Insira um valor alfanumérico com no mínimo um caractere e no máximo dois caracteres.|
|UNB7 (ID de Referência do Aplicativo)|Insira um valor alfanumérico com no mínimo um caractere e no máximo 14 caracteres|

### Confirmação
|Propriedade|Descrição |
|----|----|
|Recebimento de Mensagem (CONTRL)|Marque esta caixa de seleção se o parceiro hospedado espera receber uma confirmação técnica (CONTRL). Essa configuração especifica que o parceiro hospedado, que está enviando a mensagem, solicitou uma confirmação do parceiro convidado.|
|Confirmação (CONTRL)|Marque esta caixa de seleção se o parceiro hospedado espera receber uma confirmação funcional (CONTRL). Essa configuração especifica que o parceiro hospedado, que está enviando a mensagem, solicitou uma confirmação do parceiro convidado.|
|Gerar loop SG1/SG4 para conjuntos de transação aceitos|Se você escolher solicitar uma confirmação funcional, marque essa caixa de seleção para forçar a geração de loops SG1/SG4 em confirmações funcionais CONTRL para conjuntos de transação aceitos.|

### Esquemas
|Propriedade|Descrição |
|----|----|
|UNH2.1 (TIPO)|Selecione um tipo de conjunto de transação.|
|UNH2.2 (VERSÃO)|Insira o número de versão da mensagem.|
|UNH2.3 (LIBERAÇÃO)|Insira o número de liberação da mensagem.|
|ESQUEMA|Selecione o esquema a ser usado. Esquema estão localizados na sua conta de integração. Para acessar seus esquemas, vincule sua conta de integração ao seu aplicativo lógico.|

### Envelopes
|Propriedade|Descrição |
|----|----|
|UNB8 (Código de Prioridade de Processamento)|Insira um valor alfabético que não tenha mais de um caractere de comprimento.|
|UNB10 (Contrato de Comunicação)|Insira um valor alfanumérico com no mínimo um caractere e no máximo 40 caracteres.|
|UNB11 (Indicador de Teste)|Marque essa caixa de seleção para indicar que o intercâmbio gerado são dados de teste|
|Aplicar o Segmento UNA (Aviso de Cadeia de Caracteres de Serviço)|Marque essa caixa de seleção para gerar um segmento UNA para o intercâmbio a ser enviado.|
|Aplicar os segmentos UNG (Cabeçalho de Grupo de Função)|Marque essa caixa de seleção para criar segmentos de agrupamento no cabeçalho de grupo funcional nas mensagens enviadas ao parceiro convidado. Os seguintes valores são usados para criar os segmentos UNG:</br></br>para **UNG1**, insira um valor alfanumérico com no mínimo um caractere e no máximo seis caracteres.</br></br>Para **UNG2.1**, insira um valor alfanumérico com no mínimo um caractere e no máximo 35 caracteres.</br></br>Para **UNG2.2**, insira um valor alfanumérico com no máximo quatro caracteres.</br></br>Para **UNG3.1**, insira um valor alfanumérico com no mínimo um caractere e no máximo 35 caracteres.</br></br>Para **UNG3.2**, insira um valor alfanumérico com no máximo quatro caracteres.</br></br>Para **UNG6**, insira um valor alfanumérico com no mínimo um e no máximo três caracteres.</br></br>Para **UNG7.1**, insira um valor alfanumérico com no mínimo um caractere e no máximo três caracteres.</br></br>Para **UNG7.2**, insira um valor alfanumérico com no mínimo um e no máximo três caracteres.</br></br>Para **UNG7.3**, insira um valor alfanumérico com no mínimo um e no máximo seis caracteres.</br></br>Para **UNG8**, insira um valor alfanumérico com no mínimo um e no máximo 14 caracteres.|

### Conjuntos de Caracteres e Separadores
Além do conjunto de caracteres, você pode inserir um conjunto diferente de delimitadores a serem usados para cada tipo de mensagem. Se um conjunto de caracteres não for especificado para um determinado esquema de mensagem, o conjunto de caracteres padrão será usado.

|Propriedade|Descrição |
|----|----|
|UNB1.1 (Identificador do Sistema)|Selecione o conjunto de caracteres EDIFACT a ser aplicado no intercâmbio de saída.|
|Esquema|Selecione um esquema na lista suspensa. À medida que cada linha é concluída, uma nova linha é adicionada. Para o esquema selecionado, selecione o conjunto de separadores a ser usado: </br></br>**Separador de elemento de componente** – Insira um único caractere para separar elementos de dados compostos. </br></br>**Separador de elemento de dados** – Insira um único caractere para separar elementos de dados simples dentro elementos de dados compostos.</br></br></br></br>** Caractere de substituição** – marque essa caixa de seleção se a carga de dados contém caracteres que também são usados como separadores de componentes, de dados ou de segmentos. Em seguida,você pode inserir um caractere de substituição. Ao gerar a mensagem de saída EDIFACT, todas as instâncias de caracteres de separador na carga de dados são substituídas pelo caractere especificado.</br></br>**Terminador de segmento** – Insira um único caractere para indicar o final de um segmento de EDI.</br></br>**Sufixo** – Selecione o caractere a ser usado com o identificador de segmento. Se você designar um sufixo, o elemento de dados de terminador de segmento poderá ficar vazio. Se o terminador de segmento ficar vazio, você deverá designar um sufixo.|

### Números de Controle
|Propriedade|Descrição |
|----|----|
|UNB5 (Número de Controle de Intercâmbio)|Insira um prefixo, um intervalo de valores para o número de controle de intercâmbio e um sufixo. Esses valores são usados para gerar um intercâmbio de saída. O prefixo e sufixo são opcionais; o número de controle é obrigatório. O número de controle é incrementado para cada nova mensagem; o prefixo e sufixo permanecem os mesmos.|
|UNG5 (Número de Controle de Grupo)|Insira um prefixo, um intervalo de valores para o número de controle de intercâmbio e um sufixo. Esses valores são usados para gerar o número de controle de grupo. O prefixo e sufixo são opcionais; o número de controle é obrigatório. O número de controle é incrementado para cada nova mensagem até que o valor máximo seja alcançado; o prefixo e sufixo permanecem os mesmos.|
|UNH1 (Número de Referência do Cabeçalho da Mensagem)|Insira um prefixo, um intervalo de valores para o número de controle de intercâmbio e um sufixo. Esses valores são usados para gerar o número de referência de cabeçalho da mensagem. O prefixo e sufixo são opcionais; o número de referência é obrigatório. O número de referência é incrementado para cada nova mensagem; o prefixo e sufixo permanecem os mesmos.|

### Validações
|Propriedade|Descrição |
|----|----|
|Tipo de Mensagem|Essa opção habilita a validação no destinatário do intercâmbio. Essa validação realiza a validação de EDI em elementos de dados do conjunto de transação, validando tipos de dados, restrições de comprimento, elementos de dados vazios e separadores.|
|Validação de EDI|Marque essa caixa de seleção para executar a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita.|
|Validação Estendida|Essa opção permite a validação estendida de intercâmbios recebidos do remetente de intercâmbio. Isso inclui validação de comprimento do campo, campos opcionais e contagem repetida, além da validação de tipo de dados XSD. Você pode habilitar a validação estendida sem habilitar a validação de EDI ou vice-versa.|
|Permitir zeros à esquerda/direita|Essa opção especifica que um intercâmbio de EDI recebido não falhará na validação se um elemento de dados em um intercâmbio EDI não atender ao requisito de comprimento devido a espaços à direita, mas estiver de acordo com esse requisito quando os espaços são removidos.|
|Cortar Zeros à Esquerda/Direita|A seleção dessa opção cortará os zeros à esquerda e à direita.|
|Separador à direita|Essa opção especifica que um intercâmbio de EDI recebido não falhará na validação se um elemento de dados em um intercâmbio de EDI não estiver atender ao requisito de comprimento devido a zeros à esquerda (ou direita) ou espaços à direita,mas estiver de acordo com esse requisito quando os espaços ou zeros são removidos.</br></br>Selecione **Não Permitido** se não quiser permitir delimitadores e separadores à direita em um intercâmbio recebido do remetente de intercâmbio. Se o intercâmbio tiver delimitadores e separadores à direita, ele será declarado inválido.</br></br>Selecione **Opcional** para aceitar intercâmbios com ou sem delimitadores e separadores à direita.</br></br>Selecione **Obrigatório** se o intercâmbio recebido precisa ter delimitadores e separadores à direita.|

Depois de selecionar **OK** na folha aberta:
12. Selecione o bloco **Contratos** na folha da Conta de Integração e você verá o contrato recém-adicionado listado. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-4.png)

## Saiba mais
- [Saiba mais sobre o Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

<!---HONumber=AcomDC_0727_2016-->