<properties 
	pageTitle="Conectores entre empresas e aplicativos da API no Serviço de Aplicativo do Microsoft Azure | Azure" 
	description="Saiba como criar e configurar conectores EDIFACT, EDI, AS2 e TPM; arquitetura de microsserviços" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="mandia"/>

# Conectores entre empresas e aplicativos de API no Serviço de Aplicativo do Microsoft Azure
O Serviço de Aplicativo do Microsoft Azure (ou Serviço de Aplicativo, para abreviar) inclui muitos aplicativos de API do BizTalk que são vitais para ambientes de integração. Esses Aplicativos de API são baseados nos conceitos e ferramentas usados no BizTalk Server, mas agora estão disponíveis como parte do Serviço de Aplicativo do Azure.

Uma categoria desses aplicativos de API são os aplicativos de API B2B (Entre Empresas). Usando esses aplicativos de API B2B, você pode facilmente adicionar parceiros, criar contratos e fazer tudo o que faria localmente usando EDI, AS2 e EDIFACT.

Esses aplicativos de API B2B oferecem recursos de "Gatilho" e "Ação". Um Gatilho inicia uma nova instância com base em um evento específico, como a chegada de uma mensagem X12 de um parceiro. Uma Ação é o resultado, como após receber uma mensagem X12 e depois enviar a mensagem usando AS2.


## O que é um conector entre empresas ou aplicativos de API
O recurso B2B (Entre Empresas) inclui Aplicativos de API existentes e pré-criados que permitem que diferentes empresas, divisões, aplicativos, e assim por diante, se comuniquem usando AS2, EDI e EDIFACT.

Os aplicativos de API B2B incluem:

Conector ou aplicativos de API | Descrição
--- | ---
Gerenciamento de parceiros comerciais do BizTalk | Um aplicativo de API que cria relações B2B usando parceiros e contratos. Essas relações utilizam o protocolo AS2, EDIFACT e X12.<br/><br/>O aplicativo de API do TPM é o requisito básico do conector AS2 e dos aplicativos de API X12 ou EDIFACT. 
Conector AS2 | Um conector que recebe e envia mensagens usando o transporte AS2. O conector transporta dados de forma segura e confiável pela Internet.
BizTalk EDIFACT | Um aplicativo de API que recebe e envia mensagens usando EDIFACT. O EDIFACT é também conhecido como UN/EDIFACT (Nações Unidas/Intercâmbio Eletrônico de Dados para Administração, Comércio e Transporte) e é amplamente usado em vários setores.
BizTalk X12 | Um aplicativo de API que recebe e envia mensagens usando o protocolo X12. O X12 também é conhecido como ASC X12 (Comitê de Normas Certificadas X12) e é amplamente usado em vários setores. 


Usando esses aplicativos de API, você pode executar diferentes tarefas relacionadas a mensagens de EDI. Por exemplo, usando o conector AS2, você pode, de maneira segura, receber e enviar diferentes tipos de mensagens (EDI, XML, arquivo simples e assim por diante) para um cliente, uma divisão da empresa, como os Recursos Humanos, ou qualquer pessoa que usa AS2.

Você pode, com facilidade, criar tantos Aplicativos de API quanto desejar. Também é possível reutilizar um único aplicativo de API em vários cenários ou fluxos de trabalho.

Você pode fazer isso sem escrever nenhum código. Vamos começar.


## Requisitos para começar
Quando você cria aplicativos de API B2B, há alguns recursos necessários. Esses itens devem ser criados por você antes que possam ser usados em outros aplicativos de API. Esses requisitos incluem:

Requisito | Descrição
--- | ---
Banco de Dados SQL Azure | Armazena itens B2B incluindo esquemas, parceiros, certificados e contratos. Cada um dos aplicativos de API B2B requer seu próprio banco de dados SQL do Azure. <br/><br/>\* \* Observação \* \* Copie a cadeia de conexão para esse banco de dados.<br/><br/>[Criar um banco de dados SQL do Azure](../sql-database-create-configure.md)
Contêiner do Armazenamento de Blob do Azure | Armazena propriedades das mensagens quando o arquivamento AS2 está habilitado. Se você não precisar de arquivamento de mensagens AS2, não será necessário um contêiner de armazenamento. <br/><br/>\* \* Observação \* \* Se você estiver habilitando arquivamento, copie a cadeia de conexão para o armazenamento de Blob.<br/><br/>[Sobre contas de armazenamento do Azure](../storage-create-storage-account.md)
Namespace do Barramento de Serviço e seus valores de chave | Armazena dados de envio em lote de X12 e EDIFACT. Se você não precisar de envio em lote, não será necessário um namespace de Barramento de Serviço.<br/><br/>\*\* Observação \*\* Se você estiver habilitando o processamento em lote, copie esses valores.<br/><br/>[Criar um Namespace de barramento de serviço](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Instância do TPM | Uma instância do TPM (Gerenciamento de Parceiros Comerciais) do BizTalk é necessária para criar um conector AS2 e um aplicativo de API X12 ou EDIFACT. Quando cria o aplicativo de API do TPM, você está criando a instância do TPM. <br/><br/>\*\*Observação\*\* Saiba o nome do seu Aplicativo de API do TPM. 


## Criar os aplicativos de API
Aplicativos de API B2B podem ser criados usando o Portal do Azure ou APIs REST.


### Criar os aplicativos de API usando APIs REST
[Consulte a documentação sobre como usar as APIs REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### Criar os aplicativos de API B2B no Portal do Azure
No Portal do Azure, você pode criar aplicativos de API B2B durante a criação de aplicativos lógicos, Web ou móveis. Ou você pode criar um usando sua própria folha. Ambos os modos são fáceis, assim, depende das suas necessidades ou preferências. Alguns usuários preferem criar primeiro todos os aplicativos de API B2B com suas propriedades específicas. Em seguida, crie os aplicativos lógicos/Web/móveis e adicione os aplicativos de API B2B criados.

As seguintes etapas criam os aplicativos de API B2B usando a folha de aplicativos de API.


#### Criar os aplicativos de API de TPM (Gerenciamento de Parceiros Comerciais) do BizTalk

> [AZURE.NOTE]Uma instância do TPM (Gerenciamento de Parceiros Comerciais) do BizTalk é necessária para criar um conector AS2 e um aplicativo de API X12 ou EDIFACT. Quando cria o aplicativo de API do TPM, você está criando a instância do TPM.

As etapas a seguir criam a instância de TPM:

1. No quadro inicial (a Home page) do portal do Azure, selecione **Marketplace**. **Aplicativos de API** lista todos os aplicativos de API e conectores existentes. Você também pode **Pesquisar** por aplicativos de API B2B específicos.
2. Selecione **Gerenciamento de Parceiros Comerciais do BizTalk**. Na folha nova, selecione **Criar**. 
3. Insira as propriedades: 

	Propriedade | Descrição
--- | ---
Nome | Insira um nome para a instância do TPM. Por exemplo, você pode chamá-la de *AccountsPayableTPM*.
Configurações do pacote | Insira a **Cadeia de Conexão de Banco de Dados** ADO.NET no Banco de Dados SQL do Azure que você criou. <br/><br/>Quando você copia a cadeia de conexão, a senha não é adicionada a ela. Certifique-se de inserir a senha na cadeia de conexão.
Plano do Serviço de Aplicativo | Lista seu plano de pagamento. Você pode alterá-lo se precisar de mais ou menos recursos.
Camada de preços | Propriedade somente leitura que lista a categoria de preço em sua assinatura do Azure. 
Grupo de recursos | Crie um grupo novo ou use um existente. Todos os Aplicativos de API e conectores para aplicativos lógicos, Web e móveis devem estar no mesmo grupo de recursos. <br/><br/>[Usando grupos de recursos](../resource-group-overview.md) explica essa propriedade. 
Assinatura | Propriedade somente leitura que lista sua assinatura atual.
Local | A localização geográfica que hospeda o serviço do Azure. 
Adicionar ao quadro inicial | Selecione esta opção para adicionar o aplicativo de API B2B o ao seu quadro inicial (a home page).

4. Selecione **Criar**.

Depois que o aplicativo de API do TPM (Instância de TPM) é criado, você pode criar o conector AS2 e/ou os aplicativos de API EDIFAC ou X12.


#### Criar o conector AS2

1. No quadro inicial (a Home page) do portal do Azure, selecione **Marketplace**. **Aplicativos de API** lista todos os aplicativos de API e conectores existentes. Você também pode **Pesquisar** por aplicativos de API B2B específicos.
2. Selecione **Conector AS2**. Na folha nova, selecione **Criar**. 
3. Insira as propriedades: 

	Propriedade | Descrição
--- | ---
Nome | Insira um nome para o conector AS2. Por exemplo, você pode chamá-lo de *AS2Connector*.
Configurações do pacote | Insira as configurações específicas ao aplicativo de API, como o nome da Instância do TPM. <br/><br/>Consulte [Adicionar configurações do pacote de AS2](#AddAS2Conn) neste tópico para ver as propriedades específicas. 
Plano do Serviço de Aplicativo | Lista seu plano de pagamento. Você pode alterá-lo se precisar de mais ou menos recursos.
Camada de preços | Propriedade somente leitura que lista a categoria de preço em sua assinatura do Azure. 
Grupo de recursos | Crie um grupo novo ou use um existente. [Usando grupos de recursos](../resource-group-overview.md) explica essa propriedade. 
Assinatura | Propriedade somente leitura que lista sua assinatura atual.
Local | A localização geográfica que hospeda o serviço do Azure. 
Adicionar ao quadro inicial | Selecione esta opção para adicionar o aplicativo de API B2B o ao seu quadro inicial (a home page).

	**<a name="AddAS2Conn"></a>Configurações de pacote do conector AS2**

	Propriedade | Descrição
--- | --- 
Cadeia de conexão de banco de dados | Insira a cadeia de conexão ADO.NET ao Banco de Dados SQL do Azure que você criou. Quando você copia a cadeia de conexão, a senha não é adicionada a ela. Certifique-se de inserir a senha na cadeia de conexão antes de colar.
Habilitar arquivamento de mensagens de entrada | Opcional. Habilite esta propriedade para armazenar as propriedades das mensagens AS2 de entrada recebidas de parceiros. 
Cadeia de Conexão do Armazenamento de Blob do Azure | Insira a cadeia de conexão no contêiner do Armazenamento de Blob do Azure criado por você. Quando o Arquivamento está habilitado, as mensagens codificadas e decodificadas são armazenadas neste contêiner de armazenamento.
Nome da instância do TPM | Insira o nome do Aplicativo de API do **Gerenciamento de Parceiros Comerciais do BizTalk** criado anteriormente. Quando você cria o conector de AS2, esse conector executa somente os contratos de AS2 dentro dessa instância específica do TPM.

4. Selecione **Criar**.


#### Criar os aplicativos de API X12 ou EDIFACT

1. No quadro inicial (a Home page) do portal do Azure, selecione **Marketplace**. **Aplicativos de API** lista todos os aplicativos de API e conectores existentes. Você também pode **Pesquisar** por aplicativos de API B2B específicos.
2. Selecione **BizTalk X12** ou **BizTalk EDIFACT**. Na folha nova, selecione **Criar**. 
3. Insira as propriedades: 

	Propriedade | Descrição
--- | ---
Nome | Insira um nome para o Aplicativo de API B2B. Por exemplo, você pode chamá-lo de *EDI850APIApp*.
Configurações do pacote | Insira as configurações específicas ao aplicativo de API, como o nome da Instância do TPM. <br/><br/>Consulte [Configurações de pacote X12 ou EDIFACT](#AddX12) neste tópico para ver as propriedades específicas. 
Plano do Serviço de Aplicativo | Lista seu plano de pagamento. Você pode alterá-lo se precisar de mais ou menos recursos.
Camada de preços | Propriedade somente leitura que lista a categoria de preço em sua assinatura do Azure. 
Grupo de recursos | Crie um grupo novo ou use um existente. [Usando grupos de recursos](../resource-group-overview.md) explica essa propriedade. 
Assinatura | Propriedade somente leitura que lista sua assinatura atual.
Local | A localização geográfica que hospeda o serviço do Azure. 
Adicionar ao quadro inicial | Selecione esta opção para adicionar o aplicativo de API B2B o ao seu quadro inicial (a home page).

	**<a name="AddX12"></a>Configurações do pacote de aplicativos de API X12 e EDIFACT**

	Propriedade | Descrição
--- | --- 
Cadeia de conexão de banco de dados | Insira a cadeia de conexão ADO.NET ao Banco de Dados SQL do Azure que você criou. Quando você copia a cadeia de conexão, a senha não é adicionada a ela. Certifique-se de inserir a senha na cadeia de conexão antes de colar.
Namespace do Barramento de Serviço | Insira o namespace de Barramento de Serviço que você criou. Necessário somente quando o envio em lote estiver habilitado. 
Nome da chave de acesso compartilhado do namespace do Barramento de Serviço | Insira a chave de acesso do namespace do Barramento de Serviço que você criou. Necessário somente quando o envio em lote estiver habilitado. 
Valor da chave de acesso compartilhado do namespace do Barramento de Serviço | Insira o valor da chave de acesso do namespace do Barramento de Serviço que você criou. Necessário somente quando o envio em lote estiver habilitado. 
Nome da instância do TPM | Insira o nome do Aplicativo de API do **Gerenciamento de Parceiros Comerciais do BizTalk** criado anteriormente. Quando você cria aplicativos de API X12 ou EDIFACT, esse aplicativo de API executa somente contratos X12/EDFIACT dentro dessa instância específica do TPM.

4. Selecione **Criar**.


## Adicione seus parceiros, contratos, certificados e esquemas 
No Portal de Gerenciamento do Azure, abra seu aplicativo de API do TPM. Na seção **Componentes**, adicione seus parceiros, contratos, certificados e esquemas.

Você também pode adicionar contratos a conectores AS2, aplicativos de API do X12 e EDIFACT.


## Monitorar seus aplicativos de API
No Portal de Gerenciamento do Azure, abra seu aplicativo de API do TPM. Na seção **Operações**, você pode exibir diferentes operações de gerenciamento. Por exemplo, você pode:

- Exibir eventos de informações e erro
- Exibir a contagem de threads e uso de memória do processo de trabalho (w3wp)
- Exibir os logs do servidor de aplicativo e Web

Mais em [Monitorar seus Aplicativos Lógicos](app-service-logic-monitor-your-logic-apps.md).


## Adicionar aplicativos de API ao seu aplicativo 
O Serviço de Aplicativo do Microsoft Azure expõe os diferentes tipos de aplicativos que podem usar esses aplicativos de API B2B. Você pode criar um novo ou adicionar seus aplicativos existentes do API B2B a aplicativos lógicos, móveis ou da Web.

Dentro de seu aplicativo, a simples seleção dos aplicativos de API B2B da Galeria os adiciona automaticamente ao seu aplicativo.

> [AZURE.IMPORTANT]Para adicionar conectores e aplicativos de API criados anteriormente, crie aplicativos lógicos, móveis ou da Web no mesmo grupo de recursos.

As etapas a seguir adicionam aplicativos de API B2B a aplicativos lógicos, móveis ou da Web:

1. No quadro inicial do portal do Azure (home page), vá até **Marketplace** e procure aplicativos lógicos, móveis ou Web. 

	Se você estiver criando um novo aplicativo, pesquise aplicativos lógicos, móveis ou Web. Selecione o aplicativo e, na nova folha, selecione **Criar**. [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md) lista as etapas.

2. Abra seu aplicativo e selecione **Gatilhos e Ações**.

3. Na **Galeria**, selecione o aplicativo de API B2B, que é adicionado automaticamente a seu aplicativo. Você também pode criar um novo aplicativo de API B2B.

	> [AZURE.IMPORTANT]O conector AS2 e os aplicativos de API X12, EDIFACT exigem uma instância do TPM. Então, se você estiver criando novos aplicativos de API B2B, crie primeiro o aplicativo de API do TPM e crie o conector AS2 e o aplicativos de API X12 ou EDIFACT.

4. Selecione **OK** para salvar suas alterações.


## Mais recursos B2B

[Criando um processo B2B](app-service-logic-create-a-b2b-process.md)<br/> [Criando um acordo entre parceiros comerciais](app-service-logic-create-a-trading-partner-agreement.md)<br/> [O que são conectores e aplicativos de API do BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## Leia sobre aplicativos lógicos e aplicativos Web
[O que são aplicativos lógicos?](app-service-logic-what-are-logic-apps.md)<br/> [Sites e aplicativos Web no Serviço de Aplicativo do Azure](../app-service-web/app-service-web-overview.md)


## Mais conectores

[Lista de conectores e aplicativos de API](app-service-logic-connectors-list.md)<br/><br/> [O que são conectores e aplicativos de API do BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=July15_HO5-->