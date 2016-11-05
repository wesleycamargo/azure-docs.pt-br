---
title: Testar o desempenho do seu aplicativo Web do Azure | Microsoft Docs
description: Execute testes de desempenho de aplicativo Web do Azure para verificar como o seu aplicativo lida com a carga do usuário. Contabilize o tempo de resposta e encontre falhas que podem indicar problemas.
services: app-service\web
documentationcenter: ''
author: ecfan
manager: douge
editor: jimbe

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2016
ms.author: estfan; manasma; ahomer

---
# Teste o desempenho do seu aplicativo Web do Azure sob carga
Verifique o desempenho de seu aplicativo Web antes de iniciá-lo ou implantar atualizações na produção. Dessa forma, você pode avaliar melhor se o aplicativo está pronto para lançamento. Tenha mais confiança que seu aplicativo pode dar conta do tráfego durante o pico de uso ou na sua próxima ação de marketing.

Durante a visualização pública, você pode testar o desempenho de seu aplicativo gratuitamente no Portal do Azure. Esses testes simulam carga de usuário em seu aplicativo por um período de tempo específico e medem a resposta desse aplicativo. Por exemplo, os resultados do teste mostram a velocidade com que seu aplicativo responde a um número específico de usuários. Eles também mostram quantas solicitações falharam, o que pode indicar problemas com seu aplicativo.

![Localizar problemas de desempenho em seu aplicativo Web](./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png)

## Antes de começar
* Você precisará de uma [assinatura do Azure](https://account.windowsazure.com/subscriptions), se ainda não tiver uma. Saiba como [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* Você precisará de uma conta do [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) para manter o histórico de seus testes de desempenho. Uma conta adequada será criada automaticamente quando você configurar o teste de desempenho. Ou você pode criar uma nova conta ou usar uma conta existente, se for o proprietário da conta.
* Implante seu aplicativo para teste em um ambiente de não produção. Faça com que seu aplicativo use um plano do Serviço de Aplicativo que não seja o plano usado na produção. Desse modo, você não afeta nenhum cliente existente e nem desacelera o seu aplicativo em produção.

## Configurar e executar o teste de desempenho
1. Entre no [Portal do Azure](https://portal.azure.com). Para usar uma conta do Visual Studio Team Services que você possui, entre como o proprietário da conta.
2. Vá para seu aplicativo Web.
   
   ![Vá para Procurar Tudo, Aplicativos Web, seu aplicativo Web](./media/app-service-web-app-performance-test/azure-np-web-apps.png)
3. Vá para o **Teste de Desempenho**.
   
   ![Vá para Ferramentas, Teste de Desempenho](./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png)
4. Agora você vinculará uma conta do [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) para manter o histórico de seus testes de desempenho.
   
    Se você tiver uma conta do Team Services, selecione essa conta. Se não tem, crie uma nova conta.
   
    ![Selecione a conta do Team Services existente ou crie uma nova conta](./media/app-service-web-app-performance-test/azure-np-no-vso-account.png)
5. Crie o teste de desempenho. Defina os detalhes e execute o teste.

Você pode observar os resultados em tempo real enquanto o teste é executado.

Por exemplo, suponha que temos um aplicativo que distribuiu cupons nas vendas de Natal do ano passado. Esse evento durou 15 minutos com uma carga de pico de 100 clientes simultâneos. Gostaríamos de duplicar o número de clientes neste ano. Também queremos melhorar a satisfação do cliente, reduzindo o tempo de carregamento de página de 5 segundos para 2 segundos. Portanto, testaremos desempenho do nosso aplicativo atualizado com 250 usuários por 15 minutos.

Simularemos a carga em nosso aplicativo gerando usuários virtuais (clientes) que visitam nosso site simultaneamente. Isso nos mostrará quantas solicitações estão falhando ou respondendo com lentidão.

  ![Crie, configure e execute seu teste de desempenho](./media/app-service-web-app-performance-test/azure-np-new-performance-test.png)

* A URL do seu aplicativo Web padrão é adicionada automaticamente. Você pode alterar a URL para testar outras páginas (somente solicitações HTTP GET).
* Para simular condições locais e reduzir a latência, selecione um local mais próximo de seus usuários para a geração de carga.
  
  Aqui está o teste em andamento. Durante o primeiro minuto, nossa página carrega mais lentamente do que gostaríamos.
  
  ![Teste de desempenho em andamento com dados em tempo real](./media/app-service-web-app-performance-test/azure-np-running-perf-test.png)
  
  Após a conclusão do teste, aprendemos que a página carrega muito mais rapidamente após o primeiro minuto. Isso ajuda a identificar no qual é mais provável que desejemos começar a solucionar o problema.
  
  ![Um teste de desempenho concluído mostra os resultados, incluindo solicitações com falha](./media/app-service-web-app-performance-test/azure-np-perf-test-done.png)

## Testar várias URLs
Você também pode executar testes de desempenho ao incorporar várias URLs que representem um cenário de usuário de ponta a ponta carregando um arquivo de Teste na Web do Visual Studio. Algumas das maneiras como você pode criar um arquivo de Teste na Web do Visual Studio são:

* [Capturar o tráfego usando o Fiddler e exportar como um arquivo de Teste na Web do Visual Studio](http://docs.telerik.com/fiddler/Save-And-Load-Traffic/Tasks/VSWebTest)
* [Criar um arquivo de teste de carga no Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)

Para carregar e executar um arquivo de Teste na Web do Visual Studio:

1. Siga as etapas acima para abrir a folha **Novo teste de desempenho**. Nessa folha, escolha a opção CONFIGURAR TESTE USANDO para abrir a folha **Configurar teste usando**.  
   
    ![Abertura da folha Configurar testes de carga](./media/app-service-web-app-performance-test/multiple-01-authoring-blade.png)
2. Verifique se o TIPO DE TESTE foi definido como **Teste na Web do Visual Studio** e selecione Arquivo Morto HTTP. Use o ícone de "pasta" para abrir a caixa de diálogo do seletor de arquivo.
   
    ![Carregamento de um arquivo de Teste na Web do Visual Studio de várias URLs](./media/app-service-web-app-performance-test/multiple-01-authoring-blade2.png)
   
    Depois que o arquivo tiver sido carregado, você verá a lista de URLs a serem testadas na seção DETALHES DA URL.
3. Especifique a carga de usuário e a duração do teste, então escolha **Executar teste**.
   
    ![Seleção da duração e da carga do usuário](./media/app-service-web-app-performance-test/multiple-01-authoring-blade3.png)
   
    Após a conclusão do teste, você verá os resultados em dois painéis. O painel esquerdo mostra as informações de desempenho como uma série de gráficos.
   
    ![O painel de resultados de desempenho](./media/app-service-web-app-performance-test/multiple-01a-results.png)
   
    O painel direito mostra uma lista de solicitações que falharam, com o tipo de erro e o número de vezes que ele ocorreu.
   
    ![Painel de falhas de solicitação](./media/app-service-web-app-performance-test/multiple-01b-results.png)
4. Execute novamente o teste ao escolher o ícone **Executar Novamente** na parte superior do painel direito.
   
    ![Executando novamente o teste](./media/app-service-web-app-performance-test/multiple-rerun-test.png)

## Perguntas e respostas
#### P: Existe um limite para o tempo pelo qual posso executar um teste?
**R**: Sim, você pode executar o teste por até uma hora no Portal do Azure.

#### P: Por quanto tempo posso executar testes de desempenho?
**R**: Após a visualização pública, você obtém 20.000 VUMs (minutos de usuário virtual) gratuitamente, todo mês, com sua conta do Visual Studio Team Services. Um VUM é o número de usuários virtuais multiplicados pelo número de minutos em seu teste. Se suas necessidades excederem o limite gratuito, você pode adquirir mais tempo e pagar somente pelo que usar.

#### P: Onde posso verificar quantos VUMs usei até agora?
**R**: Você pode verificar esse valor no Portal do Azure.

![Acesse sua conta do Team Services](./media/app-service-web-app-performance-test/azure-np-vso-accounts.png)

![Verifique os VUMs usados](./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png)

#### P: Qual é a opção padrão e os meus testes existentes serão afetados?
**R**: A opção padrão para testes de carga de desempenho é um teste manual - a mesma opção de teste que existia antes de a opção de teste de várias URLs ter sido adicionada ao portal. Os testes existentes continuam a usar a URL configurada e funcionarão como antes.

#### P: Quais recursos não têm suporte no arquivo de Teste na Web do Visual Studio?
**R**: No momento este recurso não dá suporte a plug-ins de Teste da Web, a fontes de dados e a regras de extração. Você deve editar seu arquivo de Teste na Web para removê-los. Esperamos adicionar suporte a esses recursos em futuras atualizações.

#### P: Ele oferece suporte a outros formatos de arquivo de Teste na Web?
**R**: No momento, somente os arquivos no formato de Teste na Web do Visual Studio têm suporte. Adoraríamos ouvi-lo caso você precise de suporte para outros formatos de arquivo. Envie um email para [vsoloadtest@microsoft.com](mailto:vsoloadtest@microsoft.com).

#### P: O que mais posso fazer com uma conta do Visual Studio Team Services?
**R**: Para encontrar sua nova conta, acesse ```https://{accountname}.visualstudio.com```. Compartilhe seu código, compile, teste, acompanhe o trabalho e despache o software – tudo na nuvem, usando qualquer ferramenta ou idioma. Saiba mais sobre como os recursos e serviços do [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) ajudam sua equipe a colaborar mais facilmente e implantar de modo contínuo.

## Consulte também
* [Executar testes de desempenho de nuvem simples](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-simple-cloud-load-test)
* [Executar testes de desempenho do Apache Jmeter](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-jmeter-test)
* [Gravar e reproduzir testes de carga baseados na nuvem](https://www.visualstudio.com/docs/test/performance-testing/getting-started/record-and-replay-cloud-load-tests)
* [Teste de desempenho do seu aplicativo na nuvem](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)

<!---HONumber=AcomDC_0525_2016-->