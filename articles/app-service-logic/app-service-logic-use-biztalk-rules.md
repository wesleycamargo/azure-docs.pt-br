<properties
   pageTitle="Regras do BizTalk"
   description="Este tópico aborda os recursos de regras do BizTalk e fornece instruções sobre o uso"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/01/2015"
   ms.author="andalmia"/>

#Regras do BizTalk

As Regras de Negócios encapsulam as políticas e as decisões que controlam processos de negócios. Essas políticas podem ser definidas formalmente em manuais de procedimento, contratos ou acordos, ou podem existir sob a forma de conhecimentos ou experiências incorporados pelos funcionários. Essas políticas são dinâmicas e estão sujeitas a sofrer alterações com o passar do tempo, devido a mudanças em planos de negócios, regulamentações ou por outros motivos.

Implementar essas políticas em linguagens de programação tradicionais requer coordenação e tempo consideráveis e não permite que pessoas que não são programadores participem da criação e manutenção das políticas de negócios. As Regras de Negócios do BizTalk oferecem uma maneira de implementar rapidamente essas políticas e desacoplar o restante do processo de negócios. Isso permite fazer as alterações necessárias nas políticas de negócios sem afetar o restante do processo de negócios.

##Por que as regras

Há três motivos principais para usar as Regras de Negócio do BizTalk no processo de negócios:

* Desacoplar a lógica de negócio do código do aplicativo
- Permitir que analistas de negócios tenham mais controle sobre o gerenciamento da lógica de negócios
+ Alterações na lógica de negócios chegam à produção com mais rapidez

##Conceitos de regras

##Vocabulário

Um _vocabulário_ é uma coleção de definições que consiste de nomes amigáveis para os objetos de computação usados em ações e condições de regra. Definições de vocabulário facilitam a leitura, compreensão e compartilhamento das regras pelas pessoas em um domínio de negócios específico.

Os vocabulários são projetados para preencher a lacuna entre a semântica e a implementação de negócios. Por exemplo, uma vinculação de dados para um status de aprovação pode apontar para uma certa coluna de uma certa linha de um determinado banco de dados, representado como uma consulta SQL. Em vez de inserir esse tipo de representação complexa em uma regra, você pode criar uma definição de vocabulário, associada à vinculação de dados, com o nome amigável de "Status". Posteriormente, você pode incluir "Status" em qualquer regra, e o mecanismo de regra pode recuperar os dados correspondentes da tabela.

##Regra

Regras de negócio são instruções declarativas que governam a realização dos processos de negócios. Uma regra consiste de uma condição e de ações. A condição é avaliada e, se for avaliada como verdadeira, o mecanismo da regra dá início a uma ou mais ações. As regras da Estrutura de Regras de Negócios são definidas usando o formato a seguir:

_IF_ _condição_ _THEN_ _ação_

Considere o exemplo a seguir:

*IF (Se) a quantidade for menor ou igual aos fundos disponíveis* *THEN (Então) realize as transações e imprima o recibo*

Esta regra determina se uma transação deverá ser realizada por meio da aplicação da lógica de negócios, sob a forma de uma comparação de dois valores monetários, na forma de um valor de transação e fundos disponíveis. Você pode usar a Regra de Negócio para criar, modificar e implantar regras de negócio. Ou você pode realizar as tarefas anteriores de forma programática.

###Condições

Uma condição é uma expressão (booliana) verdadeira/falsa que consiste em um ou mais predicados. Em nosso exemplo, o predicado “menor ou igual a” é aplicado ao valor e aos fundos disponíveis. Essa condição será sempre avaliada como verdadeira ou falsa. Os predicados podem ser combinados com os operadores lógicos AND, OR e NOT para formar uma expressão lógica, que pode ser bastante grande, mas sempre será avaliada como verdadeira ou falsa.

###Ações

As ações são consequências funcionais da avaliação da condição. Se a condição de uma regra for atendida, uma ou mais ações correspondentes serão iniciadas. Em nosso exemplo, "realizar transação" e “imprimir recibo" são ações executadas quando, e somente quando, a condição (nesse caso, “SE o valor for menor que ou igual aos fundos disponíveis") for verdadeira. As ações são representadas na Estrutura de Regras de Negócios, executando operações de conjunto em documentos XML.

##Política

Uma política é um agrupamento lógico de regras. Você redige, salva e testa uma política e, quanto está satisfeito com os resultados, a utiliza em um ambiente de produção.

###Composição da política

Você pode compor políticas no portal de regras. Uma política pode conter um conjunto de regras de qualquer tamanho, mas normalmente você compõe uma política por meio de regras pertinentes a um domínio de negócios específico no contexto do aplicativo que usará a política.

###Teste de políticas
Você pode fazer uma execução de teste da política antes que ela seja usada em um ambiente de produção. O Portal de Regras permite que você forneça entradas para uma política, execute a política e veja sua saída. A saída inclui logs, a execução da regra, a avaliação de condição e as saídas resultantes.

##Cenário de Exemplo - Solicitação de Resgate de Seguro
Vamos selecionar um cenário de exemplo e percorrê-lo enquanto compomos sua lógica de negócios.

![Alt text][1]

Em um cenário de Solicitação de Resgate de Seguro bastante simples, o solicitante envia sua solicitação de resgate (por meio de qualquer cliente, como site, aplicativo para telefone etc). A Solicitação de Resgate de Seguro é encaminhada para a Unidade de Processamento de Solicitações e, com base no resultado do processamento, ela pode ser aprovada, rejeitada ou encaminhada para processamento manual. Em nosso cenário, a Unidade de Processamento de Solicitação seria a que contém a lógica de negócios do sistema. Examinando esta mais de perto, podemos ver o seguinte:

![Alt text][2]

Agora, vamos usar use Regras de Negócio para implementar a lógica de negócios.


##Criação de Aplicativo de API de Regras


1. Faça logon no Portal do Azure e vá para a home page.
1. Clique em Novo -> Azure Marketplace -> Aplicativos de API - > Regras do BizTalk -> Criar ![Alt text][3]
1. Na nova lâmina que será aberta, insira as seguintes informações:  
	1. Nome – dê um nome para seu Aplicativo de API de Regras
	1. Plano de hospedagem do aplicativo - selecione ou crie uma plano de hospedagem na Web
	1. Camada de preços – escolha a camada de preço na qual deseja posicionar esse aplicativo
	1. Grupo de recursos - selecione ou crie um grupo de recursos onde o aplicativo deve residir
	1. Local - escolha a região geográfica onde você quer que o aplicativo seja implantado.
4.	Clique em Criar. Seu Aplicativo de API do de Regras do BizTalk seria criado em alguns minutos.

##Criação do vocabulário
Após criar um Aplicativo de API de Regras do BizTalk, a próxima etapa seria criar vocabulários. A expectativa é que o desenvolvedor seja a pessoa mais comum a fazer esse exercício. Para fazer isso, execute as seguintes etapas:


1. Navegue até o Aplicativo de API criado em Procurar -> Aplicativo de API -><Your Rules API App>. Isso deve levar você a um Painel do Aplicativo de API de Regras semelhante a este:

   ![Alt text][4]

2\. Em seguida, clique em "Definições de vocabulário". Isso exibe a tela de Criação de Vocabulário. Clique em "Adicionar" para começar a adicionar novas definições de vocabulário. Dois tipos de definições de vocabulário têm suporte – Literal e XML.

##Definição literal
1.	Após você clicar em "Adicionar", uma nova lâmina "Adicionar Definição" abrirá. Insira os valores a seguir
  1.	Nome – apenas caracteres alfanuméricos são esperados, sem caracteres especiais. Ele deve ser exclusivo em sua lista de definições de vocabulário existente.
  2.	Descrição – campo opcional.
  3.	Tipo – dois tipos têm suporte. Neste exemplo, escolha Literal
  4.	Tipo de entrada – permite aos usuários selecionarem o tipo de dados da definição. Atualmente, você pode selecionar quatro tipos de dados: i. Cadeia de caracteres – esses valores devem ser inseridos entre aspas duplas (“Exemplo de cadeia de caracteres") ii. Booliano – pode ser verdadeiro ou falso iii. Número – pode ser qualquer número decimal iv. DateTime – significa que a definição é do tipo data. Os dados devem ser inseridos usando este formato – mm/dd/aaaa hh:mm:ss AM\\PM v. Entrada – é onde você insere o valor da sua definição. Os valores inseridos aqui devem estar de acordo com o tipo de dados escolhido. O usuário pode digitar um valor único, um conjunto de valores separados por vírgulas ou um intervalo de valores usando a palavra-chave “to” (“a”). Por exemplo, o usuário pode inserir um valor exclusivo 1; um conjunto de 1, 2, 3 ou um intervalo de 1 a 5. Observe que o intervalo tem suporte somente para números.

![Alt text][5]
##Definição XML
Se o tipo de vocabulário escolhido for XML, as entradas a seguir deverão ser especificadas a. Esquema – Clicar abrirá uma nova lâmina que permite que o usuário escolha dentre uma lista de esquemas já carregados ou carregue um novo esquema. b. XPATH – essa entrada é desbloqueada somente após a seleção de um esquema na etapa anterior. Clicar exibirá o esquema que foi selecionado e permite que o usuário selecione o nó para o qual uma definição de vocabulário precisa ser criada. FACT– esta entrada identifica qual objeto de dados deve ser fornecido ao mecanismo de regras para processamento. Esta é uma propriedade avançada e por padrão é definida como pai do XPATH selecionado. FACT se torna especialmente importante em cenários de coleta e encadeamento.

![Alt text][6]

### Adicionar em massa
As etapas acima capturaram a experiência para criar definições de vocabulário. Após serem criadas, as definições de vocabulário aparecerão em formato de lista. Há requisitos para poder gerar várias definições por meio do mesmo esquema em vez de repetir as etapas acima toda vez. E é aqui que a funcionalidade Adicionar em massa se torna muito útil. Clicar em "Adicionar em massa" levará você para uma nova lâmina. A primeira etapa é selecionar o esquema para o qual as definições serão criados. Clicar abrirá uma nova lâmina que permite que o usuário escolha dentre uma lista de esquemas já carregados ou carregue um novo esquema. Agora, a propriedade XPATHS é desbloqueada. Clicar nessa opção abrirá o Visualizador de esquema, em que vários nós podem ser selecionados. Os nomes de várias definições criadas assumirão como padrão o nome do nó selecionado. Eles podem ser modificados após a criação.

![Alt text][7]

##Criação de política
Após o desenvolvedor ter criado os vocabulários necessários, a expectativa é de que o Analista de negócios crie as Políticas comerciais por meio do Portal do Azure. 1. No Aplicativo de Regras criado, há uma lente de Política. Clicando nela, o usuário é levado à página de criação de políticas. 2. Essa página mostrará a lista de políticas que esse Aplicativo de Regras específico tem. O usuário pode adicionar uma nova Política simplesmente digitando o nome da política e pressionando a tecla tab. Várias políticas podem residir em um único Aplicativo de API de Regras. 3. Clicar na política criada levará o usuário à página de detalhes da política, em que é possível ver as regras que estão na política. ![Alt text][8] 4. Clique em "Adicionar Novo" para adicionar uma nova regra. Isso levará você a uma nova lâmina.

##Criação de regra
Uma regra é um conjunto de condições e instruções de ação. As ações são executadas se a condição for avaliada como verdadeira. Na lâmina Criar Regra, dê um nome de regra exclusivo (para essa política) e uma descrição (opcional). A caixa de Condição pode ser usada para criar instruções condicionais complexas. A seguir, estão as palavras-chave com suporte: 1. And – operador condicional 2. Or – operador condicional 3. does\_not\_exist 4. exists 5. false 6. is\_equal\_to 7. is\_greater\_than 8. is\_greater\_than\_equal\_to 9. is\_in 10. is\_less\_than 11. is\_less\_than\_equal\_to 12. is\_not\_in 13. is\_not\_equal\_to 14. mod 15. true

A caixa de Ação (Then) pode conter várias instruções, um por linha, para criar as ações que serão executadas. A seguir, estão as palavras-chave com suporte: 1. igual a 2. falso 3. verdadeiro 4. interromper 5. mod 6. nulo 7. atualizar

As caixas de condição e ação fornecem IntelliSense para ajudar o usuário a criar uma regra rapidamente. Isso pode ser disparado pressionando ctrl+espaço ou simplesmente começando a digitar. Palavras-chave correspondentes aos caracteres digitados serão filtradas e mostradas automaticamente. A janela de IntelliSense exibirá todas as palavras-chave e definições de vocabulário. ![Alt text][9]

##Encadeamento de encaminhamento explícito
As Regras do BizTalk dão suporte ao encadeamento de encaminhamento explícito. Isso significa que, se os usuários quiserem reavaliar as regras em resposta a determinadas ações, eles podem fazer isso usando palavras-chave. A seguir estão as palavras-chave com suporte: 1. atualizar <vocabulary definition> – essa palavra-chave reavalia todas as regras que usam a definição de vocabulário especificada em sua condição. 2. Halt – esta palavra-chave interrompe todas as execuções de regra

##Habilitar\\desabilitar regras
Cada regra da política pode ser habilitada ou desabilitada. Por padrão, todas as regras ficam habilitadas. Regras desabilitadas não são executadas durante a execução da política. Você pode habilitar\\desabilitar as regras diretamente na lâmina da regra – os comandos estão disponíveis na barra de comandos na parte superior da lâmina. Além disso, na política, o menu de contexto (clique com o botão direito do mouse em uma regra) tem a opção de habilitar\\desabilitar.

##Prioridade da regra
Todas as regras da política são executadas em ordem. A prioridade de execução é determinada pela ordem em que elas ocorrem na política. Essa prioridade pode ser alterada simplesmente arrastando e soltando a regra.

##Testar política
Depois de criar sua política, antes de utilizá-lo na produção, você pode testar a política. Usando o comando “Test Policy", os usuários acessam a lâmina Testar Política. Nesta lâmina, você pode ver uma lista das definições de vocabulário usadas na política que exigem uma entrada do usuário. Os usuários podem adicionar valores manualmente para essas entradas para seu cenário de teste. Eles também podem optar por importar XMLs de teste para as entradas. Após todas as entradas serem fornecidas, o teste pode ser executado e as saídas para cada definição de vocabulário serão exibidas na coluna de saída para facilitar a comparação. Para exibir logs amigáveis para o Analista de negócios, clique em "Exibir Logs" para exibir os logs de execução. Para salvar os logs, a opção "Salvar Saída" é disponibilizada para armazenar todos os dados relacionados ao teste para uma análise independente.

## Usando regras em aplicativos lógicos
Após a política ter sido criada e testada, ela está pronta para consumo. Os usuários podem criar um novo aplicativo lógico em Novo -> Aplicativo Lógico. No designer, as regras do BizTalk estão disponível na galeria à direita. Agora, é possível arrastar e soltar em qualquer lugar da superfície do designer. Feito isso, haverá a opção de escolher quais Aplicativos de API de Regras (Ação) serão o alvo. As ações incluem a lista de políticas que devem ser executadas. Escolha uma política específica após a qual as entradas necessárias para a política precisam ser inseridas. Os usuários podem usar a saída do Aplicativo de API de Regras downstream para tomadas de decisões adicionais.

## Usando regras por meio de APIs
O Aplicativo de API de Regras também pode ser invocado usando um conjunto avançado de APIs disponíveis. Assim, os usuários não ficam restritos a usar apenas fluxos, e podem usar as regras em qualquer aplicativo fazendo chamadas REST. As APIs REST exatas disponíveis podem ser exibidas clicando na lente "Definição de API" no painel de Regras.

![Alt text][10]

A seguir, há um exemplo de como se pode usar essa API em C#

   			// Constructing the JSON message to use in API call to Rules API App

			// xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0="http://tempuri.org/XMLSchema.xsd">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

			// The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
			// In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
			// This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

			// The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Observe que o aplicativo de API de regras acima tem suas configurações de segurança definidas como "Público Anôn". Isso pode ser definido de dentro do aplicativo de API usando - Todas as configurações -> Configurações do Aplicativo -> Nível de Acesso

![Alt text][11]

## Editando o vocabulário e a política
Uma das principais vantagens do uso de Regras Comerciais é que alterações feitas na lógica de negócios podem ser levadas à produção muito mais rapidamente. Qualquer alteração feita em políticas e vocabulários é aplicada imediatamente à produção. O usuário simplesmente precisa navegar até a respectiva definição de vocabulário ou política e fazer a alteração para que ela entre em vigor.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG

<!---HONumber=Oct15_HO2-->