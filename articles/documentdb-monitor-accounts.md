<properties title="Monitor a DocumentDB Account" pageTitle="Monitor a DocumentDB account | Azure" description="Learn how to monitor your DocumentDB account for performance metrics (such as requests and server errors) and usage metrics (such as storage consumption)." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, monitor, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="brradsev" />

# Monitorar uma conta de Banco de Dados de Documentos

Você pode monitorar suas contas de Banco de Dados de Documentos no [Portal de Visualização do Azure][Portal de Visualização do Azure]. Para cada conta do Banco de Dados de Documentos, tanto as métricas de desempenho (como as solicitações e erros de servidor) quanto as métricas de utilização (como consumo de armazenamento) estão disponíveis.

## Neste artigo

-   [Como: Visualizar métricas de desempenho para uma conta do Banco de Dados de Documentos][Como: Visualizar métricas de desempenho para uma conta do Banco de Dados de Documentos]
-   [Como: Personalizar visualizações de métricas de desempenho para uma conta do Banco de Dados de Documentos][Como: Personalizar visualizações de métricas de desempenho para uma conta do Banco de Dados de Documentos]
-   [Como: Criar gráficos de métricas de desempenho lado a lado][Como: Criar gráficos de métricas de desempenho lado a lado]
-   [Como: Visualizar métricas de utilização para uma conta do Banco de Dados de Documentos][Como: Visualizar métricas de utilização para uma conta do Banco de Dados de Documentos]
-   [Como: Defina alertas de métrica de desempenho para uma conta do Banco de Dados de Documentos][Como: Defina alertas de métrica de desempenho para uma conta do Banco de Dados de Documentos]
-   [Próximas etapas][Próximas etapas]

## <span id="metrics"></span></a>Como: Visualizar métricas de desempenho para uma conta do Banco de Dados de Documentos

1.  No [Portal de Visualização do Azure][Portal de Visualização do Azure], clique em **Procurar**, em seguida, em **Contas do Banco de Dados de Documentos** e, por fim, clique no nome da conta do Banco de Dados de Documentos cujas métricas de desempenho você gostaria de visualizar.
2.  Dentro da lente **Monitoramento**, você pode, por padrão, consultar:

    -   Solicitações totais para o dia de hoje.
    -   Média de solicitações por segundo para o dia de hoje

    ![][0]

3.  Clicar na parte **Solicitações totais ou Média de solicitações por segundo** abre uma lâmina de **Métrica** detalhada.
4.  A lâmina Métrica mostra a você detalhes sobre as métricas que você selecionou. Na parte superior da lâmina há um gráfico, abaixo dele há uma tabela que mostra valores de agregação das métricas selecionadas como a média, o mínimo e o máximo. A lâmina Métrica também exibe a lista de alertas que foram definidos, filtrados para as métricas que aparecem na lâmina de métricas atual (desse modo, se você possui um determinado número de alertas, você verá apenas aqueles relevantes apresentados aqui).

    ![][1]

## <span id="custom"></span></a>Como: Personalizar visualizações de métricas de desempenho para uma conta do Banco de Dados de Documentos

1.  Para personalizar as métricas exibidas em uma parte específica, clique com o botão direito no gráfico de métricas e selecione **Editar gráfico**.
    ![][2]

2.  Na lâmina **Editar Gráfico**, há opções para modificar as métricas que são exibidas nessa parte, além de seu intervalo de tempo.
    ![][3]

3.  Para mudar as métricas exibidas na parte, simplesmente marque/desmarque as métricas de desempenho disponíveis e clique em **Salvar** na parte inferior da lâmina.
4.  Para mudar o intervalo de tempo, escolha um intervalo diferente (por exemplo, **Última hora**) e clique em **Salvar** na parte inferior da lâmina.
    ![][4]

5.  O intervalo de tempo personalizado permite que você escolha qualquer período de tempo nas duas últimas semanas.
6.  Quando você clica em **Salvar**, suas alterações persistem até que você saia da lâmina de conta do Banco de Dados de Documentos. Mais tarde, quando você retornar, verá a métrica e o intervalo de tempo originais.

## <span id="create"></span></a>Como: Criar gráficos lado a lado

O portal de Visualização do Azure permite que você crie gráficos de métricas lado a lado.

1.  Primeiro, clique com o botão direito no gráfico a partir do qual você deseja começar e selecione **Personalizar**.
    ![][5]

2.  Clique em **Clonar** no menu para copiar a parte.

    ![][6]

3.  Por fim, clique em **Concluído** na barra de ferramentas presente no topo da tela. Agora, você pode tratar essa parte como qualquer outra parte de métrica, personalizando as métricas e o intervalo de tempo exibidos na parte. Fazendo isso, você pode ver um gráfico com duas métricas diferentes lado a lado simultaneamente.
    ![][7]

> Observe que o intervalo de tempo do gráfico e as métricas escolhidas serão redefinidos para os valores padrão da parte quando você sair do portal de Visualização do Azure.

## <span id="view"></span></a>Como: Visualizar métricas de utilização para uma conta do Banco de Dados de Documentos

1.  No [Portal de Visualização do Azure][Portal de Visualização do Azure], clique em **Procurar**, em **Contas do Banco de Dados de Documentos** e, por fim, clique no nome da conta do Banco de Dados de Documentos cujas métricas de uso você gostaria de visualizar.
2.  Dentro da lente **Utilização** você pode visualizar, por padrão:

    -   Armazenamento consumido na conta
    -   Armazenamento máximo disponível da conta
    -   Uso de anexos
    -   Uso de permissões e usuários
    -   Alocação de unidade de capacidade
    -   Uso de anexos na conta
        ![][8]

## <span id="setup"></span></a>Como: Defina alertas de métrica de desempenho para uma conta do Banco de Dados de Documentos

1.  No [Portal de Visualização do Azure][Portal de Visualização do Azure], clique em **Procurar**, em **Contas do Banco de Dados de Documentos** e, por fim, clique no nome da conta do Banco de Dados de Documentos para a qual você gostaria de configurar alertas de métrica de desempenho.
2.  Na lente **Operações**, clique na parte **Regras de alerta**.
    ![][9]

3.  Na lâmina Regras de alerta, clique em **Adicionar alerta**.
    ![][10]

4.  Na lâmina **Adicionar uma regra de alerta**, especifique:

    -   O nome da regra de alerta que você está configurando.
    -   Uma descrição da nova regra de alerta.
    -   A métrica para a regra de alerta.
    -   A condição, o limite e o período que determinam quando o alerta é ativado. Por exemplo, uma contagem de erros de servidor superior a cinco nos últimos 15 minutos.
    -   Se o administrador de serviços e seus coadministradores recebem ou não emails quando o alerta é enviado.
    -   Endereços de email adicionais para notificações de alerta.
        ![][11]

## <span id="next"></span></a>Próximas etapas

-   Para saber mais sobre o Banco de Dados de Documento, consulte a documentação do Banco de Dados de Documentos do Azure em [azure.com][azure.com]

<!--Anchors-->

  [Portal de Visualização do Azure]: https://portal.azure.com/
  [Como: Visualizar métricas de desempenho para uma conta do Banco de Dados de Documentos]: #metrics
  [Como: Personalizar visualizações de métricas de desempenho para uma conta do Banco de Dados de Documentos]: #custom
  [Como: Criar gráficos de métricas de desempenho lado a lado]: #create
  [Como: Visualizar métricas de utilização para uma conta do Banco de Dados de Documentos]: #view
  [Como: Defina alertas de métrica de desempenho para uma conta do Banco de Dados de Documentos]: #setup
  [Próximas etapas]: #next
  [0]: http://i.imgur.com/y7pigTT.png
  [1]: http://i.imgur.com/6rBNPBL.png
  [2]: http://i.imgur.com/tRZEHk1.png
  [3]: http://i.imgur.com/G4UTi5U.png
  [4]: ./media/documentdb-monitor-accounts/madocdb5.png
  [5]: http://i.imgur.com/vLXWftF.png
  [6]: ./media/documentdb-monitor-accounts/madocdb7.png
  [7]: ./media/documentdb-monitor-accounts/madocdb8.png
  [8]: http://i.imgur.com/sL5inOu.png
  [9]: ./media/documentdb-monitor-accounts/madocdb10.png
  [10]: ./media/documentdb-monitor-accounts/madocdb11.png
  [11]: http://i.imgur.com/Inra4Po.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
