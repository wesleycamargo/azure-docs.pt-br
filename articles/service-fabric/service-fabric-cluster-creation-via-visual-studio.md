---
title: Configurando um cluster do Service Fabric usando o Visual Studio | Microsoft Docs
description: Descreve como configurar um cluster do Service Fabric usando o modelo do Azure Resource Manager criado por um projeto de Grupo de Recursos do Azure no Visual Studio
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: adegeo
editor: 
ms.assetid: bd2c0511-36c9-4828-8dc3-69e4b6a70567
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 0eefa64d6292dec14842c031ebad80fa9094436d
ms.openlocfilehash: 535e33eec22ed1c488cace9442328084b416b7a8
ms.contentlocale: pt-br
ms.lasthandoff: 02/22/2017


---
# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>Configurar um cluster do Service Fabric usando o Visual Studio
Este artigo descreve como configurar um cluster do Service Fabric do Azure usando o Visual Studio e um modelo do Azure Resource Manager. Usaremos o projeto de grupo de recursos do Azure do Visual Studio para criar o modelo. Depois que o modelo tiver sido criado, ele pode ser implantado diretamente no Azure a partir do Visual Studio. Ele também pode ser usado em um script, ou como parte do recurso de CI (integração contínua).

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>Criar um modelo de cluster do Service Fabric usando um projeto de grupo de recursos do Azure
Para começar, abra o Visual Studio e crie um projeto de grupo de recursos do Azure (ele está disponível na pasta **Nuvem** ):

![Caixa de diálogo Novo Projeto com o projeto do Grupo de Recursos do Azure selecionado][1]

Você pode criar uma nova solução do Visual Studio para este projeto ou adicioná-lo a uma solução existente.

> [!NOTE]
> Se você não vir o projeto do grupo de recursos do Azure sob o nó da Nuvem, o SDK do Azure não estará instalado. Inicie o Web Platform Installer ([instale-o agora](http://www.microsoft.com/web/downloads/platform.aspx) caso ainda não tenha feito isso), pesquise "SDK do Azure para .NET" e instale a versão compatível com sua versão do Visual Studio.
> 
> 

Depois que pressionar o botão OK, Visual Studio solicitará que você selecione o modelo do Gerenciador de Recursos que deseja criar:

![Selecione a caixa de diálogo do Modelo do Azure com o modelo de Cluster do Service Fabric selecionado][2]

Selecione o modelo Cluster do Service Fabric e pressione o botão OK novamente. O projeto e o modelo do Gerenciador de Recursos foram criados agora.

## <a name="prepare-the-template-for-deployment"></a>Preparar o modelo para implantação
Antes da implantação do modelo para a criação do cluster, você deve fornecer valores para os parâmetros necessários do modelo. Esses valores de parâmetro são lidos no arquivo `ServiceFabricCluster.parameters.json`, que está na pasta `Templates` do projeto de grupo de recursos. Abra o arquivo e forneça os seguintes valores:

| Nome do parâmetro | Descrição |
| --- | --- |
| adminUserName |O nome da conta de administrador para máquinas do Service Fabric (nós). |
| certificateThumbprint |A impressão digital do certificado que protege o cluster. |
| sourceVaultResourceId |A *ID do recurso* do cofre da chave no qual o certificado que protege o cluster é armazenado. |
| certificateUrlValue |A URL do certificado de segurança do cluster. |

O modelo do Gerenciador de Recursos do Service Fabric do Visual Studio cria um cluster seguro que é protegido por um certificado. Esse certificado é identificado pelos três últimos parâmetros do modelo (`certificateThumbprint`, `sourceVaultValue`, e `certificateUrlValue`), e ele deve existir em um **Cofre de Chaves do Azure**. Para saber mais sobre como criar o certificado de segurança do cluster, veja o artigo [Cenários de segurança do cluster do Service Fabric](service-fabric-cluster-security.md#x509-certificates-and-service-fabric) .

## <a name="optional-change-the-cluster-name"></a>Opcional: alterar o nome do cluster
Cada cluster do Service Fabric tem um nome. Quando um cluster do Fabric é criado no Azure, o nome do cluster determina (com a região do Azure) o nome DNS (Sistema de Nomes de Domínio) para o cluster. Por exemplo, se você nomear seu cluster `myBigCluster` e o local (região do Azure) do grupo de recursos que hospedará o novo cluster for o Leste dos EUA, o nome DNS do cluster será `myBigCluster.eastus.cloudapp.azure.com`.

Por padrão, o nome do cluster é gerado automaticamente e se torna exclusivo pelo acréscimo de um sufixo aleatório a um prefixo "cluster". Isso facilita o uso do modelo como parte de um sistema de **integração contínua** (CI). Se quiser usar um nome específico para seu cluster, um que seja significativo para você, defina o valor da variável `clusterName` no arquivo de modelo do Resource Manager (`ServiceFabricCluster.json`) para o nome de sua preferência. É a primeira variável definida nesse arquivo.

## <a name="optional-add-public-application-ports"></a>Opcional: adicionar portas públicas do aplicativo
Talvez você queira alterar as portas públicas de aplicativos do cluster antes de implantá-lo. Por padrão, o modelo abre apenas duas portas TCP públicas (80 e 8081). Se você precisar de mais para seus aplicativos, modifique a definição de Balanceador de Carga do Azure no modelo. A definição é armazenada no arquivo de modelo principal (`ServiceFabricCluster.json`). Abra o arquivo e procure por `loadBalancedAppPort`. Cada porta está associada a três artefatos:

1. Uma variável de modelo que define o valor da porta TCP para a porta:
   
    ```json
    "loadBalancedAppPort1": "80"
    ```
2. Uma *investigação* que define com que frequência, e por quanto tempo, o Azure load balancer tenta usar um nó específico do Service Fabric antes de executar o failover para outro nó. As investigações fazem parte do recurso de Balanceador de Carga. Esta é a definição de investigação para a primeira porta de aplicativo padrão:
   
    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```
3. Uma *regra de balanceamento de carga* que vincula a porta e a investigação, o que permite o balanceamento de carga em um conjunto de nós de cluster do Service Fabric:
   
    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
   Se os aplicativos que planeja implantar no cluster precisarem de mais portas, você poderá adicioná-las criando mais definições de investigação e de regra de balanceamento de carga. Para saber mais sobre como trabalhar com o Azure Load Balancer por meio de modelos do Gerenciador de Recursos, veja [Introdução à criação de um balanceador de carga interno usando um modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## <a name="deploy-the-template-by-using-visual-studio"></a>Implantar o modelo usando o Visual Studio
Após salvar todos os valores de parâmetro necessários no arquivo`ServiceFabricCluster.param.dev.json` , você está pronto para implantar o modelo e criar o cluster do Service Fabric. Clique com botão direito no projeto de grupo de recursos no Gerenciador de Soluções do Visual Studio e escolha **Implantar | Nova Implantação...**. Se necessário, o Visual Studio mostrará a caixa de diálogo **Implantar no Grupo de Recursos**, solicitando a autenticação do Azure:

![Caixa de diálogo Implantar no Grupo de Recursos][3]

A caixa de diálogo permite que você escolha um grupo de recursos existente do Gerenciador de Recursos para o cluster e oferece uma opção para criar um novo. Normalmente, faz sentido usar um grupo de recursos separado para um cluster do Service Fabric.

Após pressionar o botão Implantar, o Visual Studio solicitará a confirmação dos valores de parâmetro do modelo. Clique no botão **Salvar** . Um parâmetro não tem um valor persistente: a senha da conta administrativa para o cluster. Você precisa fornecer um valor de senha quando o Visual Studio solicitar um.

> [!NOTE]
> Começando com o Azure SDK 2.9, o Visual Studio oferece suporte à leitura de senhas do **Cofre de Chaves do Azure** durante a implantação. Na caixa de diálogo de parâmetros de modelo, observe que a caixde texto `adminPassword` de parâmetro tem um pequeno ícone de "chave" à direita. Esse ícone permite que você selecione um segredo do cofre de chaves existente como a senha administrativa para o cluster. Apenas certifique-se primeiro de habilitar o acesso do Azure Resource Manager para implantação do modelo nas Políticas de Acesso Avançado do seu Cofre de Chaves. 
> 
> 

Você pode monitorar o andamento do processo de implantação na janela de saída do Visual Studio. Após a conclusão da implantação do modelo, o novo cluster estará pronto para uso!

> [!NOTE]
> Se o PowerShell nunca tiver sido usado para administrar o Azure no computador que você está usando no momento, é necessário fazer uma pequena limpeza.
> 
> 1. Habilite o script do PowerShell executando o comando [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) . Para os computadores de desenvolvimento a política "irrestrita" é geralmente aceitável.
> 2. Decida se deseja permitir a coleta de dados de diagnóstico de comandos do Azure PowerShell e execute [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) ou [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx), conforme necessário. Isso evitará solicitações desnecessárias durante a implantação do modelo.
> 
> 

Em caso de erros, vá para o [portal do Azure](https://portal.azure.com/) e abra o grupo de recursos que você implantou. Clique em **Todas as configurações** e, em seguida, clique em **Implantações** na folha de configurações. Uma implantação com falha do grupo de recursos deixa informações detalhadas sobre o diagnóstico nesse local.

> [!NOTE]
> Os clusters de Service Fabric exigem um determinado número de nós esteja ativo para manter a disponibilidade e preservar o estado – conhecido como "manter o quórum". Portanto, não é seguro desligar todos os computadores no cluster, a menos que você tenha primeiro executado um [backup completo do estado](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre como configurar o cluster do Service Fabric usando o portal do Azure](service-fabric-cluster-creation-via-portal.md)
* [Saiba mais sobre como gerenciar e implantar aplicativos do Service Fabric usando o Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

