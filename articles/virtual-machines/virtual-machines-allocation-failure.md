<properties
	pageTitle="Solução de problemas de falha na alocação de VM | Microsoft Azure"
	description="Solução de problemas de falha na alocação quando você criar, reiniciar ou redimensionar uma VM no Microsoft Azure"
	services="virtual-machines, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="virtual-machines"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="kenazk"/>



# Solução de problemas de falha na alocação quando você criar, reiniciar ou redimensionar uma VM no Microsoft Azure

Quando você cria uma VM, reinicia VMs paradas (desalocadas) ou redimensiona uma VM, o Microsoft Azure aloca recursos de computação para sua assinatura. Eventualmente, você pode receber mensagens de erro durante a execução dessas operações antes de alcançar os limites da assinatura do Microsoft Azure. Este artigo explica as causas das falhas de alocação mais comuns e sugere possíveis correções. As informações também poderão ser úteis caso você pretenda implantar serviços.

Se o problema do Azure não for resolvido neste artigo, visite os [Fóruns do Azure no MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou no @AzureSupport no Twitter. Além disso, você pode emitir uma solicitação de suporte do Azure, selecionando **Obter suporte** no site de [Suporte do Azure](http://azure.microsoft.com/support/options/).

A seção "Solucionando falhas de alocação comuns" lista as etapas para solucionar problemas comuns. A seção "Solução de cenários de falha de alocação específica" fornece etapas de resolução por mensagem de erro específica. Antes de iniciar, aqui estão algumas informações básicas para entender como funciona a alocação e por que a falha de alocação acontece.

## Informações básicas
### Como funciona a alocação
Os servidores são particionados em clusters nos datacenters do Microsoft Azure. Normalmente, uma tentativa de solicitação de alocação é feita em vários clusters, mas é possível que determinadas restrições da solicitação forcem a plataforma do Microsoft Azure a repeti-la em um único cluster. Neste artigo, chamaremos isso de "fixada a um cluster". O diagrama 1 a seguir exemplifica o caso de uma tentativa de alocação normal feita em vários clusters; o diagrama 2 demonstra o caso de uma alocação que foi fixada ao cluster 2 por se tratar do local de hospedagem do Serviço de Nuvem CS\_1 ou do Conjunto de Disponibilidade existente. ![Diagrama de alocação](./media/virtual-machines-allocation-failure/Allocation1.png)

### Motivos das falhas de alocação
Quando a solicitação de alocação é fixada a um cluster, é mais provável que haja falha na localização de recursos gratuitos, pois o pool de recursos disponíveis é menor. Além disso, quando a solicitação de alocação é fixada a um cluster, mas não há suporte para o tipo de recurso solicitado ao cluster, a solicitação falha mesmo que o cluster tenha recursos gratuitos. O diagrama 3 a seguir demonstra o caso em que uma alocação fixada falha porque o único cluster candidato não dispõe de recursos gratuitos. O diagrama 4 demonstra o caso em que uma alocação fixada falha porque o único cluster candidato não é compatível com o tamanho da VM solicitado, mesmo que o cluster disponha de recursos livres.

![Falha na alocação fixada](./media/virtual-machines-allocation-failure/Allocation2.png)

## Etapas gerais de solução de problemas
### Solução de problemas de falha na alocação comum no modelo de implantação clássico

Essas etapas podem ajudar a resolver diversas falhas de alocação em máquinas virtuais.

- Redimensione a VM para um tamanho de VM diferente.<br> Clique em Procurar tudo > Máquinas virtuais (clássicas) > sua máquina virtual > Configurações > **Tamanho**. Para obter as etapas detalhadas, consulte [Redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

- Exclua todas as VMs do serviço de nuvem e recrie-as.<br> Clique em Procurar tudo > Máquinas virtuais (clássicas) > sua máquina virtual > Excluir. Em seguida, clique em Novo > Computação > [Imagem da Máquina Virtual]

### Solução de problemas de falhas de alocação comuns no modelo de implantação do Gerenciador de Recursos

Essas etapas podem ajudar a resolver diversas falhas de alocação em máquinas virtuais.

- Interrompa a desalocação de todas as VMs no mesmo conjunto de disponibilidade e reinicie cada uma delas.<br> Para parar: clique em Grupos de recursos > seu Grupo de Recursos > Recursos > seu conjunto de disponibilidade > Máquinas virtuais > sua máquina virtual > Parar

	Depois de parar todas as VMs, selecione a primeira e clique em Iniciar.

## Etapas detalhadas de solução de problemas
### Solução de problemas em cenários específicos de falha na alocação no modelo de implantação clássico
Veja os cenários comuns de alocação que causam a fixação de uma solicitação de alocação. Vamos nos aprofundar em cada cenário posteriormente neste artigo.

- Redimensionar uma VM ou adicionar outras VMs ou instâncias de função em um serviço de nuvem existente
- Reinicialização de VMs paradas (desalocadas) – desalocação parcial
- Reinicialização de VMs paradas (desalocadas) – desalocação completa
- Implantações de preparo e de produção (apenas plataforma como serviço)
- Grupo de afinidade – proximidade de serviço/VM
- Rede virtual com base em grupo de afinidade

Caso receba um erro de alocação, verifique se os cenários descritos se aplicam a você. Use o erro de alocação retornado pela plataforma do Microsoft Azure para identificar o cenário correspondente. Caso a solicitação seja fixada, tente remover algumas das restrições de fixação para abrir a solicitação a outros clusters e aumentar a probabilidade de êxito da alocação.

Em geral, se o erro não indicar a mensagem "não há suporte para o tamanho de VM solicitado", você pode sempre tentar novamente, já que os recursos necessários podem ter sido liberados no cluster para atender à sua solicitação. Se o problema for o erro "Não há suporte para o tamanho de VM solicitado", tente usar um tamanho de VM diferente; caso contrário, a única opção é remover a restrição de fixação.

Dois cenários de falhas comuns estão relacionados ao grupo de afinidade. Anteriormente, o grupo de afinidade era usado para proporcionar uma estreita proximidade com as instâncias de serviço/VMs ou para permitir a criação de Redes Virtuais. Com a introdução da Rede Virtual Regional, não é mais necessário usar o grupo de afinidade para criar uma Rede Virtual. Com a redução da latência da rede na infraestrutura do Microsoft Azure, mudamos a recomendação de uso do grupo de afinidade para a obtenção de proximidade do serviço/VM.

O diagrama 5 a seguir apresenta a taxonomia dos cenários de alocação (afixados). ![Taxonomia de alocação fixada](./media/virtual-machines-allocation-failure/Allocation3.png)

> [AZURE.NOTE]Descrevemos o erro de forma reduzida em cada cenário de alocação. Consulte o [Apêndice](#appendix) para obter as cadeias de caracteres de erro detalhadas.

#### Cenário de alocação: redimensionar uma VM ou adicionar outras VMs ou instâncias de função em um serviço de nuvem existente
**Erro**

Upgrade\_VMSizeNotSupported* ou GeneralError*

**Causa de fixação de cluster**

A solicitação de redimensionamento ou de adição de uma VM ou instância de função em um serviço de nuvem existente deve ser tentada no cluster original que hospeda este serviço. A criação de um novo serviço de nuvem permite que a plataforma do Microsoft Azure localize outro cluster com recursos gratuitos ou um cluster compatível com o tamanho de VM solicitado.

**Solução alternativa**

Se o erro for Upgrade\_VMSizeNotSupported*, tente usar um tamanho de VM diferente. Quando não for possível usar um tamanho de VM diferente, mas for aceitável usar um endereço IP virtual diferente, crie um novo serviço de nuvem para hospedar a nova VM e adicione-o à Rede Virtual Regional na qual as VMs existentes estão em execução. Caso o serviço de nuvem existente não use a Rede Virtual Regional, você ainda poderá criar uma nova Rede Virtual para o novo serviço de nuvem e conectá-la [à Rede Virtual existente](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Saiba mais sobre a [Rede Virtual Regional](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Se o erro for GeneralError*, é provável que o cluster tenha suporte para o tipo de recurso (como um determinado tamanho de VM), embora não tenha recursos gratuitos no momento. Como descrito anteriormente, tente adicionar o recurso de computação desejado criando um novo serviço de nuvem (o novo serviço de nuvem deve usar um endereço IP virtual diferente) e use a Rede Virtual Regional para conectar os Serviços de Nuvem.

#### Cenário de alocação: reiniciamento de VMs paradas (desalocadas) – desalocação parcial

**Erro**

GeneralError*

**Causa de fixação de cluster**

A desalocação **parcial** significa que você interrompeu (desalocou) uma ou mais, mas **não todas** as VMs no serviço de nuvem. Quando você para (desaloca) uma VM, os recursos associados são liberados. Reiniciar a VM parada (desalocada), portanto, é uma nova solicitação de alocação. Reiniciar as VMs em um serviço de nuvem parcialmente desalocado é o mesmo que adicionar VMs em um serviço de nuvem existente. Nesse caso, a solicitação de alocação deve ser tentada no cluster original que hospeda este serviço. A criação de um serviço de nuvem diferente permite que a plataforma do Microsoft Azure localize outro cluster com recursos gratuitos ou um cluster compatível com o tamanho de VM solicitado.

**Solução alternativa**

Se for aceitável usar um endereço IP virtual diferente, exclua as VMs paradas ou desalocadas (mas mantenha os discos associados) e adicione-as novamente através de outro serviço de nuvem. Use a Rede Virtual Regional para conectar os Serviços de Nuvem: 1. Quando o serviço de nuvem existente usa uma Rede Virtual Regional, basta adicionar o novo serviço de nuvem à mesma Rede Virtual. 2. Caso o serviço de nuvem existente não use a Rede Virtual Regional, você poderá criar uma nova Rede Virtual para o novo serviço de nuvem e [conectá-la à Rede Virtual existente](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Saiba mais sobre a [Rede Virtual Regional](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

#### Cenário de alocação: reiniciando VMs paradas (desalocadas) – desalocação completa
**Erro**

GeneralError*

**Causa de fixação de cluster**

A desalocação **total** significa que você parou (desalocou) **todas** as VMs de um serviço de nuvem. A partir de agora, as solicitações de alocação para reiniciar essas VMs devem ser tentadas no cluster original que hospeda o serviço de nuvem. A criação de um novo serviço de nuvem permite que a plataforma do Microsoft Azure localize outro cluster com recursos gratuitos ou um cluster compatível com o tamanho de VM solicitado.

**Solução alternativa**

Se for aceitável usar um endereço IP virtual diferente, exclua as VMs originais paradas ou desalocadas (mas mantenha os discos associados) e exclua o serviço de nuvem correspondente (os recursos de computação associados foram liberados quando você parau ou desalocou as VMs). Crie um novo serviço de nuvem para adicionar novamente as VMs.

#### Cenário de alocação: implantações de preparação e de produção (apenas plataforma como serviço)
**Erro**

New\_General* ou New\_VMSizeNotSupported*

**Causa de fixação de cluster**

A implantação de preparação e a implantação de produção de um serviço de nuvem são hospedadas no mesmo cluster. Quando você adiciona a segunda implantação, a solicitação de alocação correspondente é tentada no mesmo cluster que hospeda a primeira implantação.

**Solução alternativa**

Se for aceitável, exclua a primeira implantação e o serviço de nuvem original, e reimplante o serviço de nuvem. Esta ação pode direcionar a primeira implantação para um cluster que dispõe de recursos gratuitos adequados para as duas implantações ou para um cluster compatível com os tamanhos de VM solicitados.

#### Cenário de alocação: grupo de afinidade – proximidade de serviço/VM
**Erro**

New\_General* ou New\_VMSizeNotSupported*

**Causa de fixação de cluster**

Os recursos de computação atribuídos a um grupo de afinidade estão vinculados a um único cluster. As novas solicitações de recursos de computação desse grupo de afinidade são tentadas no mesmo cluster que hospeda os recursos existentes. Isso ocorre independentemente de os novos recursos serem criados em um serviço de nuvem existente ou novo.

**Solução alternativa**

Caso não seja necessário, não use o grupo de afinidade e nem tente agrupar os recursos de computação em vários grupos de afinidade.

#### Cenário de alocação: Rede Virtual com base em grupo de afinidade
**Erro**

New\_General* ou New\_VMSizeNotSupported*

**Causa de fixação de cluster**

Antes de a Rede Virtual Regional entrar em vigor, você era solicitado a associar a Rede Virtual ao grupo de afinidade. Como resultado, os recursos de computação colocados no grupo de afinidade são vinculados pelas mesmas restrições, conforme descrito na seção anterior, "Cenário de alocação: grupo de afinidade – proximidade de serviço/VM". Os recursos de computação são vinculados a um único cluster.

**Solução alternativa**

Se você não precisar do grupo de afinidade, crie uma nova Rede Virtual Regional para os novos recursos que está adicionando e [conecte sua Rede Virtual existente à nova](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Saiba mais sobre a [Rede Virtual Regional](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Como alternativa, você pode [migrar a Rede Virtual com base em grupo de afinidade para a Rede Virtual Regional](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/). Em seguida, tente adicionar novamente os recursos desejados.

### Solução de problemas em cenários específicos de falha na alocação no modelo de implantação do Gerenciador de Recursos do Azure
Veja os cenários comuns de alocação que causam a fixação de uma solicitação de alocação. Vamos nos aprofundar em cada cenário posteriormente neste artigo.

- Redimensionar uma VM ou adicionar outras VMs ou instâncias de função em um serviço de nuvem existente
- Reinicialização de VMs paradas (desalocadas) – desalocação **parcial**
- Reinicialização de VMs paradas (desalocadas) – desalocação **completa**

Caso receba um erro de alocação, verifique se os cenários descritos se aplicam a você. Use o erro de alocação retornado pela plataforma do Microsoft Azure para identificar o cenário correspondente. Caso a solicitação seja fixada a um cluster existente, tente remover algumas das restrições de fixação para abrir a solicitação a outros clusters e aumentar a probabilidade de êxito da alocação.

Em geral, se o erro não indicar a mensagem "não há suporte para o tamanho de VM solicitado", você pode sempre tentar novamente, já que os recursos necessários podem ter sido liberados no cluster para atender à sua solicitação. Se o problema for o tamanho da VM solicitada sem suporte, consulte as soluções alternativas abaixo.

#### Cenário de alocação: redimensionar uma VM ou adicionar outras VMs a um conjunto de disponibilidade existente
**Erro**

Upgrade\_VMSizeNotSupported* ou GeneralError*

**Causa de fixação de cluster**

A solicitação de redimensionamento ou de adição de uma VM em um conjunto de disponibilidade existente deve ser tentada no cluster original que hospeda este conjunto. A criação de um novo conjunto de disponibilidade permite que a plataforma do Microsoft Azure localize outro cluster com recursos gratuitos ou um cluster compatível com o tamanho de VM solicitado.

**Solução alternativa**

Se o erro for Upgrade\_VMSizeNotSupported*, tente usar um tamanho de VM diferente. Se não for possível usar um tamanho de VM diferente, pare todas as VMs no conjunto de disponibilidade. Dessa forma, você poderá alterar o tamanho da máquina virtual que vai alocar a VM a um cluster compatível com o tamanho de VM solicitado.

Se o erro for GeneralError*, é provável que o cluster tenha suporte para o tipo de recurso (como um determinado tamanho de VM), embora não tenha recursos gratuitos no momento. Quando a VM puder fazer parte de um Conjunto de Disponibilidade diferente, crie uma nova VM em outro Conjunto de Disponibilidade (na mesma região). Essa nova VM pode ser adicionada à mesma Rede Virtual.

#### Cenário de alocação: reiniciamento de VMs paradas (desalocadas) – desalocação parcial
**Erro**

GeneralError*

**Causa de fixação de cluster**

A desalocação **parcial** significa que você interrompeu (desalocou) uma ou mais, mas **não todas** as VMs no conjunto de disponibilidade. Quando você para (desaloca) uma VM, os recursos associados são liberados. Reiniciar a VM parada (desalocada), portanto, é uma nova solicitação de alocação. Reiniciar as VMs em um Conjunto de Disponibilidade parcialmente desalocado é o mesmo que adicionar VMs a um Conjunto de Disponibilidade existente. Nesse caso, a solicitação de alocação deve ser tentada no cluster original que hospeda este Conjunto de Disponibilidade existente.

**Solução alternativa**

Tente parar todas as VMs no conjunto de disponibilidade antes de reiniciar a primeira. Dessa forma, o sistema executa uma nova tentativa de alocação e um novo cluster com capacidade disponível pode ser selecionado.

#### Cenário de alocação: reiniciando VMs paradas (desalocadas) – desalocação completa
**Erro**

GeneralError*

**Causa de fixação de cluster**

A desalocação **total** significa que você interrompeu (desalocou) **todas** as VMs no conjunto de disponibilidade. A solicitação de alocação para reiniciar essas VMs se destina a todos os clusters compatíveis com o tamanho desejado.

**Solução alternativa**

Tente selecionar um novo tamanho de VM para alocar. Se não for possível, tente novamente mais tarde.

## Apêndice
### Pesquisa de cadeia de caracteres de erro
**New\_VMSizeNotSupported***

O tamanho de VM (ou combinação de tamanhos de VM) necessário para essa implantação não pode ser provisionado devido a restrições de solicitação de implantação. Se possível, tente relaxar as restrições, como associações de Rede Virtual, implantação em um serviço hospedado sem nenhuma outra implantação nele e em um grupo de afinidade diferente ou sem nenhum grupo de afinidade ou tente implantar em uma região diferente.

**New\_General***

Falha na alocação; não é possível atender às restrições na solicitação. A implantação solicitada do novo serviço está associada a um grupo de afinidade ou é destinada a uma Rede Virtual, ou há uma implantação existente para esse serviço hospedado. Essas condições restringem a nova implantação para os recursos específicos do Microsoft Azure. Tente novamente mais tarde ou tente reduzir o tamanho da VM ou o número de instâncias de função. De forma alternativa, remova as restrições mencionadas acima ou tente implantar em uma região diferente, se possível.

**Upgrade\_VMSizeNotSupported***

Não é possível atualizar a implantação. O tamanho de VM XXX solicitado pode não estar disponível nos recursos que são compatíveis com a implantação existente. Tente novamente mais tarde, tente com um tamanho de VM diferente ou com um número de instâncias de função menor, ou crie uma implantação em um serviço hospedado vazio com um novo grupo de afinidade ou sem nenhuma associação com grupo de afinidade.

**GeneralError***

O servidor encontrou um erro interno. Repita a solicitação." ou "Falha na produção de uma alocação para o serviço.

<!---HONumber=AcomDC_1217_2015-->