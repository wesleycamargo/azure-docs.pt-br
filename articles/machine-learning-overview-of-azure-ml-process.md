<properties title="Azure Machine Learning API service operations" pageTitle="Operações de serviço de API de Aprendizado de Máquina | Azure" description="Creating and managing Azure Machine Learning web services" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


# Operações de serviço de API de Aprendizado de Máquina do Azure 
O projeto típico de Aprendizado de Máquina do Microsoft Azure (Azure AM) envolve as seguintes etapas de alto nível:  

1.	Obter, analisar e preparar dados
2.	Criar testes de Aprendizado de Máquina aprimorando vários algoritmos AM
3.	Treinar, testar e gerar um modelo treinado
4.	Criar um fluxo de trabalho operacional usando o modelo treinado e implantando o fluxo de trabalho na produção
5.	Monitorar o desempenho do modelo e das atualizações subsequentes  

>O termo "teste" é usado para descrever um fluxo de trabalho interativo que pode incluir entrada e manipulação de dados, treinadores e pontuações na forma de um gráfico acíclico direcionado (DAG). Depois que o fluxo de trabalho é publicado como um serviço Web do Azure, ele não é mais interativo, o que significa que, para alterá-lo, o modelo precisa ser atualizado e depois publicado novamente para atualizar o serviço Web e seu comportamento.  

As etapas 1 a 3 geralmente são realizadas por um cientista de dados por meio de várias iterações, no final das quais um modelo AM é manipulado para uso das equipes de engenharia e operações para então ser integrado aos sistemas de produção, de forma que o modelo AM possa ser usado na produção.  

O processo tradicional de integração e implantação do modelo AM em um sistema de produção pode levar semanas ou mesmo meses, dependendo do código usado para compilar os modelos, como R, Python, C# ou Java, a integração da plataforma, as considerações de infraestrutura e o planejamento de implantação.  

O Azure ML simplifica e agiliza esse processo, primeiro facilitando a criação de modelo e tornando a avaliação uma experiência intuitiva, depois fornecendo um processo simples para implantar o teste como um serviço Web no Azure, reduzindo assim o tempo total desde o teste do modelo até sua execução na produção como um serviço Web.  

Este documento descreve os conceitos e as etapas na configuração de um Serviço Web ML do Azure por meio de um teste AM.  

# Visão geral do Processo AM do Azure  #
O Azure AM permite a criação de serviços Web por meio de testes AM definidos no Estúdio de Aprendizado de Máquina do Azure (Estúdio AM). Um Serviço Web AM do Azure pode ser usado para fazer previsões com base na entrada atual de dados em tempo real ou no modo de lote.  
 
O diagrama a seguir mostra as etapas em alto nível em duas partes: primeiro compilando um modelo e depois publicando esse modelo como um Serviço Web. Este documento descreve o lado direito do diagrama da Figura 1 - Publicando um serviço Web de pontuação - e explica os conceitos envolvidos nesse processo. 

![][1]  

Figura 1: Provisão, compilação e publicação de um serviço Web de pontuação  

# Serviços Web AM do Azure #
Um serviço Web, no contexto de Aprendizado de Máquina (AM), é uma interface de software que fornece comunicação entre aplicativos externos e um fluxo de trabalho de Aprendizado de Máquina. Fornece uma maneira de se comunicar com um modelo de pontuação em tempo real para receber resultados de previsão e incorporar os resultados em aplicativos de cliente externos. O Azure AM aprimora o Microsoft Azure para implantação, hospedagem e gerenciamento de serviços Web AM do Azure. Dois tipos de serviços são criados usando o Azure AM.  

## Serviço de solicitação-resposta (RRS) ##
O Serviço de solicitação-resposta (RRS) é um serviço Web de baixa latência e alta escala usado para fornecer uma interface para modelos sem monitoração de estado criados e publicados a partir do ambiente de experimentação.   

- API REST: RRS é um serviço Web RESTFul. 
-	Interface de serviço: A interface de serviço Web RRS é definida como parte da configuração do teste que usa as portas de entrada/saída no teste do Estúdio AM do Azure.
-	Estágios de desenvolvimento: Como parte do fluxo de trabalho AM do Azure, o serviço RRS é gerado primeiro no ambiente temporário - e pode ser testado lá. Quando ele for considerado concluído pronto para produção, será implantado no ambiente de produção. 
-	Implantado no Azure: O resultado de implantação do RRS é um ponto de extremidade do Serviço Web do Azure.
-	Parâmetros de interface: A solicitação para um serviço RRS é que os dados precisam ser pontuados usando o teste definido no Studio. A resposta é o resultado de previsão do modelo. 
-	Valores de resposta: O RRS aceita uma única linha de parâmetros de entrada e gera uma única linha como saída. A linha de saída pode conter várias colunas.   

## Serviço de execução em lote (BES) ##
O Serviço de execução em lote (BES) é um serviço para pontuação assíncrona de um lote de registros de dados. A entrada para BES é similar à entrada de dados usada em RRS com a principal diferença de que o BES lê um lote de registros por meio de uma variedade de fontes, como blobs, tabelas no Azure, SQL Azure, HD Insight (Hive Query) e fontes HTTP.  Os resultados da pontuação são saídas para um arquivo no armazenamento de blob do Azure e dados do ponto de extremidade de armazenamento são retornados na resposta.  

O BES também fornece interfaces para obter o status do processo de pontuação em execução e o cancelamento da solicitação. O BES é capaz de executar pacotes de modelo com relação a uma grande quantidade de dados.  

-	API REST: BES é um serviço Web RESTFul.
-	Interface de serviço: Similar ao RRS, a interface de serviço Web BES é definida como parte da configuração de modelo que usa as portas de entrada/saída no teste do Estúdio AM do Azure.
-	Estágios de desenvolvimento: Como parte do fluxo de trabalho para criar o BES, primeiro ele é gerado no ambiente temporário - e pode ser testado lá. Quando estiver concluído e pronto para produção, ele será implantado no ambiente de produção. 
-	Implantado no Azure: O resultado de implantar o BES é um ponto de extremidade do Serviço Web do Azure.
-	Parâmetros de interface: A solicitação para um serviço BES é a URL de um arquivo no blob Azure ou uma entrada SAS dos registros a serem pontuados. A resposta é gravada no blob Azure e a URL do ponto de extremidade do armazenamento de resposta é retornada.  

# Publicando um Serviço Web AM do Azure #
O Estúdio AM do Azure fornece um aplicativo baseado em navegador para facilitar a criação e execução de testes de aprendizado de máquina em uma interface gráfica do usuário, usando várias fontes de dados, manipulação de dados e módulos de validação, e ainda algoritmos de AM. Um teste no Estúdio AM é construído como um gráfico acíclico direcionado (DAG) ou módulos de processamento de dados.  

Depois que o teste é configurado e executado com êxito para treinar nos dados, ele pode ser salvo como um Modelo treinado e pode ser usado para Pontuação. O modelo treinado é usado em um Teste de pontuação ou um fluxo de trabalho e é publicado como um serviço Web do Azure.  

## Teste de treinamento ##
Um teste pode incluir vários modelos para carregar e manipular dados, aplicando algoritmos de Aprendizado de Máquina e avaliando resultados. Um Modelo de treinamento utiliza conjunto de dados de treinamento e um a algoritmo de aprendizado para prever uma resposta.  

Depois que o modelo executou com êxito, um Modelo de treinamento pode ser salvo como um componente reutilizável para pontuar os conjuntos de dados de teste e as consultas.

![][2]  

Figura 2: Amostra exibindo como salvar um Modelo treinado no teste

O modelo treinado salvo então estará disponível na seção de Modelos treinados do aplicativo.

![][3]  

Figura 3: Seção de Modelos treinados mostrando a lista de modelos

## Teste de pontuação ##
Um teste de pontuação gera previsões que usam o modelo treinado e os dados de amostra.  

A Figura 1 acima mostra o uso de um Modelo de pontuação no teste. No Studio, isso é parte dos módulos de Aprendizado de Máquina.

![][4]  

Figura 4: Modelo de Pontuação 

### Serviço de solicitação-resposta versus Serviço de execução em lote (BES) ###
Ao compilar testes de pontuação que serão publicados como serviços Web, um dos dois pode ser selecionado, dependendo do cenário de pontuação. Se a solicitação de pontuação envolver pontuação de um único registro, como uma solicitação para determinar se o cliente A está para trocar portadoras (previsão de variação do cliente), isso pode ser pontuado em tempo real e deveria ser criado como um serviço Web RRS. O serviço retornará o resultado do modelo de previsão em tempo real - no caso do exemplo de previsão de variação acima, poderia ser uma resposta sim ou não.  

Para operações de pontuação em que muitos registros precisam ser pontuados em uma solicitação, por exemplo, quando um lote de registros que contém dados do cliente é enviado para o serviço a ser pontuado, então BES será o serviço apropriado. A solicitação nesse caso será uma solicitação assíncrona em que todos os registros serão processados e salvos em um blob do Azure antes de retornar a resposta após conclusão de todo processamento.  
  
### Usando o Modelo de treinamento ###
Para configurar o teste de pontuação, o Modelo de treinamento ("Previsor de receita de adulto" na Figura 3) será adicionado ao teste. Outros modelos para treinar o Modelo treinado agora podem ser removidos. O fluxo de trabalho final agora será parecido com a Figura 5 abaixo. 

### Portas de entrada e saída ###
Após configurar o teste com o Modelo treinado (veja detalhes na seção acima) e pontuar o teste atualizado, as portas de entrada e saída precisam ser configuradas para especificarem os pontos de entrada e saídas dos dados no modelo de previsão e no resultado da previsão, e atuarem como as definições de interface para o Serviço Web publicado. A porta de entrada do Modelo de Pontuação pode ser configurada como abaixo, clicando com o botão direito no ponto de entrada.

![][5]  

Figura 5: Configurando porta de entrada de pontuação 

Do mesmo modo, a porta de saída do Modelo de Pontuação pode ser definida como abaixo.

![][6]  

Figura 6: Configurando a porta de saída  

## Publicando o serviço ##
Após configurar as portas e executar o teste, o modelo pode ser publicado como um serviço Web. A primeira etapa é publicar o serviço no ambiente de preparo e testá-lo lá, para assegurar que ele esteja retornando os resultados esperados antes de entregá-lo pronto para implantação de produção.  

### Publicando no ambiente temporário ###
Clicando no ícone de Publicar serviço Web o serviço Web será implantado no ambiente temporário. 

![][7]  

Figura 7: Botão Publicar serviço Web  

Após publicar o modelo como um Serviço Web no ambiente de preparo, ele pode ser testado usando os parâmetros de entrada e marcado para implantação na produção. O Painel mostra o link para teste. 

![][8]  

Figura 8: Painel de serviço Web

Clicando no Link de teste e fornecendo os parâmetros para pontuação, o serviço Web pode ser testado no ambiente de preparo. A solicitação de teste é pontuada usando o modelo e com base nos dados fornecidos e o resultado da pontuação é retornado. 

### Publicando na produção ###
A publicação de um serviço Web na produção o tornará disponível para uso de outros aplicativos para previsão e pontuação. Depois da implantação no ambiente de preparo e de um teste bem-sucedido, o serviço Web é marcado para implantação no ambiente de produção. 

![][9]  

Figura 9: Marcando o serviço Web como pronto para implantação em produção

Isso na verdade não executa a implantação, em vez disso cria uma notificação ao usuário, com as permissões apropriadas de implantação para uso no serviço de produção.

![][10]  

Figura 10: Notificação de implantação e opção para implantar em produção

## Chamando o serviço Web ##
#### RRS ####
O serviço Web RRS é um ponto de extremidade REST e pode ser chamado por meio de aplicativos clientes usando várias linguagens de programação. A página de ajuda de API fornece um link para o código de amostra para chamar um novo serviço Web que fornece amostras em C#, R e Python.

![][11]  

Figura 11: Código de amostra para chamar RRS  

## Testes sem pontuação ##
Além de compilar Serviços Web de pontuação, os testes podem ser criados para execução de outras tarefas, como extração de dados e transformação. Nesse caso, o serviço web não poderia executar operações de aprendizado de máquina. Ele usa os recursos de manipulação de dados do Estúdio AM do Azure para leitura por meio de várias fontes, para conversão de tipos de dados ou para filtragem e aplicação de dados e manipulações matemáticas.   

### Publicando serviço Web sem pontuação ###
As etapas para publicar um serviço Web sem pontuação são similares àquelas do serviço de pontuação descrito acima. A principal diferença é que a porta de saída não é definida no Modelo de pontuação.

# Atualizando um serviço de publicação #
Um Serviço Web publicado pode precisar ser atualizado por diversos motivos, como pela atualização de dados de treinamento, mudança no esquema de dados usados para treinamento e pontuação, pela necessidade de melhorar o algoritmo, ou por outras mudanças no modelo AM original. Essas mudanças afetarão o Modelo treinado e os resultados de pontuação, e exigirão a publicação do Serviço Web atualizado.

![][12]  

Figura 12: Editando o modelo e publicando o serviço Web de pontuação atualizado  

## Atualizando o Modelo treinado ##
Mudanças no teste de Treinamento exigem um novo treinamento do Modelo treinado. Para isso, o modelo publicado precisará ser editado. O exemplo abaixo mostra o fluxo de trabalho de pontuação exibido na Figura 5 acima, após ser removido o Modelo treinado existente.

![][13]  

Figura 13: Modelo treinado removido do fluxo de trabalho  

A etapa a seguir será adicionar os módulos necessários para dividir os dados no treinamento e testar os segmentos, aplicar o algoritmo de aprendizado, treinar o modelo, pontuar os dados de treinamento e avaliar os resultados. Observe que essas etapas poderiam variar, dependendo de outras mudanças necessárias ao teste, como a necessidade de aplicar um algoritmo de aprendizado diferente, por exemplo. (Figura 14)  

Depois de adicionados novos módulos, eles precisam ser configurados conforme necessário, antes do teste ser executado novamente. Como um exemplo, o circulo vermelho no exemplo abaixo indica que a coluna de rótulo não foi configurada para o Modelo de treinamento.

![][14]  

Figura 14: Módulos adicionados para novo treinamento do modelo.  

Clicando no Modelo de treinamento e configurando o nome de coluna do Rótulo resolverá o problema. 

![][15]  

Figura 15: Selecionando a coluna de entrada como o rótulo  

## Salvando o modelo treinado atualizado ##
Após todos os novos módulos serem configurados corretamente, o teste deve ser salvo para nova execução bem-sucedida. O Modelo treinado pode então ser salvo (como mostrado na Figura 2 acima). A diferença é que nesse caso a caixa de seleção deve estar marcada para atualizar o Modelo treinado existente. 

![][16]  

Figura 16: Atualizar o Modelo treinado existente

## Publicando o serviço atualizado ##
Depois que o Modelo treinado estiver atualizado, as etapas descritas na seção acima em Publicar um **Serviço Web AM do Azure** serão repetidas:  

-	Usar o Modelo treinado (agora atualizado) no teste de pontuação
-	Configurar as portas de entrada/saída
-	Publicar o ambiente temporário
-	Publicar na produção  

Depois que o Teste tiver siado atualizado e o novo Modelo treinado estiver criado e pontuado, clique em Publicar o serviço Web para publicar o serviço. O serviço criado recentemente substituirá o anterior.


<!--Image references-->
[1]: ./media/machine-learning-overview-of-azure-ml-process/oamlp1.png
[2]: ./media/machine-learning-overview-of-azure-ml-process/oamlp2.png
[3]: ./media/machine-learning-overview-of-azure-ml-process/oamlp3.png
[4]: ./media/machine-learning-overview-of-azure-ml-process/oamlp4.png
[5]: ./media/machine-learning-overview-of-azure-ml-process/oamlp5.png
[6]: ./media/machine-learning-overview-of-azure-ml-process/oamlp6.png
[7]: ./media/machine-learning-overview-of-azure-ml-process/oamlp7.png
[8]: ./media/machine-learning-overview-of-azure-ml-process/oamlp8.png
[9]: ./media/machine-learning-overview-of-azure-ml-process/oamlp9.png
[10]: ./media/machine-learning-overview-of-azure-ml-process/oamlp10.png
[11]: ./media/machine-learning-overview-of-azure-ml-process/oamlp11.png
[12]: ./media/machine-learning-overview-of-azure-ml-process/oamlp12.png
[13]: ./media/machine-learning-overview-of-azure-ml-process/oamlp13.png
[14]: ./media/machine-learning-overview-of-azure-ml-process/oamlp14.png
[15]: ./media/machine-learning-overview-of-azure-ml-process/oamlp15.png
[16]: ./media/machine-learning-overview-of-azure-ml-process/oamlp16.png

<!--Link references-->
