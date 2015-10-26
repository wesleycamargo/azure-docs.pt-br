<properties
	pageTitle="Failover na Recuperação de Site" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre o failover no Azure ou em um datacenter secundário." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="10/12/2015" 
	ms.author="raynew"/>

# Failover na Recuperação de Site

O [serviço Azure Site Recovery](site-recovery-overview.md) contribui para uma solução robusta de BCDR (continuidade dos negócios e recuperação de desastre) que protege servidores físicos locais e máquinas virtuais, administrando e automatizando a replicação e o failover no Azure ou em um datacenter local secundário. Este artigo fornece informações e instruções de recuperação (failover e failback) de máquinas virtuais e servidores físicos que são protegidos pela Recuperação de Site.

Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Visão geral

Depois de configurar a proteção das máquinas virtuais e dos servidores físicos, eles começam a ser replicados no local secundário. Depois da replicação, você poderá executar failovers de acordo com a necessidade. A Recuperação de Site oferece suporte a vários tipos de failover.

**Failover** | **Quando executar** | **Detalhes** | **Processo**
---|---|---|---
**Failover de teste** | Executar para validar sua estratégia de replicação ou realizar uma simulação de recuperação de desastre | <p>Nenhuma perda de dados ou tempo de inatividade</p><p>Nenhum impacto na replicação</p><p>Nenhum impacto no ambiente de produção</p> | <p>Iniciar o failover</p><p>Especificar como computadores de teste serão conectados às redes após o failover</p><p>Acompanhar o progresso na guia **Trabalhos**. Computadores de teste são criados e iniciados no local secundário</p><p>Azure — conectar o computador no portal do Azure</p><p>Site secundário — acessar o computador no mesmo host e na mesma nuvem</p><p>Concluir teste e limpar automaticamente as configurações do failover de teste.</p>
**Failover planejado** | <p>Executar para atender aos requisitos de conformidade</p><p>Executar para manutenção planejada</p><p>Executar para realizar failover de dados a fim de manter as cargas de trabalho em execução durante interrupções conhecidas, como falta de energia esperada ou previsões de condições climáticas severas</p> <p>Executar para fazer failback após o failover do local primário no secundário | <p>Sem perda de dados</p><p>Tempo de inatividade durante o tempo necessário para desligar a máquina virtual no local primário e ativá-la no local secundário.</p><p>Máquinas virtuais são afetadas, à medida que os computadores de destino se tornam computadores de origem após o failover.</p> | <p>Iniciar o failover</p><p>Acompanhar o progresso na guia **Trabalhos**. Os computadores de origem são desligados</p><p>Computadores de réplica são iniciados no local secundário</p><p>Azure — conectar ao computador de réplica no portal do Azure</p><p>Site secundário — acessar o computador no mesmo host e na mesma nuvem</p><p>Confirmar o failover</p>
**Failover não planejado** | <p>Executar esse tipo de failover de maneira reativa quando um site primário se torna inacessível devido a um incidente inesperado, como falta de energia ou ataque de vírus</p><p>Você pode executar um failover não planejado mesmo se um site primário não estiver disponível.<p> | <p>Perda de dados depende das configurações da frequência de replicação</p> <p>Os dados serão atualizados de acordo com a última vez que foram sincronizados</p> | <p>Iniciar o failover</p><p>Acompanhar o progresso na guia **Trabalhos**. Como opção, tente desligar as máquinas virtuais e sincronizar os dados mais recentes</p><p>Computadores de réplica são iniciados no local secundário</p><p>Azure — conectar ao computador de réplica no portal do Azure</p><p>Site secundário — acessar o computador no mesmo host e na mesma nuvem</p><p>Confirmar o failover</p>


Os tipos de failover com suporte dependem do cenário de implantação.

**Direção do failover** | **Failover de teste** | **Failover planejado** | **Failover não planejado**
---|---|---|---
Site primário do VMM para site secundário do VMM | Com suporte | Com suporte | Com suporte 
Site secundário do VMM para site primário do VMM | Com suporte | Com suporte | Com suporte
Nuvem para nuvem (único servidor VMM) | Com suporte | Com suporte | Com suporte
Site do VMM para Azure | Com suporte | Com suporte | Com suporte 
Azure para site do VMM | Sem suporte | Com suporte | Sem suporte 
Site do Hyper-V para Azure | Com suporte | Com suporte | Com suporte
Azure para site do Hyper-V | Sem suporte | Com suporte | Sem suporte
Site da VMware para Azure | Sem suporte |Este cenário usa replicação contínua, de modo que não há distinção entre failover planejado e não planejado. Selecione **Failover** | ND
Servidor físico para Azure | Sem suporte | Este cenário usa replicação contínua, de modo que não há distinção entre failover planejado e não planejado. Selecione **Failover** | ND

## Failover e failback

Você realiza failover de máquinas virtuais em um site secundário local ou no Azure, dependendo da sua implantação. Um computador que realiza failover no Azure é criado como uma máquina virtual do Azure. É possível realizar failover de uma única máquina virtual ou de um servidor físico, ou de um plano de recuperação. Os planos de recuperação consistem em um ou mais grupos ordenados que contêm máquinas virtuais ou servidores físicos protegidos. Eles são usados para administrar o failover de vários computadores que realizam failover de acordo com o grupo em que estão. [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

Depois que o failover for concluído e que seus computadores estiverem funcionando em um local secundário, observe que:

- Se você realizou failover no Azure, depois do failover, os computadores não são protegidos nem replicados no local primário ou secundário. No Azure, as máquinas virtuais são armazenadas no armazenamento com replicação geográfica, que fornece resiliência, mas não replicação.
- Se você realizou um failover não planejado em um site secundário, depois do failover, os computadores no local secundário não são protegidos nem replicados.
- Se você realizou um failover planejado em um site secundário, depois do failover, os computadores no local secundário são protegidos.
 

### Failback do site secundário

O failback de um site secundário em um site primário usa o mesmo processo de failover do primário no secundário. Após o failover do primário no secundário, o datacenter é confirmado e concluído. Você pode iniciar a replicação inversa quando o site primário se tornar disponível. A replicação inversa inicia a replicação entre o site secundário e o primário, sincronizando os dados delta. A replicação inversa coloca as máquinas virtuais em um estado protegido, mas o datacenter secundário ainda é o local ativo. Para transformar o site primário em local ativo, inicie um failover planejado do site secundário para o primário, seguido por outra replicação inversa.

### Failback do Azure

Se você realizou failover no Azure, suas máquinas virtuais estão protegidas pelos recursos de resiliência do Azure para máquinas virtuais. Para transformar o site primário original em local ativo, execute um failover planejado. Você pode fazer failback no local original ou em um local alternativo se o site original não estiver disponível. Para iniciar a replicação novamente após o failback no local primário, inicie uma replicação inversa.

### Considerações sobre failover

- **Endereço IP após failover**: por padrão, um computador com failover terá um endereço IP diferente do computador de origem. Se deseja manter o mesmo endereço IP, consulte: 
	- **Site secundário**: se estiver fazendo failover em um site secundário e deseja manter um endereço IP [leia](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) este artigo. Observe que você pode manter um endereço IP público, se houver suporte do seu ISP (provedor de serviços de Internet).
	- **Azure**: se estiver fazendo failover no Azure, você poderá especificar o endereço IP que deseja atribuir na guia **Configurar** das propriedades da máquina virtual. Não é possível manter um endereço IP público depois de fazer failover no Azure. Você pode manter espaços de endereço não RFC 1918 que são usados como endereços internos.
- **Failover parcial**: se você deseja fazer failover de parte de um site, e não dele inteiro, observe que: 
	- **Site secundário**: se você faz failover de parte de um site primário em um site secundário e deseja se conectar de volta ao site primário, use uma conexão VPN site a site para conectar aplicativos com failover no site secundário aos componentes da infraestrutura em execução no site primário. Ao fazer failover de uma sub-rede inteira, você pode manter o endereço IP da máquina virtual. Ao fazer failover de uma sub-rede parcial, você não pode manter o endereço IP da máquina virtual porque as sub-redes não podem ser divididas entre sites.
	- **Azure**: se você faz failover de um site parcial no Azure e deseja se conectar de volta ao site primário, use uma conexão VPN site a site para conectar um aplicativo com failover no Azure aos componentes da infraestrutura em execução no site primário. Observe que se toda a sub-rede falhar, você poderá manter o endereço IP da máquina virtual. Ao fazer failover de uma sub-rede parcial, você não pode manter o endereço IP da máquina virtual porque as sub-redes não podem ser divididas entre sites.
 
- **Letra da unidade**: se deseja manter a letra da unidade em máquinas virtuais depois do failover, você pode definir a política de SAN para a máquina virtual como **Ativado**. Os discos da máquina virtual ficam online automaticamente. [Leia mais](https://technet.microsoft.com/library/gg252636.aspx).
- **Encaminhar solicitações de clientes**: a Recuperação de Site trabalha com o Gerenciador de Tráfego do Azure para encaminhar solicitações de clientes ao seu aplicativo depois do failover.




## Execute um teste de failover

Na execução de um failover de teste, é solicitado que você selecione as configurações de rede para testar os computadores de réplica. Você tem várias opções.

**Opção de failover de teste** | **Descrição** | **Verificação do failover** | **Detalhes**
---|---|---|---
**Failover no Azure — sem rede** | Não selecionar uma rede de destino do Azure | O failover verifica se a máquina virtual de teste é iniciada conforme esperado no Azure | <p>Todas as máquinas virtuais de teste em um plano de recuperação são adicionadas em um único serviço de nuvem e podem se conectar uma a outra</p><p>Os computadores não são conectados a uma rede do Azure após failover.</p><p>Os usuários podem se conectar aos computadores de teste com um endereço IP público</p>
**Failover no Azure — com rede** | Selecionar uma rede de destino do Azure | O failover verifica se os computadores de teste estão conectados à rede | <p>Crie uma rede do Azure isolada da rede de produção do Azure e configure a infraestrutura para que a máquina virtual replicada funcione conforme esperado.</p><p>A sub-rede da máquina virtual de teste se baseia na sub-rede à qual se espera que a máquina virtual com failover se conecte se um failover planejado ou não planejado ocorrer.</p>
**Failover em um site do VMM secundário — sem rede** | Não selecionar uma rede de VM | O failover verifica se os computadores de teste são criados.<p>A máquina virtual de teste será criada no mesmo host que o host no qual está a máquina virtual de réplica. Ela não é adicionada à nuvem na qual a máquina virtual de réplica está localizada.</p></p>| <p>A máquina com failover não será conectada a nenhuma rede.</p><p>O computador pode ser conectado a uma rede VM depois que ela for criada</p>
**Failover em um site do VMM secundário — com rede** | Selecionar uma rede VM existente | O failover verifica se as máquinas virtuais são criadas | <p>A máquina virtual de teste será criada no mesmo host que o host no qual está a máquina virtual de réplica. Ela não é adicionada à nuvem em que a máquina virtual de réplica está localizada.</p><p>Crie uma rede VM isolada da rede de produção</p><p>Se você estiver usando uma rede baseada em VLAN, é recomendável criar uma rede lógica separada (não usada em produção) no VMM para esse fim. Essa rede lógica é usada para criar redes VM para fins de failover de teste.</p><p>A rede lógica deve ser associada a pelo menos um dos adaptadores de rede de todos os servidores Hyper-V que hospedam máquinas virtuais.</p><p>Para redes lógicas de VLAN, os sites de rede adicionados à rede lógica devem ser isolados.</p><p>Se você está estiver usando uma rede lógica baseada na Virtualização de Rede do Windows, o Azure Site Recovery criará automaticamente redes VM isoladas.</p>
**Failover em um site do VMM secundário — criar uma rede** | Uma rede de teste temporária será criada automaticamente com base na configuração especificada em **Rede Lógica** e seus sites de rede relacionados | O failover verifica se as máquinas virtuais são criadas | <p>Use essa opção se o plano de recuperação usa mais de uma rede VM. Caso esteja usando redes da Virtualização de Rede do Windows, essa opção pode ser usada para criar automaticamente redes VM com as mesmas configurações (sub-redes e pools de endereços IP) na rede da máquina virtual de réplica. Essas redes VM são removidas automaticamente depois que o failover de teste é concluído.</p><p>A máquina virtual de teste será criada no mesmo host que o host no qual está a máquina virtual de réplica. Ela não é adicionada à nuvem na qual a máquina virtual de réplica está localizada.</p>

>[AZURE.NOTE]O IP fornecido a uma máquina virtual em um Failover de Teste é o mesmo IP que ela receberia executando um failover planejado ou não planejado, considerando que esse IP está disponível na rede de Failover de Teste. Se o mesmo IP não estiver disponível na rede de failover de teste, a máquina virtual receberá alguns outros IP disponíveis na rede de failover de teste.



### Executar um failover de teste do site local no Azure

Este procedimento descreve como executar um failover de teste para um plano de recuperação. Como alternativa, você pode executar o failover para um único computador na guia **Máquinas Virtuais**.

1. Selecione **Planos de Recuperação** > *nome\_planoderecuperação*. Clique em **Failover** > **Failover de Teste**.
2. Na página **Confirmar Failover de Teste**, especifique como os computadores de réplica serão conectados a uma rede Azure depois do failover.
3. Se estiver fazendo failover no Azure e a criptografia de dados estiver habilitada para a nuvem, em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor. 
4. Acompanhe o progresso do failover na guia **Trabalhos**. Você deve poder ver o computador de réplica de teste no portal do Azure.
5. É possível acessar computadores de réplica no Azure de seu site local iniciando uma conexão RDP com a máquina virtual. A porta 3389 precisará ser aberta no ponto de extremidade da máquina virtual.
5. Assim que terminar, quando o failover atingir a fase **Concluir teste**, clique em **Concluir Teste** para finalizar.
5. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.
8. Clique em **O failover de teste está concluído** para limpar automaticamente o ambiente de teste. Depois disso, o failover de teste mostrará o status **Concluído**.

> [AZURE.NOTE]Se um failover de teste continuar por mais de duas semanas, ele será concluído à força. Todos os elementos ou máquinas virtuais criados automaticamente durante o failover de teste serão excluídos.
  

### Executar um failover de teste de um site primário local para um site secundário local

Você precisará realizar várias tarefas para executar um failover de teste, incluindo fazer uma cópia do controlador de domínio e colocar servidores DHCP e DNS de teste no ambiente de teste. Isso pode ser feito de duas maneiras:

- Se deseja executar um failover de teste usando uma rede existente, prepare o Active Directory, DHCP e DNS nessa rede.
- Se deseja executar um failover de teste usando a opção para criar redes VM automaticamente, adicione uma etapa manual antes do Grupo-1 no plano de recuperação que usará para o failover de teste e, em seguida, adicione os recursos de infraestrutura à rede criada automaticamente antes de executar o failover de teste.

#### Elementos a serem observados

- Ao replicar em um site secundário, o tipo de rede usado pelo computador de réplica não precisa corresponder ao tipo da rede lógica usado para o failover de teste, mas algumas combinações podem não funcionar. Quando a réplica usa isolamento baseado em VLAN e DHCP, a rede VM da réplica não precisa de um pool de endereços IP estáticos. Desse modo, usar a Virtualização de Rede do Windows para o failover de teste não funcionará porque nenhum pool de endereços está disponível. Além disso, o failover de teste não funcionará se a rede de réplica for Sem Isolamento e a rede de teste for Virtualização de Rede do Windows. Isso porque a rede Sem Isolamento não tem as sub-redes necessárias para criar uma rede da Virtualização de Rede do Windows.
- O modo como as máquinas virtuais de réplica são conectadas às redes VM mapeadas após o failover depende de como a rede VM é configurada no console do VMM:
	- **Rede VM configurada sem isolamento ou com isolamento VLAN**: se DHCP for definido para a rede VM, a máquina virtual de réplica será conectada à ID da VLAN usando as configurações que são especificadas para o site da rede na rede lógica associada. A máquina virtual receberá seu endereço IP do servidor DHCP disponível. Não é necessário um pool de endereços IP estáticos definido para a rede VM de destino. Se um pool de endereços IP estáticos for usado para a rede VM, a máquina virtual de réplica será conectada à ID da VLAN usando as configurações que são especificadas para o site da rede na rede lógica associada. A máquina virtual receberá seu endereço IP do pool definido para a rede VM. Se um pool de endereços IP estáticos não for definido na rede VM de destino, a alocação do endereço IP falhará. O pool de endereços IP deve ser criado em servidores VMM de origem e de destino que você pretende usar para proteção e recuperação.
	- **Rede VM com Virtualização de Rede do Windows**: quando uma rede VM é configurada com essa definição, um pool estático deve ser definido para a rede VM de destino, independentemente de a rede VM de origem estar configurada para usar DHCP ou um pool de endereços IP estáticos. Quando você define DHCP, o servidor VMM de destino atua como um servidor DHCP e fornece um endereço IP do pool que está definido para a rede VM de destino. Quando é definido o uso de um pool de endereços IP estáticos para o servidor de origem, o servidor VMM de destino aloca um endereço IP do pool. Em ambos os casos, a alocação de endereço IP falhará se um pool de endereços IP estáticos não for definido.

#### Executar teste

Este procedimento descreve como executar um failover de teste para um plano de recuperação. Como alternativa, você pode executar o failover de uma única máquina virtual ou servidor físico na guia **Máquinas Virtuais**.

1. Selecione **Planos de Recuperação** > *nome\_planoderecuperação*. Clique em **Failover** > **Failover de Teste**.
2. Na página **Confirmar Failover de Teste**, especifique como as máquinas virtuais devem ser conectadas às redes após o failover de teste.
3. Acompanhe o progresso do failover na guia **Trabalhos**. Quando o failover atingir a fase **Concluir teste***, clique em **Concluir Teste** para finalizar o failover de teste.
4. Clique em **Observações** para gravar e salvar observações associadas ao failover de teste.
4. Depois de concluído, verifique se as máquinas virtuais iniciam com sucesso.
5. Depois de verificar que máquinas virtuais foram iniciadas com êxito, conclua o teste de failover para limpar o ambiente isolado. Se você optar por criar automaticamente as redes VM, a limpeza excluirá todas as máquinas virtuais de teste e a redes de teste.

> [AZURE.NOTE]Se um failover de teste continuar por mais de duas semanas, ele será concluído à força. Todos os elementos ou máquinas virtuais criados automaticamente durante o failover de teste serão excluídos.


#### Preparar o DHCP

Se as máquinas virtuais envolvidas no failover de teste usarem DHCP, um servidor DHCP de teste deverá ser criado na rede isolada que é criada para fins de failover de teste.


### Preparar o Active Directory
Para executar um failover de teste em um teste de aplicativo, você precisará de uma cópia do ambiente de produção do Active Directory no ambiente de teste. Leia a seção [considerações sobre failover de teste para o Active Directory](site-recovery-active-directory.md#considerations-for-test-failover]) para obter mais detalhes.


### Preparar o DNS

Prepare um servidor DNS para o failover de teste da seguinte forma:

- **DHCP**: se as máquinas virtuais usarem DHCP, o endereço IP do DNS de teste deverá ser atualizado no servidor DHCP de teste. Quando você usa um tipo de rede da Virtualização de Rede do Windows, o servidor VMM atua como o servidor DHCP. Portanto, o endereço IP do DNS deve ser atualizado na rede de failover de teste. Nesse caso, as máquinas virtuais se registrarão no servidor DNS relevante.
- **Endereço estático** – se as máquinas virtuais usarem um endereço IP estático, o endereço IP do servidor DNS de teste deverá ser atualizado na rede de failover de teste. Talvez você precise atualizar o DNS com o endereço IP das máquinas virtuais de teste. É possível usar o seguinte script de exemplo para este fim: 

	    Param(
	    [string]$Zone,
	    [string]$name,
	    [string]$IP
	    )
	    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	    $newrecord = $record.clone()
	    $newrecord.RecordData[0].IPv4Address  =  $IP
	    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## Executar um failover planejado (primário para secundário)

 Este procedimento descreve como executar um failover planejado para um plano de recuperação. Como alternativa, você pode executar o failover de uma única máquina virtual na guia **Máquinas Virtuais**.

1. Antes de começar, verifique se todas as máquinas virtuais das quais deseja fazer failover concluíram a replicação inicial.
2. Selecione **Planos de Recuperação** > *nome\_planoderecuperação*. Clique em **Failover** > **Failover Planejado**. 
3. Na página **Confirmar Failover Planejado**, escolha os locais de origem e de destino. Observe a direção do failover.

	- Se os failovers anteriores funcionaram conforme esperado e todos os servidores de máquina virtual estiverem localizados ou no local de origem, ou de destino, os detalhes da direção do failover serão apenas para fins informativos. 
	- Quando as máquinas virtuais estão ativas nos locais de origem e de destino, o botão **Alterar Direção** é exibido. Use esse botão para alterar e especificar a direção na qual o failover deve ocorrer.

5. Se estiver fazendo failover no Azure e a criptografia de dados estiver habilitada para a nuvem, em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor no servidor VMM.
6. Quando um failover planejado começa, a primeira etapa é desligar as máquinas virtuais para garantir que não haja perda de dados. Você pode acompanhar o progresso do failover na guia **Trabalhos**. Se um erro ocorrer no failover (ou em uma máquina virtual, ou em um script que está incluído no plano de recuperação), o failover planejado de um plano de recuperação será interrompido. Você pode iniciar o failover novamente.
8. Depois que as máquinas virtuais de réplica são criadas, elas ficam em um estado de confirmação pendente. Clique em **Confirmar** para confirmar o failover. 
9. Depois que a replicação é concluída, as máquinas virtuais são iniciadas no local secundário. 

## Executar um failover não planejado

Este procedimento descreve como executar um failover não planejado para um plano de recuperação. Como alternativa, você pode executar o failover de uma única máquina virtual ou servidor físico na guia **Máquinas Virtuais**.

1. Selecione **Planos de Recuperação** > *nome\_planoderecuperação*. Clique em **Failover** > **Failover Não Planejado**. 
3. Na página **Confirmar Failover Não Planejado **, escolha os locais de origem e de destino. Observe a direção do failover.

	- Se os failovers anteriores funcionaram conforme esperado e todos os servidores de máquina virtual estiverem localizados ou no local de origem, ou de destino, os detalhes da direção do failover serão apenas para fins informativos. 
	- Quando as máquinas virtuais estão ativas nos locais de origem e de destino, o botão **Alterar Direção** é exibido. Use esse botão para alterar e especificar a direção na qual o failover deve ocorrer.

4. Se estiver fazendo failover no Azure e a criptografia de dados estiver habilitada para a nuvem, em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor no servidor VMM.
5. Selecione **Desligue as máquinas virtuais e sincronize os dados mais recentes** para especificar que a Recuperação de Site deve tentar desligar as máquinas virtuais protegidas e sincronizar os dados para que ocorra o failover da versão mais recente dos dados. Se você não selecionar essa opção ou a tentativa não tiver êxito, o failover será do último ponto de recuperação disponível da máquina virtual.
6. Você pode acompanhar o progresso do failover na guia **Trabalhos**. Observe que, mesmo que ocorram erros durante um failover não planejado, o plano de recuperação será executado até que seja concluído.
7. Após o failover, as máquinas virtuais entram em um estado de **confirmação pendente**. Clique em **Confirmar** para confirmar o failover.
8. Se você configurar a replicação para usar vários pontos de recuperação, em Alterar Ponto de Recuperação, é possível optar por usar um ponto de recuperação que não é o mais recente (mais recente é usado por padrão). Depois da confirmação, pontos de recuperação adicionais serão removidos.
9. Depois que a replicação é concluída, as máquinas virtuais são ativadas no local secundário. No entanto, elas não são protegidas nem replicadas. Quando o site primário estiver disponível novamente com a mesma infraestrutura subjacente, clique em **Replicação Inversa** para começar a replicação inversa. Isso garante que todos os dados sejam replicados de volta no site primário e que a máquina virtual esteja pronta para failover novamente. A replicação inversa depois de um failover não planejado incorre em uma sobrecarga na transferência de dados. A transferência usará o mesmo método que está definido para configurações de replicação inicial da nuvem.

## Failback do local secundário no primário

 Depois do failover do local primário no secundário, as máquinas virtuais replicadas não são protegidas pela Recuperação de Site, e o local secundário agora está atuando como o primário. Siga estes procedimentos para fazer failback no site primário original. Este procedimento descreve como executar um failover planejado para um plano de recuperação. Como alternativa, você pode executar o failover de uma única máquina virtual na guia **Máquinas Virtuais**.

1. Selecione **Planos de Recuperação** > *nome\_planoderecuperação*. Clique em **Failover** > **Failover Planejado**.
2. Na página **Confirmar Failover Planejado**, escolha os locais de origem e de destino. Observe a direção do failover. Se o failover do local primário funcionar conforme esperado e todas as máquinas virtuais estiverem no local secundário, isso servirá apenas para fins informativos.
3. Se estiver fazendo failback do Azure, selecione as configurações em **Sincronização de Dados**:

	- **Sincronizar os dados antes do failover**: essa opção minimiza o tempo de inatividade das máquinas virtuais, uma vez que faz a sincronização sem desligá-las. Ele faz o seguinte:
		- Fase 1: tira instantâneo da máquina virtual no Azure e o copia no host do Hyper-V local. O computador continua em execução no Azure.
		- Fase 2: Desliga a máquina virtual no Azure para que nenhuma alteração seja feita lá. O conjunto final de alterações é transferido para o servidor local e a máquina virtual local é inicializada.
	
	> [AZURE.NOTE]É recomendável usar essa opção se você executou o Azure por algum tempo (um mês ou mais). Essa opção executa a sincronização sem desligar as máquinas virtuais. Ela não faz a sincronização novamente, mas executa um failback completo. Essa opção não executa cálculos de soma de verificação.

	- **Sincronizar os dados apenas durante o failover**: use essa opção se você executou o Azure por um curto período de tempo. Essa opção é mais rápida porque sincroniza novamente, em vez de fazer um failback completo. Ela executa um cálculo rápido de soma de verificação e baixa apenas blocos alterados.

5. Se estiver fazendo failover no Azure e a criptografia de dados estiver habilitada para a nuvem, em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor no servidor VMM.
5. Por padrão, o último ponto de recuperação é usado, mas em **Alterar Ponto de Recuperação**, você pode especificar um ponto de recuperação diferente. 
6. Clique na marca de seleção para começar o failback. Você pode acompanhar o progresso do failover na guia **Trabalhos**. 
7. Se você selecionou a opção para sincronizar os dados antes do failover, depois que a sincronização de dados inicial for concluída e você estiver pronto para desligar as máquinas virtuais no Azure, clique em **Trabalhos** > <planned failover job name> **Concluir Failover**. Isso desliga a máquina do Azure, transfere as alterações mais recentes para a máquina virtual local e a inicia.
8. Agora você pode fazer logon na máquina virtual para confirmar se ela está disponível conforme esperado. 
9. A máquina virtual está em um estado de confirmação pendente. Clique em **Confirmar** para confirmar o failover.
10. Agora, para concluir o failback, clique em **Replicação Inversa** para iniciar a proteção da máquina virtual no site primário.



## Failback em um local alternativo

Se você implantou a proteção entre um [site do Hyper-V e o Azure](site-recovery-hyper-v-site-to-azure.md), será possível fazer failback do Azure em um site local alternativo. Isso é útil quando você precisa configurar novos hardwares locais. Veja como fazer isso.

1. Se você estiver definindo um novo hardware, instale o Windows Server 2012 R2 e a função do Hyper-V no servidor.
2. Crie um comutador de rede virtual com o mesmo nome que você tinha no servidor original.
3. Selecione **Itens Protegidos** -> **Grupo de Proteção** -> <ProtectionGroupName> -> <VirtualMachineName> do qual deseja realizar failback e selecione **Failover Planejado**.
4. Em **Confirmar Failover Planejado**, selecione **Criar máquina virtual local se ela não existir**. 
5. Em **Nome do Host**, selecione o novo servidor host do Hyper-V no qual deseja colocar a máquina virtual.
6. Em Sincronização de Dados, é recomendável selecionar a opção **Sincronizar os dados antes do failover**. Essa opção minimiza o tempo de inatividade das máquinas virtuais, uma vez que faz a sincronização sem desligá-las. Ele faz o seguinte:

	- Fase 1: tira instantâneo da máquina virtual no Azure e o copia no host do Hyper-V local. O computador continua em execução no Azure.
	- Fase 2: Desliga a máquina virtual no Azure para que nenhuma alteração seja feita lá. O conjunto final de alterações é transferido para o servidor local e a máquina virtual local é inicializada.
	
7. Clique na marca de seleção para iniciar o failover (failback).
8. Depois que sincronização inicial for finalizada e você estiver pronto para desligar a máquina virtual no Azure, clique em **Trabalhos** > <planned failover job> > **Concluir Failover**. Isso desliga a máquina do Azure, transfere as alterações mais recentes para a máquina virtual local e a inicia.
9. Você pode fazer logon na máquina virtual no local para verificar se tudo está funcionando conforme esperado. Em seguida, clique em **Confirmar** para concluir o failover.
10. Clique em **Replicação Inversa** para iniciar a proteção da máquina virtual local.

 

<!---HONumber=Oct15_HO3-->