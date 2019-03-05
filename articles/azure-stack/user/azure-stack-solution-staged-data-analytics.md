---
title: Criar uma solução de análise de dados preparados com o Azure e o Azure Stack | Microsoft Docs
description: Saiba como criar uma solução de análise de dados preparados com o Azure e o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 0261dff10e553ecbd39d394eb19e7ad3dd76f0a2
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340959"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>Tutorial: Criar uma solução de análise de dados preparados com o Azure e o Azure Stack 

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como usar fontes locais e ambientes de nuvem pública para atender às demandas das instalações de várias empresas. O Azure Stack oferece uma solução rápida, segura e flexível para coletar, processar, armazenar e distribuir dados locais e remotos, especialmente quando a segurança, confidencialidade, políticas corporativas e requisitos normativos podem diferir entre os locais de e usuários.

Nesse padrão, os clientes estão coletando dados que requer análise no ponto de coleta para que decisões rápidas podem ser feitas. Geralmente, essa coleta de dados ocorre sem acesso à Internet. Quando a conectividade for estabelecida, você precisa fazer uma análise de uso intensivo de recursos dos dados para obter informações adicionais. Você ainda pode analisar os dados quando uma nuvem pública está muito atrasado ou indisponível.

Neste tutorial, crie um ambiente de exemplo para:

> [!div class="checklist"]
> - Crie o blob de armazenamento de dados brutos.
> - Crie uma nova função de pilha do Azure para mover limpar dados do Azure Stack para Azure.
> - Crie uma função disparada pelo armazenamento de Blob.
> - Crie uma conta de armazenamento do Azure Stack que contém um blob e fila.
> - Crie uma função disparada por fila.
> - Função disparada por fila de teste.

> [!Tip]  
> ![pillars.png híbrido](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> O Microsoft Azure Stack é uma extensão do Azure. O Azure Stack traz a agilidade e inovação da computação em nuvem ao seu ambiente local e habilitando a única nuvem híbrida que permite que você crie e implante aplicativos híbridos de qualquer lugar.  
> 
> O white paper [considerações sobre Design para aplicativos híbridos](https://aka.ms/hybrid-cloud-applications-pillars) analisa os pilares da qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para projetar, implantar e operar aplicativos híbridos. As considerações de design ajudar a otimizar o design do aplicativo híbrido, minimizando desafios em ambientes de produção.

## <a name="prerequisites"></a>Pré-requisitos

Algumas preparações é necessário para criar essa solução:

-   Uma pilha do Azure instalado e funcionando (mais informações podem ser encontradas aqui: [Visão geral do Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-storage-overview))

-   Uma assinatura do Azure. (Criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   Baixe e instale o [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/).

-   Você precisará fornecer seus próprios dados a ser processado pelas funções. Dados devem ser gerados e disponível para carregar no contêiner de blob de armazenamento do Azure Stack.

## <a name="issues-and-considerations"></a>Problemas e considerações

### <a name="scalability-considerations"></a>Considerações sobre escalabilidade

O Azure functions e soluções de armazenamento de dimensionamento para atender ao volume de dados e as demandas de processamento. Para obter informações de escalabilidade do Azure e destinos, consulte [documentação do Azure escalabilidade](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

### <a name="availability-considerations"></a>Considerações sobre disponibilidade

O armazenamento é a consideração de disponibilidade primário para esse padrão. Conexão por meio de links rápidos é necessária para a distribuição e o processamento de volume de dados grandes.

### <a name="manageability-considerations"></a>Considerações sobre capacidade de gerenciamento

Considere como suas ferramentas de desenvolvimento e controle de origem permitirá que você gerencie sua solução.

## <a name="create-the-raw-data-storage-blob"></a>Criar o blob de armazenamento de dados brutos

O contêiner de blob e a conta de armazenamento manterá todos os dados originais gerados por atividades locais, incluindo a atividade de máquina e funcionários, dados de recurso, as métricas de produção e outros relatórios.

1.  Entrar para o [ *portal do Azure Stack*](https://portal.local.azurestack.external/).

2.  No portal do Azure Stack, expanda o menu à esquerda para abrir o menu de serviços e escolha **todos os serviços**. Role para baixo até **armazenamento** e escolha **contas de armazenamento**. Na janela de contas de armazenamento, escolha **adicionar**.

3.  Use as seguintes informações para a conta:

     a.  Nome: **Sua escolha**

    b.  Modelo de implantação: **Resource Manager**

    c.  Tipo de conta: **Armazenamento (uso geral V1)**

    d.  Localização: **Oeste dos EUA**

    e.  Replicação: **Armazenamento com redundância local (LRS)**

    f.  Desempenho: **Standard**

    g.  Transferência segura necessária: **Desabilitado**

    h.  Assinatura: Escolher uma

    i.  Grupo de recursos: Especifique um novo grupo de recursos ou selecione um grupo de recursos

    j.  Configure redes virtuais: **Desabilitado**

4.  Selecione **criar para criar a conta de armazenamento**.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  Depois de criado, selecione o nome da conta de armazenamento.

6.  Na folha da conta, sob o **serviço BLOB** título, selecione **contêineres**.

7.  Na parte superior da folha, selecione **+ contêiner.** e selecione **contêiner**.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  Nome: **Sua escolha**

9.  Nível de acesso público: **Contêiner** (acesso de leitura anônimo para contêineres e blobs)

10.  Selecione **OK**.

## <a name="create-an-azure-stack-function"></a>Criar uma função do Azure Stack

Crie uma nova função do Azure Stack para mover limpar dados do Azure Stack para Azure.

### <a name="create-the-azure-stack-function-app"></a>Criar o aplicativo de funções do Azure Stack

1. Entrar a [portal do Azure Stack](https://portal.local.azurestack.external).
2. Selecione **Todos os serviços**.
3. Selecione **aplicativos de funções** na **Web + móvel** grupo.

4.  Crie o aplicativo de funções usando as configurações especificadas na tabela abaixo da imagem.

    | Configuração | Valor sugerido | DESCRIÇÃO |
    | ---- | ---- | ---- |
    | Nome do aplicativo | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Caracteres válidos são `a` - `z`, `0``-9`, e `-`. |
    | Assinatura | Sua assinatura | A assinatura na qual este novo aplicativo de funções será criado. |
    | **Grupo de recursos** |  |  |
    | myResourceGroup | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. |  |
    | SO |  Windows | A hospedagem sem servidor no momento só está disponível quando executada no Windows. |
    | **Plano de hospedagem** |  |  |
    | Plano de consumo | Plano de hospedagem que define como os recursos são alocados para seu aplicativo de funções. O padrão de plano de consumo, os recursos são adicionados dinamicamente conforme exigido por suas funções. Essa hospedagem sem servidor, você só paga por na execução de suas funções. |  |
    | Local padrão | Região mais próximo de você | Escolha uma região perto de você ou perto de outros serviços do access de funções. |
    | **Conta de armazenamento** |  |  |
    | \<conta de armazenamento criada acima > | Nome da nova conta de armazenamento usada pelo seu aplicativo de funções. Nomes de conta de armazenamento devem estar entre 3 e 24 caracteres de comprimento. O nome só pode usar números e letras minúsculas. Você também pode usar uma conta existente. |  |

    **Exemplo:**

    ![Definir novas configurações do aplicativo de funções](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  Selecione **Criar** para provisionar e implantar o aplicativo de funções.

6.  Selecione o ícone da Notificação no canto superior direito do portal e veja se a mensagem **Implantação concluída com êxito** é exibida.

    ![Definir novas configurações do aplicativo de funções](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  Selecione **ir para o recurso** para exibir o novo aplicativo de funções.

![Aplicativo de funções criado com êxito.](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Adicionar uma função ao aplicativo de funções do Azure Stack

1.  Crie uma nova função clicando em **funções**, em seguida, a **+ nova função** botão.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  Selecione **gatilho de temporizador**.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  Selecione **C\#**  como o idioma e o nome de função: `upload-to-azure`  Defina o agendamento `0 0 * * * *`, que em CRON notação é de uma vez de uma hora.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Criar uma função disparada pelo Armazenamento de Blobs

1.  Expanda o aplicativo de funções e selecione o **+** lado **funções**.

2.  No campo de pesquisa, digite `blob` e, em seguida, escolha a linguagem desejada para o **gatilho de Blob** modelo.

  ![Escolha o modelo de gatilho de armazenamento de Blob.](media/azure-stack-solution-staged-data-analytics/image10.png)

3.  Use as configurações conforme especificado na tabela a seguir:

    | Configuração | Valor sugerido | DESCRIÇÃO |
    | ------- | ------- | ------- |
    | NOME | Exclusivo no aplicativo de funções | O nome dessa função disparada pelo blob. |
    | Caminho | \<caminho do local de armazenamento acima > | Local no Armazenamento de Blobs que está sendo monitorada. O nome do arquivo do blob é passado na associação como o parâmetro name. |
    | Conexão da conta de armazenamento | Conexão do aplicativo de função | Você pode usar a conexão da conta de armazenamento já está sendo usado por seu aplicativo de funções ou criar um novo. |

    **Exemplo:**

    ![Crie a função disparada pelo Armazenamento de Blobs.](media/azure-stack-solution-staged-data-analytics/image11.png)

4.  Selecione **Criar** para criar a função.

### <a name="test-the-function"></a>Testar a função

1.  No portal do Azure, vá para a função. Expanda o **Logs** na parte inferior da página e verifique se o streaming de log não está em pausa.

2.  Abra o Gerenciador de armazenamento e conecte-se à conta de armazenamento criada no início desta seção.

3.  Expanda a conta de armazenamento **contêineres de Blob**, e o blob criado anteriormente. Selecione **carregue** e, em seguida **carregar arquivos**.

    ![Carregue um arquivo para o contêiner de blob.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  Na caixa de diálogo de Upload de arquivos, selecione o campo de arquivos. Navegue até um arquivo em um computador local, como um arquivo de imagem, selecione-o e selecione **aberto** e, em seguida **carregar**.

5.  Volte para os logs de função e verifique se que o blob foi lido.

    **Exemplo:**

    ![Exiba a mensagem nos logs.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Criar uma conta de armazenamento do Azure Stack

Crie uma conta de armazenamento do Azure Stack que contém um blob e fila.

### <a name="storage-blob--data-archiving"></a>Arquivamento de dados de Blob de armazenamento

Essa conta de armazenamento conterá dois contêineres. Esses contêineres são usada para armazenar dados de arquivamento de um blob e fila usado para o processamento de dados atribuídos para distribuição de escritório principal.

Use as etapas e as configurações descritas acima para criar outro contêiner de blob e a conta de armazenamento como o armazenamento de arquivos.

### <a name="storage-queue--filtered-data-holding"></a>Retenção de dados filtrados da fila de armazenamento

1.  Use as etapas e as configurações descritas acima para acessar a nova conta de armazenamento.

2.  Na seção Visão geral da conta de armazenamento, selecione **fila.**

3.  Selecione o **+ fila** e preencha **nome** campo e preencha um nome para a nova fila.

4.  Selecione **Okey.**

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>Criar uma função disparada por filas

1.  Use as etapas na seção de criação de função acima para criar uma função do Azure Stack adicional com um gatilho de fila em vez de um gatilho de blob.

2.  Use as configurações conforme especificado na tabela a seguir:

    | Configuração | Valor sugerido | DESCRIÇÃO |
    | ------- | ------- | ------- |
    | NOME | Exclusivo no aplicativo de funções | O nome dessa função disparada por filas. |
    | Caminho | \<caminho do local de armazenamento acima > | Local no armazenamento que está sendo monitorado. O nome do arquivo da fila é passado na associação como o parâmetro name. |
    | Conexão da conta de armazenamento | Conexão do aplicativo de função | Você pode usar a conexão da conta de armazenamento já está sendo usado por seu aplicativo de funções ou criar um novo. |

3.  Selecione **Criar** para criar a função.

## <a name="test-the-queue-triggered-function"></a>Função disparada por fila de teste

1.  No portal do Azure Stack, vá para a função. Expanda o **Logs** na parte inferior da página e verifique se o streaming de log não está em pausa.

2.  Abra o Gerenciador de armazenamento e conecte-se à conta de armazenamento criada no início desta seção.

3.  Expanda a conta de armazenamento **contêineres de Blob**, e o blob criado anteriormente. Selecione **carregue** e, em seguida, **carregar arquivos.**

    ![Carregue um arquivo para o contêiner de blob.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  Na caixa de diálogo de Upload de arquivos, selecione o campo de arquivos. Navegue até um arquivo em um computador local, como um arquivo de imagem, selecione-o e selecione **aberto** e, em seguida **carregar**.

5.  Volte para os logs de função e verifique se que o blob foi lido.

  **Exemplo:**

    ![Exiba a mensagem nos logs.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>Armazenados e acessados com segurança dados em conformidade

Dados da empresa global com segurança é armazenados, processados, distribuídos e acessados por meio do Azure e a análise de dados em etapas do Azure Stack e diretivas de ponto de extremidade personalizado. Atividades de funcionários e maquinaria de escritório remoto, os dados de recurso e métricas de negócios são continuamente compiladas, armazenadas, testadas quanto à conformidade e distribuídas de acordo com a política da empresa e regulamento regional.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).