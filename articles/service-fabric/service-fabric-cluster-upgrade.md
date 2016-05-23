<properties
   pageTitle="Atualizar um cluster do Service Fabric | Microsoft Azure"
   description="Atualize o código e/ou configuração do Service Fabric que executa um cluster do Service Fabric, incluindo a atualização de certificados, a adição de portas do aplicativo, a aplicação de patches no sistema operacional etc. O que você pode esperar após a execução das atualizações?"
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
   ms.date="05/02/2016"
   ms.author="chackdan"/>


# Atualizar um cluster do Service Fabric

Um cluster do Azure Service Fabric é um recurso cujo proprietário é você, mas que é parcialmente gerenciado pela Microsoft. Este artigo descreve o que é gerenciado automaticamente e o que você pode configurar por conta própria.

## Configuração do cluster gerenciada automaticamente

A Microsoft mantém o código de malha e a configuração executada em um cluster. Executamos atualizações automáticas monitoradas no software de acordo com a necessidade. Essas atualizações podem ser feitas no código, na configuração ou em ambos. Para garantir que seu aplicativo sofra o mínimo ou nenhum impacto por conta dessas atualizações, nos ás executamos nas três fases indicadas a seguir.

### Fase 1: uma atualização é executada usando todas as políticas de integridade do cluster

Durante esta fase, as atualizações realizam um domínio de atualização por vez, e os aplicativos em execução no cluster continuam em execução sem qualquer tempo de inatividade. As políticas de integridade do cluster (uma combinação de integridade do nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Em seguida, um email é enviado para o proprietário da assinatura. O email contém as seguintes informações:

- Uma notificação de que precisamos reverter uma atualização de cluster.
- Sugestões de ações corretivas, se houver alguma.
- O número de dias (n) até a execução da Fase 2.

Tentamos executar a mesma atualização algumas vezes mais, caso alguma atualização falhe por motivos de infraestrutura. Após os n dias a partir da data de envio do email, prosseguiremos para a Fase 2.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a atualização inicial ou durante qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida. Isso serve para evitar o envio de muitos emails. O recebimento de um email deve ser visto como uma exceção. Esperamos que a maioria das atualizações do cluster tenha êxito sem afetar a disponibilidade de seu aplicativo.

### Fase 2: uma atualização é executada usando apenas as políticas de integridade padrão

As políticas de integridade desta fase são definidas de forma que o número de aplicativos íntegros no início da atualização permaneça o mesmo durante o processo de atualização. Assim como na Fase 1, na Fase 2 as atualizações ocorrem em um domínio de atualização por vez, e os aplicativos em execução no cluster continuam em execução sem qualquer tempo de inatividade. As políticas de integridade do cluster (uma combinação de integridade do nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster em vigor não forem atendidas, a atualização será revertida. Em seguida, um email é enviado para o proprietário da assinatura. O email contém as seguintes informações:

- Uma notificação de que precisamos reverter uma atualização de cluster.
- Sugestões de ações corretivas, se houver alguma.
- O número de dias (n) até a execução da Fase 3.

Tentamos executar a mesma atualização algumas vezes mais, caso alguma atualização falhe por motivos de infraestrutura. Um lembrete será enviado por email alguns dias antes do término dos n dias. Após os n dias a partir da data de envio do email, prosseguiremos para a Fase 3. Os emails que enviamos na Fase 2 devem ser levados a sério e as ações corretivas devem ser realizadas.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a atualização inicial ou durante qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida.

### Fase 3: uma atualização é executada usando políticas de integridade agressivas

Essas políticas de integridade desta fase são destinadas à conclusão da atualização, em vez da integridade dos aplicativos. Pouquíssimas atualizações de cluster chegarão a esta fase. Caso seu cluster chegue a esta fase, há uma boa chance de seu aplicativo deixar de ser íntegro e/ou de perder a disponibilidade.

Assim como nas duas outras fases, as atualizações da Fase 3 realizam um domínio de atualização por vez.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Tentamos executar a mesma atualização algumas vezes mais, caso alguma atualização falhe por motivos de infraestrutura. Depois disso, o cluster será marcado para que não receba mais suporte e/ou atualizações.

Um email com essas informações será enviado ao proprietário da assinatura, juntamente com as ações corretivas. Não esperamos que qualquer cluster entre em um estado no qual a Fase 3 falhou.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a atualização inicial ou durante qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida.

## Configurações do cluster que você controla

Estas são as configurações que você pode alterar em um cluster ativo.

### Certificados

Você pode atualizar facilmente os certificados primário ou secundário no Portal do Azure (exibido abaixo) ou por meio da emissão de um comando PUT no recurso servicefabric.cluster.

![Captura de tela que mostra as impressões digitais do certificado no Portal do Azure.][CertificateUpgrade]

>[AZURE.NOTE] Antes de identificar um certificado que você deseja usar para os recursos de cluster, é necessário concluir as etapas a seguir, ou o novo certificado não será usado:
1. Carregar o novo certificado no Cofre de Chaves do Azure. Consulte [Segurança do Service Fabric](service-fabric-cluster-security.md) para obter instruções. Comece com a etapa 2 neste documento.
2. Atualize todas as VMs (máquinas virtuais) que compõem seu cluster, para que o certificado seja implantado nelas. Para fazer isso, consulte o [Blog da equipe do Cofre de Chaves do Azure](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

### Portas do aplicativo

Você pode alterar as portas do aplicativo alterando as propriedades do recurso de Balanceador de carga associadas ao tipo de nó. Você pode usar o portal ou o PowerShell do Gerenciador de Recursos diretamente.

Para abrir uma nova porta em todas as VMs em um tipo de nó, faça o seguinte:

1. Adicione uma nova investigação ao balanceador de carga apropriado.

    Se você tiver implantado o cluster usando o portal, os balanceadores de carga receberão o nome "loadBalancer-0", "loadBalancer-1" e assim por diante, um para cada tipo de nó. Como os nomes de balanceador de carga são exclusivos apenas em um grupo de recursos, será melhor pesquisá-los em um grupo de recursos específico.

    ![Captura de tela que mostra a adição de uma investigação a um balanceador de carga no portal.][AddingProbes]

2. Adicione uma nova regra ao balanceador de carga.

    Adicione uma nova regra ao mesmo balanceador de carga usando a investigação criada na etapa anterior.

    ![Captura de tela que mostra a adição de uma nova regra a um balanceador de carga no portal.][AddingLBRules]


### Propriedades de posicionamento

Para cada um dos tipos de nó, é possível adicionar as propriedades de posicionamento personalizadas que você deseja usar em seus aplicativos. NodeType é uma propriedade padrão que você pode usar sem adicioná-la explicitamente.

>[AZURE.NOTE] Para obter detalhes sobre o uso de restrições de posicionamento, propriedades de nó e como defini-las, consulte a seção "Restrições de posicionamento e propriedades de nó" no documento do Gerenciador de recursos do cluster do Service Fabric em [Descrevendo seu cluster](service-fabric-cluster-resource-manager-cluster-description.md).

### Métricas de capacidade

Para cada um dos tipos de nó, é possível adicionar métricas de capacidade personalizadas que você deseja usar em seus aplicativos para relatar a carga. Para obter detalhes sobre o uso de métricas de capacidade para relatar carga, consulte os documentos do Gerenciador de recursos do cluster do Service Fabric em [Descrevendo seu cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [Métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

### Patches do sistema operacional nas VMs que fazem parte do cluster

Essa funcionalidade está prevista como um recurso automatizado futuramente. Contudo, no momento, você é responsável por aplicar patches nas suas VMs. Você deve fazer isso uma VM por vez, para que você não desative mais de uma por vez.

### Atualizações do sistema operacional nas VMs que fazem parte do cluster

Se for necessário atualizar a imagem do sistema operacional nas máquinas virtuais do cluster, faça isso uma VM por vez. Você é responsável por esta atualização. Atualmente não há nenhuma automação para isso.

## Próximas etapas

- Saiba como [escalar e reduzir verticalmente seu cluster](service-fabric-cluster-scale-up-down.md)
- Saiba mais sobre [atualizações de aplicativo](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes.png
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png

<!---HONumber=AcomDC_0511_2016-->