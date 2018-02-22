- A rede virtual deve estar na mesma **região** e **assinatura** do Azure do que a conta do Lote.

- Para os pools criados com uma configuração de máquina virtual, somente redes virtuais baseadas no Azure Resource Manager têm suporte. Para os pools criados com uma configuração de serviços de nuvem, há suporte somente para as redes virtuais clássicas. 
  
- Para usar uma rede virtual clássica, a entidade de serviço `MicrosoftAzureBatch` deverá ter a função RBAC (Controle de Acesso Baseado em Função) `Classic Virtual Machine Contributor` para a rede virtual especificada. Para usar uma rede virtual com base no Azure Resource Manager, você precisa ter permissões para acessar a rede virtual e implantar VMs na sub-rede.

- A sub-rede especificada para o pool deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs direcionadas para o pool, ou seja, a soma das propriedades `targetDedicatedNodes` e `targetLowPriorityNodes` do pool. Se a sub-rede não tiver endereços IP não atribuídos suficientes, o pool alocará parcialmente os nós de computação e ocorrerá um erro de redimensionamento. 

- A sub-rede deve permitir a comunicação do serviço Lote para que seja capaz de agendar tarefas nos nós de computação. Isso pode ser confirmado por meio da verificação se a rede virtual tem NSGs (grupos de segurança de rede) associados. Se a comunicação com os nós de computação na sub-rede especificada for negada por um NSG, o serviço Lote definirá o estado dos nós de computação como **inutilizável**. 

- Se a rede virtual especificada tiver NSGs (Grupos de Segurança de Rede) associados e/ou um firewall, configure as portas de entrada e saída conforme mostrado nas tabelas a seguir:


  |    Portas de destino    |    Endereço IP de origem      |   Porta de origem    |    O Lote adiciona NSGs?    |    Necessário para que a máquina virtual possa ser usada?    |    Ação do usuário   |
  |---------------------------|---------------------------|----------------------------|----------------------------|-------------------------------------|-----------------------|
  |   <ul><li>Para pools criados com a configuração de máquina virtual: 29876, 29877</li><li>Para os pools criados com a configuração de serviços de nuvem: 10100, 20100, 30100</li></ul>        |    * ou para segurança adicional, especifique os endereços IP do serviço de Lote. Para obter a lista de endereços IP do serviço do Lote, entre em contato com o Suporte do Azure. | * ou 443 |    Sim. O Lote adiciona os NSGs no nível dos NIC (adaptadores de rede) anexados às VMs. Essas NSGs permitem o tráfego somente de endereços IP com função de serviço do Lote. Mesmo que você abra essas portas para toda a Web, o tráfego será bloqueado no NIC. |    sim  |  Não é necessário especificar um NSG, pois o Lote permite somente os endereços IP do Lote. <br /><br /> No entanto, se você especificar um NSG, verifique se essas portas estão abertas para tráfego de entrada. <br /><br /> Se você especificar * como o IP de origem em seu NSG, o Lote ainda adicionará os NSGs no nível de NIC anexados às VMs. |
  |    3389 (Windows), 22 (Linux)               |    Máquinas de usuário, usadas para depuração, para que você possa acessar a máquina virtual remotamente.    |   *  | Não                                     |    Não                     |    Adicione os NSGs se quiser permitir o acesso remoto (RDP ou SSH) à máquina virtual.   |                                


  |    Portas de saída    |    Destino    |    O Lote adiciona NSGs?    |    Necessário para que a máquina virtual possa ser usada?    |    Ação do usuário    |
  |------------------------|-------------------|----------------------------|-------------------------------------|------------------------|
  |    443    |    Armazenamento do Azure    |    Não     |    sim    |    Se você adicionar NSGs, verifique se essa porta está aberta para tráfego de saída.    |

   Além disso, verifique se seu ponto de extremidade do Armazenamento do Azure pode ser resolvido por servidores DNS personalizados que servem sua rede virtual. Especificamente, as URLs no formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, e `<account>.blob.core.windows.net` devem poder ser resolvidas. 

   Se você adicionar um Gerenciador de Recursos com base em NSG, você pode fazer uso de [marcas de serviço](../articles/virtual-network/security-overview.md#service-tags) para selecionar os endereços IP de armazenamento para a região específica para conexões de saída. Observe que os endereços IP de armazenamento devem ser da mesma região que sua conta de Lote e a rede virtual. As marcas de serviço estão atualmente em versão prévia nas regiões do Azure selecionadas.