# Visão geral das regiões e da disponibilidade
O Azure opera em vários datacenters no mundo inteiro. Esses datacenters estão agrupados em regiões geográficas, oferecendo a você a flexibilidade de escolher onde compilar seus aplicativos. Você também pode incorporar alta disponibilidade a seus aplicativos, tirando proveito dos recursos de redundância e disponibilidade dentro da plataforma do Azure, como os conjuntos de disponibilidade e a replicação de armazenamento.

## O que são as regiões do Azure?
O Azure permite criar recursos, como máquinas virtuais, em regiões geográficas definidas, como “Oeste dos EUA”, “Europa Setentrional” ou “Sudeste Asiático”. Atualmente, há 26 regiões do Azure no mundo todo. Você pode ver a [lista de regiões e suas localizações](https://azure.microsoft.com/regions/). Dentro de cada região, há vários datacenters para fornecer redundância e disponibilidade. Isso lhe dá a flexibilidade, ao compilar seus aplicativos, de criar VMs (máquinas virtuais) mais próximas de seus usuários, bem como de atender quaisquer requisitos legais, de conformidade ou relacionados a impostos.

## Regiões especiais do Azure
Há algumas regiões especiais do Azure para fins de conformidade ou legais que talvez você queira usar ao compilar seus aplicativos.

As regiões especiais existentes incluem:

- **Gov. EUA - Virgínia** e **Gov. EUA - Iowa**
    - Uma instância lógica e física do Azure isolada da rede, destinada a parceiros e órgãos do governo dos EUA, operada por cidadãos americanos selecionados. Inclui certificações de conformidade adicionais, como [FedRAMP](https://www.microsoft.com/pt-BR/TrustCenter/Compliance/FedRAMP) e [DISA](https://www.microsoft.com/pt-BR/TrustCenter/Compliance/DISA). Leia mais sobre o [Azure Governamental](https://azure.microsoft.com/features/gov/).
- **Leste da Austrália** e **Sudeste da Austrália**
    - Essas regiões estão disponíveis para clientes com presença comercial na Austrália ou na Nova Zelândia.
- **Índia Central**, **Índia Ocidental** e **Sul da Índia**
    - Atualmente, essas regiões estão disponíveis para clientes de licenciamento por volume e parceiros com um registro local na Índia, e o acesso será disponibilizado para assinaturas online diretas ao longo de 2016.
- **Norte da China** e **Leste da China**
    - Essas regiões estão disponíveis por meio de uma parceria exclusiva entre a Microsoft e a 21Vianet, segundo a qual a Microsoft não mantém diretamente os data centers. Saiba mais sobre o [Microsoft Azure na China](http://www.windowsazure.cn/).

As regiões especiais anunciadas incluem:

- **Centro da Alemanha** e **Nordeste Alemanha**
    - O Azure estará disponível por meio de um novo modelo de objeto de confiança de dados, por meio do qual os dados do cliente continuam na Alemanha sob o controle da T-Systems, uma empresa Deutsche Telekom, que atua como o objeto de confiança dos dados da Alemanha.

## Pares de regiões
Cada região do Azure é emparelhada com outra região na mesma área geográfica (como EUA, Europa ou Ásia). Isso permite a replicação de recursos, como o armazenamento de VM, em uma região geográfica, o que deve reduzir a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física afetarem as duas regiões ao mesmo tempo. Vantagens adicionais dos pares de regiões incluem:

- No caso de uma interrupção mais ampla do Azure, é priorizada uma região de cada par para ajudar a reduzir o tempo de restauração dos aplicativos.
- As atualizações planejadas do Azure são distribuídas para regiões emparelhadas uma por vez, de modo a minimizar o tempo de inatividade e o risco de interrupção dos aplicativos.
- Os dados continuarão residindo na mesma região geográfica que seu par (com exceção do Sul do Brasil) para atender a requisitos de jurisdição de vigência fiscal e legal.

Exemplos de pares de regiões incluem:

| Primário | Secundário |
|:---------------|:------------|
| Oeste dos EUA | Leste dos EUA |
| Norte da Europa | Europa Ocidental |
| Sudeste Asiático | Ásia Oriental |

Você pode ver a [lista completa de pares de regiões aqui](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## Disponibilidade de recursos
Alguns serviços ou recursos de VM estão disponíveis somente em determinadas regiões, como tamanhos específicos de VMs ou tipos de armazenamento. Também há alguns serviços globais do Azure que não exigem que você selecione uma região específica, como [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Gerenciador de Tráfego](../articles/traffic-manager/traffic-manager-overview.md) ou [DNS do Azure](../articles/dns/dns-overview.md). Para ajudá-lo na criação de seu ambiente de aplicativos, você pode verificar a [disponibilidade de serviços do Azure em cada região](https://azure.microsoft.com/regions/#services).


## Disponibilidade de armazenamento
Compreender as áreas e regiões do Azure torna-se importante quando você considera as opções de replicação de armazenamento do Azure disponíveis. Quando você cria uma conta de armazenamento, deve selecionar uma das seguintes opções de replicação:

- Armazenamento com redundância local (LRS)
    - Replica seus dados três vezes dentro da região em que você criou sua conta de armazenamento.
- ZRS (Armazenamento com redundância de zona)
    - Replica seus dados três vezes para duas ou três instalações, em uma única região ou em duas regiões.
- Armazenamento com redundância geográfica (GRS)
    - Replica seus dados para uma região secundária a centenas de quilômetros da região primária.
- Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)
    - Replica os dados para uma região secundária, assim como acontece com o GRS, mas também fornece acesso somente leitura aos dados na localização secundária.

A tabela a seguir oferece uma visão geral das diferenças entre os tipos de replicação de armazenamento:

| Estratégia de replicação | LRS | ZRS | GRS | RA-GRS |
|:-----------------------------------------------------------------------------------|:----|:----|:----|:-------|
| Os dados são replicados entre várias instalações. | Não | Sim | Sim | Sim |
| Os dados podem ser lidos do local secundário, bem como do local primário. | Não | Não | Não | Sim |
| Número de cópias de dados mantidas em nós separados. | 3 | 3 | 6 | 6 |

Você pode ler mais sobre as [Opções de replicação de armazenamento do Azure aqui](../articles/storage/storage-redundancy.md).

### Custos de armazenamento
Os preços variam dependendo do tipo de armazenamento e da disponibilidade que você selecionar.

- O armazenamento padrão é feito em discos giratórios comuns e é cobrado com base na capacidade utilizada e na disponibilidade de armazenamento desejada.
    - Para o RA-GRS, há uma cobrança adicional de Transferência de Dados com Replicação Geográfica pela largura de banda para a replicação desses dados em outra região do Azure.
- O armazenamento Premium é feito em SSDs (unidades de estado sólido) e é cobrado com base na capacidade do disco.

Consulte [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter informações sobre preços para diferentes opções de tipos de armazenamento e de disponibilidade.


## Imagens do Azure
No Azure, as VMs são criadas de uma imagem. Normalmente, será uma imagem criada por meio do [Azure Marketplace](https://azure.microsoft.com/marketplace/), em que os parceiros poderão fornecer imagens de aplicativos ou SOs completos pré-configurados.

Quando cria uma VM de uma imagem no Azure Marketplace, você, na verdade, está trabalhando com modelos. Modelos do Azure Resource Manager são arquivos JSON (JavaScript Object Notation) declarativos que podem ser usados para criar ambientes de aplicativo complexos que consistem em VMs, armazenamento, redes virtuais etc. Você pode ler mais sobre o uso de [modelos do Azure Resource Manager](../articles/resource-group-overview.md), incluindo como [criar seus próprios modelos](../articles/resource-group-authoring-templates.md).

Você também pode criar suas próprias imagens personalizadas e carregá-las usando a [CLI do Azure](../articles/virtual-machines/virtual-machines-linux-upload-vhd.md) ou o [Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-upload-image.md) para criar rapidamente VMs personalizadas para os requisitos do seu build específico. Ao usar imagens personalizadas, as VMs precisam ser armazenados na mesma conta de armazenamento que a própria imagem. Você não pode carregar uma imagem em uma única região e depois criar VMs por meio dela em outras regiões do Azure.


## Conjuntos de disponibilidade
Um conjunto de disponibilidade é um agrupamento lógico de VMs que permite que o Azure entenda como o seu aplicativo foi criado para fornecer redundância e disponibilidade. Recomenda-se que duas ou mais VMs sejam criadas dentro de um conjunto de disponibilidade para fornecer um aplicativo altamente disponível e para atender o [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). O conjunto de disponibilidade é composto por dois agrupamentos adicionais que protegem contra falhas de hardware e permitem que atualizações sejam aplicadas com segurança – FDs (domínios de falha) e UDs (domínios de atualização).

![Desenho conceitual da configuração do domínio de atualização e do domínio de falha](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

Você pode ler mais sobre como gerenciar a disponibilidade de [VMs Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md) ou [VMs Windows](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

### Domínios de falha
Um domínio de falha é um grupo lógico de hardwares subjacentes que compartilham a mesma fonte de alimentação e o mesmo comutador de rede, de forma semelhante a um rack em um datacenter local. Conforme você cria VMs dentro de um conjunto de disponibilidade, a plataforma do Azure distribui automaticamente suas VMs entre esses domínios de falha para limitar o impacto de possíveis falhas de hardware físico, interrupções de rede ou interrupções de energia.

### Domínios de atualização
Um domínio de atualização é um grupo lógico de hardwares subjacentes que podem passar por manutenção ou ser reinicializados ao mesmo tempo. Conforme você cria VMs dentro de um conjunto de disponibilidade, a plataforma do Azure distribui automaticamente suas VMs entre esses domínios de atualização para garantir que pelo menos uma instância do seu aplicativo sempre permaneça em execução quando a plataforma do Azure passar por manutenção periódica. Observe que a ordem de reinicialização dos domínios de atualização pode não ser sequencial durante a manutenção planejada, mas apenas um domínio de atualização será reinicializado por vez.


## Próximas etapas
Você pode ler detalhes mais específicos sobre as [práticas recomendadas de disponibilidade do Azure](../articles/best-practices-availability-checklist.md).

<!---HONumber=AcomDC_0803_2016-->