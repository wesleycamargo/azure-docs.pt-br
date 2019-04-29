---
title: Executar uma fábrica de imagem de DevOps do Azure no Azure DevTest Labs | Microsoft Docs
description: Saiba como criar uma fábrica de imagem personalizada no Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: abb85d568e26e4b6f85b960a2560aae570daf201
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61320184"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Executar uma fábrica de imagem do Azure DevOps
Este artigo aborda todas as preparações necessárias para executar a fábrica de imagem de DevOps do Azure (anteriormente conhecido como Visual Studio Team Services).

> [!NOTE]
> Qualquer mecanismo de orquestração funcionará! DevOps do Azure não é obrigatório. A fábrica de imagem é executada usando scripts do PowerShell do Azure, então ele pode ser executado manualmente, usando o Agendador de tarefas do Windows, outros sistemas de CI/CD e assim por diante.

## <a name="create-a-lab-for-the-image-factory"></a>Criar um laboratório para a fábrica de imagem
A primeira etapa na configuração de fábrica de imagem é criar um laboratório no Azure DevTest Labs. Este laboratório é o laboratório de fábrica de imagem onde criamos as máquinas virtuais e salvar imagens personalizadas. Este laboratório é considerado como parte do processo geral de fábrica de imagem. Depois de criar um laboratório, certifique-se de salvar o nome, pois você precisará dele mais tarde.

## <a name="scripts-and-templates"></a>Scripts e modelos
A próxima etapa na adoção de fábrica de imagem para sua equipe é entender o que está disponível. Os scripts de fábrica de imagem e modelos estão disponíveis publicamente na [repositório do GitHub do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Aqui está uma descrição das partes:

- Fábrica de imagem. É a pasta raiz.
    - Configuração. As entradas para a fábrica de imagem
        - GoldenImages. Esta pasta contém arquivos JSON que representam as definições de imagens personalizadas.
        - Labs.JSON. Arquivo onde as equipes se inscrever para receber imagens personalizadas específicas.
- Scripts. O mecanismo para a fábrica de imagem.

Os artigos nesta seção fornecem mais detalhes sobre esses scripts e modelos.

## <a name="create-an-azure-devops-team-project"></a>Criar um projeto de equipe de DevOps do Azure
DevOps do Azure permitem que você armazene o código-fonte, execute o PowerShell do Azure em um só lugar. Você pode agendar execuções recorrentes para manter as imagens atualizadas. Há bons recursos para registrar em log os resultados para diagnosticar problemas.  Usando o Azure DevOps não é um requisito no entanto, você pode usar qualquer estrutura/mecanismo que pode se conectar ao Azure e pode executar o PowerShell do Azure.

Se você tiver uma conta de DevOps existente ou um projeto que você deseja usar em vez disso, ignore esta etapa.

Para começar, crie uma conta gratuita no DevOps do Azure. Visite https://www.visualstudio.com/ e selecione **comece de graça** imediatamente sob **DevOps do Azure** (anteriormente conhecido como VSTS). Você precisará escolher um nome de conta exclusivo e certifique-se optar por gerenciar código usando o Git. Depois de criar, salve a URL para seu projeto de equipe. Aqui está um exemplo de URL: `https://<accountname>.visualstudio.com/MyFirstProject`.

## <a name="check-in-the-image-factory-to-git"></a>Fazer check-in a fábrica de imagem para o Git
Todos os o PowerShell, modelos e configuração para a fábrica de imagem estão localizados na [repositório público do GitHub do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). É a maneira mais rápida para obter o código em seu novo projeto de equipe importar um repositório. Isso extrairá o repositório inteiro do DevTest Labs (de modo que você obterá docs extras e exemplos).

1. Visite o projeto de DevOps do Azure que você criou na etapa anterior (URL se parece com **https:\//\<accountname >.visualstudio.com/MyFirstProject**).
2. Selecione **importar um repositório**.
3. Insira o **URL de clone** para o repositório de laboratórios de desenvolvimento/teste: `https://github.com/Azure/azure-devtestlab`.
4. Selecione **Importar**.

    ![Importar do repositório de Git](./media/set-up-devops-lab/import-git-repo.png)

Se você quiser apenas fazer check-in exatamente o que é necessário (os arquivos de fábrica de imagem), siga as etapas [aqui](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) clonar o repositório Git e enviar por push somente os arquivos localizados na **scripts/ImageFactory** directory.

## <a name="create-a-build-and-connect-to-azure"></a>Criar uma compilação e conecte-se para o Azure
Neste ponto, você tem os arquivos de origem armazenados em um repositório Git no DevOps do Azure. Agora, você precisa configurar um pipeline para executar o PowerShell do Azure. Há muitas opções para executar essas etapas. Neste artigo, você usa a definição de compilação para manter a simplicidade, mas ele funciona com a compilação de DevOps, versão de DevOps (únicos ou vários ambientes), outros mecanismos de execução como o Agendador de tarefas do Windows ou qualquer outro equipamento que pode executar o PowerShell do Azure.

> [!NOTE]
> Um ponto importante para ter em mente que alguns dos arquivos do PowerShell levar muito tempo para ser executado quando há muita (10 +) imagens personalizadas para criar. Agentes de Build/versão de DevOps hospedado gratuito tem um tempo limite de 30 minutos, para que você não pode usar o agente hospedado gratuito após começar a criar muitas imagens. Esse desafio de tempo limite se aplica a qualquer agente que você decida usar, é uma boa ideia verificar com antecedência que você pode estender os tempos limite típicos para scripts de longa execução do Azure PowerShell. No caso de DevOps do Azure, você pode usar agentes hospedados pagos ou usar seu próprio agente de compilação.

1. Para iniciar, selecione **configurar compilação** na home page do seu projeto de DevOps:

    ![Botão de compilação da configuração](./media/set-up-devops-lab/setup-build-button.png)
2. Especifique um **nome** para a compilação (por exemplo: Criar e fornecer imagens para laboratórios de desenvolvimento/teste).
3. Selecione uma **vazio** definição de compilação e, em seguida, selecione **aplicar** para criar sua compilação.
4. Nesse estágio, você pode escolher **hospedado** para o agente de compilação.
5. **Salvar** a definição de compilação.

    ![Definição da compilação](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configurar as variáveis de compilação
Para simplificar os parâmetros de linha de comando, encapsule os valores de chave que orientam a fábrica de imagem para um conjunto de variáveis de compilação. Selecione o **variáveis** guia e você verá uma lista das diversas variáveis de padrão. Aqui está a lista de variáveis para entrar no Azure DevOps:


| Nome de variável | Value | Observações |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Esse é o caminho completo no repositório para o **configuração** pasta. Se você importou o repositório inteiro acima, o valor para a esquerda está correto. Caso contrário, atualize para apontar para o local de configuração. |
| DevTestLabName | MyImageFactory | O nome do laboratório no Azure DevTest Labs usado como a fábrica para produzir imagens. Se você não tiver uma, crie um. Certifique-se de que o laboratório é na mesma assinatura que o ponto de extremidade de serviço tem acesso ao. |
| ImageRetention | 1 | O número de imagens que você deseja salvar de cada tipo. Defina o valor padrão como 1. |
| MachinePassword | ******* | A senha da conta de administrador interno para as máquinas virtuais. Esta é uma conta transitória, portanto certifique-se de que ele é seguro. Selecione o pequeno ícone de cadeado à direita para garantir que ele é uma cadeia de caracteres segura. |
| MachineUserName | ImageFactoryUser | O nome da conta de administrador interno para as máquinas virtuais. Esta é uma conta transitória. |
| StandardTimeoutMinutes | 30 | O tempo limite que deveríamos esperar para operações do Azure regulares. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | A ID da assinatura em que o laboratório existe e que o ponto de extremidade de serviço tem acesso ao. |
| VMSize | Standard_A3 | O tamanho da máquina virtual a ser usado para o **criar** etapa. As VMs criadas são transitórias. O tamanho deve ser aquele que [habilitada para o laboratório](devtest-lab-set-lab-policy.md). Confirme se há suficiente [cota de núcleos de assinatura](../azure-subscription-service-limits.md).

![As variáveis de compilação](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Conecte-se ao Azure
A próxima etapa é configurar o serviço principal. Esta é uma identidade no Azure Active Directory que permite que o agente de compilação do DevOps operar no Azure em nome do usuário. Para configurá-lo, inicie com a adição é a primeira etapa de Build do Azure PowerShell.

1. Selecione **adicionar tarefa**.
2. Pesquise **o Azure PowerShell**.
3. Depois de encontrá-lo, selecione **adicionar** para adicionar a tarefa para a compilação. Quando você fizer isso, você verá a tarefa são exibidos no lado esquerdo como adicionado.

![Configurar a etapa do PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

A maneira mais rápida para configurar o serviço principal é permitir que o Azure DevOps fazê-lo para nós.

1. Selecione o **tarefa** você acabou de adicionar.
2. Para **tipo de Conexão do Azure**, escolha **do Azure Resource Manager**.
3. Selecione o **gerenciar** link para configurar a entidade de serviço.

Para obter mais informações, consulte este [postagem de blog](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Quando você seleciona os **gerenciar** link, você entrará no lugar certo em DevOps (segunda captura de tela na postagem do blog) para configurar a conexão para o Azure. Certifique-se de escolher **ponto de extremidade de serviço do Azure Resource Manager** ao configurar isso.

## <a name="complete-the-build-task"></a>Concluir a tarefa de build
Se você selecionar a tarefa de build, você verá todos os detalhes no painel à direita deve ser preenchido.

1. Primeiro, nomeie a tarefa de build: **Criar máquinas virtuais**.
2. Escolha o **entidade de serviço** criado escolhendo **do Azure Resource Manager**
3. Escolha o **ponto de extremidade de serviço**.
4. Para **caminho de Script**, selecione **... (reticências)**  à direita.
5. Navegue até **MakeGoldenImageVMs.ps1** script.
6. Parâmetros de script devem ter esta aparência: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Concluir a definição de compilação](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Enfileirar o build
Vamos verificar se você tem tudo configurado corretamente, bastando enfileirar uma nova compilação. Enquanto a compilação está em execução, alterne para o [portal do Azure](https://portal.azure.com) e selecione **todas as máquinas virtuais** em seu laboratório de fábrica de imagem para confirmar que tudo está funcionando corretamente. Você deverá ver três máquinas virtuais são criadas no laboratório.

![VMs do laboratório](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Próximas etapas
A primeira etapa na configuração de fábrica de imagem com base no Azure DevTest Labs é concluída. No próximo artigo na série, você os receba VMs generalizada e salvos em imagens personalizadas. Em seguida, você os tiver distribuído para todos os seus laboratórios. Consulte o próximo artigo na série: [Salvar imagens personalizadas e distribuir a vários laboratórios da](image-factory-save-distribute-custom-images.md).
