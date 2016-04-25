<properties
	pageTitle="Gerenciar e monitorar conectores e aplicativos de API no Serviço de Aplicativo | Microsoft Azure"
	description="Visualizar o desempenho de conectores e aplicativos de API no Serviço de Aplicativo do Azure; arquitetura de microsserviços"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="mandia"/>

# Gerenciar e monitorar aplicativos de API e conectores internos

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos.

Você criou um aplicativo de API interno. E agora?

No Azure, cada aplicativo de API é um site separado hospedado no Azure. Como resultado, você pode ver facilmente quantas solicitações são realizadas, bem como a quantidade de dados usados pelo conector. Também é possível realizar backup do aplicativo de API, criar alertas, habilitar o Tinfoil Security e adicionar usuários e funções.

Este tópico descreve algumas das diferentes opções para gerenciar o aplicativo de API.

Para ver esses recursos internos, abra o aplicativo de API no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Se o aplicativo de API estiver em seu quadro inicial, selecione-o para abrir as propriedades. Você também pode selecionar **Procurar**, selecionar **Aplicativos de API**e selecionar o aplicativo de API:

![][browse]

## Consulte as propriedades inseridas

Quando você abre o aplicativo de API, há vários recursos e tarefas disponíveis:

![][settings]

Você pode:

- **Configurações** mostra informações específicas sobre o aplicativo de API, incluindo detalhes da sua assinatura, e lista os usuários que têm acesso ao aplicativo de API. Você também pode aumentar ou diminuir o número de instâncias do aplicativo de API usando o recurso de escala, entre outros recursos.
- Use os botões **Iniciar** e **Parar** para controlar o aplicativo de API.
- Quando são feitas atualizações de produto para os arquivos subjacentes usados pelo aplicativo de API, você pode clicar em **Atualizar** para obter as versões mais recentes. Por exemplo, se houver uma correção ou uma atualização de segurança lançada pela Microsoft, clique em **Atualizar** para atualizar automaticamente o aplicativo de API de modo a incluir a correção.
- Selecione **Alterar Plano** para atualizar ou fazer downgrade com base no uso de dados do aplicativo de API. Você também pode usar esse recurso para ver o uso de dados.
- Ao criar um conector com tabelas, como o conector SQL, você pode digitar o nome de uma tabela para se conectar a ela. Um esquema com base na tabela é criado automaticamente e é disponibilizado quando você clica em **Baixar esquemas**. Em seguida, você pode usar o esquema baixado para criar uma transformação ou um mapa.

## Alterar valores de configuração de API ou conector inseridos

Após a configuração ou criação do conector interno, você pode alterar os valores inseridos. Por exemplo: se tiver configurado o conector SQL e quiser alterar o nome do SQL Server ou da tabela, você poderá fazer isso na folha Aplicativo de API do conector.

As etapas são as seguintes:

1. Abra o conector ou aplicativo de API. Quando você fizer isso, a folha Aplicativo de API será aberta.
2. Em **Fundamentos**, clique no hiperlink sob a propriedade de host. O hiperlink tem um nome como *slackconnector* ou *microsoftsqlconnector123*:

	![][apiapphost]

3. Na folha Host do aplicativo de API, selecione **Configurações**. Na folha Configurações, clique em **Configurações do aplicativo**. Os valores de configuração estão listados em **Configurações do aplicativo**:

	![][hostsettings]

4. Clique na configuração que deseja alterar, digite o novo valor e **salve** suas alterações.


## Instalar o Gerenciador de Conexão Híbrida - opcional

![][hcsetup]

O Gerenciador de Conexão Híbrida lhe oferece a capacidade de conectar-se a um sistema local, como SQL Server ou SAP. Essa conectividade híbrida usa o Barramento de Serviço do Azure para conectar e controlar a segurança entre os recursos do Azure e seus recursos locais.

Consulte [Usando o Gerenciador de Conexão Híbrida no Serviço de Aplicativo do Azure](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] O Gerenciador de Conexão Híbrida será necessário apenas se você estiver se conectando a um recurso local por trás do firewall. Se você não estiver se conectando a um sistema local, o Gerenciador de Conexão Híbrida talvez não esteja listado na folha do conector.

## Monitorar o desempenho
Métricas de desempenho são recursos internos incluídos em cada aplicativo de API criado. Essas métricas são específicas ao aplicativo de API hospedado no Azure. Métricas de exemplo:

![][monitoring]

Você pode:

- Selecione **Solicitações e erros** para adicionar diferentes métricas de desempenho, incluindo códigos de erro HTTP comumente conhecidos, como códigos de status HTTP 200, 400 ou 500. Também é possível consultar os tempos de resposta, quantas solicitações são feitas ao aplicativo de API, a quantidade de dados em entrada e a quantidade de dados em saída. Com base nas métricas de desempenho, você pode criar alertas de email quando uma métrica excede um limite de sua escolha.
- Em **Uso**, veja a quantidade de **CPU** usada pelo aplicativo de API, examine a **cota de uso** atual em MB e consulte a utilização máxima de dados com base na faixa de custo. **Despesa estimada** pode ajudá-lo a determinar os custos potenciais da execução do aplicativo de API.
- Selecione **Processos** para abrir o Gerenciador de Processos. Isso mostra as instâncias da Web e suas propriedades, incluindo o uso de memória e contagem de threads.

Usando essas ferramentas, você pode determinar se o plano do Serviço de Aplicativo deve ser escalado ou reduzido verticalmente, com base em suas necessidades de negócios. Esses recursos são internos ao portal, e nenhuma ferramenta adicional é necessária.

## Controlar a segurança

Os aplicativos de API usam segurança baseada em funções. Essas funções se aplicam a toda a experiência do Azure, incluindo aplicativos de API e outros recursos do Azure. As funções incluem:

Função | Descrição
--- | ---
Proprietário | Tem acesso completo à experiência de gerenciamento e pode conceder acesso a outros usuários ou grupos.
Colaborador | Tem acesso completo à experiência de gerenciamento. Não é capaz de conceder acesso a outros usuários ou grupos.
Leitor | Pode exibir todos os recursos, exceto segredos.
Administrador de Acesso do Usuário | Pode exibir todos os recursos, criar/gerenciar funções e criar/gerenciar tíquetes de suporte.

Consulte [Controle de acesso baseado em função no portal do Microsoft Azure](../active-directory/role-based-access-control-configure.md).

Você pode adicionar usuários e atribuir funções específicas ao aplicativo de API com facilidade. O portal mostra os usuários com acesso e as funções atribuídas a eles:

![][access]

- Selecione **Usuários** para adicionar um usuário, atribuir uma função e remover um usuário.
- Selecione **Funções** para ver todos os usuários em uma função específica, adicionar um usuário a uma função e remover um usuário de uma função.


## Mais coisas boas
- Selecione **Definição de API** para abrir o arquivo de Swagger criado automaticamente para o seu aplicativo de API específico.
- Selecione **Dependências** para exibir os arquivos exigidos pelo aplicativo de API. Por exemplo: ao usar o conector SAP, você instala alguns arquivos adicionais no Gerenciador de Conexão Híbrida local. Essas dependências são mostradas na folha do aplicativo de API.

> [AZURE.IMPORTANT] Quando você abre as propriedades do aplicativo de API e visualiza **Fundamentos**, há links de **Host** e **Gateway** que abrem novas folhas:
>
> ![][host]
>
> Essas propriedades são específicas ao site que hospeda o aplicativo de API. Ao usar um aplicativo de API ou um conector interno, a maioria dessas propriedades não se aplica, e é recomendável que você não as atualize. Se tiver criado seu aplicativo de API no Visual Studio e implantado-o em sua assinatura do Azure, você poderá usar as folhas Host e Gateway.


>[AZURE.NOTE] Se quiser começar a usar os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Criar seu aplicativo do Serviço de Aplicativo Azure](https://tryappservice.azure.com/?appservice=logic). Você pode criar um aplicativo lógico de início e de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido e não há compromissos.

## Leia mais

[Monitorar seus aplicativos lógicos](app-service-logic-monitor-your-logic-apps.md)<br/> [Lista de conectores de aplicativos de API no Serviço de Aplicativo](app-service-logic-connectors-list.md)<br/> [Controle de acesso baseado em função no portal do Microsoft Azure](../active-directory/role-based-access-control-configure.md)<br/> [Usando o Gerenciador de Conexão Híbrida no Serviço de Aplicativo do Azure](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png

<!---HONumber=AcomDC_0413_2016-->