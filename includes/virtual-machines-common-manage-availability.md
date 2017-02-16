## <a name="understand-planned-vs-unplanned-maintenance"></a>Compreender a manutenção planejada versus a não planejada
Há dois tipos de eventos de plataforma Microsoft Azure que podem afetar a disponibilidade das máquinas virtuais: manutenção planejada e manutenção não planejada.

* **Eventos de manutenção planejada** são atualizações periódicas feitas pela Microsoft na plataforma subjacente do Azure para melhorara a confiabilidade, o desempenho e a segurança geral da infraestrutura da plataforma executada pela máquina virtual. A maioria dessas atualizações é realizada sem nenhum impacto às suas máquinas virtuais ou aos serviços de nuvem. Entretanto, há instâncias em que essas atualizações exigem uma reinicialização para sua máquina virtual aplicar as atualizações solicitadas na infraestrutura da plataforma.
* **Eventos de manutenção não planejada** ocorrem quando o hardware ou a infraestrutura física subjacente à sua máquina virtual apresenta algum tipo de falha. Isso inclui falhas na rede local, falhas no disco local ou outras falhas no nível de rack. Quando tal falha é detectada, a plataforma do Azure automaticamente irá migrar sua máquina virtual de uma máquina virtual não íntegra hospedando sua máquina virtual para uma máquina física íntegra. Esses eventos são raros, mas podem também reinicializar a sua máquina virtual.

Para reduzir o impacto do tempo de inatividade devido a um ou mais desses eventos, sugerimos que siga as práticas recomendadas de alta disponibilidade para suas máquinas virtuais:

* [Configurar diversas máquinas virtuais em um conjunto de disponibilidade para redundância]
* [Configurar cada camada de aplicativo em conjuntos de disponibilidade separados]
* [Combinar o balanceador de carga com os conjuntos de disponibilidade]
* [Usar várias contas de armazenamento para cada conjunto de disponibilidade]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurar diversas máquinas virtuais em um conjunto de disponibilidade para redundância
Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um conjunto de disponibilidade. Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual estará disponível e atenderá os 99,95% SLA do Azure. Para saber mais, confira [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Evite deixar uma única máquina virtual sozinha em um conjunto de disponibilidade. As máquinas virtuais com esta configuração não se qualificam para a garantia de SLA e enfrentarão tempo de inatividades durante os eventos de manutenção planejada do Azure.
> 
> 

Cada máquina virtual em seu conjunto de disponibilidade receberá um **domínio de atualização** e um **domínio de falha** da plataforma subjacente do Azure. Para determinado conjunto de disponibilidade, cinco domínios de atualização não configuráveis pelo usuário são atribuídos por padrão (é possível aumentar as implantações do Resource Manager para fornecer até 20 domínios de atualização) para indicar grupos de máquinas virtuais e hardware físico subjacente que pode ser reinicializado ao mesmo tempo. Quando mais do que cinco máquinas virtuais são configuradas com um único conjunto de disponibilidade, a sexta máquina virtual será alocada com o mesmo domínio de atualização da primeira máquina virtual, a sétima com o mesmo domínio de atualização da segunda máquina virtual e assim sucessivamente. A ordem de reinicialização dos domínios de atualização pode não ser sequencial durante a manutenção planejada, mas apenas um domínio de atualização é reinicializado por vez.

Os domínios de falha definem o grupo de máquinas virtuais que compartilham uma fonte de energia e chave de rede comum. Por padrão, as máquinas virtuais configuradas em seu conjunto de disponibilidade são separadas entre até três domínios de falha para implantações do Resource Manager (dois domínios de falha para o Clássico). Embora colocar suas máquinas virtuais em um conjunto de disponibilidade não proteja seu aplicativo de falhas de sistema operacional e nem específicas de aplicativo, isso limita o impacto das potencias falhas físicas de hardware, panes de rede ou interrupções de energia.

<!--Image reference-->
   ![Desenho conceitual da configuração do domínio de atualização e do domínio de falha](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Configurar cada camada de aplicativo em conjuntos de disponibilidade separados
Se as suas máquinas virtuais forem quase idênticas e servirem para o mesmo propósito para o seu aplicativo, recomendamos que configure o seu conjunto de disponibilidade para cada camada de seu aplicativo.  Se você colocar duas camadas diferentes no mesmo conjunto de disponibilidade, todas as máquinas virtuais na mesma camada de aplicativo podem ser reinicializadas ao mesmo tempo. Ao configurar ao menos duas máquinas virtuais no conjunto de disponibilidade de cada camada, você garante que ao menos uma máquina virtual de cada camada estará disponível.

Por exemplo, você pode colocar todas as máquinas virtuais no front-end de seu aplicativo com IIS, Apache, Ngnix execução em um único conjunto de disponibilidade. Certifique-se de que apenas as máquinas virtuais em front-end estão colocadas no mesmo conjunto de disponibilidade. Da mesma forma, certifique-se de que apenas as máquinas virtuais de camadas de dados sejam colocadas no seu próprio conjunto de disponibilidade, como as máquinas virtuais do SQL Server ou suas máquinas virtuais do MySQL.

<!--Image reference-->
   ![Camadas de aplicativo](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Combinar o balanceador de carga com os conjuntos de disponibilidade
Combine o [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) com o conjunto de disponibilidade para obter a melhor resiliência de aplicativo. O Balanceador de Carga do Azure distribui o tráfego entre as múltiplas máquinas virtuais. Para as nossas máquinas virtuais de camadas padrões, o Balanceador de carga do Azure está incluso. Nem todas as camadas de máquinas virtuais incluem o Azure Load Balancer. Para saber mais sobre o balanceamento de carga de suas máquinas virtuais, confira [Balanceamento de Carga em máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Se o balanceador de carga não estiver configurado para balancear o tráfego entre múltiplas máquinas virtuais, então qualquer evento de manutenção planejada afetará a única máquina virtual atendendo ao tráfego causando uma pane na sua camada de aplicativo. Colocar diversas máquinas virtuais na mesma camada sob o mesmo balanceador de carga e conjunto de disponibilidade habilita o tráfego a ser atendido continuamente pelo menos por uma instância.

## <a name="use-multiple-storage-accounts-for-each-availability-set"></a>Usar várias contas de armazenamento para cada conjunto de disponibilidade
Existem melhores práticas a serem seguidas com relação a contas de armazenamento usadas pelos VHDs (discos rígidos virtuais) contidos na VM. Cada disco (VHD) é um blob de páginas em uma conta de Armazenamento do Azure. É importante garantir que haja redundância e isolamento entre as contas de armazenamento para fornecer alta disponibilidade para VMs no Conjunto de Disponibilidade.

1. **Manter todos os discos (sistema operacional e dados) associados a uma VM na mesma conta de armazenamento**
2. **Os [limites](../articles/storage/storage-scalability-targets.md) da conta de armazenamento devem ser considerados** ao adicionar mais VHDs a uma conta de armazenamento
3. **Use uma conta de armazenamento distinta para cada VM em um conjunto de disponibilidade.** Várias VMs no mesmo conjunto de disponibilidade NÃO devem compartilhar a mesma conta de armazenamento. É aceitável que VMs em diferentes conjuntos de disponibilidade compartilhem contas de armazenamento, desde que as melhores práticas acima sejam seguidas

<!-- Link references -->
[Configurar diversas máquinas virtuais em um conjunto de disponibilidade para redundância]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurar cada camada de aplicativo em conjuntos de disponibilidade separados]: #configure-each-application-tier-into-separate-availability-sets
[Combinar o balanceador de carga com os conjuntos de disponibilidade]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Usar várias contas de armazenamento para cada conjunto de disponibilidade]: #use-multiple-storage-accounts-for-each-availability-set



<!--HONumber=Dec16_HO3-->


