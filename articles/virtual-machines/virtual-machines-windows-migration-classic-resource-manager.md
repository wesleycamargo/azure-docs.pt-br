<properties
	pageTitle="Migração de recursos de IaaS com suporte da plataforma do Clássico para o Azure Resource Manager"
	description="Este artigo apresenta as funcionalidades de serviço de migração com suporte da plataforma, do Gerenciamento de Serviços, para o Azure Resource Manager"
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

# Migração de recursos de IaaS com suporte da plataforma do Clássico para o Azure Resource Manager

Faz quase um ano desde que anunciamos o suporte a Máquinas Virtuais no Azure Resource Manager. Aqui você pode ler mais sobre os aprimoramentos e recursos adicionais com suporte. Além disso, também fornecemos orientação sobre como conectar-se da melhor forma e ter recursos dos dois modelos de implantação coexistindo em sua assinatura usando Gateways Site a Site de Rede Virtual. Neste artigo, gostaríamos de mostrar como estamos habilitando a migração de recursos de IaaS do Clássico para o Gerenciador de Recursos.

## Qual é nosso objetivo com a migração?

Estamos muito entusiasmados com a capacidade e a funcionalidade oferecidas pela nova experiência e as APIs disponíveis em Máquinas Virtuais. Acreditamos que isso realmente mudará a forma como você pode usar a nuvem de diversas maneiras. Com o lançamento do novo modelo, permitimos que você implante, gerencie e monitore os serviços relacionados em um grupo de recursos. O Gerenciador de Recursos permite implantar aplicativos complexos usando modelos, configura máquinas virtuais usando extensões de VM e incorpora o gerenciamento de acesso e a marcação. Ele também inclui implantação paralela e escalonável para máquinas virtuais em conjuntos de disponibilidade. Além disso, o novo modelo oferece gerenciamento de ciclo de vida de computação, rede e armazenamento independentemente. Por fim, há um enfoque para habilitar a segurança por padrão com a imposição de Máquinas Virtuais em uma Rede Virtual.

Do ponto de vista dos recursos, quase todos os recursos do modelo de implantação Clássico têm suporte para computação, rede e armazenamento no Azure Resource Manager, com algumas exceções que estamos nos empenhando para concluir nos próximos meses. Além disso, estamos aprimorando continuamente a experiência do usuário e adicionando recursos ao portal do Azure para vincular as experiências.

Devido a essa nova funcionalidade e à crescente base de implantação no novo Azure Resource Manager, queremos habilitar os clientes a migrar as implantações existentes no Clássico.

>[AZURE.NOTE] Com este comunicado, estamos lançando a visualização pública do serviço de migração. Durante a visualização pública, nós só recomendamos a migração de suas cargas de trabalho que não sejam de produção em sua assinatura do Azure.

## Alterações na automação e nas ferramentas após a migração

Observe que uma das alterações importantes que você precisará fazer como parte da migração de seus recursos do modelo Clássico para o modelo do Gerenciador de Recursos seriam as atualizações em sua automação ou ferramentas existente para garantir que continuem a funcionar mesmo depois que os recursos forem migrados.

## O que significa a migração de recursos de IaaS do Clássico para o Gerenciador de Recursos?

Antes de analisarmos os detalhes, gostaríamos de explicar rapidamente a diferença entre as operações de Plano de Dados e Plano de Gerenciamento dos recursos de IaaS. É crucial entender essas diferenças, pois isso explica como estamos planejando dar suporte à migração.

- Plano de Gerenciamento -‒descreve as chamadas que vão para o plano de gerenciamento ou para a API para modificar recursos. Por exemplo: criar uma VM, reiniciar uma VM, atualizar uma Rede Virtual com uma nova sub-rede etc. Todas essas operações gerenciam os recursos em execução, mas não afetam diretamente a conexão às instâncias.
- Plano de Dados (Aplicativo) – descreve o "tempo de execução" do próprio aplicativo e envolve a interação com instâncias que não passam pela API do Azure. Acessar seu site ou obter dados de um SQL Server ou servidor mongoDB em execução seriam considerados um plano de dados ou uma interação com o aplicativo. Copiar um blob de uma conta de armazenamento e acessar um endereço IP público para RDP ou SSH na Máquina Virtual também são planos de dados. Essas operações mantêm o aplicativo em execução entre computação, rede e armazenamento.

>[AZURE.NOTE] Em alguns cenários de migração (há mais detalhes nas configurações sem suporte), vamos deixar de desalocar e reiniciar as máquinas virtuais, o que causará um breve tempo de inatividade do plano de dados.

## Quais são os escopos de migração com suporte?

Durante a visualização pública, estamos oferecendo dois escopos de migração direcionados principalmente a computação e rede. O suporte para a migração de contas de armazenamento está planejado e será lançado em breve. No entanto, para permitir a migração perfeita, habilitamos as contas de armazenamento clássico para conter discos para VMs do Resource Manager. Você pode obter mais detalhes sobre isso abaixo.

### Migração de máquinas virtuais (não em uma rede virtual)

No modelo de implantação do Gerenciador de Recursos, impomos a segurança de seus aplicativos por padrão. Portanto, todas as VMs precisam estar em uma rede virtual no modelo do Gerenciador de Recursos. Portanto, reiniciaremos (Parar, Desalocar e Iniciar) as VMs como parte da migração. Você terá algumas opções em relação às redes virtuais:
- Você pode solicitar que a plataforma crie uma nova rede virtual e migre a máquina virtual para a nova rede virtual (ou)
- Migrar a máquina virtual para uma rede virtual existente no Gerenciador de Recursos

- Você pode solicitar que a plataforma crie uma nova rede virtual e migre a VM para a nova rede virtual (ou)
- Migrar a VM para uma rede virtual existente no Gerenciador de Recursos

>[AZURE.NOTE] Nesse escopo da migração, as operações do 'plano de gerenciamento' e do 'plano de dados' podem não ser permitidas por determinado período de tempo durante a migração.

### Migração de Máquinas Virtuais (em uma Rede Virtual)

Nesse escopo, para a maioria das configurações de VM, estamos migrando apenas os metadados entre o Clássico e o Gerenciador de Recursos. As VMs subjacentes estão em execução no mesmo hardware, na mesma rede e com o mesmo armazenamento. Portanto, quando nos referimos à migração dos metadados do Clássico para o Gerenciador de Recursos, as operações do 'plano de gerenciamento' podem não ser autorizadas por determinado período de tempo durante a migração. No entanto, o plano de dados continua a funcionar, ou seja, os aplicativos em execução nas VMs (Clássico) não terão um tempo de inatividade durante a migração.

No momento, não há suporte para as configurações a seguir. Quando adicionarmos suporte a elas no futuro, algumas VMs nessa configuração poderão ter um tempo de inatividade (deixar de desalocar e reiniciar):

-	Se você tiver mais de um conjunto de disponibilidade em um único Serviço de Nuvem
-	Se você tiver 'um ou mais conjuntos de disponibilidade 'e 'VMs que não estão em um conjunto de disponibilidade' em um único serviço de nuvem

>[AZURE.NOTE] Nesse escopo de migração, o 'plano de gerenciamento' talvez não tenha permissão por determinado período de tempo durante a migração. Para certas configurações especiais, como descrito acima, haverá um tempo de inatividade do 'plano de dados'.

### Contas de armazenamento e migração

Atualmente, não há suporte à migração de contas de armazenamento para esta Visualização Pública. No entanto, o suporte para a migração de contas de armazenamento está planejado e deve ser lançado em breve. Há dois aspectos importantes quanto à migração e seu comportamento de conta de armazenamento.

- Habilitar VMs do Gerenciador de Recursos para serem implantadas na conta de armazenamento Clássica para permitir a migração perfeita; habilitamos a capacidade de implantar VMs do Gerenciador de Recursos em uma conta de armazenamento Clássico. Com esse recurso, recursos de computação e rede podem ser migrados independentemente de contas de armazenamento.
- Prática recomendada para migração de conta de armazenamento
	- Quando houver suporte para a migração de contas de armazenamento, a plataforma imporá os recursos de computação e rede separadamente das contas de armazenamento para habilitar uma experiência perfeita.

## Configurações e recursos sem suporte

No momento, não damos suporte a um determinado conjunto de recursos e configurações. No entanto, estamos trabalhando na adição de suporte para eles. A seção a seguir aborda nossa recomendação e os planos relacionados.

### Recursos sem suporte

Os recursos listados a seguir não têm suporte para visualização pública. Opcionalmente, você pode remover essas configurações, migrar as VMs e, em seguida, habilitá-las novamente no modelo de implantação do Gerenciador de Recursos. O suporte à maioria desses recursos está planejado e será lançado assim que estiver disponível.

Provedor de recursos | Recurso
---------- | ------------
Computação | Diagnósticos de inicialização
Computação | Discos de VM não associados
Computação | Imagens de máquinas virtuais
Rede | IPs Reservados Não Associados [se não conectados a uma VM]. Há suporte para IPs reservados conectados a VMs.
Rede | Grupos de segurança de rede não associados [se não conectados a uma rede virtual ou a uma interface de rede]. Há suporte para NSGs referenciados por redes virtuais.
Rede | ACLs de ponto de extremidade
Rede | Gateways de redes virtuais (site a site, Rota Expressa, ponto a site)
Armazenamento | Contas de armazenamento

### Configurações sem suporte

As configurações listadas a seguir não têm suporte para visualização pública. Você pode obter nossas recomendações abaixo. O suporte a algumas dessas configurações está planejado e será lançado assim que estiver disponível.

O Barramento de | Configuração | Recomendações
---------- | ------------ | ------------
Gerenciador de Recursos | Controle de acesso baseado em função para recursos clássicos | Como o URI dos recursos é modificado após a migração. É recomendável que você planeje previamente as atualizações de política de RBAC que precisam ocorrer após a migração.
Computação | Várias sub-redes associadas a uma VM | Você deve atualizar a configuração de sub-rede para fazer referência somente à sub-rede.
Computação | Máquinas virtuais que pertencem a uma rede virtual, mas não têm uma sub-rede explícita atribuída. | Opcionalmente, você pode excluir a VM. O suporte a esse recurso está planejado atualmente.
Computação | Máquinas virtuais com alertas e políticas de dimensionamento automático | No momento, a migração prosseguirá e essas configurações serão descartadas. Então, é altamente recomendável avaliar seu ambiente antes de fazer a migração. Como alternativa, você também pode redefinir as configurações de alerta após a conclusão da migração.
Computação | Extensões de VM XML [Depurador de VS, WebDeploy e RemoteDebug] | Isso não terá suporte. Recomendamos que você remova essas extensões da máquina virtual para continuar a migração.
Computação | Serviços de nuvem que contêm funções de trabalho/Web | Isso não tem suporte atualmente e está no processo de planejamento.
Rede | Redes virtuais que contêm máquinas virtuais e funções de trabalho/Web | Isso não tem suporte atualmente e está no processo de planejamento.
Rede | Sub-redes que contêm espaços no nome | O suporte a esse recurso está planejado.
Serviços de Aplicativos | Redes virtuais que contêm ambientes de serviço de aplicativo | Isso não tem suporte atualmente e está no processo de planejamento.
Serviços do HDInsight | Redes virtuais que contêm serviços do HDInsight | Isso não tem suporte atualmente e está no processo de planejamento.
Serviços de ciclo de vida do Dynamics | Redes virtuais que contêm máquinas virtuais gerenciadas pelos Serviços de Ciclo de Vida do Dynamics | Isso não tem suporte atualmente e está no processo de planejamento.

## A experiência de migração

Antes de iniciar a experiência de migração, é altamente recomendável seguir algumas etapas

1. Verifique se os recursos que estão sendo planejados para migração não tiram proveito de recursos ou configurações sem suporte. Na maioria dos casos, a plataforma detecta esses problemas e gera um erro.
2. Se você tiver VMs que não estão em uma rede virtual, elas serão interrompidas e desalocadas como parte da operação de preparação. Se você não quiser perder o endereço IP público, procure reservar o endereço IP antes de disparar a preparação. No entanto, se as máquinas virtuais estiverem em uma rede virtual, não serão interrompidas e desalocadas.
3. O Azure recomenda que você não tente migrar recursos de produção nesse momento.
4. Planeje a migração fora do horário comercial para acomodar falhas inesperadas que possam ocorrer durante a migração.
5. Baixe a configuração atual das VMs usando o PowerShell, os comandos da CLI ou as APIs REST para facilitar a validação após a etapa de preparação ser concluída.
6. Atualize seus scripts de automação/operacionalização para lidar com o modelo de implantação do Gerenciador de Recursos antes de iniciar a migração. Além disso, você também poderá realizar operações GET opcionalmente quando os recursos estiverem no estado preparado.
7. Avalie as políticas de RBAC configuradas nos recursos de IaaS Clássico e tenha um plano depois que a migração for concluída.

Com o anúncio da visualização pública, adicionamos suporte para disparar a migração por meio de APIs REST, do PowerShell e da CLI do Azure. O suporte do Portal do Azure para a migração para que você visualize e percorra a migração desde o Clássico até a migração de ARM está atualmente planejado.

![Captura de tela que mostra o fluxo de trabalho de migração](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

1.	Preparar
	* Essa é a primeira etapa no processo de migração. O objetivo dessa etapa é simular a transformação dos recursos de IaaS do Clássico para recursos do Gerenciador de Recursos e apresentar isso lado a lado para que você possa visualizar. O fluxo detalhado de ações é descrito abaixo.
  * Você selecionará a rede virtual ou o serviço hospedado (se não for uma VNET) que deseja preparar para a migração.
  *	Primeiro, a plataforma sempre fará a análise de dados em segundo plano para o(s) recurso(s) em migração e retornará êxito/falha se o(s) recurso(s) for(em) capaz(es) de fazer a migração.
	*	Se o recurso não for capaz de fazer a migração, listaremos os motivos pelos quais não há suporte para a migração.
	* Se o recurso for capaz de fazer migração, primeiro a plataforma bloqueará as operações do plano de gerenciamento para o(s) recurso(s) em migração. Por exemplo: você não poderá adicionar um disco de dados a uma VM em migração.
  *	Em seguida, a plataforma iniciará a migração de metadados do Clássico para o Gerenciador de Recursos para o(s) recurso(s) em migração.  
  *	Uma vez concluída a operação de preparação, você terá a opção de visualizar os recursos no Clássico e no Gerenciador de Recursos. Para todos os serviço de nuvem no modelo de implantação Clássico, criaremos um nome de grupo de recursos que tem o padrão `cloud-service-name>-migrated`.

2.	Verificação manual ou com scripts
  * Nessa etapa, opcionalmente, você pode usar a configuração que baixou anteriormente para validar que a migração parece correta. Como alternativa, você também pode entrar no portal e verificar as propriedades e os recursos para validar que os metadados de migração parecem corretos.
	* Se você estiver migrando de uma rede virtual, a maior parte da configuração de máquinas virtuais não será reiniciada. Para aplicativos nessas VMs, você pode validar que o aplicativo está ainda em execução.
	* Você pode testar seus scripts de monitoramento/automação e scripts operacionais para ver se as VMs estão funcionando conforme o esperado e se os scripts atualizados funcionam corretamente. Observe que somente operações GET terão suporte quando os recursos estiverem no estado Preparado.
  * Não haverá uma janela de tempo definida antes da qual você precisa 'confirmar' a migração. Você pode levar tanto tempo quanto desejar nesse estado. No entanto, observe que o plano de gerenciamento será bloqueado para esses recursos até você 'anular' ou 'confirmar'.
  * Se encontrar problemas, você sempre poderá 'anular' a migração e voltar para o modelo de implantação 'Clássico'. Depois que você voltar, abriremos as operações do plano de gerenciamento nos recursos para que você possa retomar as operações normais nessas VMs no modelo de implantação Clássico.

3. Anular
  * Essa é uma etapa opcional que permite reverter as alterações para o modelo de implantação Clássico e anular a migração. Observe que essa operação não pode ser executada depois que você dispara a operação 'Confirmar'. 	

4.	Confirmar
  * Quando terminar a validação, você poderá 'confirmar' a migração, e os recursos não aparecerão mais no Clássico, mas estarão disponíveis apenas no modelo de implantação do Gerenciador de Recursos. Isso também significa que os recursos migrados só poderão ser gerenciados no novo portal.
	* Se essa operação falhar, recomendamos que você repita isso algumas vezes. Se a falha persistir, crie um tíquete de suporte ou crie uma postagem no fórum com uma marca ClassicIaaSMigration [aqui](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=WAVirtualMachinesforWindows)

>[AZURE.NOTE] Observe que todas as operações descritas abaixo são idempotentes. Caso encontre algo que não seja uma funcionalidade sem suporte ou um erro de configuração, é recomendável tentar novamente a operação de preparação, anulação ou confirmação e a plataforma tentará executar a ação novamente.


## Perguntas frequentes

**Este plano de migração afeta qualquer um de meus serviços existentes ou aplicativos executados em máquinas virtuais do Azure?**

Não. As VMs (Clássico) são serviços de GA com suporte completo, e você pode continuar a aproveitar esses recursos para expandir seu volume na nuvem do Microsoft Azure.

**O que acontecerá com minhas VMs se eu não planejar a migração no futuro próximo?**

Não estamos preterindo as APIs Clássicas e o modelo de recursos existentes. Queremos tornar a migração extremamente fácil, considerando os recursos avançados disponíveis no modelo de implantação do Gerenciador de Recursos. Portanto, é altamente recomendável que você examine alguns dos avanços feitos como parte do IaaS no Gerenciador de Recursos [aqui](virtual-machines-windows-compare-deployment-models.md).

**O que este plano de migração significa para minhas ferramentas existentes?**

Atualizar suas ferramentas para o modelo de implantação do Gerenciador de Recursos seria uma das alterações mais importantes que você precisa considerar em seus planos de migração.

**O tempo de inatividade do plano de gerenciamento será de quanto tempo?**

Isso depende do número de recursos que estão sendo migrados. Para implantações menores (algumas dezenas de VMs), a migração inteira de ponta a ponta deve levar menos de uma hora. No entanto, para implantações em grande escala (centenas de VMs), ela pode ser executada em poucas horas. Como o serviço está em visualização pública, é altamente recomendável que você execute isso em sua assinatura de Desenvolvimento ou Teste para avaliar o impacto.

**Poderei reverter depois que meus recursos de migração forem confirmados no Gerenciador de Recursos?**

Você pode anular sua migração, desde que os recursos estejam no estado 'Preparado'. Não há suporte para reversão após os recursos serem migrados com êxito usando a operação de Confirmação.

**Poderei reverter minha migração se as operações de confirmação falharem?**

Não será possível anular a migração se a operação de confirmação falhar. Todas as operações de migração, incluindo a operação de confirmação, são idempotentes. Portanto, recomendamos que você repita a operação após um curto período. Se a falha ainda persistir, crie um tíquete de suporte ou crie uma postagem no fórum com a marca ClassicIaaSMigration [aqui](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=WAVirtualMachinesforWindows)

**Será necessário comprar outro circuito de Rota Expressa se eu precisar aproveitar o IaaS no Gerenciador de Recursos?**

Não. Recentemente, habilitamos a [coexistência de um Circuito de Rota Expressa no Clássico e no Gerenciador de Recursos](../expressroute/expressroute-howto-coexist-resource-manager.md). Você não precisará comprar um novo circuito de Rota Expressa se já tiver um.

**Há um roteiro para quando vocês adicionarão os cenários sem suporte à lista de migração?**

No momento, estamos lidando com o primeiro conjunto de cenários no S1 do AC de 2016. Continuaremos a adicionar suporte aos recursos sem suporte após o primeiro lançamento.

**E se eu tiver configurado políticas de controle de acesso com base em função para meus recursos de IaaS Clássico?**

Durante a migração, os recursos se transformam do Clássico para o Gerenciador de Recursos. Portanto, é recomendável que você planeje previamente as atualizações de política de RBAC que precisam ocorrer após a migração.

**E se eu estiver usando os serviços Azure Site Recovery ou Backup do Azure hoje?**

O suporte ao Azure Site Recovery e ao Backup do Azure para VMs no Gerenciador de Recursos foi adicionado recentemente. Estamos trabalhando ativamente com as equipes para habilitar a capacidade de dar suporte à migração de VMs no Gerenciador de Recursos também. No momento, é recomendável não executar a migração se você estiver utilizando essas funcionalidades.

**Posso validar minha assinatura ou meus recursos para ver se podem fazer a migração?**

No momento, a operação de preparação faz uma validação implícita para os recursos que estão sendo preparados para a migração. Na opção de migração com suporte de plataforma, a primeira etapa para preparar a migração é validar se os recursos são capazes de fazer a migração. Se a validação falhar, os recursos não serão tocados. No entanto, também pretendemos lançar uma nova ação de validação que tornará tudo muito mais simples.

**O que acontecerá se eu encontrar um erro de cota ao preparar os recursos de IaaS para migração?**

Recomendamos que você anule sua migração. Em seguida, registre em log uma solicitação de suporte para aumentar as cotas na região em que você está migrando as VMs. Depois que a solicitação de cota for aprovada, inicie a execução das etapas de migração novamente.

**Como relatar um problema se eu não tiver um contrato de suporte?**

Poste suas perguntas e questões de migração em nosso Fórum de VMs com a palavra-chave ClassicIaaSMigration [aqui](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=WAVirtualMachinesforWindows). É recomendável postar todas as suas perguntas nesse fórum. No entanto, se você tiver um contrato de suporte, fique à vontade para também registrar em log um tíquete de suporte.

**E se eu não gostar dos nomes que a plataforma escolheu para meus grupos de recursos durante a migração?**

Em breve, anunciaremos o suporte para mover recursos entre grupos de recursos. Depois que esse recurso tiver suporte, você poderá mover os recursos para o grupo de recursos de sua escolha.

**E se eu não gostar dos nomes dos recursos que a plataforma escolheu durante a migração?**

Para todos os recursos para os quais você fornecer nomes explicitamente no modelo de implantação Clássico, eles serão mantidos durante a migração. Em alguns casos, novos recursos serão criados. Por exemplo: uma interface de rede será criada para cada VM. No momento, não há suporte para a capacidade de controlar os nomes dos novos recursos criados durante a migração. Registre seus votos nesse recurso [aqui](http://feedback.azure.com)


## Próximas etapas
Agora que você compreende a migração de recursos de IaaS do Clássico para o Resource Manager, poderá começar a migrar os recursos.

- [Technical Deep Dive on Platform supported migration from Classic to Azure Resource Manager (Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Azure Resource Manager)](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Usar o PowerShell para migrar recursos de IaaS do Clássico para o Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Usar a CLI para migrar recursos de IaaS do Clássico para o Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Clonar uma máquina virtual clássica para o Azure Resource Manager usando scripts da comunidade do PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0518_2016-->