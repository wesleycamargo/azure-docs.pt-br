---
Título: Importar dados de arquivo – titleSuffix: Descrição do Azure Machine Learning Studio: Saiba como carregar um arquivo de dados de treinamento do disco rígido para o Azure Machine Learning Studio. Isso cria um módulo de conjunto de dados no workspace.
serviços: machine-learning ms.service: machine-learning ms.component: studio ms.topic: artigo

autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 20/03/2017
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-azure-machine-learning-studio"></a>Importar dados de treinamento de um arquivo no disco rígido para o Azure Machine Learning Studio

Saiba como carregar um arquivo de dados do disco rígido a fim de usar como dados de treinamento no Azure Machine Learning Studio. Ao importar o arquivo de dados, você terá um módulo de conjunto de dados pronto para uso em seu workspace.

## <a name="steps-to-import-data-from-a-local-file"></a>Etapas para importar dados de um arquivo local
Para importar dados de um disco rígido local, faça o seguinte:

1. clique em **+NOVO** na parte inferior da janela do Machine Learning Studio.
2. Selecione **CONJUNTO DE DADOS** e **DO ARQUIVO LOCAL**.
3. Na caixa de diálogo **Carregar um novo conjunto de dados** , navegue até o arquivo que deseja carregar
4. Digite um nome, identifique o tipo de dados e, opcionalmente, insira uma descrição. Uma descrição é recomendada – ela permite registrar características sobre os dados que você deseja lembrar ao usar os dados no futuro.
5. A caixa de seleção **Esta é a nova versão de um conjunto de dados existente** permite que você atualize um conjunto de dados existente com novos dados. Clique nesta caixa de seleção e digite o nome de um conjunto de dados existente.

![Carregar um novo conjunto de dados](./media/import-data-from-local-file/upload-dataset.png)

Durante o upload, você verá uma mensagem informando que seu arquivo está sendo carregado. O tempo de upload depende do tamanho dos dados e da velocidade da conexão com o serviço. Se souber que o arquivo levará muito tempo, você pode fazer outras coisas dentro do Machine Learning Studio enquanto espera. No entanto, fechar o navegador fará com que o upload de dados falhe.

## <a name="dataset-module-is-ready-for-use"></a>O módulo do conjunto de dados está pronto para uso
Uma vez carregados, seus dados são armazenados em um módulo de conjunto de dados e ficam disponíveis para qualquer experimento em seu workspace.

Quando estiver editando um experimento, você pode encontrar os conjuntos de dados criados na lista **Meus Conjuntos de Dados**, na lista **Conjuntos de Dados Salvos** na paleta de módulos. É possível arrastar e soltar o conjunto de dados na tela de teste quando você desejar usá-lo para outras análises e aprendizado de máquina.
