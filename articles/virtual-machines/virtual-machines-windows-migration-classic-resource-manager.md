<properties
	pageTitle="Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager | Microsoft Azure"
	description="Este artigo apresenta a migração de recursos com suporte da plataforma do clássico para o Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager

Faz quase um ano desde que anunciamos o suporte a máquinas virtuais no Azure Resource Manager. Aqui você pode ler mais sobre os aprimoramentos e recursos adicionais com suporte. Além disso, fornecemos diretrizes sobre como conectar-se da melhor forma e ter recursos dos dois modelos de implantação coexistindo em sua assinatura usando gateways site a site de rede virtual. Neste artigo, descrevemos como estamos possibilitando a migração de recursos de IaaS (infraestrutura como serviço) do clássico para o Gerenciador de Recursos.

## Meta de migração

Com o lançamento do novo modelo, você pode implantar, gerenciar e monitorar os serviços relacionados em um grupo de recursos. O Gerenciador de Recursos possibilita implantar aplicativos complexos por meio de modelos, configurar máquinas virtuais usando extensões de VM e incorporar o gerenciamento de acesso e a marcação. Ele também inclui implantação paralela e escalonável para máquinas virtuais em conjuntos de disponibilidade. Além disso, o novo modelo oferece gerenciamento de ciclo de vida de computação, rede e armazenamento de maneira independente. Por fim, há um enfoque para habilitar a segurança por padrão com a imposição de máquinas virtuais em uma rede virtual.

Há suporte para quase todos os recursos do modelo de implantação clássica referentes a computação, rede e armazenamento no Azure Resource Manager. Devido a essa nova funcionalidade e à crescente base de implantação no Azure Resource Manager, queremos que os clientes possam migrar as implantações existentes no modelo de implantação clássica.

## Alterações à automação e às ferramentas após a migração

Como parte da migração dos recursos do modelo clássico para o modelo do Gerenciador de Recursos, você terá de atualizar sua automação ou ferramentas existentes para garantir que elas continuem funcionando após a migração.

## Significado da migração de recursos de IaaS do clássico para o Gerenciador de Recursos

Antes de analisarmos os detalhes, gostaríamos de explicar rapidamente a diferença entre as operações do plano de dados e do plano de gerenciamento dos recursos de IaaS. É crucial entender essas diferenças, pois isso explica como estamos planejando dar suporte à migração.

- O *plano de gerenciamento* descreve as chamadas que vão para o plano de gerenciamento ou para a API para modificar recursos. Por exemplo, operações como a criação de uma VM, reinicialização de uma VM e atualização de uma rede virtual com uma nova sub-rede para gerenciar os recursos em execução. Elas não afetam diretamente a conexão com as instâncias.
- O *plano de dados* (aplicativo) descreve o tempo de execução do próprio aplicativo e envolve a interação com instâncias que não passam pela API do Azure. Acessar seu site ou efetuar pull de dados de uma instância do SQL Server ou servidor MongoDB em execução seriam considerados um plano de dados ou uma interação com o aplicativo. Copiar um blob de uma conta de armazenamento e acessar um endereço IP público para RDP ou SSH na máquina virtual também são planos de dados. Essas operações mantêm o aplicativo em execução entre computação, rede e armazenamento.

>[AZURE.NOTE] Em alguns cenários de migração, vamos parar, desalocar e reiniciar as máquinas virtuais. Isso acarretará em um curto tempo de inatividade do plano de dados.

## Escopos de migração com suporte

Há três escopos de migração destinados principalmente à computação, à rede e ao armazenamento.

### Migração de máquinas virtuais (não em uma rede virtual)

No modelo de implantação do Gerenciador de Recursos, impomos a segurança de seus aplicativos por padrão. Todas as VMs precisam estar em uma rede virtual no modelo do Gerenciador de Recursos. Portanto, reiniciaremos (`Stop`, `Deallocate` e `Start`) as VMs como parte da migração. Você tem duas opções para as redes virtuais:

- Você pode solicitar que a plataforma crie uma nova rede virtual e migre a máquina virtual para a nova rede virtual.
- Você pode migrar a máquina virtual para uma rede virtual existente no Gerenciador de Recursos.

>[AZURE.NOTE] Nesse escopo de migração, as operações do “plano de gerenciamento” e do “plano de dados” podem não ser permitidas por determinado período durante a migração.

### Migração de máquinas virtuais (em uma rede virtual)

Nesse escopo, para a maioria das configurações de VM, estamos migrando apenas os metadados entre o modelo de implantação clássica e o modelo de implantação do Gerenciador de Recursos. As VMs subjacentes estão em execução no mesmo hardware, na mesma rede e com o mesmo armazenamento. Portanto, quando nos referimos à migração dos metadados do clássico para o Gerenciador de Recursos, as operações do “plano de gerenciamento” podem não ser permitidas por determinado período durante a migração. No entanto, o plano de dados continuará funcionando. Ou seja, os aplicativos em execução nas VMs (clássicas) não incorrerão em tempo de inatividade durante a migração.

No momento, não há suporte para as configurações a seguir. Se adicionarmos suporte a elas no futuro, algumas VMs nessa configuração poderão incorrer em tempo de inatividade (passaremos por operações de parar, desalocar e reiniciar a VM).

-	Você tem mais de um conjunto de disponibilidade em um único serviço de nuvem.
-	Você tem um ou mais conjuntos de disponibilidade e as VMs que não estão em um conjunto de disponibilidade em um único serviço de nuvem.

>[AZURE.NOTE] Nesse escopo de migração, o plano de gerenciamento pode não ser permitido por determinado período durante a migração. Para algumas configurações, conforme descrito acima, isso incorrerá em tempo de inatividade do plano de dados.

### Migração das contas de armazenamento

Para permitir uma migração perfeita, habilitamos a funcionalidade de implantar VMs do Gerenciador de Recursos em uma conta de armazenamento clássico. Com essa funcionalidade, recursos de computação e rede podem e devem ser migrados independentemente de contas de armazenamento. Depois de migrar suas Máquinas Virtuais e a Rede Virtual, você precisará migrar suas contas de armazenamento para concluir o processo de migração.

>[AZURE.NOTE] O modelo de implantação do Resource Manager não tem o conceito de discos e imagens clássicas. Quando a conta de armazenamento é migrada, eles não ficarão visíveis na pilha do Resource Manager, mas os VHDs de backup permanecerão na conta de armazenamento.

## Recursos e configurações sem suporte

No momento, não há suporte para alguns recursos e configurações. As seções a seguir descrevem nossas recomendações a respeito deles.

### Recursos sem suporte

Atualmente, não há suporte para os seguintes recursos. Se preferir, você pode remover essas configurações, migrar as VMs e, em seguida, habilitar as configurações novamente no modelo de implantação do Gerenciador de Recursos.

Provedor de recursos | Recurso
---------- | ------------
Computação | Discos de máquina virtual não associados.
Computação | Imagens de máquinas virtuais.
Rede | IPs reservados não associados (se não anexados a uma VM). Há suporte para IPs reservados anexados a VMs.
Rede | Grupos de segurança de rede não associados (se não anexados a uma rede virtual ou a uma interface de rede). Há suporte para NSGs referenciados pelas redes virtuais.
Rede | ACLs de ponto de extremidade.
Rede | Gateways de rede virtual (site a site, Rota Expressa do Azure, ponto a site).

### Configurações sem suporte

Atualmente, não há suporte para as seguintes configurações.

O Barramento de | Configuração | Recomendações
---------- | ------------ | ------------
Gerenciador de Recursos | RBAC (Controle de Acesso Baseado em Função) para recursos clássicos | Como o URI dos recursos é modificado após a migração, é recomendável planejar as atualizações da política de RBAC que precisam ocorrer após a migração.
Computação | Várias sub-redes associadas a uma VM | É necessário atualizar a configuração de sub-rede para fazer referência apenas às sub-redes.
Computação | Máquinas virtuais que pertencem a uma rede virtual, mas que não têm uma sub-rede explícita atribuída | Opcionalmente, você pode excluir a VM.
Computação | Máquinas virtuais que têm alertas e políticas de Escala Automática | No momento, a migração prosseguirá e essas configurações serão descartadas. Portanto, é altamente recomendável que você avalie seu ambiente antes de fazer a migração. Se preferir, você pode redefinir as configurações de alerta após a conclusão da migração.
Computação | Extensões de VM do XML (Depurador, Implantação da Web e Depuração Remota do Visual Studio) | Não há suporte para isso. Recomendamos a remoção dessas extensões da máquina virtual para continuar a migração.
Computação | Diagnóstico de inicialização com o armazenamento Premium | Desabilite o recurso de Diagnóstico de Inicialização para as VMs antes de continuar com a migração. Você pode habilitar novamente o diagnóstico de inicialização na pilha do Gerenciador de Recursos após a migração ser concluída. Além disso, os blobs que estão sendo usados para captura de tela e logs seriais devem ser excluídos para que você não seja cobrado por eles.
Computação | Serviços de nuvem que contêm funções de trabalho/web | Não há suporte para esse recurso no momento.
Rede | Redes virtuais que contêm máquinas virtuais e funções de trabalho/web | Não há suporte para esse recurso no momento.
Serviço de aplicativo do Azure | Redes virtuais que contêm ambientes do Serviço de Aplicativo | Não há suporte para esse recurso no momento.
Azure HDInsight | Redes virtuais que contêm serviços do HDInsight | Não há suporte para esse recurso no momento.
Serviços de Ciclo de Vida do Microsoft Dynamics | Redes virtuais que contêm máquinas virtuais gerenciadas pelos Serviços de Ciclo de Vida do Microsoft Dynamics | Não há suporte para esse recurso no momento.

## A experiência de migração

Antes de iniciar a experiência de migração, é altamente recomendável realizar o seguinte:

- Certifique-se de que os recursos que você deseja migrar não usam nenhum recurso ou nenhuma configuração sem suporte. Na maioria dos casos, a plataforma detecta esses problemas e gera um erro.
- Se você tiver VMs que não estão em uma rede virtual, elas serão interrompidas e desalocadas como parte da operação de preparação. Se você não quiser perder o endereço IP público, procure reservar o endereço IP antes de disparar a operação de preparação. No entanto, se as VMs estiverem em uma rede virtual, elas não serão interrompidas nem desalocadas.
- Planeje a migração fora do horário comercial para acomodar falhas inesperadas que possam ocorrer durante a migração.
- Baixe a configuração atual das VMs usando o PowerShell, os comandos da CLI (interface de linha de comando) ou as APIs REST para facilitar a validação após a conclusão da etapa de preparação.
- Atualize os scripts de automação/operacionalização para lidar com o modelo de implantação do Gerenciador de Recursos antes de iniciar a migração. Se preferir, você poderá realizar operações GET quando os recursos estiverem no estado preparado.
- Avalie as políticas de RBAC configuradas nos recursos clássicos de IaaS e tenha um plano após a conclusão da migração.

O fluxo de trabalho de migração está descrito a seguir

![Captura de tela que mostra o fluxo de trabalho de migração](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] Todas as operações descritas nas seções a seguir são idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma repetirá a ação.

### Validar

A operação de validação é a primeira etapa do processo de migração. O objetivo desta etapa é analisar os dados em segundo plano para os recursos em migração e retornará êxito/falha caso eles possam fazer a migração.

Você selecionará a rede virtual ou o serviço hospedado (se não for uma rede virtual) que deseja preparar para a validação.

* Se o recurso não for capaz de fazer a migração, a plataforma listará todos os motivos pelos quais não há suporte para a migração.

### Preparar

A operação de preparação é a segunda etapa do processo de migração. O objetivo dessa etapa é simular a transformação dos recursos de IaaS do clássico para os recursos do Gerenciador de Recursos e apresentar isso lado a lado para sua visualização.

Você selecionará a rede virtual ou o serviço hospedado (se não for uma rede virtual) que deseja preparar para a migração.

* Se o recurso não for capaz de migração, a plataforma listará o processo de parada da migração e o motivo pelo qual a operação de preparação falhou.
* Se o recurso for capaz de fazer migração, primeiro a plataforma bloqueará as operações do plano de gerenciamento para os recursos em migração. Por exemplo, você não poderá adicionar um disco de dados a uma VM em migração.

Em seguida, a plataforma iniciará a migração de metadados do clássico para o Gerenciador de Recursos para os recursos em migração.

Assim que a operação de preparação for concluída, você terá a opção de visualizar os recursos no clássico e no Gerenciador de Recursos. Para todos os serviço de nuvem no modelo de implantação clássica, criaremos um nome de grupo de recursos que tem o padrão `cloud-service-name>-migrated`.

>[AZURE.NOTE] Máquinas Virtuais que não estão em uma Rede Virtual clássica serão interrompidas e desalocadas nesta fase da migração.

### Verificação (manual ou com scripts)

Nessa etapa de verificação, opcionalmente, você pode usar a configuração que baixou anteriormente para validar se a migração parece correta. Se preferir, você pode entrar no portal e verificar pontualmente as propriedades e os recursos para validar se os metadados de migração parecem corretos.

Se estiver migrando uma rede virtual, a maior parte da configuração das máquinas virtuais não será reiniciada. Para aplicativos nessas VMs, você pode validar que o aplicativo está ainda em execução.

Você pode testar seus scripts de monitoramento/automação e scripts operacionais para ver se as VMs estão funcionando conforme o esperado e se os scripts atualizados funcionam corretamente. Observe que somente as operações GET terão suporte quando os recursos estiverem no estado preparado.

Não haverá uma janela de tempo definida antes da qual você precisará confirmar a migração. Você pode levar tanto tempo quanto desejar nesse estado. No entanto, observe que o plano de gerenciamento será bloqueado para esses recursos até você anular ou confirmar.

Caso encontre problemas, sempre será possível anular a migração e voltar para o modelo de implantação clássica. Depois que você voltar, abriremos as operações do plano de gerenciamento nos recursos, para que você possa retomar as operações normais nessas VMs no modelo de implantação clássico.

### Anular

Anulação é uma etapa opcional que pode ser usada para reverter as alterações para o modelo de implantação clássico e interromper a migração.

>[AZURE.NOTE] Essa operação não pode ser executada depois que a operação de confirmação é disparada.

### Confirmar

Após a conclusão da validação, é possível confirmar a migração. Os recursos não aparecerão mais no clássico e estarão disponíveis apenas no modelo de implantação do Gerenciador de Recursos. Isso também significa que os recursos migrados só poderão ser gerenciados no novo portal.

>[AZURE.NOTE] Esta é uma operação idempotente. Se ela falhar, recomendamos que você repita isso algumas vezes. Se a falha persistir, crie um tíquete de suporte ou uma postagem no fórum com uma marcação ClassicIaaSMigration em nosso [fórum sobre VMs](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=WAVirtualMachinesforWindows).

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

Não. Recentemente, habilitamos a [coexistência de um circuito de Rota Expressa no clássico e no Gerenciador de Recursos](../expressroute/expressroute-howto-coexist-resource-manager.md). Você não precisará comprar um novo circuito de Rota Expressa se já tiver um.

**E se eu tiver configurado políticas de Controle de Acesso Baseado em Função para meus recursos clássicos de IaaS?**

Durante a migração, os recursos se transformam do clássico para o Gerenciador de Recursos. Portanto, é recomendável planejar as atualizações da política de RBAC que precisam ocorrer após a migração.

**E se eu estiver usando o Azure Site Recovery ou o Backup do Azure hoje?**

O suporte ao Azure Site Recovery e ao Backup do Azure para VMs no Gerenciador de Recursos foi adicionado recentemente. Estamos trabalhando para habilitar a funcionalidade de dar suporte à migração de VMs no Gerenciador de Recursos também. No momento, é recomendável não executar a migração caso você esteja usando essas funcionalidades.

**Posso validar minha assinatura ou meus recursos para ver se eles podem fazer a migração?**

Sim. Na opção de migração com suporte de plataforma, a primeira etapa para preparar a migração é validar se os recursos podem fazer a migração. Caso a operação de validação falhe, você receberá todas as mensagens relativas a todos os motivos pelos quais a migração não pode ser concluída.

**O que acontecerá se eu encontrar um erro de cota ao preparar os recursos de IaaS para migração?**

É recomendável anular a migração e, em seguida, registrar uma solicitação de suporte para aumentar as cotas na região onde você está migrando as VMs. Depois que a solicitação de cota for aprovada, você poderá iniciar a execução das etapas de migração novamente.

**Como faço para relatar um problema?**

Poste suas perguntas e dúvidas sobre migração em nosso [fórum sobre VMs](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=WAVirtualMachinesforWindows), com a palavra-chave ClassicIaaSMigration. É recomendável postar todas as suas dúvidas neste fórum. Caso você tenha um contrato de suporte, será possível registrar um tíquete de suporte.

**E se eu não gostar dos nomes dos recursos que a plataforma escolheu durante a migração?**

Todos os recursos para os quais você fornecer nomes explicitamente no modelo de implantação clássica serão retidos durante a migração. Em alguns casos, novos recursos serão criados. Por exemplo: uma interface de rede será criada para cada VM. No momento, não há suporte para a capacidade de controlar os nomes dos novos recursos criados durante a migração. Registre seus votos para esse recurso no [fórum de comentários do Azure](http://feedback.azure.com).

**Recebi uma mensagem *"A VM está informando o status geral do agente como Não está pronto. Portanto, a VM não pode ser migrada. Certifique-se de que o Agente da VM esteja informando o status geral do agente como Pronto"* ou *"A VM contém uma Extensão cujo status não está sendo informado. Portanto, esta VM não pode ser migrada."***

Essa mensagem é recebida quando a VM não tem conectividade de saída com a Internet. O agente de VM utiliza conectividade de saída para acessar a conta de armazenamento do Azure para atualizar o status do agente a cada 5 minutos.

## Próximas etapas
Agora que você compreende a migração de recursos clássicos de IaaS para o Gerenciador de Recursos, poderá começar a migrar os recursos.

- [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Clonar uma máquina virtual clássica para o Azure Resource Manager usando scripts da comunidade do PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0720_2016-->