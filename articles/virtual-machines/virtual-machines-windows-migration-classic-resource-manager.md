<properties
	pageTitle="Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager | Microsoft Azure"
	description="Este artigo apresenta a migração de recursos com suporte da plataforma do clássico para o Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="mahthi"/>

# Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager

Neste artigo, descrevemos como estamos possibilitando a migração de recursos de IaaS (infraestrutura como serviço) dos de implantação clássicos para o Resource Manager. Você pode ler mais sobre os [recursos e benefícios do Azure Resource Manager](../resource-group-overview.md). Fornecemos os detalhes sobre como conectar recursos dos dois modelos de implantação coexistindo em sua assinatura usando gateways site a site de rede virtual.

## Meta de migração

O Gerenciador de Recursos possibilita implantar aplicativos complexos por meio de modelos, configurar máquinas virtuais usando extensões de VM e incorporar o gerenciamento de acesso e a marcação. O Azure Resource Manager inclui implantação paralela e escalonável para máquinas virtuais em conjuntos de disponibilidade. O novo modelo também oferece gerenciamento de ciclo de vida de computação, rede e armazenamento de maneira independente. Por fim, há um enfoque para habilitar a segurança por padrão com a imposição de máquinas virtuais em uma rede virtual.

Há suporte para quase todos os recursos do modelo de implantação clássica referentes a computação, rede e armazenamento no Azure Resource Manager. Para aproveitar os novos recursos no Azure Resource Manager, você pode migrar as implantações existentes do modelo de implantação clássico.

## Alterações à automação e às ferramentas após a migração

Como parte da migração dos recursos do modelo de implantação clássico para o modelo de implantação do Resource Manager, você deve atualizar sua automação ou ferramentas existentes para garantir que elas continuem funcionando após a migração.

## Significado da migração de recursos de IaaS do clássico para o Gerenciador de Recursos

Antes de analisarmos os detalhes, vamos ver a diferença entre as operações do plano de dados e do plano de gerenciamento dos recursos de IaaS.

- O *plano de gerenciamento* descreve as chamadas que vão para o plano de gerenciamento ou para a API para modificar recursos. Por exemplo, operações como a criação de uma VM, reinicialização de uma VM e atualização de uma rede virtual com uma nova sub-rede para gerenciar os recursos em execução. Elas não afetam diretamente a conexão com as instâncias.
- O *plano de dados* (aplicativo) descreve o tempo de execução do próprio aplicativo e envolve a interação com instâncias que não passam pela API do Azure. Acessar seu site ou efetuar pull de dados de uma instância do SQL Server ou servidor MongoDB em execução seriam considerados um plano de dados ou uma interação com o aplicativo. Copiar um blob de uma conta de armazenamento e acessar um endereço IP público para RDP ou SSH na máquina virtual também são planos de dados. Essas operações mantêm o aplicativo em execução entre computação, rede e armazenamento.

>[AZURE.NOTE] Em alguns cenários de migração, a plataforma Azure interrompe, desaloca e reinicia as máquinas virtuais. Isso acarreta um curto tempo de inatividade do plano de dados.

## Escopos de migração com suporte

Há três escopos de migração destinados principalmente à computação, à rede e ao armazenamento.

### Migração de máquinas virtuais (não em uma rede virtual)

No modelo de implantação do Resource Manager, a segurança de seus aplicativos é imposta por padrão. Todas as VMs precisam estar em uma rede virtual no modelo do Gerenciador de Recursos. As plataforma Azure reinicia (`Stop`, `Deallocate`, e `Start`) as VMs como parte da migração. Você tem duas opções para as redes virtuais:

- Você pode solicitar que a plataforma crie uma nova rede virtual e migre a máquina virtual para a nova rede virtual.
- Você pode migrar a máquina virtual para uma rede virtual existente no Gerenciador de Recursos.

>[AZURE.NOTE] Nesse escopo de migração, as operações do “plano de gerenciamento” e do “plano de dados” podem não ser permitidas por determinado período durante a migração.

### Migração de máquinas virtuais (em uma rede virtual)

Para a maioria das configurações de VM, somente os metadados são migrados entre os modelos Clássico e o Resource Manager. As VMs subjacentes estão em execução no mesmo hardware, na mesma rede e com o mesmo armazenamento. As operações do plano de gerenciamento talvez não tenham permissão por determinado período de tempo durante a migração. No entanto, o plano de dados continua funcionando. Ou seja, os aplicativos em execução nas VMs (clássicas) não incorrem em tempo de inatividade durante a migração.

Atualmente, não há suporte para as seguintes configurações. Se for adicionado suporte a elas no futuro, algumas VMs nessa configuração poderão incorrer em tempo de inatividade (passarão por operações de parar, desalocar e reiniciar a VM).

-	Você tem mais de um conjunto de disponibilidade em um único serviço de nuvem.
-	Você tem um ou mais conjuntos de disponibilidade e as VMs que não estão em um conjunto de disponibilidade em um único serviço de nuvem.

>[AZURE.NOTE] Nesse escopo de migração, o plano de gerenciamento pode não ser permitido por determinado período durante a migração. Para algumas configurações, conforme descrito acima, ocorre tempo de inatividade do plano de dados.

### Migração das contas de armazenamento

Para permitir uma migração perfeita, você implantar VMs do Resource Manager em uma conta de armazenamento clássico. Com essa funcionalidade, recursos de computação e rede podem e devem ser migrados independentemente de contas de armazenamento. Depois de migrar suas Máquinas Virtuais e a Rede Virtual, você precisa migrar suas contas de armazenamento para concluir o processo de migração.

>[AZURE.NOTE] O modelo de implantação do Resource Manager não tem o conceito de discos e imagens clássicas. Quando a conta de armazenamento é migrada, os discos e imagens clássicos não ficarão visíveis na pilha do Resource Manager, mas os VHDs de backup permanecem na conta de armazenamento.

## Recursos e configurações sem suporte

No momento, não oferecemos suporte para alguns recursos e configurações. As seções a seguir descrevem nossas recomendações a respeito deles.

### Recursos sem suporte

Atualmente, não há suporte para os seguintes recursos. Se preferir, você pode remover essas configurações, migrar as VMs e, em seguida, habilitar as configurações novamente no modelo de implantação do Gerenciador de Recursos.

Provedor de recursos | Recurso
---------- | ------------
Computação | Discos de máquina virtual não associados.
Computação | Imagens de máquinas virtuais.
Rede | ACLs de ponto de extremidade.
Rede | Gateways de rede virtual (site a site, Rota Expressa do Azure, Gateway de Aplicativo, ponto a site).
Rede | Redes virtuais usando Emparelhamento VNet. (Migrar a VNet para o ARM e emparelhar) Saiba mais sobre o [Emparelhamento VNet](../virtual-network/virtual-network-peering-overview.md).
Rede | Perfis do Gerenciador de Tráfego.

### Configurações sem suporte

Atualmente, não há suporte para as seguintes configurações.

O Barramento de | Configuração | Recomendações
---------- | ------------ | ------------
Gerenciador de Recursos | RBAC (Controle de Acesso Baseado em Função) para recursos clássicos | Como o URI dos recursos é modificado após a migração, é recomendável planejar as atualizações da política de RBAC que precisam ocorrer após a migração.
Computação | Várias sub-redes associadas a uma VM | Atualize a configuração de sub-rede para fazer referência apenas às sub-redes.
Computação | Máquinas virtuais que pertencem a uma rede virtual, mas que não têm uma sub-rede explícita atribuída | Opcionalmente, você pode excluir a VM.
Computação | Máquinas virtuais que têm alertas e políticas de Escala Automática | A migração passa e essas configurações serão descartadas. É altamente recomendável que você avalie seu ambiente antes de fazer a migração. Se preferir, você pode redefinir as configurações de alerta após a conclusão da migração.
Computação | Extensões de VM do XML (BGInfo 1.*, Depurador, Implantação da Web e Depuração Remota do Visual Studio) | Não há suporte para isso. Recomendamos que você remova essas extensões da máquina virtual para continuar a migração, ou elas serão eliminadas automaticamente durante o processo de migração.
Computação | Diagnóstico de inicialização com o armazenamento Premium | Desabilite o recurso de Diagnóstico de Inicialização para as VMs antes de continuar com a migração. Você pode habilitar novamente o diagnóstico de inicialização na pilha do Gerenciador de Recursos após a migração ser concluída. Além disso, os blobs que estão sendo usados para captura de tela e logs seriais devem ser excluídos para que você não seja cobrado por eles.
Computação | Serviços de nuvem que contêm funções de trabalho/web | Não há suporte para esse recurso no momento.
Rede | Redes virtuais que contêm máquinas virtuais e funções de trabalho/web | Não há suporte para esse recurso no momento.
Serviço de aplicativo do Azure | Redes virtuais que contêm ambientes do Serviço de Aplicativo | Não há suporte para esse recurso no momento.
Azure HDInsight | Redes virtuais que contêm serviços do HDInsight | Não há suporte para esse recurso no momento.
Serviços de Ciclo de Vida do Microsoft Dynamics | Redes virtuais que contêm máquinas virtuais gerenciadas pelos Serviços de Ciclo de Vida do Microsoft Dynamics | Não há suporte para esse recurso no momento.
Computação | Extensões da Central de Segurança do Azure com uma Rede Virtual que tenha um gateway de VPN ou um gateway de ER com o servidor DNS local | A Central de Segurança do Azure instala automaticamente extensões em suas Máquinas Virtuais a fim de monitorar a segurança e emitir alertas. Essas extensões normalmente são instaladas automaticamente se a política da Central de Segurança do Azure for habilitada na assinatura. Como a migração do gateway não tem suporte no momento, e o gateway precisa ser excluído antes de prosseguir com a confirmação da migração, o acesso à internet para a conta de armazenamento da VM é perdido quando o gateway é excluído. A migração não continuará quando isso acontece, pois o blob de status do agente convidado não pode ser preenchido. Recomendamos a desabilitação da política da Central de Segurança do Azure na assinatura três horas antes de continuar com a migração.

## A experiência de migração

Antes de iniciar a experiência de migração, é recomendável fazer o seguinte:

- Certifique-se de que os recursos que você deseja migrar não usam nenhum recurso ou nenhuma configuração sem suporte. Normalmente a plataforma detecta esses problemas e gera um erro.
- Se você tiver VMs que não estão em uma rede virtual, elas serão interrompidas e desalocadas como parte da operação de preparação. Se você não quiser perder o endereço IP público, procure reservar o endereço IP antes de disparar a operação de preparação. No entanto, se as VMs estiverem em uma rede virtual, elas não são interrompidas nem desalocadas.
- Planeje a migração fora do horário comercial para acomodar falhas inesperadas que possam ocorrer durante a migração.
- Baixe a configuração atual das VMs usando o PowerShell, os comandos da CLI (interface de linha de comando) ou as APIs REST para facilitar a validação após a conclusão da etapa de preparação.
- Atualize os scripts de automação/operacionalização para lidar com o modelo de implantação do Gerenciador de Recursos antes de iniciar a migração. Se preferir, você poderá realizar operações GET quando os recursos estiverem no estado preparado.
- Avalie as políticas de RBAC configuradas nos recursos clássicos de IaaS e tenha um plano após a conclusão da migração.

O fluxo de trabalho de migração está descrito a seguir

![Captura de tela que mostra o fluxo de trabalho de migração](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] Todas as operações descritas nas seções a seguir são idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, recomenda-se que você repita a operação de preparação, anulação ou confirmação. A plataforma Azure tenta novamente a ação.

### Validar

A operação de validação é a primeira etapa do processo de migração. O objetivo desta etapa é analisar os dados em segundo plano para os recursos em migração e retornará êxito/falha caso eles possam fazer a migração.

Você seleciona a rede virtual ou o serviço hospedado (se não for uma rede virtual) que deseja preparar para a validação.

* Se o recurso não for capaz de fazer a migração, a plataforma Azure listará todos os motivos pelos quais não há suporte para a migração.

### Preparar

A operação de preparação é a segunda etapa do processo de migração. O objetivo dessa etapa é simular a transformação dos recursos de IaaS do clássico para os recursos do Gerenciador de Recursos e apresentar isso lado a lado para sua visualização.

Você seleciona a rede virtual ou o serviço hospedado (se não for uma rede virtual) que deseja preparar para a migração.

* Se o recurso não for capaz de migração, a plataforma Azure interrompe o processo de migração e lista o motivo pelo qual a operação de preparação falhou.
* Se o recurso for capaz de fazer migração, primeiro a plataforma Azure bloqueará as operações do plano de gerenciamento para os recursos em migração. Por exemplo, você não pode adicionar um disco de dados a uma VM em migração.

Em seguida, a plataforma Azure inicia a migração de metadados do clássico para o Resource Manager para os recursos em migração.

Assim que a operação de preparação for concluída, você tem a opção de visualizar os recursos no clássico e no Resource Manager. Para todos os serviços de nuvem no modelo de implantação clássica, a plataforma Azure cria um nome de grupo de recursos com o padrão `cloud-service-name>-migrated`.

>[AZURE.NOTE] Máquinas Virtuais que não estão em uma Rede Virtual clássica são interrompidas e desalocadas nesta fase da migração.

### Verificação (manual ou com scripts)

Nessa etapa de verificação, opcionalmente, você pode usar a configuração que baixou anteriormente para validar se a migração parece correta. Se preferir, você pode entrar no portal e verificar pontualmente as propriedades e os recursos para validar se os metadados de migração parecem corretos.

Se estiver migrando uma rede virtual, a maior parte da configuração das máquinas virtuais não é reiniciada. Para aplicativos nessas VMs, você pode validar que o aplicativo está ainda em execução.

Você pode testar seus scripts de monitoramento/automação e scripts operacionais para ver se as VMs estão funcionando conforme o esperado e se os scripts atualizados funcionam corretamente. Somente as operações GET terão suporte quando os recursos estiverem no estado preparado.

Não haverá uma janela de tempo definida antes da qual você precisará confirmar a migração. Você pode levar tanto tempo quanto desejar nesse estado. No entanto, o plano de gerenciamento é bloqueado para esses recursos até você anular ou confirmar.

Caso encontre problemas, sempre será possível anular a migração e voltar para o modelo de implantação clássica. Depois que você voltar, a plataforma Azure abrirá as operações do plano de gerenciamento nos recursos, para que você possa retomar as operações normais nessas VMs no modelo de implantação clássico.

### Anular

Anulação é uma etapa opcional que pode ser usada para reverter as alterações para o modelo de implantação clássico e interromper a migração.

>[AZURE.NOTE] Essa operação não pode ser executada depois que a operação de confirmação é disparada.

### Confirmar

Após a conclusão da validação, é possível confirmar a migração. Os recursos não aparecem mais no clássico e estão disponíveis apenas no modelo de implantação do Resource Manager. Os recursos migrados só podem ser gerenciados no novo portal.

>[AZURE.NOTE] Esta é uma operação idempotente. Se falhar, é recomendável que você repita a operação. Se a falha persistir, crie um tíquete de suporte ou uma postagem no fórum com uma marcação ClassicIaaSMigration em nosso [fórum sobre VMs](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=WAVirtualMachinesforWindows).

## Perguntas frequentes

**Este plano de migração afeta qualquer um de meus serviços existentes ou aplicativos executados em máquinas virtuais do Azure?**

Não. As VMs (clássicas) são serviços com suporte total na disponibilidade geral. É possível continuar usando esses recursos para expandir seu volume no Microsoft Azure.

**O que acontecerá com minhas VMs se eu não planejar a migração no futuro próximo?**

Não estamos preterindo as APIs clássicas e o modelo de recursos existentes. Queremos simplificar a migração, considerando os recursos avançados disponíveis no modelo de implantação do Gerenciador de Recursos. É altamente recomendável que você examine [alguns dos avanços feitos](virtual-machines-windows-compare-deployment-models.md) como parte do IaaS no Gerenciador de Recursos.

**O que este plano de migração significa para minhas ferramentas existentes?**

Atualizar suas ferramentas para o modelo de implantação do Gerenciador de Recursos é uma das alterações mais importantes que você precisa considerar em seus planos de migração.

**O tempo de inatividade do plano de gerenciamento será de quanto tempo?**

Isso depende do número de recursos que estão sendo migrados. Para implantações menores (algumas dezenas de VMs), a migração inteira deverá levar menos de uma hora. Para implantações de larga escala (centenas de VMs), a migração poderá levar algumas horas.

**Poderei reverter depois que meus recursos de migração forem confirmados no Gerenciador de Recursos?**

É possível anular a migração, desde que os recursos estejam no estado preparado. Não há suporte para reversão depois que os recursos forem migrados com êxito por meio da operação de confirmação.

**Poderei reverter minha migração se a operação de confirmação falhar?**

Não será possível anular a migração se a operação de confirmação falhar. Todas as operações de migração, incluindo a operação de confirmação, são idempotentes. Portanto, recomendamos que você repita a operação após um curto período. Se a falha ainda persistir, crie um tíquete de suporte ou uma postagem no fórum com a marcação ClassicIaaSMigration em nosso [fórum sobre VMs](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=WAVirtualMachinesforWindows).

**Será necessário comprar outro circuito de Rota Expressa se eu precisar aproveitar usar a IaaS no Gerenciador de Recursos?**

Não. Habilitamos recentemente [a movimentação dos circuitos da ExpressRoute do clássico para o modelo de implantação do Gerenciador de Recursos](../expressroute/expressroute-move.md). Você não precisará comprar um novo circuito de Rota Expressa se já tiver um.

**E se eu tiver configurado políticas de Controle de Acesso Baseado em Função para meus recursos clássicos de IaaS?**

Durante a migração, os recursos se transformam do clássico para o Gerenciador de Recursos. Portanto, é recomendável planejar as atualizações da política de RBAC que precisam ocorrer após a migração.

**E se eu estiver usando o Azure Site Recovery ou o Backup do Azure hoje?**

Para migrar sua máquina virtual habilitada para backup, veja [Fiz backup de minhas VMs clássicas no cofre de backup. Agora quero migrar minhas VMs do modo clássico para o modo do Gerenciador de Recursos. Como fazer backup delas no cofre dos serviços de recuperação?](../backup/backup-azure-backup-ibiza-faq.md#i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode-how-can-i-backup-them-in-recovery-services-vault)

**Posso validar minha assinatura ou meus recursos para ver se eles podem fazer a migração?**

Sim. Na opção de migração com suporte de plataforma, a primeira etapa para preparar a migração é validar se os recursos podem fazer a migração. Caso a operação de validação falhe, você recebe todas as mensagens relativas a todos os motivos pelos quais a migração não pode ser concluída.

**O que acontecerá se eu encontrar um erro de cota ao preparar os recursos de IaaS para migração?**

É recomendável anular a migração e, em seguida, registrar uma solicitação de suporte para aumentar as cotas na região onde você está migrando as VMs. Depois que a solicitação de cota for aprovada, você poderá iniciar a execução das etapas de migração novamente.

**Como faço para relatar um problema?**

Poste suas perguntas e dúvidas sobre migração em nosso [fórum sobre VMs](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=WAVirtualMachinesforWindows), com a palavra-chave ClassicIaaSMigration. É recomendável postar todas as suas dúvidas neste fórum. Caso você tenha um contrato de suporte, será possível registrar um tíquete de suporte.

**E se eu não gostar dos nomes dos recursos que a plataforma escolheu durante a migração?**

Todos os recursos para os quais você fornecer nomes explicitamente no modelo de implantação clássica são retidos durante a migração. Em alguns casos, novos recursos são criados. Por exemplo: uma interface de rede é criada para cada VM. No momento, não há suporte para a capacidade de controlar os nomes dos novos recursos criados durante a migração. Registre seus votos para esse recurso no [fórum de comentários do Azure](http://feedback.azure.com).

**Recebi uma mensagem *"A VM está informando o status geral do agente como Não está pronto. Portanto, a VM não pode ser migrada. Certifique-se de que o Agente da VM esteja informando o status geral do agente como Pronto"* ou *"A VM contém uma Extensão cujo status não está sendo informado. Portanto, esta VM não pode ser migrada."***

Essa mensagem é recebida quando a VM não tem conectividade de saída com a Internet. O agente de VM utiliza conectividade de saída para acessar a conta de armazenamento do Azure para atualizar o status do agente a cada cinco minutos.


## Próximas etapas
Agora que você compreende a migração de recursos clássicos de IaaS para o Gerenciador de Recursos, poderá começar a migrar os recursos.

- [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Clonar uma máquina virtual clássica para o Azure Resource Manager usando scripts da comunidade do PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0928_2016-->