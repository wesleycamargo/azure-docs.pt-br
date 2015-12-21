<properties
   pageTitle="Atualizando um cluster do Service Fabric | Microsoft Azure"
   description="Atualize o código e/ou configuração de malha que executa um cluster do Service Fabric, incluindo a atualização de certificado, a adição de portas do aplicativo, os patches de sistema operacional etc. O que você pode esperar após a execução das atualizações?"
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
   ms.date="11/23/2015"
   ms.author="chackdan"/>

# Atualizando um cluster do Service Fabric

Você é o proprietário de um cluster do Service Fabric, mas ele é parcialmente gerenciado pela Microsoft. Este artigo descreve o que é gerenciado automaticamente e o que você pode configurar por conta própria.

## Configuração do cluster gerenciada automaticamente

A Microsoft mantém o código e a configuração da malha executada em um cluster, nós executamos atualizações automáticas monitoradas no software como necessário. Essas atualizações podem ser feitas no código, na configuração ou em ambos. Para garantir que seu aplicativo não seja afetado, ou que o impacto seja o menor possível, por essas atualizações, executamos as atualizações em três fases.

### Fase 1: a atualização é executada usando todas as políticas de integridade do cluster

Durante esta fase, as atualizações realizam um domínio de atualização por vez, e os aplicativos em execução no cluster continuam em execução sem qualquer tempo de inatividade. As políticas de integridade do cluster (uma combinação de integridade do Nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida, um email será enviado ao proprietário da assinatura indicando que foi necessário reverter uma atualização do cluster, junto com ações corretivas, se houver alguma, e que executaremos a Fase 2 em n dias. No caso, n é uma variável. Tentamos executar a mesma atualização algumas outras vezes para desconsiderar as atualizações que falharam por motivos de infraestrutura e, após n dias a partir da data de envio do email, prosseguimos para a fase 2.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a execução inicial ou de qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida. (Isso serve para evitar o envio de muitos emails. O recebimento de um email deve ser visto como uma exceção. Esperamos que a maioria das atualizações do cluster seja executada sem afetar a disponibilidade de seu aplicativo).

Para obter detalhes sobre como definir as políticas de integridade personalizadas para seu cluster, confira [Atualização do cluster e parâmetros de integridade](service-fabric-cluster-health-parameters.md).

### Fase 2: a atualização é executada usando apenas as políticas de integridade padrão

As políticas de integridade são definidas de forma que o número de aplicativos íntegros no início da atualização permaneça o mesmo durante o processo de atualização. Nesta fase, assim como na fase 1, as atualizações são realizadas um domínio de atualização por vez, e os aplicativos que estavam em execução no cluster continuam em execução sem qualquer tempo de inatividade. As políticas de integridade do cluster (uma combinação de integridade do Nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster em vigor não forem atendidas, a atualização será revertida, um email será enviado ao proprietário da assinatura indicando que foi necessário reverter uma atualização do cluster, junto com ações corretivas, se houver alguma, e que executaremos a Fase 3 em n dias. No caso, n é uma variável.

Tentaremos executar a mesma atualização algumas outras vezes para descartarmos as atualizações que falharam por causa da infraestrutura. Um lembrete será enviado por email alguns dias antes do término dos n dias. Após os n dias a partir da data de envio do email, prosseguiremos para a Fase 3. Os emails que enviamos na Fase 2 devem ser levados a sério e as ações corretivas devem ser realizadas.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a execução inicial ou de qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida.

### Fase 3: a atualização é executada usando políticas de integridade agressivas

Essas políticas de integridade são destinadas à conclusão da atualização, em vez da integridade dos aplicativos. Pouquíssimas atualizações de cluster chegarão a esta fase. Se o seu cluster chegar a esta fase, há uma boa chance de que seu aplicativo deixará de ser íntegro e/ou perderá a disponibilidade.

Assim como nas duas outras fases, as atualizações da Fase 3 realizam um domínio de atualização por vez.

Se as políticas de integridade do cluster em vigor não forem atendidas, a atualização será revertida. Tentaremos executar a mesma atualização algumas outras vezes para desconsiderar as atualizações que falharam por motivos de infraestrutura e, depois disso, o cluster será fixado, de modo que não receba mais suporte e/ou atualizações.

Um email será enviado ao proprietário da assinatura com essas informações e com as ações corretivas. Não esperamos que qualquer cluster entre em um estado no qual a Fase 3 tenha falhado.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a execução inicial ou de qualquer nova execução das atualizações desta fase. Nenhum email de confirmação é enviado após uma execução bem-sucedida.

## Configuração do cluster que você controla

Estas são as configurações que você pode alterar em um cluster ativo.

### Certificados

Você pode atualizar facilmente os certificados primário ou secundário no portal ou por meio da emissão de um comando PUT no recurso servicefabric.cluster.

![CertificateUpgrade][CertificateUpgrade]

**Observação** Antes de você identificar os certificados que você deseja usar para os recursos de cluster, você precisará concluir as etapas a seguir; caso contrário, o novo certificado não será usado. 1) carregue o novo certificado para o keyvault - consulte a [Segurança do Service Fabric](service-fabric-cluster-security.md) para instruções - comece com a etapa #2 nesse documento. 2) atualize todas as Máquinas Virtuais que compõem o nosso cluster, para que o certificado seja implantado nelas. Consulte [esta postagem de blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) sobre como fazer.

### Portas do Aplicativo

Você pode fazer isso alterando as propriedades de recurso do balanceador de carga associadas ao Tipo de Nó. É possível usar o portal ou o ARM PowerShell diretamente.

Para abrir uma nova porta em todas as VMs em um Tipo de Nó, você precisará fazer o seguinte.

1. **Adicionar uma nova investigação ao balanceador de carga apropriado**

    Se você tiver implantado o cluster usando o portal, o balanceador de carga receberá o nome "loadBalancer-0", "loadBalancer-1" e assim por diante, um para cada Tipo de Nó. Como os nomes do balanceador de carga são exclusivos apenas em um grupo de recursos (RG), será melhor procurá-los em um determinado RG.

    ![AddingProbes][AddingProbes]


2. **Adicionar uma nova regra ao balanceador de carga**

    Adicione uma nova regra ao mesmo balanceador de carga usando a investigação criada na etapa anterior.

    ![AddingLBRules][AddingLBRules]


### Propriedades de Posicionamento

  Para cada um dos Tipos de Nó, é possível adicionar as propriedades de posicionamento personalizadas que você deseja usar em seus aplicativos. NodeType é uma propriedade padrão que você pode usar sem adicioná-la explicitamente.

  >[AZURE.NOTE]Para obter detalhes sobre o uso da propriedade de posicionamento, veja a [documentação sobre restrições de posicionamento](service-fabric-placement-constraint.md).

### Métricas de Capacidade

Para cada um dos Tipos de Nó, é possível adicionar métricas de capacidade personalizadas que você deseja usar em seus aplicativos para relatar a carga. Para obter detalhes sobre o uso de métricas de capacidade para relatar a carga, veja a [visão geral dos relatórios de carga dinâmica](service-fabric-resource-balancer-dynamic-load-reporting.md).

### Aplicação de patches do sistema operacional nas máquinas virtuais que compõem o cluster
Esse recurso ainda será lançado. Hoje, você é responsável por aplicar patches a suas VMs. É necessário fazer isso VM por VM, para que você não desative mais de uma VM por vez.

### Atualizando o sistema operacional para um novo nas máquinas virtuais que compõem o cluster
Se for necessário atualizar a imagem do sistema operacional usada, você será responsável por essa atualização e deverá executá-la uma VM por vez. Atualmente, não há automação para isso.


## Próximas etapas

- Saiba como [escalar e reduzir verticalmente seu cluster](service-fabric-cluster-scale-up-down.md)
- Saiba mais sobre [atualizações de aplicativo](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes.png
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png

<!---HONumber=AcomDC_1210_2015-->