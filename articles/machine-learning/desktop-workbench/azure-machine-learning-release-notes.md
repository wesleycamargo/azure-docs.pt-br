---
title: Novidades do Azure Machine Learning
description: Este documento detalha as atualizações para o Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.service: machine-learning
ms.workload: data-services
ms.topic: reference
ms.date: 03/28/2018
ms.openlocfilehash: e30943426ad68171e1464f828a9c8672b06c975a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Novidades do Azure Machine Learning

Neste artigo, saiba mais sobre as novas versões do [Serviços do Azure Machine Learning](../service/overview-what-is-azure-ml.md). 

## <a name="2018-03-sprint-4"></a>2018-03 (Sprint 4)
**Número de versão**:  0.1.1801.24353 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([localizar sua versão](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Bem-vindo à quinta atualização do Azure Machine Learning Workbench. Muitas das atualizações a seguir foram feitas como resultado direto de seus comentários. Continue enviando-os!

**Alterações e novos recursos importantes**

- Suporte para executar os scripts em VMs remotas do Ubuntu nativamente em seu próprio ambiente, além de execução baseada em docker remoto.
- A nova experiência de ambiente no aplicativo Workbench permite que você crie destinos de computação e execute configurações além de nossa experiência com base em CLI.
![Guia Ambientes](media/azure-machine-learning-release-notes/environment-page.png)
- O Histórico de Execução Personalizável relata a ![Imagem de novos Relatórios de Histórico de Execução](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Atualizações detalhadas**

A seguir, está uma lista de atualizações detalhadas em cada área de componente do Azure Machine Learning neste sprint.

### <a name="workbench-ui"></a>Interface do usuário do Workbench
- Relatórios de Histórico de Execução Personalizável
  - Configuração de gráfico aprimorado para relatórios de Histórico de Execução
    - Os pontos de entrada usados podem ser alterados
    - Filtros de nível superior podem ser adicionados e modificados em ![Adicionar filtros](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Gráficos e estatísticas podem ser adicionados ou modificados (e reorganizados por arrastar e soltar).
    ![Criar novos Gráficos](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD para relatórios de Histórico de Execução
  - Movidas todas as configurações de exibição de lista do histórico de execução existente para relatórios no lado do servidor, que agem como pipelines em execuções dos pontos de entrada selecionados.

- Guia Ambientes
  - Adicionar novo destino de computação e executar arquivos de configuração com facilidade para seu projeto ![Novo destino de computação](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Gerenciar e atualizar os arquivos de configuração usando um UX simples baseado em formulário
  - Novo botão para preparar seus ambientes para execução

- Melhorias de desempenho para a lista de arquivos na barra lateral

### <a name="data-preparation"></a>Preparação dos dados 
- Azure Machine Learning Workbench agora permite que você seja capaz de pesquisar uma coluna usando o nome de uma coluna conhecida.


### <a name="experimentation"></a>Experimentação
- O Azure Machine Learning Workbench agora oferece suporte à execução dos scripts de modo nativo em seu próprio ambiente python ou pyspark. Para esse recurso, o usuário cria e gerencia seu próprio ambiente na VM remota e usa o Azure Machine Learning Workbench para executar scripts no destino. Consulte [Configuração do Serviço de Experimentação do Azure Machine Learning](experimentation-service-configuration.md) 

### <a name="model-management"></a>Gerenciamento de Modelos
- Suporte para personalizar os contêineres implantados: habilita a personalização da imagem de contêiner, permitindo a instalação de bibliotecas externas usando apt-get, etc. Não é mais limitado às bibliotecas instaláveis com pip. Para saber mais, confira a [documentação](model-management-custom-container.md).
  - Use o sinalizador `--docker-file myDockerStepsFilename` e o nome do arquivo com o manifesto, imagem ou comandos de criação de serviço.
  - Observe que a imagem base é Ubuntu e não pode ser modificada.
  - Exemplo de comando: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



## <a name="2018-01-sprint-3"></a>2018-01 (Sprint 3) 
**Número de versão**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([localizar sua versão](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Veja a seguir as atualizações e melhorias neste sprint. Muitas dessas atualizações são feitas como resultado direto dos comentários do usuário. 


A seguir, está uma lista de atualizações detalhadas em cada área de componente do Azure Machine Learning neste sprint.

- Atualizações na pilha de autenticação forçam a seleção de logon e conta na inicialização

### <a name="workbench"></a>Workbench
- Capacidade de instalar/desinstalar o aplicativo em Adicionar/Remover Programas
- Atualizações na pilha de autenticação forçam a seleção de logon e conta na inicialização
- Melhor experiência de SSO (Logon Único) no Windows
- Os usuários que pertencem a vários locatários com credenciais diferentes agora poderão entrar no Workbench

### <a name="ui"></a>Interface do usuário
- Melhorias gerais e correções de bugs

### <a name="notebooks"></a>Blocos de anotações
- Melhorias gerais e correções de bugs

### <a name="data-preparation"></a>Preparação dos dados 
- Melhorias de sugestões automáticas durante a execução de transformações Por Exemplo
- Algoritmo aprimorado para o inspetor de Frequência do Padrão
- Capacidade de enviar dados de exemplo e comentários durante a execução de transformações Por Exemplo ![Imagem do link Enviar comentários na transformação Derivar coluna](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Melhorias no Tempo de Execução do Spark
- O Scala substituiu o Pyspark
- Correção da incapacidade de fechar Dados Não Aplicáveis no Time Series Inspector 
- Correção do tempo de travamento para a execução de Preparação de Dados no HDI

### <a name="model-management-cli-updates"></a>Atualizações da CLI de Gerenciamento de Modelos 
  - A propriedade da assinatura não é mais necessária para o provisionamento de recursos. O acesso de Colaborador ao grupo de recursos será suficiente para configurar o ambiente de implantação.
  - Configuração do ambiente local habilitada para assinaturas gratuitas 

## <a name="2017-12-sprint-2-qfe"></a>2017-12 (Sprint 2 QFE) 
**Número de versão**: 0.1.1711.15323 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([localizar sua versão](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Esta é a versão QFE (Quick Fix Engineering), uma versão secundária. Ela aborda vários problemas de telemetria e ajuda a equipe de produto a entender melhor como o produto está sendo usado. Esse conhecimento pode ser usado em esforços futuros para melhorar a experiência de produto. 

Além disso, há duas atualizações importantes:

- Foi corrigido um bug na preparação de dados que impedia o inspetor de série temporal de exibir em pacotes de preparação de dados.
- Na ferramenta de linha de comando, não é mais necessário ter uma assinatura do Azure para provisionar clusters de ACS de Computação do Machine Learning. 

## <a name="2017-12-sprint-2"></a>2017-12 (Sprint 2)
**Número de versão**: 0.1.1711.15263  &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([localizar sua versão](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Bem-vindo à terceira atualização do Azure Machine Learning. Esta atualização inclui melhorias no aplicativo workbench, a CLI (Interface de Linha de Comando) e os serviços de back-end. Agradecemos imensamente por nos enviar sorrisos e rostos tristes. Muitas das atualizações a seguir foram feitas como resultado direto de seus comentários. 

**Novos recursos importantes**
- [Suporte para SQL Server e Azure SQL DB como uma fonte de dados](data-prep-appendix2-supported-data-sources.md#types) 
- [Aprendizagem profunda no Spark com suporte de GPU utilizando MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Todos os contêineres AML são compatíveis com os dispositivos do Azure IoT Edge quando implantados (sem etapas extras necessárias)](http://aka.ms/aml-iot-edge-blog)
- Lista de modelos registrados e exibições de detalhes disponíveis no Portal do Azure
- Acessando destinos de computação utilizando autenticação baseada em chave SSH, além de acesso baseado em senha/nome de usuário. 
- Novo Inspetor de Frequência de Padrão na experiência de preparação de dados. 

**Atualizações Detalhadas** A seguir, está uma lista de atualizações detalhadas em cada área de componente do Azure Machine Learning neste sprint.

### <a name="installer"></a>Instalador
- O instalador pode se atualizar automaticamente, de modo que as correções de bugs e novos recursos possam ser suportados sem que o usuário tenha que reinstalá-lo

### <a name="workbench-authentication"></a>Autenticação do Workbench
- Correções múltiplas para o sistema de autenticação. Informe-nos caso ainda esteja com problemas de logon.
- Alterações de UI que facilitam a localização das configurações do Gerenciador de Proxy.

### <a name="workbench"></a>Workbench
- A exibição de arquivo somente leitura agora tem plano de fundo azul claro
- Botão Editar movido para a direita para torná-lo melhor detectável.
- Os formatos de arquivo "dsource", "dprep" e "ipynb" agora podem ser renderizados em formato de texto
- O Workbench agora possui uma nova experiência de edição que guia os usuários para o uso de IDEs externos para editar scripts e use o Workbench somente para editar tipos de arquivos com uma experiência de edição avançada (como Notebooks, Fontes de dados, Pacotes de preparação de dados)
- Carregar a lista de espaços de trabalho e projetos aos quais o usuário tenha acesso, agora está significativamente mais rápido

### <a name="data-preparation"></a>Preparação dos dados 
- Um Inspector de Frequência de Padrão para exibir os padrões de sequência de cadeia de caracteres em uma coluna. Também é possível filtrar seus dados utilizando esses padrões. Isso mostra uma exibição semelhante ao inspetor de Contagem de Valores. A diferença é que a Frequência de Padrão mostra as contagens dos padrões exclusivos dos dados, em vez das contagens de dados exclusivos. Além disso, também é possível filtrar dentro ou fora todas as linhas que se encaixam em um determinado padrão.

![Imagem do inspetor de frequência de padrão no Número do Produto](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Melhorias de desempenho ao recomendar casos de borda para revisão na transformação 'derivar colunas por exemplo'

- [Suporte para SQL Server e Azure SQL DB como uma fonte de dados](data-prep-appendix2-supported-data-sources.md#types) 

![Imagem da criação de uma nova fonte de dados do SQL Server](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- Habilitado exibição "Visão rápida" das contagens de linhas e colunas

![Imagem da visão rápida da contagem de coluna da linha](media/azure-machine-learning-release-notes/row-col-count.png)

- A preparação de dados está habilitada em todos os contextos de computação
- As fontes de dados que utilizam um banco de dados do SQL Server estão habilitadas em todos os contextos de computação
- As colunas de grade de preparação de dados podem ser filtradas pelo tipo de dados
- Problema corrigido com a conversão de várias colunas até a data
- Problema corrigido, o qual o usuário poderia selecionar a coluna de saída como fonte em Derivar Coluna por Exemplo, se o usuário alterasse o nome da coluna de saída no modo avançado.

### <a name="job-execution"></a>Execução do trabalho
Agora é possível criar e acessar um destino de computação tipo cluster ou remotedocker utilizando a autenticação baseada em chave SSH, seguindo essas etapas:
- Anexar um destino de computação utilizando o seguinte comando na CLI

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>-k (ou a opção --use-azureml-ssh-key) no comando especifica para gerar e usar a chave SSH.

- O Azure ML Workbench irá gerar uma chave pública e emitirá isso em seu console. Faça logon no destino de computação utilizando o mesmo nome de usuário e acrescente o arquivo ~/.ssh/authorized_keys com essa chave pública.

- É possível pode preparar esse destino de computação e utilizá-lo para execução e o Azure ML Workbench usará essa chave para autenticação.  

Para obter mais informações sobre como criar alvos de computação, consulte [Configurando o Serviço de Experimentação do Azure Machine Learning](experimentation-service-configuration.md)

### <a name="visual-studio-tools-for-ai"></a>Ferramentas do Visual Studio para IA
- Adicionado suporte para [Ferramentas do Visual Studio para AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Interface de Linha de Comando (CLI)
- O comando `az ml datasource create` adicionado permite criar um arquivo de fonte de dados a partir da linha de comando

### <a name="model-management-and-operationalization"></a>Gerenciamento de modelos e operacionalização
- [Todos os contêineres do AML são compatíveis com os dispositivos do Azure IoT Edge quando operacionalizados (sem etapas extras necessárias)](http://aka.ms/aml-iot-edge-blog) 
- Aprimoramentos de mensagens de erro na CLI o16n
- Correções de bug no portal de gerenciamento de modelo UX  
- Capitalização consistente para atributos de gerenciamento de modelo na página de detalhes
- O tempo limite de chamadas em tempo real é definido como 60 segundos
- Lista de modelos registrados e exibições de detalhes disponíveis no Portal do Azure

![detalhes do modelo no portal](media/azure-machine-learning-release-notes/model-list.jpg)

![visão geral do modelo no portal](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Aprendizagem profunda no Spark com [Suporte de GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Suporte para modelos Gerenciador de Recursos para fácil implantação de recursos
- Suporte para o ecossistema de SparklyR
- [Integração de AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Projetos de exemplo
- Amostras [Íris](https://github.com/Azure/MachineLearningSamples-Iris) e [MMLSpark](https://github.com/Azure/mmlspark) atualizadas com a nova versão do SDK do ML do Azure

### <a name="breaking-changes"></a>Alterações de última hora
- Promovido para `--type` alternar em `az ml computetarget attach` para um subcomando. 

    - `az ml computetarget attach --type remotedocker` agora é `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` agora é `az ml computetarget attach cluster`

## <a name="2017-11-sprint-1"></a>2017-11 (Sprint 1) 
**Número de versão**:  0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([localizar sua versão](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Nessa versão, fizemos melhorias em segurança, estabilidade e facilidade de manutenção no aplicativo Workbench, na CLI e na camada de serviços back-end. Agradecemos imensamente por nos enviar sorrisos e rostos tristes. Muitas das atualizações abaixo foram feitas como resultado direto de seus comentários. Continue enviando-os!

### <a name="notable-new-features"></a>Novos recursos importantes
- O Azure ML agora está disponível em duas novas regiões do Azure: **Europa Ocidental** e **Sudeste Asiático**. Elas se juntam às regiões anteriores **Leste dos EUA 2**, **Centro-oeste dos EUA** e **Leste da Austrália**, elevando o número total de regiões implantadas para cinco.
- Habilitamos o realce de sintaxe do código Python no aplicativo Workbench para facilitar a leitura e edição do código-fonte Python. 
- Agora você pode iniciar seu IDE favorito diretamente por meio de um arquivo, em vez de todo o projeto.  Abrir um arquivo no Workbench e, em seguida, clicar em “Editar” inicia o IDE (atualmente, há suporte para o VS Code e PyCharm) no arquivo e projeto atuais.  Também clique na seta ao lado do botão Editar para editar o arquivo no editor de texto do Workbench.  Os arquivos são somente leitura até você clicar em Editar, prevenindo alterações acidentais.
- A popular biblioteca de plotagem `matplotlib` versão 2.1.0 agora é enviada com o aplicativo Workbench.
- Atualizamos a versão do .NET Core para 2.0 do mecanismo de preparação de dados. Isso removeu o requisito de instalação do openssl pelo Brew durante a instalação do aplicativo no macOS. Isso também abre caminho para os recursos de preparação de dados mais interessantes que estão chegando em um futuro próximo. 
- Habilitamos uma home page do aplicativo específica à versão, de modo que você obtenha as notas de versão mais relevantes e os prompts de atualização com base na versão atual do aplicativo.
- Caso seu nome de usuário local tenha um espaço, o aplicativo agora poderá ser instalado com êxito. 

### <a name="detailed-updates"></a>Atualizações detalhadas
Veja abaixo uma lista de atualizações detalhadas em cada área de componente do Azure Machine Learning deste sprint.

#### <a name="installer"></a>Instalador
- O instalador de aplicativo agora limpa o diretório de instalação criado por uma versão mais antiga do aplicativo.
- Correção de um bug que leva o instalador a travar em 100% no macOS High Sierra.
- Agora há um link direto para o diretório do instalador para que o usuário examine os logs do instalador em caso de falha de instalação.
- A instalação agora funciona para usuários que têm espaço em seus nomes de usuário.

#### <a name="workbench-authentication"></a>Autenticação do Workbench
- Suporte para autenticação no Gerenciador de Proxy.
- O logon agora terá êxito se o usuário estiver protegido por um firewall. 
- Se o usuário tiver contas de experimentação em várias regiões do Azure e se uma região não estiver disponível, o aplicativo deixará de parar de responder.
- Quando a autenticação não é concluída e a caixa de diálogo de autenticação ainda está visível, o aplicativo não tenta mais carregar o espaço de trabalho por meio do cache local.

#### <a name="workbench-app"></a>Aplicativo Workbench
- O realce de sintaxe do código do Python está habilitado no editor de texto.
- O botão Editar do editor de texto permite editar o arquivo em um IDE (há suporte para o VS Code e PyCharm) ou no editor de texto interno.
- O editor de texto está no modo somente leitura por padrão. 
- Agora o estado visual do botão Salvar é alterado para desabilitado depois que o arquivo atual é salvo e, portanto, não fica mais sujo.
- O Workbench salva _todos_ os arquivos não salvos quando você inicia uma execução.
- O Workbench lembra o último Espaço de Trabalho utilizado no computador local para que ele seja aberto automaticamente.
- Apenas uma única instância do Workbench agora tem permissão para ser executada. Anteriormente, era possível iniciar várias instâncias, o que causava problemas durante a operação no mesmo projeto.
- Menu Arquivo “Abrir Projeto...” renomeado para “Adicionar Pasta Existente como Projeto...” 
- A alternância de guia agora é muito mais rápida.
- Os links de ajuda foram adicionados à caixa de diálogo Configurando o IDE.
- Agora, o formulário de comentários lembra o endereço de email inserido na última vez.
- A área de texto de formulários de sorrisos e rostos tristes agora é maior, para que você possa nos enviar mais comentários! 
- O texto de ajuda da opção `--owner` em `az ml workspace create` foi corrigido.
- Adicionamos uma caixa de diálogo “Sobre” para ajudar o usuário a facilmente exibir e copiar o número de versão do aplicativo.
- Um item de menu “Sugerir um recurso” foi adicionado ao menu Ajuda.
- O nome da conta de Experimentação agora está visível na barra de título do aplicativo, antes do nome do aplicativo “Azure Machine Learning Workbench”.
- Uma home page do aplicativo específica à versão agora é exibida com base na versão do aplicativo detectado.

#### <a name="data-preparation"></a>Preparação dos dados 
- Um site externo não pode mais ser carregado no Inspetor de Mapa, a fim de prevenir problemas de segurança potenciais.
- Os inspetores de Histograma e Contagem de Valor agora têm a opção para mostrar o gráfico em escala logarítmica.
- Quando um cálculo está em andamento, a barra de qualidade de dados agora mostra uma cor diferente para sinalizar o estado “calculando”.
- As métricas de coluna agora mostram estatísticas para colunas de valores categóricos.
- O último caractere no nome da fonte de dados não é mais truncado.
- O pacote de preparação de dados agora permanece aberto ao alternar guias, resultando em ganhos de desempenho perceptíveis.
- Na fonte de dados, ao alternar entre exibições de dados e métricas, a ordem das colunas agora não é mais alterada.
- A abertura de um arquivo `.dprep` ou `.dsource` inválido não causa mais falhas no Workbench.
- O pacote de preparação de dados agora pode usar o caminho relativo para a saída na transformação _Gravar em CSV_.
- A transformação _Manter Coluna_ agora permite ao usuário adicionar outras colunas quando editadas.
- O menu _Substituir isto_ agora realmente inicia a caixa de diálogo _Substituir Valor_.
- A transformação _Substituir Valor_ agora funciona conforme esperado, em vez de gerar um erro.
- O pacote de preparação de dados agora usa o caminho absoluto ao referenciar arquivos de dados fora da pasta do projeto, possibilitando a execução do pacote no contexto local com o caminho absoluto para o arquivo de dados.
- Agora há suporte para o _arquivo completo_ como uma estratégia de amostragem ao usar o blob do Azure como uma fonte de dados.
- O código Python gerado (do pacote de preparação de dados) agora traz CR e LF, tornando-os amigáveis no Windows.
- O menu suspenso _Escolher Métricas_ agora oculta a propriedade ao alternar para a exibição Dados.
- O Workbench agora pode processar arquivos parquet mesmo quando está usando o tempo de execução do Python. Anteriormente, apenas o Spark podia ser usado durante o processamento de arquivos parquet. 
- A filtragem de valores em uma coluna com o tipo de dados _date_ não causa mais uma falha no mecanismo de preparação de dados.
- A exibição de métrica agora respeita as atualizações da estratégia de amostragem.
- Os trabalhos de amostragem remota agora funcionam corretamente.

#### <a name="job-execution"></a>Execução do trabalho
- O argumento agora está incluído no registro do histórico de execuções.
- Os trabalhos iniciados na CLI agora são mostrados no painel do Trabalho do Histórico de execuções automaticamente.
- O painel do trabalho agora mostra os trabalhos criados por usuários convidados adicionados ao locatário do Azure Active Directory.
- As ações de cancelamento e exclusão do painel do trabalho são mais estáveis.
- Ao clicar no botão Executar, uma mensagem de erro agora é disparada se os arquivos de configuração estão no formato incorreto.
- O encerramento do aplicativo não interfere mais nos trabalhos iniciados na CLI.
- Os trabalhos iniciados na CLI agora continuam obtendo a saída padrão mesmo após uma hora de execução.
- Melhores mensagens de erro são mostradas quando a execução do pacote de preparação de dados falha no Python/PySpark.
- O `az ml experiment clean` agora limpa as imagens do Docker em VMs remotas também.
- O comando `az ml experiment clean` agora funciona corretamente para o destino local no macOS.
- As mensagens de erro exibidas ao direcionar execuções locais ou remotas do Docker foram aprimoradas e são mais fáceis de serem lidas.
- Uma melhor mensagem de erro é exibida quando o nome do nó de cabeçalho do cluster HDInsight não está formatado corretamente quando anexado como um destino de execução.
- Uma melhor mensagem de erro é exibida quando o segredo não é encontrado no serviço de credencial. 
- A biblioteca do MMLSpark foi atualizada para dar suporte ao Apache Spark 2.2.
- O MMLSpark agora inclui a transformação de codificação de entidade (codificação de Malha) para documentos médicos.
- O `matplotlib` versão 2.1.0 agora é enviado pronto para uso com o Workbench.

#### <a name="jupyter-notebook"></a>Bloco de anotações do Jupyter
- A pesquisa de nome de bloco de anotações agora funciona corretamente na exibição Blocos de Anotações.
- Agora você pode excluir um Bloco de Anotações da exibição Blocos de Anotações.
- Uma nova mágica `%upload_artifact` foi adicionada para carregar arquivos produzidos no ambiente de execução do Notebook no armazenamento de dados do histórico de execuções.
- Os erros de kernel agora são apresentados no status do trabalho do Notebook para uma depuração mais fácil.
- O servidor Jupyter agora é desligado corretamente quando o usuário faz logoff do aplicativo.

#### <a name="azure-portal"></a>Portal do Azure
- A conta de Experimentação e a conta de Gerenciamento de Modelos agora podem ser criadas em duas novas regiões do Azure: Europa Ocidental e Sudeste Asiático.
- O plano DevTest da conta do Gerenciamento de Modelos agora está disponível apenas quando ele é o primeiro a ser criado na assinatura. 
- O link de ajuda no portal do Azure foi atualizado para apontar para a página de documentação correta.
- O campo de descrição foi removido da página de detalhes da imagem do Docker, pois não é aplicável.
- Detalhes, incluindo configurações do AppInsights e de escala automática, foram adicionados à página de detalhes do serviço Web.
- A página do gerenciamento de modelos agora é renderizada mesmo se cookies de terceiros estão desabilitados no navegador. 

#### <a name="operationalization"></a>Operacionalização
- Um serviço Web com “pontuação” em seu nome não falha mais.
- Agora o usuário pode criar um ambiente de implantação com apenas o acesso de Colaborador a um grupo de recursos do Azure ou à assinatura. O acesso de Proprietário à assinatura inteira não é mais necessário.
- A CLI de operacionalização agora aproveita o preenchimento automático de guias no Linux.
- O serviço de construção de imagens agora dá suporte à criação de imagens para serviços/dispositivos IoT do Azure.

#### <a name="sample-projects"></a>Projetos de exemplo
- Projeto de exemplo [_Classificando o Iris_](./tutorial-classifying-iris-part-1.md):
    - `iris_pyspark.py` foi renomeado para `iris_spark.py`.
    - `iris_score.py` foi renomeado para `score_iris.py`.
    - `iris.dprep` e `iris.dsource` foram atualizados para refletirem as últimas atualizações do mecanismo de preparação de dados.
    - O `iris.ipynb` Notebook foi corrigido para funcionar no cluster HDInsight.
    - O histórico de execuções foi ativado na célula do Notebook `iris.ipynb`.
- Correção da etapa “Resolver o Valor de Erro” do projeto de exemplo [_Preparação Avançada de Dados usando os Dados de Compartilhamento de Bicicletas_](./tutorial-bikeshare-dataprep.md).
- Formato `docker.runconfig` do projeto de exemplo [_MMLSpark em Dados de Censo Adulto_](https://github.com/Azure/MachineLearningSamples-mmlspark) atualizado de JSON para YAML.
- Formato `docker.runconfig` do projeto de exemplo [_Ajuste de Hiperparâmetro Distribuído_](./scenario-distributed-tuning-of-hyperparameters.md) atualizado de JSON para YAML.
- Novo projeto de exemplo [_Classificação de imagens usando o CNTK_](./scenario-image-classification-using-cntk.md).


## <a name="2017-10-sprint-0"></a>2017-10 (Sprint 0) 
**Número de versão**:  0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([localizar sua versão](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Bem-vindo à primeira atualização ao Workbench de Machine Learning do Azure seguindo nossa visualização pública inicial na conferência Microsoft Ignite 2017. As principais atualizações nesta versão são correções de confiabilidade e estabilização.  Alguns dos problemas críticos que abordamos incluem:

### <a name="new-features"></a>Novos recursos
- Agora há suporte para macOS High Sierra

### <a name="bug-fixes"></a>Correções de bug
#### <a name="workbench-experience"></a>Experiência de Workbench
- Arrastar e soltar um arquivo no Workbench faz o Workbench falhar.
- A janela do terminal no código VS configurado como um IDE para o Workbench não reconhece comandos _az ml_.

#### <a name="workbench-authentication"></a>Autenticação do Workbench
Fizemos várias atualizações para melhorar vários problemas de logon e autenticação relatados.
- A janela de autenticação continua aparecendo, especialmente quando a conexão de Internet não é estável.
- Melhoramos problemas de confiabilidade relativos à expiração do token de autenticação.
- Em alguns casos, a janela de autenticação aparece duas vezes.
- A janela principal do Workbench ainda exibe a mensagem "autenticando" quando o processo de autenticação, na verdade, já foi concluído e a caixa de diálogo pop-up já foi descartada.
- Se não houver conexão com a Internet, a caixa de diálogo de autenticação surgirá com uma tela em branco.

#### <a name="data-preparation"></a>Preparação dos dados 
- Quando um valor específico é filtrado, erros e valores ausentes também são filtrados.
- Alterar uma estratégia de amostragem remove as operações de junção existentes subsequentes.
- Substituir a transformação de Valor Ausente não leva NaN em consideração.
- Inferência de tipos de data gera exceção quando o valor nulo é encontrado.

#### <a name="job-execution"></a>Execução do trabalho
- Não há mensagem de erro clara quando a execução do trabalho falha em carregar a pasta do projeto porque ela excedeu o limite de tamanho.
- Se o script Python do usuário alterar o diretório de trabalho, os arquivos gravados nas pastas de saída não serão rastreados. 
- Se a assinatura ativa do Azure for diferente daquela à qual o projeto atual pertence, o envio do trabalho resultará em um erro 403.
- Quando o Docker não está presente, nenhuma mensagem de erro clara será retornada se o usuário tentar usar o Docker como um destino de execução.
- O arquivo .runconfig não é salvo automaticamente quando o usuário clica no botão _Executar_.

#### <a name="jupyter-notebook"></a>Bloco de anotações do Jupyter
- O servidor de notebook não conseguirá iniciar se o usuário o utilizar com certos tipos de logon.
- Mensagens de erro de servidor de notebook não ocorrem em logs visíveis ao usuário.

#### <a name="azure-portal"></a>Portal do Azure
- Selecionar o tema escuro do portal do Azure faz com que a folha de Gerenciamento de Modelos seja exibida como uma caixa preta.

#### <a name="operationalization"></a>Operacionalização
- Reutilizar um manifesto para atualizar um serviço Web faz com que uma nova imagem do Docker seja criada com um nome aleatório.
- Não possível recuperar logs de serviço Web do cluster Kubernetes.
- Mensagem de erro enganosas são impressas quando o usuário tenta criar uma conta de Gerenciamento de Modelos ou uma conta de Computação ML e encontra problemas de permissões.

## <a name="next-steps"></a>Próximas etapas

Leia a visão geral do [Azure Machine Learning](../service/overview-what-is-azure-ml.md).