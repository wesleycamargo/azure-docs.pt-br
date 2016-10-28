<properties 
	pageTitle="Criar uma API para Aplicativos Lógicos" 
	description="Criando uma API personalizada para usar com Aplicativos Lógicos" 
	authors="jeffhollan" 
	manager="dwrede" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"	
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jehollan"/>
    
# Criando uma API personalizada para usar com Aplicativos Lógicos

Se você deseja estender a plataforma de Aplicativos Lógicos, há várias maneiras que você pode chamar APIs e sistemas que não estão disponíveis como um dos nossos muitos conectores prontos para uso. Uma dessas maneiras é criar um Aplicativo de API que pode ser chamado de dentro de um fluxo de trabalho do aplicativo lógico.

## Ferramentas úteis

Para as APIs funcionarem melhor com Aplicativos Lógicos, é recomendável gerar um documento do [swagger](http://swagger.io) detalhando os parâmetros e operações com suporte para sua API. Há muitas bibliotecas (como [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) que gerarão automaticamente o swagger para você. Você também pode usar o [TRex](https://github.com/nihaue/TRex) para ajudar a anotar o swagger para funcionar bem com Aplicativos Lógicos (nomes de exibição, tipos de propriedade etc.). Para alguns exemplos de Aplicativos de API criados para Aplicativos Lógicos, não se esqueça de consultar nosso [repositório GitHub](http://github.com/logicappsio) ou [blog](http://aka.ms/logicappsblog).

## Ações

A ação básica para um aplicativo Lógico é um controlador que aceitará uma solicitação HTTP e retornará uma resposta (normalmente 200). No entanto, existem padrões diferentes que você pode seguir para estender ações com base em suas necessidades.

Por padrão, o mecanismo de aplicativo lógico atingirá o tempo limite de uma solicitação após um minuto. No entanto, você pode fazer com que sua API execute ações que demoram mais e fazer o mecanismo aguardar a conclusão, seguindo um padrão assíncrono ou de webhook detalhado abaixo.

Para ações padrão, simplesmente escreva um método de solicitação HTTP em sua API, que é exposto por meio de swagger. Veja exemplos de aplicativos de API que funcionam com Aplicativos Lógicos em nosso [repositório do GitHub](https://github.com/logicappsio). Veja abaixo maneiras de realizar padrões comuns com um conector personalizado.

### Ações de execução longa: padrão assíncrono

Ao executar uma tarefa ou etapa longa, a primeira coisa que você precisa fazer é se certificar de que o mecanismo saiba que você ainda não atingiu o tempo limite. Você também precisa comunicar ao mecanismo como ele saberá quando tiver terminado a tarefa e, finalmente, precisa retornar dados relevantes para o mecanismo para que ele possa continuar com o fluxo de trabalho. Você pode concluir isso por meio de uma API seguindo o fluxo abaixo. Essas etapas são do ponto de vista da API personalizada:

1. Quando uma solicitação for recebida, retorne imediatamente uma resposta (antes de o trabalho ser concluído). Esta resposta será uma resposta `202 ACCEPTED`, informando ao mecanismo que você recebeu os dados, aceitou a carga e agora está processando. A resposta 202 deve conter os seguintes cabeçalhos:
 * Cabeçalho `location` (obrigatório): esse é um caminho absoluto para a URL que os Aplicativos Lógicos podem usar para verificar o status do trabalho.
 * `retry-after` (opcional, será padronizado como 20 para ações). Este é o número de segundos que o mecanismo deve aguardar antes de sondar a URL do cabeçalho do local para verificar o status.

2. 2\. Quando o status de um trabalho for verificado, execute as seguintes verificações:
 * Se o trabalho estiver concluído: retorne uma resposta `200 OK`, com a carga de resposta.
 * Se o trabalho ainda estiver em processamento: retorne outra resposta `202 ACCEPTED`, com os mesmos cabeçalhos que a resposta inicial

Esse padrão permite que você execute tarefas muito longas em um thread de sua API personalizada, mas mantenha uma conexão ativa com o mecanismo dos Aplicativos Lógicos de forma que ele não atinja o tempo limite ou continue antes de o trabalho ser concluído. Ao adicionar isso ao seu aplicativo lógico, é importante observar que você não precisa fazer nada em sua definição para o aplicativo lógico continuar a sondar e verificar o status. Assim que o mecanismo vê resposta 202 - ACEITO com um cabeçalho de local válido, ele aceitará o padrão assíncrono e continuará a sondar o cabeçalho do local até uma resposta não 202 ser retornada.

Você pode ver um exemplo desse padrão no GitHub [aqui](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### Ações webhook

Durante o fluxo de trabalho, você pode fazer com que o aplicativo lógico pause e aguarde um "retorno de chamada" para continuar. Esse retorno de chamada é fornecido na forma de um HTTP POST. Para implementar esse padrão, você precisa fornecer dois pontos de extremidade em seu controlador: assinar e cancelar assinatura.

Em ‘assinar’, o aplicativo lógico criará e registrará uma URL de retorno de chamada que sua API pode armazenar e executar o retorno de chamada com pronto como um HTTP POST. Quaisquer conteúdos/cabeçalhos serão passados para o aplicativo lógico e podem ser usados no restante do fluxo de trabalho. O mecanismo do aplicativo lógico chamará o ponto de assinatura em execução assim que chegar a essa etapa.

Se a execução foi cancelada, o mecanismo do aplicativo lógico fará uma chamada para o ponto de extremidade 'cancelar assinatura'. A API poderá cancelar a URL de retorno de chamada não registrada conforme necessário.

Atualmente o Designer de Aplicativo Lógico não dá suporte à descoberta de um ponto de extremidade de webhook por meio do swagger, portanto, para usar esse tipo de ação você precisa adicionar a ação “webhook” e especificar a URL, os cabeçalhos e o corpo da sua solicitação. Você pode usar a função de fluxo de trabalho `@listCallbackUrl()` em qualquer um desses campos conforme necessário para passar a URL de retorno de chamada.

Você pode ver um exemplo de um padrão de webhook no GitHub [aqui](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## Gatilhos

Além de ações, você pode fazer com que sua API personalizada atue como um gatilho para um aplicativo lógico. Há dois padrões que você pode seguir abaixo para disparar um aplicativo lógico:

### Gatilhos de sondagem

Os gatilhos de sondagem atuam de forma muito semelhante às ações assíncronas de execução longa acima. O mecanismo do aplicativo lógico chama o ponto de extremidade do gatilho após um determinado período de tempo decorrido (dependendo do SKU, 15 segundos para Premium, um minuto para Standard e uma hora para Gratuito).

Se não houver dados disponíveis, o gatilho retornará uma resposta `202 ACCEPTED` com um cabeçalho `location` e `retry-after`. No entanto, para gatilhos, é recomendável que o cabeçalho `location` contenha um parâmetro de consulta de `triggerState`. Isso é um identificador para sua API saber quando foi a última vez que o aplicativo lógico foi disparado. Se houver dados disponíveis, o gatilho retornará uma resposta `200 OK` com a carga de conteúdo. Isso disparará o aplicativo lógico.

Por exemplo, se eu estivesse sondando para ver se havia um arquivo disponível, você poderia criar um gatilho de sondagem que faria o seguinte:

* Se uma solicitação fosse recebida sem nenhum triggerState, a API retornaria um `202 ACCEPTED` com um cabeçalho `location` que tem um triggerState da hora atual e um `retry-after` de 15.
* Se uma solicitação fosse recebida com um triggerState:
 * Verificaria se algum arquivo foi adicionado após o DateTime do triggerState.
  * Se houvesse um arquivo, retornaria uma resposta `200 OK` com a carga de conteúdo, incrementaria o triggerState para a DateTime do arquivo que eu retornei e definiria o `retry-after` como 15.
  * Se houvesse vários arquivos, eu poderia retornar um de cada vez com um `200 OK`, incrementar meu triggerState no cabeçalho `location` e definir `retry-after` como 0. Isso informaria ao mecanismo que há mais dados disponíveis e ele os solicitaria imediatamente no cabeçalho `location` especificado.
  * Se não houvesse nenhum arquivo disponível, retornaria uma resposta `202 ACCEPTED` e deixaria o triggerState `location` igual. Definiria `retry-after` para 15.

Você pode ver um exemplo de um gatilho de sondagem no GitHub [aqui](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### Gatilhos webhook

Os gatilhos webhook atuam como as ações webhook acima. O mecanismo do aplicativo lógico chama o ponto de extremidade 'assinar' sempre que um gatilho webhook é adicionado e salvo. Sua API pode registrar a URL do webhook e chamá-la por meio do HTTP POST, sempre que os dados estiverem disponíveis. A carga de conteúdo e os cabeçalhos serão passados para na execução do aplicativo lógico.

Se um gatilho webhook nunca for excluído (o aplicativo lógico inteiramente ou apenas o gatilho webhook), o mecanismo fará uma chamada para a URL de 'cancelar assinatura' em que sua API poderá cancelar o registro da URL de retorno de chamada e parar quaisquer processos conforme necessário.

Atualmente o Designer de Aplicativo Lógico não dá suporte à descoberta de um gatilho webhook por meio do swagger, portanto, para usar esse tipo de ação você precisa adicionar o gatilho “webhook” e especificar a URL, os cabeçalhos e o corpo da sua solicitação. Você pode usar a função de fluxo de trabalho `@listCallbackUrl()` em qualquer um desses campos conforme necessário para passar a URL de retorno de chamada.

Você pode ver um exemplo de um gatilho webhook no GitHub [aqui](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

<!---HONumber=AcomDC_0803_2016-->