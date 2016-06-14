<properties
   pageTitle="Usando o conector de Aprendizado de Máquina do Azure nos Aplicativos lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o conector de Aprendizado de Máquina do Azure e usá-lo em um Aplicativo lógico no Serviço de Aplicativo do Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="jehollan"/>
   
# Visão geral
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview dos Aplicativos Lógicos.

O conector AM do Azure para os Aplicativos lógicos permite chamar as APIs de AM do Azure para a pontuação em lote (Serviço de Execução em Lote) e a readaptação. Esses recursos, junto com os Gatilhos do Aplicativo lógico, permitem o agendamento de trabalhos em lote e a configuração da readaptação dos modelos.

 ![][1]
 
## Introdução ao conector de Aprendizado de Máquina do Azure e adição dele ao seu Aplicativo lógico
Para começar, crie um experimento no Estúdio AM do Azure e depois configure e implante um serviço Web. Você pode usar a URL da API e a Chave da URL de Postagem de BES, encontradas na página de Ajuda de Execução em Lote. ([Passo a passo de Aprendizado de Máquina](../machine-learning/machine-learning-walkthrough-5-publish-web-service.md))

Para executar um trabalho BES usando o conector, adicione o conector AM do Azure ao seu Aplicativo lógico. Em seguida, insira as informações necessárias (veja mais informações sobre isso logo abaixo). Para configurar a Readaptação, adicione um segundo conector AM do Azure e forneça os parâmetros de entrada (consulte [definir um modelo para a readaptação](../machine-learning/machine-learning-retrain-models-programmatically.md).

## Executar um trabalho de Execução em Lote de AM do Azure
O conector AM do Azure oferece as quatro opções a seguir para rodar os trabalhos de Execução em Lote (BES):
1.	Trabalho em Lote com Entrada e Saída: o teste tem módulos de entrada e saída do serviço Web
2.	Trabalho em Lote Sem Entrada e Saída: o teste não tem nenhum módulo de entrada e saída do serviço Web (por exemplo, usa os módulos Leitor e Gravador).
3.	Trabalho em Lote apenas com Entrada: o teste tem um módulo de entrada do serviço Web, mas nenhum módulo de saída do serviço Web (por exemplo, usa um módulo Gravador).
4.	Trabalho em Lotes apenas com Saída: o Teste não tem um módulo de entrada de serviço Web, mas tem um módulo de saída de serviço Web (por exemplo, usa um módulo de Leitor). Observe que o BES é uma solicitação assíncrona e pode demorar para ser concluído dependendo do tamanho dos dados e da complexidade do modelo. Quando o trabalho estiver concluído, o conector retornará o resultado da saída.

### Executar a Execução em Lotes: com Entrada e Saída
Se o Teste no Estúdio tiver módulos de entrada e saída do serviço Web, será necessário ([fornecer informações sobre a Conta de armazenamento de blobs e o local](../machine-learning/machine-learning-consume-web-services.md)). Além disso, você pode incluir Parâmetros Globais (serviço Web) se estiver configurado em seu teste ([parâmetros do serviço Web do Aprendizado de Máquina](../machine-learning/machine-learning-web-service-parameters.md)).

![][2]

Clique na reticências para mostrar e ocultar os campos de Parâmetros Globais. Isso permite que você forneça um ou mais parâmetros globais (serviço Web) em uma lista de campos e valores separados por vírgula.

![][3]

Outras variações nos trabalhos BES, como um trabalho sem entrada ou saída do serviço Web, também estão disponíveis por meio do conector.

## Configuração da readaptação

Use a ação Definir readaptação para configurar uma readaptação única ou agendada de seu modelo de AM. Junto com um trabalho de Execução em Lote criado no conector, você pode concluir as etapas para o treinamento e a atualização do modelo de um serviço Web. Nesse fluxo de trabalho, você usaria o conector duas vezes.
1.	O primeiro conector é usado para executar o trabalho BES para readaptar seu modelo e retornar a saída. A saída dessa execução terá a URL do novo modelo (.ilearner). Opcionalmente, ela também poderá, se você tiver configurado isso em seu teste, retornar a URL da saída do módulo Avaliar, que é um arquivo csv. Na próxima etapa, você poderá usar os dados na saída do módulo Avaliar para tomar uma decisão sobre substituir ou não o modelo em seu serviço Web (por exemplo, se Precisão for maior do que 0,85).
1.	O segundo conector é usado para configurar a Readaptação. Ele usa os parâmetros da saída do primeiro conector para verificar, opcionalmente, a condição do modelo de atualização e atualizar o serviço Web com o modelo treinado recentemente.
  *	Por exemplo, você pode acessar a saída do Trabalho BES com a URL para o modelo treinado recentemente digitando `@{body('besconnector').Results.output2.FullURL}` no campo URL do Modelo Readaptado. Isso pressupõe que a saída do serviço Web no Teste de Treinamento é denominado output2.
  *	Para o Nome do Recurso, use o nome completo do Modelo Treinado salvo no Teste Preditivo, por exemplo, MyTrainedModel [modelo treinado]
  *	Para o campo Chave de Resultado da Avaliação, você pode inserir qualquer um dos parâmetros retornados na saída do Módulo Avaliar do Teste de Treinamento (se o tiver incluído). Você pode ver a lista de parâmetros disponíveis ao visualizar os resultados do módulo Avaliar no Teste de Treinamento no Estúdio AM do Azure. Para um teste de classificação, isso incluiria a Exatidão, a Precisão, o Cancelamento, a Pontuação F, o AUC, a Perda Média de Log e a Perda de Log de Treinamento.

![][4]
 
### Readaptação agendada
 
Usando os Gatilhos do Aplicativo lógico, você pode configurar o conector para a execução com um agendamento. Isso pode permitir a readaptação regular de um modelo à medida que novos dados chegam. O trabalho de BES readaptaria o modelo e a ação de Readaptação atualizaria o modelo após a conclusão do treinamento.
 
![][5]
 
## Saída do conector 
 
**BES**: após a conclusão com êxito do Trabalho em lote, a saída do conector terá as seguintes informações para cada saída do serviço Web.
 
 ![][6]
 
Observe que isso não estará disponível se você não tiver incluído uma saída do serviço Web (por exemplo, se você estiver usando um Módulo de Gravador para gravar em um banco de dados desde o Teste no Estúdio).

**Readaptação**: após a conclusão da Readaptação, a saída terá as seguintes informações.

![][7]

## Resumo

Usando o conector AM do Azure para os Aplicativos lógicos, você pode executar a pontuação em lote e readaptar os trabalhos a serem executados sob demanda ou de acordo com um agendamento recorrente. A combinação das duas ações pode classificar seus dados e readaptar, avaliar e atualizar automaticamente o modelo do serviço Web sem a necessidade de escrever qualquer código.

 <!--Image references-->
[1]: ./media/app-service-logic-connector-azureml/img1.png
[2]: ./media/app-service-logic-connector-azureml/img2.png
[3]: ./media/app-service-logic-connector-azureml/img3.png
[4]: ./media/app-service-logic-connector-azureml/img4.png
[5]: ./media/app-service-logic-connector-azureml/img5.png
[6]: ./media/app-service-logic-connector-azureml/img6.png
[7]: ./media/app-service-logic-connector-azureml/img7.png

<!---HONumber=AcomDC_0601_2016-->