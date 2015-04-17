<properties 
	pageTitle="Conectores Sociais de Aplicativos de API do Microsoft Azure | Microsserviço de Aplicativos de API" 
	description="Aprenda a criar Aplicativos de API do Conector Social do Microsoft Azure e adicionar o Aplicativo de API ao aplicativo lógico; microsserviços" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="connectors" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="deonhe"/> 

# Conectores sociais no Serviço de Aplicativo do Microsoft Azure


## O que é um conector de aplicativo de API Social
Conectores sociais são "aplicativos de API" que podem se conectar a aplicativos sociais e realizar várias *Actions* em nome do usuário autenticado.  A maioria dos conectores também pode ser configurada com um *Trigger*. Os gatilhos são eventos (semelhantes a eventos no .NET Framework) que podem ser configurados em alguns conectores.  Gatilhos então podem iniciar as ações que foram configuradas para o conector (ou qualquer outro).  Por exemplo, uma instância do conector do Twitter pode ser configurada com um gatilho *new message* em que o gatilho seja definido como qualquer novo tweet que mencione @VisualStudio.  Esse gatilho então pode ser configurado para iniciar um *Action* para retweetar o tweet. 

Os seguintes conectores sociais estão disponíveis na Microsoft:

- Facebook
- Twitter
- Chatter
- Yammer
- Twilio.

Aqui está uma breve descrição de cada um dos conectores sociais. 

<table>
<tr>
<th> Nome</th>
<th> Descrição</th>
<th> Gatilhos</th>
<th>Ações</th>

<tr>
<td>Facebook
<td>Um conector do Facebook permite postar e ler mensagens, comentários, eventos e status do seu feed do Facebook.  Você também pode postar fotos e vídeos.
<td>Nova postagem
<td><li>Publicar postagem
	<li>Publicar foto 
</tr>

<tr>
<td>Twitter
<td>Um conector do Twitter permite realizar várias operações, como postar, receber tweets e pesquisar tweets.  Você também pode usar um conector do Twitter para acompanhar o que está acontecendo no Twitter, obtendo detalhes como tweets, linhas de tempo, seguidores e amigos.
<td>Novos tweets
<td><li>Pesquisar tweets
	<li>Tweet
	<li>Obter a linha do tempo do usuário
	<li>Retweet
</tr>

<tr>
<td>Chatter
<td>Um Conector de Conversas pode ser usado para ler, postar e pesquisar mensagens no seu feed de conversas.
<td>Nova mensagem
<td><li>Postar mensagem
<li>Pesquisar mensagem
</tr>

<tr>
<td>Yammer
<td>Um conector Yammer permite postar e ler mensagens de grupos do Yammer.
<td>Novas mensagens
<td><li>Postar mensagem
	<li>Tweet
	<li>Obter a linha do tempo do usuário
	<li>Retweet
</tr>

<tr>
<td>Twilio
<td>Um conector Twilio permite enviar e receber SMSes de sua conta do Twilio.  Também permite recuperar os números de telefone e dados de uso.
<td>N/D
<td><li>Mensagens: enviar, listar, obter e pesquisar
	<li>Números de telefone: comprar, listar e obter números locais e gratuitos
</tr>
</table>


Agora que você tem uma ideia do que os nossos conectores sociais podem fazer, vamos analisar alguns casos de uso simples para esses conectores.

##Por que usar conectores?

Os conectores aceleram o desenvolvimento de aplicativos e até mesmo permitem que não desenvolvedores criem aplicativos totalmente funcionais de nível empresarial sem necessidade de aprender uma linguagem de programação ou escrever qualquer código. 

### Monitoramento de feedback do cliente ###
Imagine que sua empresa recentemente lançou um novo aplicativo e a equipe quer saber o que os clientes estão dizendo sobre o aplicativo na mídia social.  Cada membro da equipe pode verificar periodicamente os vários sites de mídia social e estimar que palavras-chave os clientes podem usar para discutir seu aplicativo.  Mas, de maneira mais elegante, você pode simplesmente criar um conector do Twitter, configurá-lo para monitorar o Twitter para palavras-chave, menções e hashtags específicas.  Você pode usar o conector SMTP para enviar um email com o conteúdo dos tweets correspondentes aos membros da equipe.  Você pode fazer algo semelhante monitorando o Facebook e o Yammer e enviar um SMS, por meio de um conector de Twilio, para sua equipe DevOps se o conteúdo das postagens do Facebook ou do Yammer indicar que pode haver um problema crítico que atinja seus clientes.  Você pode fazer tudo isso sem escrever uma única linha de código.  

Vamos começar. 


##Criar um conector

Há duas maneiras de criar conectores:

- usando o portal do Microsoft Azure (sem necessidade de codificação!)
- usando APIs REST. 

### Criar um Conector Social no Portal do Microsoft Azure

1. No portal do Azure, selecione **NOVO** > **Web + móvel** > **Azure Marketplace**
2. **Pesquise** o conector ou selecione-o na lista.  Quando selecionado, uma nova folha ou janela se abre.  Selecione **Criar**. 
3. Insira as seguintes propriedades para o conector: 
	<table>
	    <tr><th>Propriedade</th> <th>Descrição</th> </tr>
	    <tr><td>Nome</td> <td>Digite um nome para seu Aplicativo de API.  Por exemplo, é possível atribuir o nome RulesDiscountTaxCode ou APIAppValidateXML</td> </tr>
	    <tr><td>Plano de Serviço de Aplicativo</td> <td>Lista seu plano de pagamento.  Você pode alterá-lo se precisar de mais ou menos recursos</th> </td>
	    <tr><td>Camada de Preços</td> <td>Propriedade somente leitura que lista a categoria de preços dentro de sua assinatura do Azure.</td> </tr>
	    <tr><td>Grupo de Recursos</td> <td>Crie um novo ou use um grupo existente.  O uso de grupos de recursos explica esta propriedade</td> </tr>
	    <tr><td>Assinatura</td> <td>Propriedade somente leitura que lista sua assinatura atual</td> </tr>
	    <tr><td>Local</td> <td>A localização geográfica que hospeda o serviço do Azure </td></tr>
        <tr><td>Adicionar ao quadro inicial</td> <td>Selecione essa opção para adicionar o Aplicativo de API ao seu quadro inicial</td></tr>
	</table> 
4. Selecione **Criar**.  Seu conector será criado.  Pode levar algum tempo para ser concluído, e a tela inicial será exibida durante a criação do conector.  Use o item de menu Notificações à esquerda para monitorar o status do seu conector.

Agora que você criou seu primeiro conector, considere a criação de um aplicativo Web, móvel ou de lógica com ele. 


### Criar um conector usando APIs REST

[Criar conectores com APIs REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)


## Adicionar seu conector a um aplicativo 
O Serviço de Aplicativo do Microsoft Azure expõe diferentes tipos de aplicativos que podem usar esses conectores.  Por exemplo, você pode criar um *Logic* aplicativo combinando um ou mais dos seus conectores *logically* em um único aplicativo.

Para usar os conectores no seu aplicativo *Logic*, selecione um conector pré-configurado na lista, adicione-o ao fluxo de trabalho de design, faça as alterações de configuração necessárias e ele estará pronto para uso. 

Para executar essas etapas, você precisa de um aplicativo Web, aplicativos móveis ou aplicativo lógico.  Consulte <> para obter as etapas específicas.  Quando seu aplicativo estiver disponível, adicione os conectores.  Faça assim:

Use as etapas a seguir para adicionar um conector a um aplicativo lógico: 

1. No quadro inicial do portal do Azure (página inicial), vá até **Marketplace** e pesquise o aplicativo lógico, móvel ou Web. 

	Se estiver criando um novo aplicativo, pesquise pelo aplicativo lógico, móvel ou Web.  Selecione o aplicativo e, na nova folha, selecione **Criar**.  [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md) lista as etapas. 

2. Abra seu aplicativo e selecione **Gatilhos e Ações**. 
3. Na **Galeria**, selecione o conector.  Ele será adicionado ao seu aplicativo.
4. Configure o conector:
5. Cada conector tem propriedades que são específicas para o serviço e o ambiente ao qual ele está se conectando.  Insira os detalhes das propriedades.  Tenha em mente que algumas propriedades são opcionais.
6. Selecione **OK** para salvar suas alterações.


## Segurança
Conectores usam OAuth ou nomes de usuário e senhas.

## Leia sobre os aplicativos lógicos
[O que são aplicativos lógicos?](app-service-logic-what-are-logic-apps.md) |
[Sites e aplicativos Web no Serviço de Aplicativo do Azure](app-service-web-app-azure-portal.md) |


## Mais conectores

[Conectores de integração do BizTalk](app-service-logic-integration-connectors.md) |
[Conectores empresariais](app-service-logic-enterprise-connectors.md) |
[Conectores Business-to-Business](app-service-logic-b2b-connectors.md) |
[Conectores de protocolo](app-service-logic-protocol-connectors.md) |
[Conectores de Aplicativo + Serviços de Dados](app-service-logic-data-connectors.md) |
[Lista de Aplicativos de API e conectores](app-service-logic-connectors-list.md)

<!--HONumber=49-->