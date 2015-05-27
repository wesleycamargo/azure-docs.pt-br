<properties 
	pageTitle="Conectores Sociais de Aplicativos de API do Microsoft Azure | Microsserviço de Aplicativos de API" 
	description="Aprenda a criar Aplicativos de API do Conector Social do Microsoft Azure e adicionar o Aplicativo de API ao aplicativo lógico; microsserviços" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="connectors" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>

# Conectores sociais no Serviço de Aplicativo do Microsoft Azure


## O que é um conector de aplicativo de API Social
Conectores sociais são "aplicativos de API" que podem se conectar a aplicativos sociais e realizar várias *Ações* em nome do usuário autenticado. A maioria desses conectores também pode ser configurada com um *Gatilho*. Gatilhos são eventos (semelhantes a eventos no .NET Framework) que podem ser configurados em alguns conectores. Gatilhos então podem iniciar as ações que foram configuradas para o conector (ou qualquer outro). Por exemplo, uma instância do conector do Twitter pode ser configurada com um gatilho de *nova mensagem* em que o gatilho seja definido como qualquer novo tweet que mencione @VisualStudio. Esse gatilho então pode ser configurado para iniciar uma *Ação* para retweetar o tweet.

Os seguintes conectores sociais estão disponíveis na Microsoft:

- Facebook
- Twitter
- Chatter
- Yammer
- Twilio

Aqui está uma breve descrição de cada um dos conectores sociais:

<table>
<tr>
<th> Nome</th>
<th> Descrição</th>
<th> Gatilhos</th>
<th>Ações</th>

<tr>
<td>Facebook
<td>Um conector do Facebook permite postar e ler mensagens, comentários, eventos e status do feed do Facebook. Você também pode postar fotos e vídeos.
<td>Nova postagem
<td><li>Publicar postagem
	<li>Publicar foto 
</tr>

<tr>
<td>Twitter
<td>Um conector do Twitter permite realizar várias operações, como postar, receber tweets e pesquisar tweets. Você também pode usar um conector do Twitter para acompanhar o que está acontecendo no Twitter, obtendo detalhes como tweets, linhas de tempo, seguidores e amigos.
<td>Novos tweets
<td><li>Pesquisar tweets
	<li>Tweet
	<li>Obter a linha do tempo do usuário
	<li>Retweet
</tr>

<tr>
<td>Chatter
<td>Um conector Chatter pode ser usado para ler, postar e pesquisar mensagens no feed de conversas.
<td>Nova mensagem
<td><li>Postar mensagem
<li>Pesquisar mensagem
</tr>

<tr>
<td>Yammer
<td>Um conector Yammer permite postar e ler mensagens de grupos do Yammer.
<td>Novas mensagens
<td><li>Postar mensagem	
	<li>Obter mensagem do feed\do grupo	
</tr>

<tr>
<td>Twilio
<td>Um conector Twilio permite enviar e receber SMSes de sua conta do Twilio. Também permite recuperar os números de telefone e dados de uso.
<td>N/D
<td><li>Mensagens: enviar, listar, obter e pesquisar
	<li>Números de telefone: comprar, listar e obter números locais e gratuitos
</tr>
</table>


Agora que você tem uma ideia do que os nossos conectores sociais podem fazer, vamos analisar alguns casos de uso simples para esses conectores.

## Por que usar conectores?

Os conectores aceleram o desenvolvimento de aplicativos e até mesmo permitem que não desenvolvedores criem aplicativos totalmente funcionais de nível empresarial sem necessidade de aprender uma linguagem de programação ou escrever qualquer código.

### Monitorando comentários do cliente ###
Imagine que sua empresa recentemente lançou um novo aplicativo e a equipe quer saber o que os clientes estão dizendo sobre o aplicativo na mídia social. Cada membro da equipe pode verificar periodicamente os vários sites de mídia social e estimar quais palavras-chave os clientes podem usar para discutir seu aplicativo. Mas, de maneira mais elegante, você pode simplesmente criar um conector do Twitter, configurá-lo para monitorar palavras-chave, menções e hastags específicas no Twitter. Você pode usar o conector SMTP para enviar um email com o conteúdo dos tweets correspondentes aos membros da equipe. Você pode fazer algo semelhante monitorando o Facebook e o Yammer e enviar um SMS, por meio de um conector de Twilio, para sua equipe DevOps se o conteúdo das postagens do Facebook ou do Yammer indicar que pode haver um problema crítico que atinja seus clientes. Você pode fazer tudo isso sem escrever uma única linha de código.

Vamos começar.


## Criar um conector
É possível criar conectores no Portal do Azure

### Criar um Conector Social no Portal do Microsoft Azure

1. No portal do Azure, selecione **NOVO** > **Web + móvel** > **Azure Marketplace**.
2. **Pesquise** o conector ou selecione-o na lista. Quando selecionada, uma nova folha ou janela é aberta. Selecione **Criar**. 
3. Insira as seguintes propriedades para o conector: <table> <tr><th>Propriedade</th> <th>Descrição</th> </tr> <tr><td>Nome</td> <td>Insira qualquer nome para seu aplicativo de API. Por exemplo, você pode chamá-lo de RulesDiscountTaxCode ou APIAppValidateXML</td> </tr> <tr><td>Plano do Serviço de Aplicativo</td> <td>Lista seu plano de pagamento. Você pode alterá-lo se precisar de mais ou menos recursos</th> </td> <tr><td>Camada de Preços</td> <td>Propriedade somente leitura que lista a categoria de preço na assinatura do Azure.</td> </tr> <tr><td>Grupo de Recursos</td> <td>Crie um novo ou use um grupo existente. Usando grupos de recursos explica essa propriedade</td> </tr> <tr><td>Assinatura</td> <td>Propriedade somente leitura que lista sua assinatura atual</td> </tr> <tr><td>Local</td> <td>O local geográfico que hospeda o serviço do Azure </td></tr> <tr><td>Adicionar ao Quadro Inicial</td> <td>Selecione esta opção para adicionar o aplicativo de API ao seu quadro inicial (a home page)</td></tr> </table> 
4. Selecione **Criar**. O conector será criado. Pode levar algum tempo para ser concluído, e a tela inicial será exibida durante a criação do conector. Use o item de menu Notificações à esquerda para monitorar o status do conector.

Agora que você criou seu primeiro conector, considere a criação de um aplicativo Web, móvel ou lógico com ele.


### Acessar o conector usando APIs REST

[Acessar conectores com as APIs REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)


## Adicionar seu conector a um aplicativo 
O Serviço de Aplicativo do Microsoft Azure expõe os diferentes tipos de aplicativos que podem usar esses conectores. Por exemplo, você pode criar um aplicativo *lógico* combinando *logicamente* um ou mais de seus conectores em um único aplicativo.

Para usar os conectores no aplicativo *Lógico*, selecione um conector configurado na lista, adicione-o ao seu fluxo de trabalho de design, faça as alterações de configuração necessárias e estará pronto para uso.

Para executar essas etapas, você precisa de um aplicativo Web, móvel ou lógico. Consulte <> para conhecer as etapas específicas. Quando seu aplicativo estiver disponível, adicione os conectores. Faça assim:

Use as etapas a seguir para adicionar um conector a um aplicativo lógico:

1. No quadro inicial do portal do Azure (home page), vá até o **Marketplace**, e procure seu aplicativo Web, móvel ou lógico. 

	Se você estiver criando um novo aplicativo, procure por aplicativo Web, móvel ou lógico. Selecione o aplicativo e, na nova folha, selecione **Criar**. [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md) lista as etapas.

2. Abra seu aplicativo e selecione **Gatilhos e Ações**.
3. Na **Galeria**, selecione o conector. Ele será adicionado ao seu aplicativo.
4. Configure o conector:
5. Cada conector tem propriedades específicas ao serviço e ao ambiente com o qual ele está se conectando. Insira os detalhes das propriedades. Lembre-se de que algumas propriedades são opcionais.
6. Selecione **OK** para salvar suas alterações.


## Segurança
Conectores usam nomes OAuth ou nomes e senhas de usuário.

## Leia sobre aplicativos lógicos e aplicativos Web
[O que são aplicativos lógicos?](app-service-logic-what-are-logic-apps.md) | [Sites e aplicativos Web no Serviço de Aplicativo do Azure](app-service-web-app-azure-portal.md) |


## Mais conectores

[Conectores de integração do BizTalk](app-service-logic-integration-connectors.md) | [Conectores empresariais](app-service-logic-enterprise-connectors.md) | [Conectores entre empresas](app-service-logic-b2b-connectors.md) | [Conectores de protocolo](app-service-logic-protocol-connectors.md) | [Conectores de aplicativos + serviços de dados](app-service-logic-data-connectors.md) | [Lista de conectores e aplicativos de API](app-service-logic-connectors-list.md)<br/><br/> [O que são conectores e aplicativos de API do BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->