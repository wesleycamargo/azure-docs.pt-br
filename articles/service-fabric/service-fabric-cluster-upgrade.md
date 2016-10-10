<properties
   pageTitle="Atualizar um cluster do Service Fabric | Microsoft Azure"
   description="Atualize o código e/ou configuração do Service Fabric que executa um cluster do Service Fabric, incluindo a definição do modo de atualização do cluster, a atualização de certificados, a adição de portas do aplicativo, a aplicação de patches no sistema operacional etc. O que você pode esperar após a execução das atualizações?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="chackdan"/>


# Atualizar um cluster do Service Fabric

Um cluster do Azure Service Fabric é um recurso cujo proprietário é você, mas que é parcialmente gerenciado pela Microsoft. Este artigo descreve o que é gerenciado automaticamente e o que você pode configurar por conta própria.

## Controlando a versão do Fabric em execução no Cluster

Você pode definir o cluster para receber atualizações automáticas do Fabric, quando a Microsoft lançar uma nova versão ou você optar por selecionar uma versão com suporte do Fabric na qual deseja que o cluster se encontre.

Você pode fazer isso definindo a configuração do cluster "upgradeMode" no portal ou usando o Gerenciador de Recursos no momento da criação ou posteriormente em um cluster ativo

>[AZURE.NOTE] Certifique-se de manter o cluster sempre executando uma versão do Fabric com suporte. Assim como anunciamos o lançamento de uma nova versão do Service Fabric, a versão anterior está marcada para fim da vida útil após um mínimo de 60 dias a partir dessa data. As novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Então, a nova versão está disponível para escolha.

14 dias antes da expiração da versão do cluster em execução, um evento de integridade é gerado, colocando seu cluster em um estado de integridade de aviso. O cluster permanecerá em um estado de aviso até você atualize para uma versão do Fabric com suporte.


### Definindo o modo de atualização por meio do portal 

Você pode definir o cluster para manual ou automático ao criá-lo.

![Create\_Manualmode][Create_Manualmode]

Você pode definir o cluster como automático ou manual quando estiver em um cluster ativo, usando a experiência de gerenciamento.

#### Atualizando para uma nova versão em um cluster que está definido para o modo Manual por meio do portal.
 
Para atualizar para uma nova versão, tudo o que você precisa fazer é selecionar a versão disponível no menu suspenso e salvar. A atualização do Fabric é inicializada automaticamente. As políticas de integridade do cluster (uma combinação de integridade do nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Role para baixo deste documento para ler mais sobre como definir as políticas de integridade personalizadas.

Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas de antes.

![Manage\_Automaticmode][Manage_Automaticmode]

### Definindo o modo de atualização por meio de um modelo do Resource Manager 

Adicione a configuração "upgradeMode" à definição de recursos Microsoft.ServiceFabric/clusters e defina "clusterCodeVersion" para uma das versões do Fabric com suporte, conforme mostrado abaixo e, em seguida, implante o modelo. Os valores válidos para "upgradeMode" são "Manual" ou "Automático"
 
![ARMUpgradeMode][ARMUpgradeMode]

#### Atualizando para uma nova versão em um cluster que está definido para o modo Manual por meio do modelo do Resource Manager.
 
Quando o cluster está no modo Manual, para atualizar para uma nova versão, altere "clusterCodeVersion" para uma versão com suporte e implante-o. A implantação do modelo ativa a atualização do Fabric, que é inicializada automaticamente. As políticas de integridade do cluster (uma combinação de integridade do nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Role para baixo deste documento para ler mais sobre como definir as políticas de integridade personalizadas.

Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas de antes.

### Obter uma lista de todas as versões disponíveis para todos os ambientes para determinada assinatura

Execute o comando a seguir e obtenha uma saída semelhante a esta.

"supportExpiryUtc" informa o quando determinada versão está expirando ou expirou. A versão mais recente não tem uma data válida – ela terá um valor de "9999-12-31T23:59:59.9999999", que significa apenas que a data de vencimento ainda não foi definida.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/clusterVersions?api-version= 2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## Comportamento de atualização do Fabric quando Modo de Atualização do cluster for Automático

A Microsoft mantém o código de malha e a configuração executada em um cluster. Executamos atualizações automáticas monitoradas no software de acordo com a necessidade. Essas atualizações podem ser feitas no código, na configuração ou em ambos. Para garantir que seu aplicativo sofra o mínimo ou nenhum impacto por conta dessas atualizações, nos ás executamos nas fases indicadas a seguir:

### Fase 1: uma atualização é executada usando todas as políticas de integridade do cluster

Durante esta fase, as atualizações realizam um domínio de atualização por vez, e os aplicativos em execução no cluster continuam em execução sem qualquer tempo de inatividade. As políticas de integridade do cluster (uma combinação de integridade do nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Em seguida, um email é enviado ao proprietário da assinatura. O email contém as seguintes informações:

- Uma notificação de que precisamos reverter uma atualização de cluster.
- Sugestões de ações corretivas, se houver alguma.
- O número de dias (n) até a execução da Fase 2.

Tentamos executar a mesma atualização algumas vezes mais, caso alguma atualização falhe por motivos de infraestrutura. Após os n dias a partir da data de envio do email, prosseguiremos para a Fase 2.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a atualização inicial ou durante qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida. Isso serve para evitar o envio de muitos emails. O recebimento de um email deve ser visto como uma exceção. Esperamos que a maioria das atualizações do cluster tenha êxito sem afetar a disponibilidade de seu aplicativo.

### Fase 2: uma atualização é executada usando apenas as políticas de integridade padrão

As políticas de integridade desta fase são definidas de forma que o número de aplicativos íntegros no início da atualização permaneça o mesmo durante o processo de atualização. Assim como na Fase 1, na Fase 2 as atualizações ocorrem em um domínio de atualização por vez, e os aplicativos em execução no cluster continuam em execução sem qualquer tempo de inatividade. As políticas de integridade do cluster (uma combinação de integridade do nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster em vigor não forem atendidas, a atualização será revertida. Em seguida, um email é enviado ao proprietário da assinatura. O email contém as seguintes informações:

- Uma notificação de que precisamos reverter uma atualização de cluster.
- Sugestões de ações corretivas, se houver alguma.
- O número de dias (n) até a execução da Fase 3.

Tentamos executar a mesma atualização algumas vezes mais, caso alguma atualização falhe por motivos de infraestrutura. Um lembrete será enviado por email alguns dias antes do término dos n dias. Após os n dias a partir da data de envio do email, prosseguiremos para a Fase 3. Os emails que enviamos na Fase 2 devem ser levados a sério e as ações corretivas devem ser realizadas.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a atualização inicial ou durante qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida.

### Fase 3: uma atualização é executada usando políticas de integridade agressivas

Essas políticas de integridade desta fase são destinadas à conclusão da atualização, em vez da integridade dos aplicativos. Pouquíssimas atualizações de cluster chegam a esta fase. Caso seu cluster chegue a esta fase, há uma boa chance de seu aplicativo deixar de ser íntegro e/ou de perder a disponibilidade.

Assim como nas duas outras fases, as atualizações da Fase 3 realizam um domínio de atualização por vez.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Tentamos executar a mesma atualização algumas vezes mais, caso alguma atualização falhe por motivos de infraestrutura. Depois disso, o cluster será marcado para que não receba mais suporte e/ou atualizações.

Um email com essas informações é enviado ao proprietário da assinatura, juntamente com as ações corretivas. Não esperamos que qualquer cluster entre em um estado no qual a Fase 3 falhou.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a atualização inicial ou durante qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida.

## Configurações do cluster que você controla

Além da capacidade de definir o cluster de modo de atualização, aqui estão as configurações que podem ser alteradas em um cluster ativo.

### Certificados

Você pode adicionar ou excluir novos certificados do cluster e do cliente facilmente por meio do portal. Confira [este documento para obter instruções detalhadas](service-fabric-cluster-security-update-certs-azure.md)

![Captura de tela que mostra as impressões digitais do certificado no Portal do Azure.][CertificateUpgrade]


### Portas do aplicativo

Você pode alterar as portas do aplicativo alterando as propriedades do recurso de Balanceador de carga associadas ao tipo de nó. Você pode usar o portal ou o PowerShell do Gerenciador de Recursos diretamente.

Para abrir uma nova porta em todas as VMs em um tipo de nó, faça o seguinte:

1. Adicione uma nova investigação ao balanceador de carga apropriado.

    Se você implantou o cluster usando o portal, os balanceadores de carga são nomeados como "LB-nome do grupo de recursos-NodeTypename", um para cada tipo de nó. Como os nomes de balanceador de carga são exclusivos apenas em um grupo de recursos, será melhor pesquisá-los em um grupo de recursos específico.

    ![Captura de tela que mostra a adição de uma investigação a um balanceador de carga no portal.][AddingProbes]

2. Adicione uma nova regra ao balanceador de carga.

    Adicione uma nova regra ao mesmo balanceador de carga usando a investigação criada na etapa anterior.

    ![Adicionando uma nova regra a um balanceador de carga no portal.][AddingLBRules]


### Propriedades de posicionamento

Para cada um dos tipos de nó, é possível adicionar as propriedades de posicionamento personalizadas que você deseja usar em seus aplicativos. NodeType é uma propriedade padrão que você pode usar sem adicioná-la explicitamente.

>[AZURE.NOTE] Para obter detalhes sobre o uso de restrições de posicionamento, propriedades de nó e como defini-las, confira a seção "Restrições de posicionamento e propriedades de nó" no documento do Gerenciador de Recursos do cluster do Service Fabric em [Descrevendo seu cluster](service-fabric-cluster-resource-manager-cluster-description.md).

### Métricas de capacidade

Para cada um dos tipos de nó, é possível adicionar métricas de capacidade personalizadas que você deseja usar em seus aplicativos para relatar a carga. Para obter detalhes sobre o uso de métricas de capacidade para relatar carga, confira os documentos do Gerenciador de Recursos do cluster do Service Fabric em [Descrevendo seu cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [Métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

### Configurações de atualização do Fabric – Políticas de integridade

Você pode especificar as políticas de integridade personalizadas para atualização do Fabric. Se você tiver configurado o cluster para atualizações automáticas do Fabric, essas políticas serão aplicadas à Fase 1 das atualizações automáticas do Fabric. Se você tiver configurado o cluster para atualizações manuais do Fabric, essas políticas serão aplicadas cada vez que você selecionar uma nova versão ao disparar o sistema para acionar a atualização do Fabric em seu cluster. Se você não substituir as políticas, os padrões serão usados.

Você pode especificar as políticas de integridade personalizados ou examinar as configurações atuais na folha de "atualização de malha", selecionando as configurações avançadas de atualização. Examine a imagem a seguir com detalhes.

![Gerenciar políticas de integridade personalizadas][HealthPolices]

### Personalizar as configurações do Fabric para seu cluster

Consulte [configurações de malha do cluster do Service Fabric](service-fabric-cluster-fabric-settings.md) para saber o que e como você pode personalizá-lo.

### Patches do sistema operacional nas VMs que fazem parte do cluster

Essa funcionalidade está prevista como um recurso automatizado futuramente. Contudo, no momento, você é responsável por aplicar patches nas suas VMs. Você deve fazer isso uma VM por vez, para que você não desative mais de uma por vez.

### Atualizações do sistema operacional nas VMs que fazem parte do cluster

Se for necessário atualizar a imagem do sistema operacional nas máquinas virtuais do cluster, faça isso uma VM por vez. Você é responsável por esta atualização. Atualmente não há nenhuma automação para isso.

## Próximas etapas
- Saiba como personalizar algumas das [configurações de malha do cluster do Service Fabric](service-fabric-cluster-fabric-settings.md)
- Saiba como [reduzir e escalar horizontalmente seu cluster](service-fabric-cluster-scale-up-down.md)
- Saiba mais sobre [atualizações de aplicativo](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG

<!---HONumber=AcomDC_0928_2016-->