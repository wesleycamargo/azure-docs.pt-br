---
title: Teste de código de ciência de dados no Azure com o conjunto de dados de previsão de receita de adulto UCI – Processo de Ciência de Dados de Equipe e o Azure DevOps Services
description: Teste de código de ciência de dados com dados de previsão de receita de adulto UCI
services: machine-learning, team-data-science-process
documentationcenter: ''
author: weig
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2018
ms.author: weig
ms.openlocfilehash: ad0a8b5b0bb9afbbe626c9481961f20ccd4797bf
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294682"
---
# <a name="data-science-code-testing-with-the-uci-adult-income-prediction-dataset"></a>Teste de código de ciência de dados com o conjunto de dados de previsão de receita de adulto UCI
Este artigo oferece diretrizes preliminares para testar o código em um fluxo de trabalho de ciência de dados. Esse teste fornece aos cientistas de dados uma maneira sistemática e eficiente de verificar a qualidade e o resultado esperado de seu código. Usamos um projeto [TDSP (Processo de Ciência de Dados de Equipe) que usa o conjunto de dados Renda de Adulto UCI](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome), o qual publicamos anteriormente para mostrar como o teste de código pode ser feito. 

## <a name="introduction-on-code-testing"></a>Introdução ao teste de código
"Teste de unidade" é uma prática permanente no desenvolvimento de software. Mas, para ciência de dados, normalmente não fica claro o que significa e como você deve testar o código para estágios diferentes de um ciclo de vida de ciência de dados, como:

* Preparação dos dados
* Análise da qualidade dos dados
* Modelagem
* Implantação de modelo 

Este artigo substitui o termo "teste de unidade" por "teste do código". Ele se refere a teste como as funções que ajudam a avaliar se o código de uma determinada etapa de um ciclo de vida de ciência de dados está produzindo resultados "esperados". A pessoa que está escrevendo o teste define o que é o "esperado", dependendo do resultado da função, por exemplo, verificação de qualidade de dados ou modelagem.

Este artigo fornece referências como recursos úteis.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps para a estrutura de testes
Este artigo descreve como realizar e automatizar os testes usando o Azure DevOps. Você pode decidir usar outras ferramentas. Também mostramos como configurar um build automático usando o Azure DevOps e os agentes de build. Para agentes de build, usamos as DSVMs (Máquinas Virtuais da Ciência de Dados do Azure).

## <a name="flow-of-code-testing"></a>Fluxo do teste de código
O fluxo de trabalho geral do teste de código em um projeto de ciência de dados tem esta aparência: 

![Fluxograma do teste de código](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Etapas detalhadas

Use estas etapas para configurar e executar o teste de código e um build automatizado usando um agente de build e o Azure DevOps:

1. Crie um projeto no aplicativo de área de trabalho do Visual Studio:

    ![Tela "Criar um novo projeto" no Visual Studio](./media/code-test/create_project.PNG)

   Depois de criar seu projeto, você o encontrará no Gerenciador de Soluções no painel à direita:
    
    ![Etapas para a criação de um projeto](./media/code-test/create_python_project_in_vs.PNG)

    ![Gerenciador de soluções](./media/code-test/solution_explorer_in_vs.PNG)

1. Insira o código do seu projeto no repositório de códigos do projeto do Azure DevOps: 

    ![Repositório do código do projeto](./media/code-test/create_repo.PNG)

1. Vamos supor que você preparou os dados, por exemplo, fez a ingestão de dados, engenharia de recursos e criou colunas de rótulo. Você quer se certificar de que seu código está gerando os resultados que você espera. Este é o código que você pode usar para testar se o código de processamento de dados está funcionando corretamente:

    * Verifique se os nomes de coluna estão corretos:
    
      ![Código para correspondência de nomes de coluna](./media/code-test/check_column_names.PNG)

    * Verifique se os níveis de resposta estão certos:

      ![Código para correspondência de níveis](./media/code-test/check_response_levels.PNG)

    * Verifique se a porcentagem de resposta é razoável:

      ![Código para o percentual de resposta](./media/code-test/check_response_percentage.PNG)

    * Verifique a taxa de ausência de cada coluna nos dados:
    
      ![Código para taxa de ausência](./media/code-test/check_missing_rate.PNG)


1. Após o processamento dos dados e a engenharia dos recursos, e quando tiver treinado um bom modelo, certifique-se de que o modelo treinado possa conseguir novos conjuntos de dados corretamente. Você pode usar os dois testes a seguir para verificar os níveis de previsão e a distribuição de valores de rótulo:

    * Verificar níveis de previsão:
    
      ![Código para verificação dos níveis de previsão](./media/code-test/check_prediction_levels.PNG)

    * Verifique a distribuição dos valores de previsão:

      ![Código para verificação dos valores de previsão](./media/code-test/check_prediction_values.PNG)

1. Coloque todas as funções de teste em um script Python chamado **test_funcs.py**:

    ![Script de Python para funções de teste](./media/code-test/create_file_test_func.PNG)


1. Após a preparação dos códigos de teste, você pode configurar o ambiente de teste no Visual Studio.

   Crie um arquivo de Python chamado **test1.py**. Nesse arquivo, crie uma classe que inclui todos os testes que você deseja realizar. O exemplo a seguir mostra seis testes preparados:
    
    ![Arquivo Python com uma lista de testes em uma classe](./media/code-test/create_file_test1_class.PNG)

1. Esses testes podem ser descobertos automaticamente se você colocar **codetest.testCase** após o nome da classe. Abra o Gerenciador de Testes no painel direito e selecione **Executar Tudo**. Todos os testes serão executados sequencialmente e você receberá uma notificação se o teste foi bem-sucedido.

    ![Execução dos testes](./media/code-test/run_tests.PNG)

1. Faça o check-in de seu código no repositório do projeto usando os comandos do Git. Seu trabalho mais recente será refletido em breve no Azure DevOps.

    ![Comandos do Git para fazer check-in do código](./media/code-test/git_check_in.PNG)

    ![Trabalho mais recente no Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Configurar build e teste automáticos no Azure DevOps:

    a. No repositório do projeto, selecione **Build e Versão** e, em seguida, selecione **+Novo** para criar um novo processo de build.

       ![Selections for starting a new build process](./media/code-test/create_new_build.PNG)

    b. Siga os prompts para selecionar o local do código-fonte, nome do projeto, repositório e informações de branch.
    
       ![Source, name, repository, and branch information](./media/code-test/fill_in_build_info.PNG)

    c. Selecione um modelo. Como não há um modelo de projeto do Python, comece selecionando **Processo vazio**. 

       ![List of templates and "Empty process" button](./media/code-test/start_empty_process_template.PNG)

    d. Nomeie o build e selecione o agente. Escolha o padrão se quiser usar um DSVM para concluir o processo de build. Para saber mais sobre agentes de configuração, consulte [Build e agentes de versão](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts).
    
       ![Build and agent selections](./media/code-test/select_agent.PNG)

    e. Selecione **+** no painel esquerdo para adicionar uma tarefa a essa fase de build. Já que vamos executar o script em Python **test1.py** para concluir todas as verificações, esta tarefa usará um comando do PowerShell para executar o código em Python.
    
       !["Add tasks" pane with PowerShell selected](./media/code-test/add_task_powershell.PNG)

    f. Nos detalhes do PowerShell, preencha as informações necessárias, como o nome e a versão do PowerShell. Escolha **Script Embutido** como o tipo. 
    
       In the box under **Inline Script**, you can type **python test1.py**. Make sure the environment variable is set up correctly for Python. If you need a different version or kernel of Python, you can explicitly specify the path as shown in the figure: 
    
       ![PowerShell details](./media/code-test/powershell_scripts.PNG)

    g. Selecione **Salvar e enfileirar** para concluir o processo de pipeline de build.

       !["Save & queue" button](./media/code-test/save_and_queue_build_definition.PNG)

Agora, sempre que uma nova confirmação é enviada por push para o repositório de código, o processo de build iniciará automaticamente. (Usamos mestre como o repositório aqui, mas você pode definir qualquer branch). O processo executa o arquivo **test1.py** no computador do agente para certificar-se de que tudo o que foi definido no código seja executado corretamente. 

Se os alertas estiverem configurados corretamente, você receberá uma notificação por email após a conclusão do build. Você também pode verificar o status do build no Azure DevOps. Se ele falhar, verifique os detalhes do build e descubra qual parte está incorreta.

![Notificação por email sobre o êxito do build](./media/code-test/email_build_succeed.PNG)

![Notificação do Azure DevOps sobre o êxito do build](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Próximas etapas
* Veja o [repositório de previsão de receita UCI](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) para ver exemplos concretos de testes de unidade em cenários de ciência de dados.
* Siga a estrutura e os exemplos anteriores do cenário de previsão de receita UCI em seus próprios projetos de ciência de dados.

## <a name="references"></a>Referências
* [Processo de Ciência de Dados de Equipe](https://aka.ms/tdsp)
* [Ferramentas de Teste do Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Recursos de teste do Azure DevOps](https://www.visualstudio.com/team-services/)
* [Máquinas Virtuais de Ciência de Dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)