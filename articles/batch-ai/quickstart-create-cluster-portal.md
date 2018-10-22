---
title: Início Rápido do Azure – criar cluster da IA do Lote – Portal | Microsoft Docs
description: Início Rápido – criar um cluster da IA do Lote para treinar modelos de aprendizado de máquina e de IA – portal do Azure
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 8b9daa0fbbf84e0f602498a0847c9e120f709b17
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057403"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>Início Rápido: criar um cluster para trabalhos de treinamento da IA do Lote usando o portal do Azure

Este início rápido mostra como usar o portal do Azure para criar um cluster da IA do Lote que você pode usar para treinar modelos de IA e de aprendizado de máquina. A IA do Lote é um serviço gerenciado para que cientistas de dados e pesquisadores de IA possam treinar modelos de IA e de aprendizado de máquina em larga escala em clusters de máquinas virtuais do Azure.

Inicialmente, o cluster tem um único nó de GPU e um servidor de arquivos anexado. Depois de concluir este início rápido, você terá um cluster que poderá ser escalado verticalmente e usado para treinar modelos de aprendizado profundo. Envie trabalhos de treinamento para o cluster usando a IA do Lote, as ferramentas do [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) ou as [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Você precisa de um par de chaves SSH para concluir este início rápido. Se você já tiver um par de chave SSH, essa etapa pode ser ignorada.

Para criar um par de chaves SSH, execute o comando a seguir em um shell Bash e siga as instruções na tela. Por exemplo, você pode usar o [Azure Cloud Shell](../cloud-shell/overview.md) ou, no Windows, o [Subsistema do Windows para Linux](/windows/wsl/install-win10). A saída do comando inclui o nome do arquivo da chave pública. Copie o conteúdo do arquivo de chave pública (`cat ~/.ssh/id_rsa.pub`) para a área de transferência ou para outro local que você possa acessar em uma etapa posterior.

```bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações sobre como criar pares de chaves SSH, consulte [Criar e usar um par de chaves SSH públicas-privadas para VMs Linux no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-batch-ai-workspace"></a>Criar um espaço de trabalho da IA do Lote

Comece criando um espaço de trabalho da IA do Lote para organizar os recursos da IA do Lote. Um espaço de trabalho pode conter um ou mais clusters ou outros recursos da IA do Lote.

1. Selecione **Todos os serviços** e filtre pela **IA do Lote**.

2. Selecione **Adicionar Espaço de Trabalho**.

3. Insira valores de **Nome do espaço de trabalho** e **Grupo de recursos**. Se desejar, selecione opções diferentes de **Assinatura** e **Local** no espaço de trabalho. Selecione **Criar Espaço de Trabalho**.

  ![Criar um workspace da IA do Lote](./media/quickstart-create-cluster-portal/create-workspace.png)

Quando a mensagem **Implantação bem-sucedida** for exibida, acesse o recurso criado e selecione o espaço de trabalho.

## <a name="create-a-file-server"></a>Criar um servidor de arquivos

Um servidor de arquivos da IA do Lote é um NFS de nó único que pode ser montado automaticamente em nós de cluster. É uma das várias maneiras de fornecer armazenamento para dados de entrada e saída de seus trabalhos de treinamento.

1. No espaço de trabalho, selecione **Servidor de arquivos** > **Adicionar servidor de arquivos da IA do Lote**.

2. Insira valores de **Nome do servidor de arquivos** e **Tamanho da VM**. Para este início rápido, o tamanho de VM *Standard D1_v2* é sugerido para o servidor de arquivos. Escolha um tamanho diferente se você precisa armazenar grandes quantidades de dados de entrada ou saída para trabalhos de treinamento.

3. Insira um **Nome de usuário de administrador** e copie o conteúdo do arquivo de chave pública SSH em **Chave SSH**. Aceite os padrões para os demais valores e selecione **Criar servidor de arquivos**.

  ![Criar servidor de arquivos da IA do Lote](./media/quickstart-create-cluster-portal/create-file-server.png)

O servidor de arquivos leva alguns minutos para ser implantado.

Depois que o servidor é criado, clique em **Propriedades** e anote as **Configurações de montagem**. Você pode usar SSH no endereço IP público do servidor para fazer o upload e o download de dados de treinamento e arquivos de saída diretório indicado (*/data*).

![Propriedades do servidor de arquivos](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>Criar um cluster

As etapas a seguir criam um cluster com um único nó de GPU. O nó do cluster executa uma imagem padrão do Ubuntu Server, criada para hospedar aplicativos baseados em contêiner e que pode ser usada na maioria das cargas de trabalho de treinamento. O nó de cluster monta o servidor de arquivos em seu ponto de montagem. 

1. No seu espaço de trabalho da IA do Lote, selecione **Cluster** > **Adicionar cluster da IA do Lote**.

2. Insira valores de **Nome do cluster** e as configurações a seguir. O tamanho de VM sugerido tem uma GPU NVIDIA Tesla K80.
  
   |Configuração  |Valor  |
   |---------|---------|
   |**Tamanho da VM**     |Standard NC6|
   |**Número de nós de destino**     |1|

3. Insira um **Nome de usuário de administrador** e copie o conteúdo do arquivo de chave pública SSH em **Chave SSH**. Aceite os padrões para os valores restantes nesta página e selecione **Avançar: Instalação do nó**.

   ![Inserir informações de cluster básicas](./media/quickstart-create-cluster-portal/create-cluster.png)

4. Em **Volumes de Montagem**, selecione **Referências do servidor de arquivos** > **Adicionar**. Selecione o servidor de arquivos que você criou anteriormente. Insira um **Caminho de montagem relativo**, em que o servidor de arquivos é montado em cada nó de cluster. Selecione **Salvar e continuar**.

   ![Adicionar referência do servidor de arquivos](./media/quickstart-create-cluster-portal/file-server-reference.png)

Salve a configuração do nó e selecione **Criar Cluster**.

A IA do Lote leva alguns minutos para alocar o nó. Durante esse tempo, o **Estado de alocação** do cluster é **Resizing**. Depois de alguns minutos, o estado do cluster é **Steady** e o nó é iniciado.

![Inicialização do cluster](./media/quickstart-create-cluster-portal/cluster-resizing.png)

Selecione o nome do cluster para verificar o estado do nó. Quando o estado do nó é **Idle**, ele está pronto para executar trabalhos de treinamento.

### <a name="list-cluster-nodes"></a>Listar nós de cluster

Se você precisa se conectar aos nós do cluster (nesse caso, um único nó) para instalar aplicativos ou realizar a manutenção, obtenha as informações de conexão no portal. Depois que o cluster for criado, clique em **Nós** e anote as configurações da **Conexão** SSH (número de porta e endereço IP).

![Nós de cluster](./media/quickstart-create-cluster-portal/cluster-nodes.png)

Use essas informações a fim de criar uma conexão SSH para o nó. Por exemplo, insira o endereço IP e o número da porta corretos do seu nó no seguinte comando:

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>Redimensionar o cluster

Quando você usa o cluster para treinar um modelo, pode ser necessário usar mais recursos de computação. Por exemplo, para aumentar o tamanho para dois nós de um trabalho de treinamento distribuído, selecione **Escala** e defina o **Número de nós de destino** como 2. Salve a configuração.

![Dimensionar Cluster](./media/quickstart-create-cluster-portal/scale-cluster.png)

O redimensionamento do cluster demora alguns minutos.

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser continuar com exemplos e tutoriais da IA do Lote, use o espaço de trabalho da IA do Lote, o servidor de arquivos e o cluster criados neste início rápido.

Você será cobrado pelo servidor de arquivos e pelo cluster da IA do Lote enquanto as máquinas virtuais subjacentes estiverem em execução, mesmo se não houver trabalhos agendados. Se você quiser manter a configuração do cluster quando não tiver nenhum trabalho a ser executado, redimensione o número de nós do cluster para zero. Depois, redimensione para um ou mais nós a fim de executar os trabalhos. 

Quando não for mais necessário, exclua o espaço de trabalho da IA do Lote que contém o servidor de arquivo e o cluster. Para fazer isso, selecione o espaço de trabalho da IA do Lote e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um cluster da IA do Lote e um servidor de arquivos anexado usando o portal do Azure. Para saber mais sobre como usar um cluster da IA do Lote para treinar um modelo, prossiga para o início rápido Treinar um modelo de aprendizado profundo.

> [!div class="nextstepaction"]
> [Treinar um modelo de aprendizado profundo](./quickstart-tensorflow-training-cli.md)
