<properties 
	pageTitle="Criar um processo empresarial" 
	description="Criar um processo empresarial" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service-logic" 
	documentationCenter=""/>



<tags
	ms.service="app-service-logic"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="stepsic"/>

# Desenvolvendo de um processo empresarial usando aplicativos de API e Fluxo

Você criou um fluxo básico, mas agora quer trabalhar de verdade e fazer um processo empresarial usando aplicativos de Fluxo.  Eu vou abordar vários tópicos importantes neste artigo:

- Adicionar lógica condicional (por exemplo, executar uma ação somente se determinada condição for atendida) e adicionar loops
- Aplicativos de API do BizTalk
- Usar o Modo de Exibição de Código para editar um fluxo
- Os tipos de gatilhos diferentes

Para começar, clique aqui para criar um item em sua assinatura que já tenha um fluxo básico preenchido.  Após ter criado este fluxo, clique nos gatilhos e ações para editá-lo.

## Adicionando lógica condicional e loops

Embora o fluxo básico funcione bem, talvez você tenha notado alguns problemas com ele.  Primeiro, ele enviar por email apenas o tweet superior, e não todos os tweets com a palavra-chave.  Para repetir a ação para uma lista de itens, você pode usar a propriedade de "repetição".

### Repetição

A repetição pega uma lista de itens e executa a ação para cada item da lista.  Para usar a repetição, clique no ícone de seta circular na parte superior da ação.  Isso abrirá uma caixa de texto. Digite:

    @triggers().outputs.body

Assim como acontece com o fluxo básico, a saída é uma lista de tweets.  Para usar o tweet dentro da ação, substitua a mensagem por:

    @repeatItem().message

"repeatItem()" é uma função que dará a você cada elemento na lista.  Clique na marca de seleção.

### Condicional

Agora, esse fluxo ainda pode ser inconveniente porque resultará em muitos emails.  Você pode adicionar mais lógica para receber um email somente quando a pessoa responsável pelo tweet tiver um determinado número de seguidores.  Para fazer isso, clique no ícone de losango na parte superior da ação. 

Na caixa de texto, digite:

    @greaterThan(repeatItem().person.followers, 1000)

Isso mostra a função repeatItem(), mas também uma nova função chamada greaterThan().  Essa função compara dois valores e só permite a execução da ação se o primeiro for maior do que o segundo.  Observe a sintaxe para obter os seguidores - para cada propriedade em que você estiver interessado, adicione um ponto seguido pelo nome da propriedade.  Clique na marca de seleção para salvar a condição.

## Aplicativos de API do BizTalk

Há algumas categorias diferentes de aplicativos de API disponíveis no Azure.  O Twitter e o Office 365 são ótimos conectores para este cenário, mas também há diversos aplicativos de API fornecidos pelo BizTalk que você pode usar em seu fluxo.

Clique no serviço XXXXXX no painel à direita.  Isso adicionará ao designer o serviço fornecido pelo BizTalk.

### Transmitindo dados entre ações

Para usar mais de uma ação em um fluxo, você precisa transmitir dados entre as ações.  Isso é feito com a função actions(). 

Após adicionar a ação XXXXXX, digite:

    @actions('SendMail').outputs.YYYYYY

Isso fará a propriedade YYYYYY fluir da saída da ação SendMail.  Você sempre pode ver o nome de uma ação olhando logo abaixo de seu ícone.  Da mesma forma, você pode ver todas as saídas da ação na parte inferior da caixa.

## Usar o Modo de Exibição de Código para editar um fluxo

Além do designer, você pode editar diretamente a definição de um fluxo manualmente.  Para fazer isso, clique no botão do modo de exibição de Código na barra de comandos.  Isso abrirá um editor completo que mostra a definição do fluxo que você acabou de editar.

Um editor de texto pode facilitar determinadas operações. Por exemplo, você pode copiar e colar qualquer quantidade de ações no mesmo fluxo (ou mesmo entre fluxos).  Você também pode, facilmente. adicionar ou remover seções inteiras do fluxo de uma vez.

### Parâmetros

Há também alguns recursos que são expostos apenas na exibição de código, como os parâmetros.  Os parâmetros facilitam a reutilização dos valores ao longo de um fluxo.  Por exemplo, se tiver um endereço de email que você quer incluir em várias ações, você pode fazer dele um parâmetro.

Para definir parâmetros, vá até o objeto de parâmetros {} e adicione dentro dele:
    "emailAddress" : {
	    "type" : "string",
	    "defaultValue" : "me@example.com"
    }

Agora, role até ação de enviar email.  Você verá a entrada para o email de destino. Substitua seu email por:

    @parameters('emailAddress')

Os parâmetros são uma boa maneira de recuperar valores que você provavelmente vai alterar várias vezes.

## Diferentes tipos de gatilhos

Há algumas maneiras diferentes de iniciar os fluxos, e cada uma delas tem um comportamento um pouco diferente. Portanto, certifique-se de selecionar a maneira correta para seus cenários.

### Gatilhos de sondagem

Alguns gatilhos fazem verificações no aplicativo de API com base em um intervalo.  Você pode definir esse intervalo, mas também precisa transmitir um campo chamado "triggerState".  O estado do gatilho contém informações a serem transmitidas para a invocação do gatilho, para que ele saiba o que retornou na verificação anterior.  O estado do gatilho normalmente será a cadeia de caracteres:

    @triggers().outputs.body.triggerState

### Gatilhos de retorno de chamada

Outros gatilhos retornam a chamada do fluxo quando têm uma nova solicitação.  Nesse caso, você precisa dar ao gatilho o URI de retorno de chamada do seu fluxo.  Para isso, copie o ponto de extremidade de acesso da lâmina Propriedades na tela de Configurações quando navegar para um fluxo. 

### Invocar manualmente

Em terceiro lugar, você pode ter um fluxo é executado manualmente.  No portal, há um botão Executar no qual você pode clicar para disparar um fluxo. 

<!--HONumber=49-->