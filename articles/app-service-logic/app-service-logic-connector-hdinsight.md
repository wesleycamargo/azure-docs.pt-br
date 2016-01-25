<properties
   pageTitle="Usando o Conector do HDInsight em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o Conector do HDInsight ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
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
   ms.date="01/12/2016"
   ms.author="sameerch"/>


# Introdução ao Conector do HDInsight e à adição dele ao Aplicativo Lógico
Um Conector do HDInsight permite que você crie um cluster Hadoop no Azure e envie vários trabalhos do Hadoop, como Hive, Pig, MapReduce e Streaming MapReduce. O serviço Azure HDInsight implanta e provisiona clusters do Apache Hadoop na nuvem, fornecendo uma estrutura de software criada para gerenciar, analisar e relatar Big Data. O núcleo do Hadoop fornece armazenamento de dados confiável com o HDFS (Sistema de Arquivos Distribuído Hadoop) e um modelo de programação simples MapReduce para processar e analisar, paralelamente, os dados armazenados nesse sistema distribuído. Usando o conector do HDInsight, você pode criar ou excluir um cluster, enviar um trabalho e aguardar sua conclusão.

Conectores podem ser usados em aplicativos de lógicos para obter, processar ou enviar dados como parte de um fluxo. Você pode adicionar o Conector do HDInsight a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.

### Ações básicas

- Criar Cluster
- Aguardar a Criação do Cluster
- Enviar Trabalho do Pig
- Enviar Trabalho do Hive
- Enviar Trabalho do MapReduce
- Aguardar a Conclusão do Trabalho
- Excluir Cluster


## Criar o Aplicativo de API do Conector do HDInsight ##

Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Conector do HDInsight", selecione-o e selecione **Criar**.
3. Digite o Nome, o Plano do Serviço de Aplicativo e outras propriedades.
4. Nas configurações de pacote, insira o nome de usuário e a senha do cluster do HDInsight Selecione **OK**.
5. Selecione **Criar**: ![][1]  

## Configuração do certificado (opcional) ##

> [AZURE.NOTE]essa etapa será necessária apenas se você quiser realizar operações de gerenciamento (criar e excluir clusters) no aplicativo lógico.

Navegue até o aplicativo de API do Conector do HDInsight recém-criado e você verá que o componente “Segurança” mostra 0 – o que significa que nenhum certificado de gerenciamento será carregado: ![][2]

Para carregar o certificado de gerenciamento para seu Aplicativo de API:

1. Selecione o componente ‘Segurança’.
2. Na folha ‘Segurança’, selecione **CARREGAR CERTIFICADO**.
3. Navegue e selecione o arquivo de certificado na folha seguinte.
4. Depois de selecionar o certificado, selecione **OK**.

Depois que o certificado for carregado com êxito, os detalhes do certificado serão mostrados: ![][3]

> [AZURE.NOTE]Caso deseje alterar o certificado, você poderá carregar outro certificado, que substitui o certificado existente.

## Usando o conector em um aplicativo lógico ##

O Conector do HDInsight pode ser usado apenas como uma ação em um aplicativo lógico. Vamos usar um aplicativo lógico simples que cria um cluster, executa um trabalho do “Hive” e exclui o cluster após a conclusão do trabalho.


1. No cartão “Iniciar lógica”, clique em “Executar essa lógica manualmente”.
2. Selecione o Aplicativo de API do Conector do HDInsight criado anteriormente na galeria (você encontrará o conector do HDInsight criado na lista de Aplicativos de API à direita da tela). Selecione a seta para a direita preta. As ações disponíveis são apresentadas: ![][12]

3. Selecione ‘Criar Cluster’, insira todos os parâmetros de cluster necessários e selecione ✓:![][6]

4. A ação agora aparece conforme configurado no aplicativo lógico. A(s) saída(s) da ação são mostradas e podem ser usadas como entradas em quaisquer ações posteriores: ![][7]

5. Selecione o mesmo conector do HDInsight da galeria como uma ação. Selecione a ação “Aguardar Criação do Cluster”, forneça todos os parâmetros necessários e selecione ✓:![][8]

6. Selecione o mesmo conector do HDInsight da galeria como uma ação. Selecione a ação “Enviar Trabalho do Hive”, insira todos os parâmetros necessários e selecione ✓: ![][9]

7. Selecione o mesmo conector do HDInsight da galeria como uma ação. Selecione a ação “Aguardar Conclusão do Trabalho”, insira todos os parâmetros necessários e selecione ✓: ![][10]

8. Selecione o mesmo conector do HDInsight da galeria como uma ação. Selecione a ação “Excluir Cluster”, insira todos os parâmetros necessários e selecione ✓: ![][11]

9. Salve o aplicativo lógico usando o comando salvar na parte superior do designer.

Para testar o cenário, selecione **Executar Agora** para iniciar o aplicativo lógico manualmente.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-hdinsight/Create.png
[2]: ./media/app-service-logic-connector-hdinsight/CertNotConfigured.png
[3]: ./media/app-service-logic-connector-hdinsight/CertConfigured.png
[5]: ./media/app-service-logic-connector-hdinsight/LogicApp1.png
[6]: ./media/app-service-logic-connector-hdinsight/LogicApp2.png
[7]: ./media/app-service-logic-connector-hdinsight/LogicApp3.png
[8]: ./media/app-service-logic-connector-hdinsight/LogicApp4.png
[9]: ./media/app-service-logic-connector-hdinsight/LogicApp5.png
[10]: ./media/app-service-logic-connector-hdinsight/LogicApp6.png
[11]: ./media/app-service-logic-connector-hdinsight/LogicApp7.png
[12]: ./media/app-service-logic-connector-hdinsight/LogicApp8.PNG

<!---HONumber=AcomDC_0114_2016-->