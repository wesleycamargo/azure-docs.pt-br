<properties 
	pageTitle="Gerenciar a disponibilidade de máquinas virtuais - Azure" 
	description="Saiba como usar várias máquinas virtuais para garantir a alta disponibilidade do aplicativo do Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="kenazk" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/26/2014" 
	ms.author="kenazk"/>

#Gerenciar a disponibilidade de máquinas virtuais

## Compreenda a manutenção planejada x não planejada
Há dois tipos de eventos de plataforma do Azure que podem afetar a disponibilidade das máquinas virtuais: manutenção planejada e manutenção não planejada.

- **Eventos de manutenção planejada** são atualizações periódicas feitas pela Microsoft na plataforma subjacente do Azure para melhorara a confiabilidade, o desempenho e a segurança geral da infraestrutura da plataforma na qual suas máquinas virtuais são executadas. A maioria dessas atualizações são realizadas sem nenhum impacto às suas máquinas virtuais ou aos serviços de nuvem. Entretanto, há instâncias em que essas atualizações exigem uma reinicialização para sua máquina virtual aplicar as atualizações solicitadas na infraestrutura da plataforma. 

- **Eventos de manutenção não planejada** ocorrem quando o hardware ou a infraestrutura física subjacente à sua máquina virtual apresenta algum tipo de falha. Isso inclui falhas na rede local, falhas no disco local ou outras falhas no nível de rack. Quando tal falha é detectada, a plataforma do Azure automaticamente migrará sua máquina virtual de um computador físico não íntegro hospedando sua máquina virtual para um computador íntegro. Esses eventos são raros, mas podem também reinicializar a sua máquina virtual. 

## Siga as práticas recomendadas ao projetar seu aplicativo para alta disponibilidade
Para reduzir o impacto do tempo de inatividade devido a um ou mais desses eventos, sugerimos que siga as práticas recomendadas de alta disponibilidade para suas máquinas virtuais:

* [Configure diversas máquinas virtuais em um Conjunto de Disponibilidade para redundância] 
* [Configure cada camada de aplicativo em Conjuntos de Disponibilidade separados] 
* [Combine o Balanceador de Carga com os Conjuntos de Disponibilidade] 
* [Evite máquinas virtuais de instância única em Conjuntos de Disponibilidade] 

### Configure diversas máquinas virtuais em um Conjunto de Disponibilidade para redundância 
Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um Conjunto de Disponibilidade. Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual estará disponível e atenderá os 99,95% SLA do Azure. Para obter mais informações sobre contratos de nível de serviço, consulte a seção "Serviços de nuvem, máquinas virtuais e rede virtual" nos [Contratos de Nível de Serviço](../../../support/legal/sla/). 

Toda máquina virtual no conjunto de disponibilidade recebe um Domínio de Atualização (UD) e um Domínio de Falha (FD) da plataforma do Azure subjacente. Para um dado Conjunto de Disponibilidade, cinco UDs de usuário não configurável são atribuídos aos grupos indicados das máquinas virtuais e do hardware físico subjacente que podem ser reinicializados a qualquer momento. Quando mais do que cinco máquinas virtuais são configuradas com um único Conjunto de Disponibilidade, a sexta máquina virtual será alocada com o mesmo UD da primeira máquina virtual, a sétima com o mesmo UD da segunda máquina virtual e assim sucessivamente. A ordem das UDs sendo reinicializadas podem não acontecer em sequência durante a manutenção planejada, mas apenas uma UD será reinicializada por vez.

FDs definem o grupo de máquinas virtuais que compartilham uma fonte de energia e chave de rede comum. Por padrão, as máquinas virtuais configuradas dentro do seu Conjunto de Disponibilidade são separadas entre os dois FDs. Enquanto colocar suas máquinas virtuais em um Conjunto de Disponibilidade não protege seu aplicativo de falhas de sistema operacional e nem específicas de aplicativo, isso limita o impacto das potencias falhas físicas de hardware, panes de rede ou interrupções de energia.   

<!--Image reference-->
   ![UD FD configuration](./media/virtual-machines-manage-availability/ud-fd-configuration.png)

>[AZURE.NOTE] Para obter instruções, consulte [Como configurar um conjunto de disponibilidade para máquinas virtuais] [].

### Configure cada camada de aplicativo em Conjuntos de Disponibilidade separados
Nas máquinas virtuais, no seu conjunto de disponibilidade são todas quase idênticas e servem para o mesmo propósito para o seu aplicativo, recomendamos que configure o seu Conjunto de Disponibilidade de cada camada do seu aplicativo.  Se você colocar em duas camadas diferentes no mesmo Conjunto de Disponibilidade, todas as máquinas virtuais na mesma camada de aplicativo podem ser reinicializadas ao mesmo tempo. Ao configurar ao menos duas máquinas virtuais no Conjunto de Disponibilidade de cada camada, você garante que ao menos uma máquina virtual de cada camada estará disponível.   

Por exemplo, você pode colocar todas as VMs em front-end da execução IIS, Apache, Ngnix, etc do seu aplicativo em um único conjunto de disponibilidade. Certifique-se de que apenas as máquinas virtuais em front-end estão colocadas no mesmo Conjunto de Disponibilidade. Da mesma forma, certifique-se de que apenas as máquinas virtuais de camadas de dados estão colocadas nos seus próprios Conjunto de Disponibilidade, como as suas VMs replicadas do SQL Server ou suas VMs do MySQL.

<!--Image reference-->
   ![Application tiers](./media/virtual-machines-manage-availability/application-tiers.png)

 
### Combine o Balanceador de Carga com os Conjuntos de Disponibilidade
Combine o Balanceador de Carga do Azure com o Conjunto de Disponibilidade para obter a melhor resiliência de aplicativo. O Balanceador de Carga do Azure distribui o tráfego entre as múltiplas máquinas virtuais. Para as nossas máquinas virtuais de camadas padrões, o Balanceador de carga do Azure está incluso. Observe que nem todas as camadas de Máquina Virtual incluem o Balanceador de Carga do Azure. Para obter mais informações sobre balanceamento de carga de suas máquinas virtuais, consulte [Balanceamento de carga de máquinas virtuais](../load-balance-virtual-machines/). 

Se o balanceador de carga não estiver configurado para balancear o tráfego entre múltiplas máquinas virtuais, então qualquer evento de manutenção planejada afetará a única máquina virtual atendendo ao tráfego causando uma pane na sua camada de aplicativo. Colocar diversas máquinas virtuais na mesma camada sob o mesmo balanceador de carga e Conjunto de Disponibilidade habilita o tráfego a ser atendido continuamente pelo menos por uma instância. 

### Evite máquinas virtuais de instância única em Conjuntos de Disponibilidade
Evite deixar uma única máquina virtual sozinha em um Conjunto de disponibilidade. Máquinas virtuais com esta configuração não se qualificam como garantia SLA e enfrentarão tempo de inatividades durante os eventos de manutenção planejada do Azure.  Além disso, se você implantar uma única instância VM em um conjunto de disponibilidade, você não receberá nenhum alerta ou notificação antecipada de manutenção de plataforma. Nessa configuração, a instância de sua única máquina virtual pode e será reinicializada sem nenhum alerta antecipado quando a manutenção da plataforma acontecer.

[Configure diversas máquinas virtuais em um Conjunto de Disponibilidade para redundância]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy 
[Configure cada camada de aplicativo em Conjuntos de Disponibilidade separados]: #configure-each-application-tier-into-separate-availability-sets 
[Combine o Balanceador de Carga com os Conjuntos de Disponibilidade]: #combine-the-load-balancer-with-availability-sets 
[Evite máquinas virtuais de instância única em Conjuntos de Disponibilidade]: #avoid-single-instance-virtual-machines-in-availability-sets 

 
<!-- Link references -->
[Como configurar um conjunto de disponibilidade para máquinas virtuais]: ../virtual-machines-how-to-configure-availability



<!--HONumber=45--> 
