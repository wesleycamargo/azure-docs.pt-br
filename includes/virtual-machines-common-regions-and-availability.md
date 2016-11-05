# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Regiões e disponibilidade para máquinas virtuais do Azure
É importante entender como e onde as VMs (máquinas virtuais) operam no Azure, juntamente com suas opções para maximizar o desempenho, a disponibilidade e a redundância. O Azure opera em vários datacenters no mundo inteiro. Esses datacenters estão agrupados em regiões geográficas, oferecendo a você a flexibilidade de escolher onde compilar seus aplicativos. Este artigo fornece uma visão geral dos recursos de redundância e disponibilidade do Azure.

## <a name="what-are-azure-regions?"></a>O que são as regiões do Azure?
O Azure permite criar recursos, como máquinas virtuais, em regiões geográficas definidas, como “Oeste dos EUA”, “Europa Setentrional” ou “Sudeste Asiático”. Atualmente, há 30 regiões do Azure no mundo todo. Você pode ver a [lista de regiões e suas localizações](https://azure.microsoft.com/regions/). Dentro de cada região, há vários datacenters para fornecer redundância e disponibilidade. Essa abordagem lhe dá a flexibilidade, ao compilar seus aplicativos, de criar VMs mais próximas de seus usuários, bem como de atender quaisquer requisitos legais, de conformidade ou relacionados a impostos.

## <a name="special-azure-regions"></a>Regiões especiais do Azure
Há algumas regiões especiais do Azure para fins de conformidade ou legais que talvez você queira usar ao compilar seus aplicativos. Essas regiões especiais incluem:

* **Gov. EUA - Virgínia** e **Gov. EUA - Iowa**
  * Uma instância lógica e física do Azure isolada da rede, destinada a parceiros e órgãos do governo dos EUA, operada por cidadãos americanos selecionados. Inclui certificações de conformidade adicionais, como [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) e [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Leia mais sobre o [Azure Governamental](https://azure.microsoft.com/features/gov/).
* **Índia Central**, **Índia Ocidental** e **Sul da Índia**
  * Essas regiões estão disponíveis no momento para clientes de licenciamento por volume e parceiros com registro local na Índia. O acesso está aberto para assinaturas online diretas ao longo de 2016.
* **Norte da China** e **Leste da China**
  * Essas regiões estão disponíveis por meio de uma parceria exclusiva entre a Microsoft e a 21Vianet, segundo a qual a Microsoft não mantém diretamente os data centers. Saiba mais sobre o [Microsoft Azure na China](http://www.windowsazure.cn/).
* **Centro da Alemanha** e **Nordeste Alemanha**
  * Essas regiões estão disponíveis atualmente por meio de um modelo de objeto de confiança de dados, por meio do qual os dados do cliente continuam na Alemanha sob o controle da T-Systems, uma empresa Deutsche Telekom, que atua como o objeto de confiança dos dados da Alemanha.

## <a name="region-pairs"></a>Pares de regiões
Cada região do Azure é emparelhada com outra região na mesma área geográfica (como EUA, Europa ou Ásia). Essa abordagem permite a replicação de recursos, como o armazenamento de VM, em uma região geográfica, o que deve reduzir a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física afetarem as duas regiões ao mesmo tempo. Vantagens adicionais dos pares de regiões incluem:

* No caso de uma interrupção mais ampla do Azure, é priorizada uma região de cada par para ajudar a reduzir o tempo de restauração dos aplicativos. 
* As atualizações planejadas do Azure são distribuídas para regiões emparelhadas uma por vez, de modo a minimizar o tempo de inatividade e o risco de interrupção dos aplicativos.
* Os dados continuam residindo na mesma região geográfica que seu par (com exceção do Sul do Brasil) para fins de jurisdição fiscal e aplicação da lei.

Exemplos de pares de regiões incluem:

| Primário | Secundário |
|:--- |:--- |
| Oeste dos EUA |Leste dos EUA |
| Norte da Europa |Europa Ocidental |
| Sudeste Asiático |Ásia Oriental |

Você pode ver a [lista completa de pares de regiões aqui](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Disponibilidade de recursos
Alguns serviços ou recursos de VM estão disponíveis somente em determinadas regiões, como tamanhos específicos de VMs ou tipos de armazenamento. Também há alguns serviços globais do Azure que não exigem que você selecione uma região específica, como [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Gerenciador de Tráfego](../articles/traffic-manager/traffic-manager-overview.md) ou [DNS do Azure](../articles/dns/dns-overview.md). Para ajudá-lo na criação de seu ambiente de aplicativos, você pode verificar a [disponibilidade de serviços do Azure em cada região](https://azure.microsoft.com/regions/#services). 

## <a name="storage-availability"></a>Disponibilidade de armazenamento
Compreender as áreas e regiões do Azure torna-se importante quando você considera as opções de replicação de armazenamento do Azure disponíveis. Quando você cria uma conta de armazenamento, deve selecionar uma das seguintes opções de replicação:

* Armazenamento com redundância local (LRS)
  * Replica seus dados três vezes dentro da região em que você criou sua conta de armazenamento.
* ZRS (Armazenamento com redundância de zona)
  * Replica seus dados três vezes para duas ou três instalações, em uma única região ou em duas regiões.
* Armazenamento com redundância geográfica (GRS)
  * Replica seus dados para uma região secundária a centenas de quilômetros da região primária.
* Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)
  * Replica os dados para uma região secundária, assim como acontece com o GRS, mas também fornece acesso somente leitura aos dados na localização secundária.

A tabela a seguir oferece uma visão geral das diferenças entre os tipos de replicação de armazenamento:

| Estratégia de replicação | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Os dados são replicados entre várias instalações. |Não |Sim |Sim |Sim |
| Os dados podem ser lidos do local secundário e do local primário. |Não |Não |Não |Sim |
| Número de cópias de dados mantidas em nós separados. |3 |3 |6 |6 |

Você pode ler mais sobre as [Opções de replicação de armazenamento do Azure aqui](../articles/storage/storage-redundancy.md).

### <a name="storage-costs"></a>Custos de armazenamento
Os preços variam dependendo do tipo de armazenamento e da disponibilidade que você selecionar. 

* O armazenamento Premium é feito em SSDs (unidades de estado sólido) e é cobrado com base na capacidade do disco.
* O armazenamento padrão é feito em discos giratórios comuns e é cobrado com base na capacidade utilizada e na disponibilidade de armazenamento desejada.
  * Para o RA-GRS, há uma cobrança adicional de Transferência de Dados com Replicação Geográfica pela largura de banda para a replicação desses dados em outra região do Azure.

Consulte [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter informações sobre preços para diferentes opções de tipos de armazenamento e de disponibilidade.

## <a name="azure-images"></a>Imagens do Azure
No Azure, as VMs são criadas de uma imagem. Normalmente, as imagens são do [Azure Marketplace](https://azure.microsoft.com/marketplace/), em que os parceiros podem fornecer um SO pré-configurado completo ou imagens do aplicativo.

Quando cria uma VM de uma imagem no Azure Marketplace, você, na verdade, está trabalhando com modelos. Modelos do Azure Resource Manager são arquivos JSON (JavaScript Object Notation) declarativos que podem ser usados para criar ambientes de aplicativo complexos que consistem em VMs, armazenamento, redes virtuais etc. Você pode ler mais sobre o uso de [modelos do Azure Resource Manager](../articles/resource-group-overview.md), incluindo como [criar seus próprios modelos](../articles/resource-group-authoring-templates.md).

Você também pode criar suas próprias imagens personalizadas e carregá-las usando a [CLI do Azure](../articles/virtual-machines/virtual-machines-linux-upload-vhd.md) ou o [Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-upload-image.md) para criar rapidamente VMs personalizadas para os requisitos do seu build específico.

## <a name="availability-sets"></a>Conjuntos de disponibilidade
Um conjunto de disponibilidade é um agrupamento lógico de VMs que permite que o Azure entenda como o seu aplicativo foi criado para fornecer redundância e disponibilidade. Recomenda-se que duas ou mais VMs sejam criadas dentro de um conjunto de disponibilidade para fornecer um aplicativo altamente disponível e para atender o [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). O conjunto de disponibilidade é composto por dois agrupamentos adicionais que protegem contra falhas de hardware e permitem que atualizações sejam aplicadas com segurança – FDs (domínios de falha) e UDs (domínios de atualização).

![Desenho conceitual da configuração do domínio de atualização e do domínio de falha](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

Você pode ler mais sobre como gerenciar a disponibilidade de [VMs Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md) ou [VMs Windows](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

### <a name="fault-domains"></a>Domínios de falha
Um domínio de falha é um grupo lógico de hardwares subjacentes que compartilham a mesma fonte de alimentação e o mesmo comutador de rede, de forma semelhante a um rack em um datacenter local. À medida que você cria máquinas virtuais em um conjunto de disponibilidade, a plataforma Windows Azure distribui automaticamente suas VMs entre esses domínios de falha. Essa abordagem limita o impacto de possíveis falhas de hardware físico, interrupções de rede ou interrupções de energia.

### <a name="update-domains"></a>Domínios de atualização
Um domínio de atualização é um grupo lógico de hardwares subjacentes que podem passar por manutenção ou ser reinicializados ao mesmo tempo. À medida que você cria máquinas virtuais em um conjunto de disponibilidade, a plataforma Windows Azure distribui automaticamente suas VMs entre esses domínios de atualização. Essa abordagem garante que pelo menos uma instância do aplicativo sempre permaneça em execução enquanto a plataforma Windows Azure passar por manutenção periódica. A ordem de reinicialização dos domínios de atualização pode não ser sequencial durante a manutenção planejada, mas apenas um domínio de atualização é reinicializado por vez.

## <a name="next-steps"></a>Próximas etapas
Agora você pode começar a usar esses recursos de redundância e disponibilidade para criar seu ambiente do Azure. Para obter informações de práticas recomendadas, confira [Práticas recomendadas de disponibilidade do Azure](../articles/best-practices-availability-checklist.md).

<!--HONumber=Oct16_HO2-->


