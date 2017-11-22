---
title: "Notas de versão do Azure ML Workbench para o sprint 1 de novembro de 2017"
description: "Este documento fornece detalhes das atualizações para a versão de sprint 1 do Azure ML"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: 2b2f35b3241bd1700bb1fb3319d38fdfda2545f9
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2017
---
# <a name="sprint-1---november-2017"></a>Sprint 1 – novembro de 2017 

**Número de versão: 0.1.1710.31013**

Bem-vindo à segunda atualização do Azure Machine Learning Workbench. Continuamos fazendo melhorias em segurança, estabilidade e facilidade de manutenção no aplicativo Workbench, na CLI e na camada de serviços back-end. Agradecemos imensamente por nos enviar sorrisos e rostos tristes. Muitas das atualizações abaixo foram feitas como resultado direto de seus comentários. Continue enviando-os!

## <a name="notable-new-features"></a>Novos recursos importantes
- O Azure ML agora está disponível em duas novas regiões do Azure: **Europa Ocidental** e **Sudeste Asiático**. Elas se juntam às regiões anteriores **Leste dos EUA 2**, **Centro-oeste dos EUA** e **Leste da Austrália**, elevando o número total de regiões implantadas para cinco.
- Habilitamos o realce de sintaxe do código Python no aplicativo Workbench para facilitar a leitura e edição do código-fonte Python. 
- Agora você pode iniciar seu IDE favorito diretamente por meio de um arquivo, em vez de todo o projeto.  Abrir um arquivo no Workbench e, em seguida, clicar em “Editar” inicia o IDE (atualmente, há suporte para o VS Code e PyCharm) no arquivo e projeto atuais.  Também clique na seta ao lado do botão Editar para editar o arquivo no editor de texto do Workbench.  Os arquivos são somente leitura até você clicar em Editar, prevenindo alterações acidentais.
- A popular biblioteca de plotagem `matplotlib` versão 2.1.0 agora é enviada com o aplicativo Workbench.
- Atualizamos a versão do .NET Core para 2.0 do mecanismo de preparação de dados. Isso removeu o requisito de instalação do openssl pelo Brew durante a instalação do aplicativo no macOS. Isso também abre caminho para os recursos de preparação de dados mais interessantes que estão chegando em um futuro próximo. 
- Habilitamos uma home page do aplicativo específica à versão, de modo que você obtenha as notas de versão mais relevantes e os prompts de atualização com base na versão atual do aplicativo.
- Caso seu nome de usuário local tenha um espaço, o aplicativo agora poderá ser instalado com êxito. 

## <a name="detailed-updates"></a>Atualizações detalhadas
Veja abaixo uma lista de atualizações detalhadas em cada área de componente do Azure Machine Learning deste sprint.

### <a name="installer"></a>Instalador
- O instalador de aplicativo agora limpa o diretório de instalação criado por uma versão mais antiga do aplicativo.
- Correção de um bug que leva o instalador a travar em 100% no macOS High Sierra.
- Agora há um link direto para o diretório do instalador para que o usuário examine os logs do instalador em caso de falha de instalação.
- A instalação agora funciona para usuários que têm espaço em seus nomes de usuário.

### <a name="workbench-authentication"></a>Autenticação do Workbench
- Suporte para autenticação no Gerenciador de Proxy.
- O logon agora terá êxito se o usuário estiver protegido por um firewall. 
- Se o usuário tiver contas de experimentação em várias regiões do Azure e se uma região não estiver disponível, o aplicativo deixará de parar de responder.
- Quando a autenticação não é concluída e a caixa de diálogo de autenticação ainda está visível, o aplicativo não tenta mais carregar o espaço de trabalho por meio do cache local.

### <a name="workbench-app"></a>Aplicativo Workbench
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

### <a name="data-preparation"></a>Preparação dos dados 
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

### <a name="job-execution"></a>Execução do trabalho
- O argumento agora está incluído no registro do histórico de execuções.
- Os trabalhos iniciados na CLI agora são mostrados no painel do Trabalho do Histórico de execuções automaticamente.
- O painel do trabalho agora mostra os trabalhos criados por usuários convidados adicionados ao locatário do AAD.
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

### <a name="jupyter-notebook"></a>Bloco de anotações do Jupyter
- A pesquisa de nome de bloco de anotações agora funciona corretamente na exibição Blocos de Anotações.
- Agora você pode excluir um Bloco de Anotações da exibição Blocos de Anotações.
- Uma nova mágica `%upload_artifact` foi adicionada para carregar arquivos produzidos no ambiente de execução do Notebook no armazenamento de dados do histórico de execuções.
- Os erros de kernel agora são apresentados no status do trabalho do Notebook para uma depuração mais fácil.
- O servidor Jupyter agora é desligado corretamente quando o usuário faz logoff do aplicativo.

### <a name="azure-portal"></a>Portal do Azure
- A conta de Experimentação e a conta de Gerenciamento de Modelos agora podem ser criadas em duas novas regiões do Azure: Europa Ocidental e Sudeste Asiático.
- O plano DevTest da conta do Gerenciamento de Modelos agora está disponível apenas quando ele é o primeiro a ser criado na assinatura. 
- O link de ajuda no portal do Azure foi atualizado para apontar para a página de documentação correta.
- O campo de descrição foi removido da página de detalhes da imagem do Docker, pois não é aplicável.
- Detalhes, incluindo configurações do AppInsights e de escala automática, foram adicionados à página de detalhes do serviço Web.
- A página do gerenciamento de modelos agora é renderizada mesmo se cookies de terceiros estão desabilitados no navegador. 

### <a name="operationalization"></a>Operacionalização
- Um serviço Web com “pontuação” em seu nome não falha mais.
- Agora o usuário pode criar um ambiente de implantação com apenas o acesso de Proprietário a um grupo de recursos do Azure. O acesso de Proprietário à assinatura inteira não é mais necessário.
- A CLI de operacionalização agora aproveita o preenchimento automático de guias no Linux.
- O serviço de construção de imagens agora dá suporte à criação de imagens para serviços/dispositivos IoT do Azure.

### <a name="sample-projects"></a>Projetos de exemplo
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
