<properties 
   pageTitle="Conector do HDInsight" 
   description="Como usar o conector do HDInsight" 
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
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Conector do Microsoft HDInsight #

Conectores podem ser usados em aplicativos de lógicos para obter, processar ou enviar dados como parte de um fluxo. O Conector do HDInsight permite criar clusters do Hadoop no Azure e enviar vários trabalhos do Hadoop, como trabalhos de Pig, Hive, MapReduce e Streaming MapReduce. O serviço Azure HDInsight implanta e provisiona clusters do Apache Hadoop na nuvem, fornecendo uma estrutura de software criada para gerenciar, analisar e relatar Big Data. O núcleo do Hadoop fornece armazenamento de dados confiável com o HDFS (Sistema de Arquivos Distribuído Hadoop) e um modelo de programação simples MapReduce para processar e analisar, paralelamente, os dados armazenados nesse sistema distribuído. Usando o conector do HDInsight, você pode criar um cluster, enviar um trabalho e aguardar sua conclusão.

###Ações básicas
		
- Criar Cluster
- Aguardar Criação do Cluster
- Enviar Trabalho de Pig
- Enviar Trabalho de Hive
- Enviar Trabalho de MapReduce
- Aguardar Conclusão do Trabalho
- Excluir Cluster


## Criar uma instância do Aplicativo de API do Conector do HDInsight ##

Para usar o Conector do HDInsight, você precisa criar uma instância do Aplicativo de API para o Conector do HDInsight. Isso pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace usando a opção '+ NEW' na parte inferior esquerda do Portal do Azure.
2. Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "Conector do HDInsight".
3. Forneça os detalhes genéricos, como nome, plano de serviço de aplicativo e assim por diante na primeira lâmina
4. Como parte das configurações de pacote, forneça o nome de usuário e a senha do cluster HDInsight


 ![][1]  

## Configuração do certificado (opcional) ##

Observação: essa etapa será necessária apenas se você quiser realizar operações de gerenciamento (criar e excluir clusters) no aplicativo lógico.

Navegue até o Aplicativo de API recém-criado, em Procurar -> Aplicativos de API -> <nome do aplicativo de API recém-criado>, e você verá o comportamento a seguir. O componente 'Segurança' mostra 0, o que significa que não há nenhum certificado de gerenciamento carregado.

![][2] 

Para carregar o certificado de gerenciamento para seu Aplicativo de API, você precisa fazer o seguinte
1. Clique no componente 'Segurança'
2. Clique em 'Carregar certificado' na folha 'Segurança' que será aberta
3. Navegue e selecione o arquivo de certificado na folha seguinte
4. Quando o certificado for selecionado, clique em OK.

Depois que o certificado for carregado com êxito, os detalhes do certificado serão mostrados.

![][3] 

Observação: Caso deseje alterar o certificado, você poderá carregar outro certificado, que substituirá o existente.

## Uso em um aplicativo lógico ##

O Conector do HDInsight pode ser usado apenas como uma ação no aplicativo lógico. Vamos falar sobre um aplicativo lógico simples que cria um cluster, executa um trabalho 'Hive' e exclui o cluster no fim da conclusão do trabalho.


- Ao criar/editar um aplicativo lógico, escolha o Aplicativo de API do Conector do HDInsight criado como a ação, e ele mostrará todas as ações disponíveis.

![][5] 


- Selecione 'Criar Cluster', forneça todos os parâmetros de cluster necessários e clique em ✓.

![][6] 



- A ação aparecerá conforme configurado no aplicativo lógico. A(s) saída()s da ação será(ão) mostrada(s) e pode(m) ser usada(s) como entrada(s) em ações posteriores. 

![][7] 



- Selecione o mesmo conector do HDInsight da galeria como uma ação. Selecione a ação 'Esperar a Criação de Cluster', forneça todos os parâmetros necessários e clique em ✓.

![][8] 



- Selecione o mesmo conector do HDInsight da galeria como uma ação. Selecione a ação 'Enviar Trabalho de Hive', forneça todos os parâmetros necessários e clique em ✓.

![][9] 



- Selecione o mesmo conector do HDInsight da galeria como uma ação. Selecione a ação 'Esperar a Conclusão do Trabalho', forneça todos os parâmetros necessários e clique em ✓.

![][10] 



- Selecione o mesmo conector do HDInsight da galeria como uma ação. Selecione a ação 'Enviar Trabalho de Hive', forneça todos os parâmetros necessários e clique em ✓.

![][11] 


Você pode clicar em 'Executar' para iniciar o aplicativo lógico manualmente para testar o cenário.

<!--Image references-->
[1]: ./media/app-service-logic-connector-hdinsight/Create.jpg
[2]: ./media/app-service-logic-connector-hdinsight/CertNotConfigured.jpg
[3]: ./media/app-service-logic-connector-hdinsight/CertConfigured.jpg
[5]: ./media/app-service-logic-connector-hdinsight/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-hdinsight/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-hdinsight/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-hdinsight/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-hdinsight/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-hdinsight/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-hdinsight/LogicApp7.jpg

<!--HONumber=52-->