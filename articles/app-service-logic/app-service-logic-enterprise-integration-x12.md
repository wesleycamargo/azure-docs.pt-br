<properties 
	pageTitle="Visão geral do X12 e do Enterprise Integration Pack | Serviço de Aplicativo do Microsoft Azure | Microsoft Azure" 
	description="Saiba como usar contratos X12 para criar aplicativos lógicos" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Integração corporativa com o X12 

>[AZURE.NOTE]Esta página cobre os recursos de X12 dos Aplicativos Lógicos. Para obter informações sobre X12 clique [aqui](app-service-logic-enterprise-integration-edifact.md).

## Criar um contrato X12 
Para poder trocar mensagens X12, você precisará criar um contrato X12 e armazená-lo em sua conta de integração. As etapas a seguir orientarão você pelo processo de criação de um contrato X12.

### Veja o que você precisa antes de começar
- Um [conta de integração](./app-service-logic-enterprise-integration-accounts.md) definida na sua assinatura do Azure
- Pelo menos dois [parceiros](./app-service-logic-enterprise-integration-partners.md) já definidos na sua conta de integração

>[AZURE.NOTE]Ao criar um contrato, o conteúdo do arquivo do contrato deve corresponder ao tipo de contrato.


Depois de você [criar uma conta de integração](./app-service-logic-enterprise-integration-accounts.md) e [adicionar os parceiros](./app-service-logic-enterprise-integration-partners.md), poderá criar um contrato X12 executando estas etapas:

### Na página inicial do Portal do Azure

Depois de fazer logon no [Portal do Azure](http://portal.azure.com "Portal do Azure"):
1. Selecione **Procurar** no menu à esquerda.

>[AZURE.TIP]Se você não vir o link **Procurar**, talvez seja necessário expandir o menu primeiro. Faça isso selecionando o link **Mostrar menu** que está localizado na parte superior esquerda do menu recolhido.

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Digite *integração* na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados ![](./media/app-service-logic-enterprise-integration-x12/x12-1-3.png)
3. Na folha **Contas de Integração** que é aberta, selecione a conta de integração na qual você criará o contrato. Se você não vir nenhuma lista de contas de integração, [crie uma primeiro](./app-service-logic-enterprise-integration-accounts.md "Tudo sobre contas de integração"). ![](./media/app-service-logic-enterprise-integration-x12/x12-1-4.png)
4.  Selecione o bloco **Contratos**. Se você não vir o bloco de contratos, adicione-o primeiro. ![](./media/app-service-logic-enterprise-integration-x12/x12-1-5.png)
5. Selecione o botão **Adicionar** na folha Contratos que se abre. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)
6. Insira um **Nome** para seu contrato, selecione **Tipo de Contrato**, **Parceiro Host**, **Identidade do Host** e **Parceiro Convidado**, **Identidade do Convidado** na folha Contratos que se abre. ![](./media/app-service-logic-enterprise-integration-x12/x12-1.png)
7. Depois de definir as propriedades das configurações de recebimento, selecione o botão **OK**. Continuando:
8. Selecione **Configurações de Recebimento** para configurar como as mensagens recebidas por este contrato devem ser manipuladas.
9. O controle de Configurações de Recebimento é dividido nas seções a seguir, incluindo Identificadores, Confirmação, Esquemas, Envelopes, Números de Controle, Validações e Configurações Internas. Configure essas propriedades com base no seu contrato com o parceiro com o qual você trocará mensagens. Veja a seguir uma exibição desses controles e configure-os com base em como você deseja que este contrato identifique e manipule as mensagens recebidas: ![](./media/app-service-logic-enterprise-integration-x12/x12-2.png)

![](./media/app-service-logic-enterprise-integration-x12/x12-3.png)
10. Selecione o botão **OK** para salvar suas configurações.

### Identificadores

|Propriedade|Descrição |
|---|---|
|ISA1 (Qualificador de Autorização)|Selecione o valor do Qualificador de autorização na lista suspensa.|
|ISA2|Opcional. Insira o valor de Informações de autorização. Se o valor inserido para ISA1 é diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10.|
|ISA3 (Qualificador de Segurança)|Selecione o valor do Qualificador de segurança na lista suspensa.|
|ISA4|Opcional. Insira o valor de Informações de segurança. Se o valor inserido para ISA3 é diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10.|

### Agradecimentos 

|Propriedade|Descrição |
|----|----|
|TA1 esperado|Marque essa caixa de seleção para retornar uma confirmação técnica (TA1) ao remetente do intercâmbio. Essas confirmações são enviadas ao remetente do intercâmbio com base nas configurações de envio do contrato.|
|FA esperado|Marque essa caixa de seleção para retornar uma confirmação funcional (FA) ao remetente do intercâmbio. Selecione se deseja as confirmações 997 ou 999, com base nas versões de esquema com as quais você está trabalhando. Essas confirmações são enviadas ao remetente do intercâmbio com base nas configurações de envio do contrato.|
|Incluir Loop AK2/IK2|Marque essa caixa de seleção para habilitar a geração de loops AK2 em confirmações funcionais para conjuntos de transações aceitos. Observação: essa caixa de seleção somente será habilitada se você marcar a caixa de seleção FA pertinente.|

### Esquemas

Escolha um esquema para cada tipo de transação (ST1) e o Aplicativo de Remetente (GS2). O pipeline de recebimento desmontará a mensagem recebida associando os valores de ST1 e GS2 na mensagem recebida aos valores definidos aqui e o esquema da mensagem recebida com o esquema definido aqui.

|Propriedade|Descrição |
|----|----|
|Versão|Selecione a versão X12|
|Tipo de Transação (ST01)|Selecione o tipo de transação|
|Aplicativo do Remetente (GS02)|Selecione o aplicativo do remetente|
|Esquema|Selecione o arquivo de esquema que você deseja usar. Arquivos de esquema estão localizados na sua conta de integração.|

### Envelopes

|Propriedade|Descrição |
|----|----|
|Uso de ISA11|Use este campo para especificar o separador em um conjunto de transações:</br></br>Selecione o identificador Padrão para usar a notação decimal de "." em vez da notação decimal no documento no pipeline de recebimento do EDI.</br></br>Selecione o separador Repetição para especificar o separador para ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetidos. Por exemplo, (^) geralmente é usado como separador de repetição. Para esquemas HIPPA, só é possível usar (^).|

### Números de Controle

|Propriedade|Descrição |
|----|----|
|Recusar duplicatas de Números de Controle de Intercâmbio|Marque essa opção para bloquear intercâmbios duplicados. Se essa opção for selecionada, o Portal dos Serviços BizTalk verificará se o número de controle de intercâmbio (ISA13) do intercâmbio recebido não corresponde ao número de controle de intercâmbio. Se uma correspondência for detectada, o pipeline de recebimento não processará o intercâmbio.<br/>Se você optou por não permitir números de controle de intercâmbio duplicados, poderá especificar o número de dias em que a verificação é executada, fornecendo o valor apropriado para Verificar ISA13 duplicados a cada x dias.|
|Recusar duplicatas de Números de controle de grupo|Marque essa opção para bloquear intercâmbios com números de controle de grupo duplicados.|
|Recusar duplicatas de Números de controle de Conjuntos de transações|Marque essa opção para bloquear intercâmbios com números de controle de conjuntos de transações duplicados.|

### Validações

|Propriedade|Descrição |
|----|----|
|Tipo de Mensagem|O tipo de mensagem EDI, como uma Ordem de Compra 850 ou uma Confirmação de Implementação 999.|
|Validação de EDI|Executa a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita.|
|Validação Estendida|Se o tipo de dados não for EDI, a validação será realizada nos requisitos de elementos de dados e na permissão de repetições, enumerações e validação de tamanho de elementos de dados (mín./máx.).|
|Permitir Zeros à Esquerda/Direita|Qualquer caractere adicional de espaço e zeros à esquerda ou à direita serão mantidos. Eles não são removidos.|
|Política de Separador à Direita|Gera separadores à direita no intercâmbio recebido. As opções incluem Não Permitido, Opcional e Obrigatório.|

### Configurações Internas

|Propriedade|Descrição |
|----|----|
|Converter o formato decimal implícito Nn para o valor numérico de base 10|Converte um número de EDI que é especificado no formato Nn em um valor numérico de base 10 no XML intermediário do Portal de Serviços BizTalk.|
|Criar marcas XML vazias se forem permitidos separadores à direita|Marque essa caixa de seleção para que o remetente do intercâmbio inclua marcas XML vazias para separadores à direita.|
|Processamento em lote de entrada|Dividir o intercâmbio em conjuntos de transações – suspender conjuntos de transações com erro: analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado ao conjunto de transação. Com essa opção, se um ou mais conjuntos de transações do intercâmbio falharem na validação, os Serviços BizTalk suspenderão somente esses conjuntos de transação. </br></br>Dividir o intercâmbio em conjuntos de transações – suspender intercâmbios com erro: analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado. Com essa opção, se um ou mais conjuntos de transações do intercâmbio falharem na validação, os Serviços BizTalk suspenderão o intercâmbio inteiro.</br></br>Preservar intercâmbio – suspender conjuntos de transação com erro: deixa o intercâmbio intacto, criando um documento XML para o intercâmbio em lote inteiro. Com essa opção, se um ou mais conjuntos de transações do intercâmbio falharem na validação, os Serviços BizTalk suspenderão somente esses conjuntos de transação, continuando o processamento dos demais.</br></br>Preservar intercâmbio - suspender intercâmbios com erro: deixa o intercâmbio intacto, criando um documento XML para o intercâmbio em lote inteiro. Com essa opção, se um ou mais conjuntos de transações do intercâmbio falharem na validação, os Serviços BizTalk suspenderão o intercâmbio inteiro.</br></br>|

O contrato está pronto para manipular mensagens recebidas que estão em conformidade com o esquema selecionado.

Para definir as configurações que manipulam as mensagens enviadas aos parceiros:
11. Selecione **Configurações de Envio** para configurar como as mensagens enviadas por este contrato devem ser manipuladas.

O controle de Configurações de Envio é dividido nas seções a seguir, incluindo Identificadores, Confirmação, Esquemas, Envelopes, Números de Controle, Conjuntos de Caracteres e Separadores e Validação.

Veja a seguir uma exibição desses controles. Faça as seleções com base em como você deseja manipular as mensagens enviadas aos parceiros por meio deste contrato: ![](./media/app-service-logic-enterprise-integration-x12/x12-4.png)

![](./media/app-service-logic-enterprise-integration-x12/x12-5.png)

![](./media/app-service-logic-enterprise-integration-x12/x12-6.png)
12. Selecione o botão **OK** para salvar suas configurações.

### Identificadores
|Propriedade|Descrição |
|----|----|
|Qualificador de Autorização (ISA1)|Selecione o valor do Qualificador de autorização na lista suspensa.|
|ISA2|Insira o valor de Informações de autorização. Se esse valor é diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10.|
|Qualificador de segurança (ISA3)|Selecione o valor do Qualificador de segurança na lista suspensa.|
|ISA4|Insira o valor de Informações de segurança. Se esse valor é diferente de 00, insira no mínimo um valor alfanumérico e no máximo 10 na caixa de texto Valor (ISA4).|

### Confirmação
|Propriedade|Descrição |
|----|----|
|TA1 esperado|Marque essa caixa de seleção para retornar uma confirmação técnica (TA1) ao remetente do intercâmbio. Essa configuração especifica que o parceiro host que está enviando a mensagem solicitou uma confirmação do parceiro convidado no contrato. Essas confirmações são esperadas pelo parceiro host com base nas Configurações de Recebimento do contrato.|
|FA esperado|Marque essa caixa de seleção para retornar uma confirmação funcional (FA) ao remetente do intercâmbio e selecione se deseja as confirmações 997 ou 999 com base nas versões de esquema com as quais você está trabalhando. Essas confirmações são esperadas pelo parceiro host com base nas Configurações de Recebimento do contrato.|
|Versão de FA|Selecione a versão de FA|

### Esquemas
|Propriedade|Descrição |
|----|----|
|Versão|Selecione a versão X12|
|Tipo de Transação (ST01)|Selecione o tipo de transação|
|ESQUEMA|Selecione o esquema a ser usado. Esquema estão localizados na sua conta de integração. Para acessar seus esquemas, vincule sua conta de integração ao seu aplicativo lógico.|

### Envelopes
|Propriedade|Descrição |
|----|----|
|Uso de ISA11|Use este campo para especificar o separador em um conjunto de transações:</br></br>Selecione o identificador Padrão para usar a notação decimal de "." em vez da notação decimal no documento no pipeline de recebimento do EDI.</br></br>Selecione o separador Repetição para especificar o separador para ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetidos. Por exemplo, (^) geralmente é usado como separador de repetição. Para esquemas HIPPA, só é possível usar (^).</br>|
|Separador de repetição|Insira o separador de repetição|
|Número de Versão de Controle (ISA12)|Selecione a versão do padrão X12 que é usada pelo Portal de Serviços BizTalk para gerar um intercâmbio de saída.|
|Indicador de Uso (ISA15)|Especifique se o contexto de um intercâmbio é informações (I), dados de produção (P) ou dados de teste (T). O pipeline de recebimento do EDI promove essa propriedade de acordo com o contexto.|
|Esquema|Você pode definir como o Portal de Serviços BizTalk gera os segmentos GS e ST para um intercâmbio de codificação X12 que ele envia para o Pipeline de Envio.</br></br>Você pode associar os valores dos elementos de dados GS1, GS2, GS3, GS4, GS5, GS7 e GS8 aos valores dos elementos de dados Tipo de Transação e Versão/Lançamento. Quando o Portal de Serviços BizTalk determina que uma mensagem XML tem os valores definidos para os elementos Tipo de Transação e Versão/Lançamento em uma linha da grade, ele preenche os elementos de dados GS1, GS2, GS3, GS4, GS5, GS7 e GS8 no envelope do intercâmbio de saída com os valores da mesma linha da grade. Os valores dos elementos Tipo de Transação e Versão/Lançamento devem ser exclusivos.</br></br>Opcional. Para GS1, selecione um valor para o código funcional na lista suspensa.</br></br>Obrigatório. Para GS2, insira um valor alfanumérico para o remetente do aplicativo no mínimo dois caracteres e no máximo 15 caracteres.</br></br>Obrigatório. Para GS3, insira um valor alfanumérico para o destinatário do aplicativo no mínimo dois caracteres e no máximo 15 caracteres.</br></br>Opcional. Para GS4, selecione CCYYMMDD ou YYMMDD.</br></br>Opcional. Para GS5, selecione HHMM, HHMMSS ou HHMMSSdd.</br></br>Opcional. Para GS7, selecione um valor para a agência responsável na lista suspensa.</br></br>Opcional. Para GS8, insira um valor alfanumérico para o documento identificado com no mínimo um caractere e no máximo 12 caracteres.</br></br>**Observação**: esses são os valores que o Portal de Serviços BizTalk insere nos campos GS do intercâmbio que está criando se os elementos Tipo de Transação e Versão/Lançamento da mesma linha são correspondentes aos valores associados ao intercâmbio.|

### Números de Controle
|Propriedade|Descrição |
|----|----|
|Número de Controle de Intercâmbio (ISA13)|Obrigatório. Insira um intervalo de valores para o número de controle de intercâmbio usado pelo Portal de Serviços BizTalk para gerar um intercâmbio de saída. Insira um valor numérico entre 1 e 999.999.999.|
|Número de Controle de Grupo (GS06)|Obrigatório. Insira o intervalo de números que o Portal de Serviços BizTalk deve usar para o número de controle de grupo. Insira um valor numérico com no mínimo um caractere e no máximo nove caracteres.|
|Número de Controle de Conjunto de Transações (ST02)|Em Número de Controle de Conjunto de Transações (ST02), insira um intervalo de valores numéricos para os campos centrais obrigatórios e valores alfanuméricos para os campos opcionais de prefixo e sufixo. O comprimento máximo de todos os quatro campos é de nove caracteres.|
|Prefixo|Para designar o intervalo de números de controle de conjuntos de transações usados em uma confirmação, insira valores nos campos de Número de Controle de ACK (ST02). Insira um valor numérico nos dois campos centrais e um valor alfanumérico (se desejado) nos campos de prefixo e sufixo. Os campos centrais são obrigatórios e contêm os valores mínimo e máximo para o número de controle; os campos de prefixo e sufixo são opcionais. O comprimento máximo de todos os três campos é de nove caracteres.|
|Suffix|Para designar o intervalo de números de controle de conjuntos de transações usados em uma confirmação, insira valores nos campos de Número de Controle de ACK (ST02). Insira um valor numérico nos dois campos centrais e um valor alfanumérico (se desejado) nos campos de prefixo e sufixo. Os campos centrais são obrigatórios e contêm os valores mínimo e máximo para o número de controle; os campos de prefixo e sufixo são opcionais. O comprimento máximo de todos os três campos é de nove caracteres.|

### Conjuntos de Caracteres e Separadores
Além do conjunto de caracteres, você pode inserir um conjunto diferente de delimitadores a serem usados para cada tipo de mensagem. Se um conjunto de caracteres não for especificado para um determinado esquema de mensagem, o conjunto de caracteres padrão será usado.

|Propriedade|Descrição |
|----|----|
|Conjunto de Caracteres a ser usado|Selecione o conjunto de caracteres X12 para validar as propriedades que você inseriu para o contrato.</br></br>**Observação**: o Portal de Serviços BizTalk usa essa configuração apenas para validar os valores inseridos nas propriedades relacionadas do acordo. O pipeline de recebimento ou o de envio ignora essa propriedade de conjunto de caracteres ao executar o processamento de tempo de execução.|
|Esquema|Selecione o símbolo (+) e selecione um esquema na lista suspensa. Para o esquema selecionado, selecione o conjunto de separadores a ser usado:</br></br>Separador de elemento de componente – insira um único caractere para separar elementos de dados compostos.</br></br>Separador de elemento de dados – insira um único caractere para separar elementos de dados simples dentro elementos de dados compostos.</br></br></br></br>Caractere de substituição – marque essa caixa de seleção se a carga de dados contém caracteres que também são usados como separadores de componentes, de dados ou de segmentos. Em seguida,você pode inserir um caractere de substituição. Ao gerar a mensagem de saída X12, todas as instâncias de caracteres de separador na carga de dados são substituídas pelo caractere especificado.</br></br>Terminador de segmento – insira um único caractere para indicar o final de um segmento de EDI.</br></br>Sufixo – selecione o caractere a ser usado com o identificador de segmento. Se você designar um sufixo, o elemento de dados de terminador de segmento poderá ficar vazio. Se o terminador de segmento ficar vazio, você deverá designar um sufixo.|

### Validação
|Propriedade|Descrição |
|----|----|
|Tipo de Mensagem|Essa opção habilita a validação no destinatário do intercâmbio. Essa validação realiza a validação de EDI em elementos de dados do conjunto de transação, validando tipos de dados, restrições de comprimento, elementos de dados vazios e separadores à direita.|
|Validação de EDI||
|Validação Estendida|Essa opção permite a validação estendida de intercâmbios recebidos do remetente de intercâmbio. Isso inclui validação de comprimento do campo, campos opcionais e contagem repetida, além da validação de tipo de dados XSD. Você pode habilitar a validação estendida sem habilitar a validação de EDI ou vice-versa.|
|Permitir zeros à esquerda/direita|Essa opção especifica que um intercâmbio de EDI recebido não falhará na validação se um elemento de dados em um intercâmbio EDI não atender ao requisito de comprimento devido a espaços à direita, mas estiver de acordo com esse requisito quando os espaços são removidos.|
|Separador à direita|Essa opção especifica que um intercâmbio de EDI recebido não falhará na validação se um elemento de dados em um intercâmbio de EDI não estiver atender ao requisito de comprimento devido a zeros à esquerda (ou direita) ou espaços à direita,mas estiver de acordo com esse requisito quando os espaços ou zeros são removidos.</br></br>Selecione Não Permitido se não quiser permitir delimitadores e separadores à direita em um intercâmbio recebido do remetente de intercâmbio. Se o intercâmbio tiver delimitadores e separadores à direita, ele será declarado inválido.</br></br>Selecione Opcional para aceitar intercâmbios com ou sem delimitadores e separadores à direita.</br></br>Selecione Obrigatório se o intercâmbio recebido precisa ter delimitadores e separadores à direita.|

Depois de selecionar **OK** nas folhas abertas:
13. Selecione o bloco **Contratos** na folha da Conta de Integração e você verá o contrato recém-adicionado listado. ![](./media/app-service-logic-enterprise-integration-x12/x12-7.png)

## Saiba mais
- [Saiba mais sobre o Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

<!---HONumber=AcomDC_0803_2016-->