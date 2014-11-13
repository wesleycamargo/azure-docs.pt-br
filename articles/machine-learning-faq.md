<properties title="Perguntas frequentes (FAQ) do Aprendizado de M&aacute;quina do Microsoft Azure" pageTitle="Perguntas frequentes do Aprendizado de M&aacute;quina | Azure" description="Perguntas frequentes sobre o Aprendizado de M&aacute;quina do Microsoft Azure" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="pamehta" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="pamehta" />

# Perguntas frequentes (FAQ) do Aprendizado de Máquina do Microsoft Azure

### GERAL

**1. O que é o Aprendizado de Máquina do Microsoft Azure?**

O Aprendizado de Máquina do Microsoft Azure é um serviço totalmente gerenciado para criar, testar, operacionalizar e gerenciar soluções analíticas preditivas na nuvem. Com apenas um navegador, agora você pode entrar no Aprendizado de Máquina do Azure, fazer upload de dados e iniciar imediatamente experimentos de aprendizado de máquina. A composição visual, uma grande paleta de módulos e uma biblioteca de modelos iniciais transformam as tarefas comuns de aprendizado de máquina em tarefas simples e rápidas. Transformar um modelo em um serviço Web é fácil - com alguns cliques, um modelo Preditivo criado no Estúdio AM pode ser transformado em uma API REST pública que encapsula a lógica de transformação de dados personalizados e os modelos de aprendizado de máquina sofisticados.

**2. O que é o Estúdio de Aprendizado de Máquina do Microsoft Azure?**

O Estúdio AM do Azure é um ambiente de trabalho acessível por meio de um navegador da Web. O Estúdio AM hospeda uma paleta de módulos com uma interface de composição visual que permite criar um fluxo de trabalho de ciência de dados de ponta a ponta na forma de um experimento. Há módulos para ingestão de dados, transformação, seleção de recurso para criar, pontuar e avaliar um modelo preditivo. Alguns dos algoritmos mais avançados que capacitam o aprendizado de máquina no Bing e Xbox são criados no Estúdio AM. Pacotes de aprendizado de máquina escaláveis e de software livre, como Vowpal Wabbit também estão incluídos. O Estúdio AM é compatível com R. É possível trazer seus códigos R existentes e incorporá-los em seus experimentos. Ele permite combinar esses algoritmos com seu código R para criar modelos preditivos. O Estúdio AM facilita a colaboração permitindo convidar sua equipe para seus espaços de trabalho, onde eles podem exibir e modificar seus experimentos.

**3. O que é o serviço de API do AM do Azure?**

O serviço de API do AM permite implantar um modelo preditivo no Estúdio AM como um serviço Web escalável e tolerante a falhas. Os serviços Web criados pelo serviço de API do AM são APIs REST, que fornecem uma interface para comunicação entre aplicativos externos e seu modelo analítico preditivo. O serviço Web oferece uma forma de comunicar-se com um modelo preditivo em tempo real para receber resultados preditivos e incorporá-los em qualquer aplicativo externo do cliente. O serviço de API do AM utiliza o Microsoft Azure para implantação, host e gerenciamento das APIs REST do AM do Azure. Dois tipos de serviços são criados usando o serviço de API do AM do Azure. O serviço de Execução em Lote para acesso assíncrono em lote e o serviço Resposta de Solicitação para resposta assíncrona de baixa latência.

Um modelo preditivo pode ser colocado em preparação dentro de seu espaço de trabalho. O serviço de API do AM também gera páginas de ajuda para serviços Web. As páginas de ajuda do serviço Web fornecem amostras de código para invocar o serviço Web em C#, R e Python. É possível testar seu serviço Web fazendo chamadas interativas ao serviço. O serviço Web pré-configurado pode então ser colocado em produção em apenas alguns cliques. Uma vez em produção, é possível monitorar serviços implantados, bem como rastrear o uso e erros no portal do Azure. A atualização dos serviços Web é tão simples quanto atualizar o modelo no Estúdio AM e colocar as mudanças no serviço de preparação.

**4. Como fazer para acessar o Aprendizado de Máquina do Microsoft Azure?**

Para uma introdução ao Aprendizado de Máquina do Azure, visite a [página de introdução][página de introdução]. Visite o [Centro de Aprendizado de Máquina do Azure][Centro de Aprendizado de Máquina do Azure] para obter atualizações do serviço, ler o que há de mais recente no blog da equipe do AM, participar de nossa comunidade Aprendizado de Máquina por meio de fóruns, acessar a ajuda do produto, exibir a galeria de modelos e fornecer feedback sobre o serviço para nos ajudar a dar forma ao roteiro do produto.

### COBRANÇA

**5. Como a cobrança do Aprendizado de Máquina funciona?**

O serviço do Estúdio AM do Azure é cobrado por hora de computação para experimentação ativa e o faturamento é rateado para horas parciais. O serviço de API do AM do Azure é faturado por 1.000 chamadas de API de previsão e por hora de computação quando uma previsão está sendo executada ativamente. O faturamento é rateado para quantidades de previsão menores que 1.000 e horas parciais de computação.

Os encargos são agregados por espaço de trabalho para sua assinatura. Em cada espaço de trabalho, você verá os encargos para os três itens

-   Horas de Teste no Estúdio - este medidor agrega todos os encargos de computação resultantes da execução de experimentos no Estúdio AM e da execução de previsões no ambiente de preparação.
-   Horas de Previsão do Serviço de API - este medidor inclui encargos de computação resultantes dos serviços Web em execução na produção.
-   Previsões de Serviço de API (em 1000s) - este medidor inclui encargos resultantes por chamada ao serviço Web de produção.

Visite a página Preços para obter detalhes de preços, <http://azure.microsoft.com/pt-br/pricing/details/machine-learning/>.

**6. O Aprendizado de Máquina do Azure tem uma Avaliação Gratuita?**

O AM do Azure é uma parte da avaliação gratuita do Azure. Ao assinar uma avaliação gratuita do Azure, você pode testar quaisquer serviços Azure por um mês. Para aprender mais sobre a avaliação gratuita do Azure, visite <http://azure.microsoft.com/pt-br/pricing/free-trial-faq/>.

### ESTÚDIO DE APRENDIZADO DE MÁQUINA

**7. Para quais fontes de dados o AM do Azure tem suporte?**

Os dados podem ser carregados no Estúdio AM de uma de duas formas, ao fazer upload de arquivos locais como conjunto de dados ou usando o módulo de leitor para importar dados. Os arquivos locais podem ser transferidos por upload como conjuntos de dados, adicionando novos conjuntos de dados no Estúdio AM. Consulte o tópico de ajuda **Obtendo Dados** no Estúdio AM para saber mais sobre formatos de arquivo com suporte.

O módulo **Leitor** pode ler dados da Tabela do Azure, Blob do Azure, Banco de Dados SQL (Azure) ou HDInsight. Também é possível extrair dados de uma fonte de dados via http. Consulte o tópico de ajuda para o módulo **Leitor** no Estúdio AM para obter detalhes.

**8. Que tamanho meu conjunto de dados pode ter?**

O Estúdio AM dá suporte a conjuntos de dados de treinamento de até 10 GB. Não há limite de tamanho de conjunto de dados para Serviços Web. A amostragem de conjuntos de dados maiores pelo Hive ou consultas SQL antes da ingestão também tem suporte. Se você estiver trabalhando com dados maiores que 10 GB, poderá criar diversos conjuntos de dados e usar os módulos ‘Partição e Amostra’, ‘Divisão’ ou ‘Junção’ para combinar novamente esses conjuntos de dados no Estúdio AM para criar conjuntos de treinamentos para criação de modelos preditivos. Visite a ajuda do módulo no Estúdio AM para saber mais sobre esses módulos.

Para conjuntos de dados maiores que alguns GB, a abordagem recomendada é fazer upload de dados para o armazenamento do Azure ou Banco de Dados SQL (Azure) ou usar o HDInsight, em vez de fazer upload diretamente do arquivo local.

**9. Quais algoritmos existentes de AM têm suporte no Estúdio AM?**

O Estúdio AM fornece os mais avançados algoritmos de AM, como árvores de Decisão Impulsionada Escalável, sistemas de Recomendação Bayesiana, Redes Neurais Profundas e Selvas de Decisões (Decision Jungles) desenvolvidos na Microsoft Research. Pacotes de aprendizado de máquina escaláveis e de software livre, como Vowpal Wabbit também estão incluídos. O Estúdio AM dá suporte a algoritmos de aprendizado de máquina para Classificação, Regressão e Armazenamento em Cluster de diversas classes e binários. A lista completa de algoritmos de aprendizado de máquina está disponível na ajuda do Estúdio AM.

**10. O algoritmo, a fonte de dados, o formato de dados e a operação de transformação de dados de Aprendizado de Máquina que busco não está no Estúdio AM do Azure. Quais são minhas opções?**

É possível visitar o [fórum de feedback do usuário][fórum de feedback do usuário] para ver solicitações de recurso que estamos acompanhando. Inclua seu voto nesta solicitação se um recurso que está procurando já tiver sido solicitado. Se o recurso que está procurando não existir, crie uma nova solicitação. É possível exibir o status de sua solicitação neste fórum também. Acompanhamos esta lista de perto e atualizamos o status da disponibilidade do recurso com frequência.

**11. Posso trazer meu código existente para o Estúdio AM?**

O Estúdio AM atualmente dá suporte a R; é possível trazer seus códigos R existentes no Estúdio AM e executá-los no mesmo experimento com os aprendizes fornecidos no AM do Azure e publicá-lo como um serviço Web pelo AM do Azure. O AM do Azure é a forma mais rápida de transformar ativos analíticos em R em serviços Web de produção de nível corporativo. Consulte o tópico da ajuda do Estúdio AM **Extensibilidade com R** para saber como trazer seus códigos R e visualização no Estúdio AM.

**12. Quais pacotes R estão disponíveis no Estúdio AM?**

O Estúdio AM dá suporte atualmente a mais de 350 pacotes R, e a lista continua crescendo. Consulte o tópico de ajuda do Estúdio AM **Extensibilidade com R** para saber como obter uma lista de pacotes R com suporte. Se o pacote que desejar não estiver nesta lista, forneça o nome do pacote no [fórum de feedback do usuário][fórum de feedback do usuário].

### SERVIÇO DE API DO AM

**13. Em que situação eu poderia executar meu modelo preditivo como um Serviço de Execução em Lote x serviço Web de solicitação/resposta?**

O Request-Response Service (RRS) é um Serviço Web de alta escala e baixa latência usado para fornecer uma interface a modelos sem monitoração de estado criados e publicados a partir do ambiente Experimentação. O Batch Execution Service (BES) é um serviço para pontuação assíncrona em lote de registros de dados. A entrada para o BES é semelhante a entrada de dados usada no RRS. A principal diferença é que o BES lê um bloco de registros de uma variedade de origens, como blobs, tabelas no Azure, Banco de Dados SQL (Azure), HDInsight (Consulta Hive) e origens HTTP. Os resultados da pontuação são publicados em um arquivo no armazenamento de blob do Azure e o terminal de armazenamento é retornado na resposta.

O Batch Execution Service é útil em um cenário em que um grande número de pontos de dados precisam ser pontuados, de forma em lote ou muitos dos seus dados já estão no formato de arquivo em um armazenamento do Azure ou um cluster Hadoop. O serviço Web pode transformar os dados que lê antes de enviá-los ao modelo, portanto, você pode apenas apontar seus dados de transação do fim de semana para um serviço em Lote que transformará e fornecerá os resultados.

O Request Response Service é útil em um cenário em que a análise preditiva é necessária em tempo quase real para alimentar um painel ativo, conta do usuário guiada ou conteúdo oferecido via aplicativo móvel ou Web.

**14. Como fazer para atualizar o modelo para a produção do serviço implantado?**

Atualizar um modelo preditivo para um serviço já implantado é tão simples quanto modificar e executar novamente o experimento usado para criar e salvar o modelo treinado. Uma vez que tiver uma nova versão do modelo treinado disponível, o Estúdio AM perguntará se você deseja atualizar seu serviço Web de preparação. Após a atualização ser aplicada ao serviço Web de preparação, a mesma atualização ficará disponível para que você também aplique ao serviço Web de produção. Consulte o tópico de ajuda do Estúdio AM **Atualizando o Serviço Web** para obter detalhes sobre como atualizar um serviço Web implantado.

### SEGURANÇA E DISPONIBILIDADE

**15. Quem possui acesso ao terminal http para o serviço Web implantado na produção por padrão? Como fazer para restringir o acesso ao terminal?**

Uma vez que um modelo preditivo foi colocado em produção, o Portal do Azure lista a URL para os serviços Web implantados. URLs de Serviço de Preparação são acessíveis a partir do Ambiente do Estúdio AM sob a seção de serviços Web e as URLs de serviço de Produção são acessíveis a partir do Portal do Azure, sob a seção Aprendizado de Máquina. As chaves de acesso fornecidas para serviços Web de Preparação e Produção a partir do Painel do serviço Web no Estúdio AM e no ambiente do portal do Azure respectivamente. Elas são necessárias para fazer chamadas ao serviço Web em produção e preparação.

**16. Como fazer para monitorar meu serviço Web implantado em produção?**

Uma vez que um modelo preditivo tiver sido colocado em produção, é possível monitorar a partir do portal do Azure. Cada serviço implantado possui seu próprio painel, em que você poderá ver as informações de monitoramento para esse serviço.

### SUPORTE E TREINAMENTO

**17. Onde posso obter treinamento para o AM do Azure?**

O [Centro de Aprendizado de Máquina do Azure][Centro de Aprendizado de Máquina do Azure] hospeda tutoriais em vídeo e guias de instruções. Esses guias de instruções passo a passo fornecem uma introdução aos serviços e explicam o ciclo de vida de ciência dos dados da importação de dados, limpeza de dados, criação de modelos preditivos e implantação deles na produção com o AM do Azure.

Os tutoriais de vídeo fornecem um tour visual do Estúdio AM e serviço de API do AM. Os tutoriais de vídeo demonstram a amplitude do serviço, o ingresso de dados mais usado, limpeza e processamento de módulos; criação de um modelo preditivo e implantação de modelos preditivos. Eles também abrangem tarefas, como provisionamento de espaço de trabalho e implantação de modelos pré-configurados para produção.

Incluiremos novo material continuamente no Centro de Aprendizado de Máquina. É possível enviar a solicitação para material de aprendizado adicional no Centro de Aprendizado de Máquina no [fórum de feedback do usuário][1].

**18. Como fazer para obter suporte ao AM do Azure?**

O AM do Azure tem suporte como parte da oferta de suporte do Azure. Para obter suporte técnico no AM do Azure, selecione ‘Aprendizado de Máquina' como serviço e você verá uma categoria de tópicos para arquivar seu chamado de suporte. Para aprender mais sobre a oferta do Suporte do Azure, visite <http://azure.microsoft.com/pt-br/support/options/>

O Aprendizado de Máquina do Azure também possui um fórum de comunidade no MSDN, em que é possível fazer questões relacionadas ao AM do Azure. O fórum é monitorado pela equipe do AM do Azure. Visite o [Fórum do Azure][Fórum do Azure].

  [página de introdução]: http://go.microsoft.com/fwlink/?LinkId=404226
  [Centro de Aprendizado de Máquina do Azure]: http://azure.microsoft.com/pt-br/documentation/services/machine-learning/
  [fórum de feedback do usuário]: http://go.microsoft.com/fwlink/?LinkId=404231
  [1]: https://windowsazure.uservoice.com/forums/257792-machine-learning
  [Fórum do Azure]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning
