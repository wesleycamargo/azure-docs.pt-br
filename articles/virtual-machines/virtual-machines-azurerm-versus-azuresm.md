<properties
   pageTitle="Provedores de Computação, Rede e Armazenamento do Azure no Gerenciador de Recursos do Azure"
   description="Visão geral conceitual dos Provedores de Recursos de Computação, Rede e Armazenamento (CRP, NRP e SRP)"
   services="virtual-machines"
   documentationCenter="dev-center-name"
   authors="mahthi"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="mahthi"/>

# Computação do Azure, Provedores de Rede e Armazenamento no Gerenciador de Recursos do Azure

A inclusão de recursos de Computação, Rede e Armazenamento no Gerenciador de Recursos do Azure simplificará  fundamentalmente a implantação e o gerenciamento de aplicativos complexos em execução no IaaS. Muitos aplicativos exigem uma combinação de recursos, incluindo uma Rede Virtual, uma Conta de Armazenamento, uma Máquina Virtual e uma Interface de Rede. O Gerenciador de Recursos do Azure oferece a capacidade de criar um modelo JSON para implantar e gerenciar todos esses recursos juntos como um único aplicativo.

## Vantagens da integração de Computação, Rede e Armazenamento no Gerenciador de Recursos do Azure

O Gerenciador de Recursos do Azure oferece a capacidade de utilizar facilmente modelos de aplicativos predefinidos ou criar um modelo de aplicativo para implantar e gerenciar recursos de computação, rede e armazenamento no Azure. Nesta seção, vamos examinar as vantagens da implantação de recursos por meio do Gerenciador de Recursos do Azure.

-	Complexidade simplificada: crie, integre e colabore em aplicativos complicados que podem incluir toda a gama de recursos do Azure (como Sites, Bancos de Dados SQL, Máquinas Virtuais ou Redes Virtuais) por meio de um arquivo de modelo compartilhável
-	A flexibilidade de ter implantações repetíveis para desenvolvimento, administradores de sistema e operação de desenvolvimento quando você usa o mesmo arquivo de modelo
-	A profunda integração de Extensões de VM (Scripts Personalizados, DSC, Chef, Puppet etc.) com o Gerenciador de Recursos do Azure em um arquivo de modelo permite a fácil orquestração da configuração na VM
-	Definição de marcas e propagação de cobrança dessas marcas para recursos de Computação, Rede e Armazenamento
-	Gerenciamento de acesso de recursos organizacionais simples e preciso usando o RBAC (Controle de Acesso Baseado em Função) do Azure
-	Atualização simplificada, modificando o modelo original e, em seguida, reimplantando-o


## Avanços das APIs de Computação, Rede e Armazenamento no Gerenciador de Recursos do Azure

Além das vantagens mencionadas acima, há alguns avanços de desempenho significativos nas APIs lançadas.

-	Habilitação da implantação paralela e de grande volume de Máquinas Virtuais
-	Suporte a três Domínios de Falha em Conjuntos de Disponibilidade
-	Extensão de Script Personalizado aprimorada que permite a especificação de scripts por meio de qualquer URL personalizada publicamente acessível
- Integração de Máquinas Virtuais ao Cofre da Chave do Azure para o armazenamento de alta segurança e a implantação privada de segredos de [Módulos de Segurança de Hardware](http://wikipedia.org/wiki/FIPS_140-2) [validados para FIPS](http://wikipedia.org/wiki/Hardware_security_module)
-	Fornece os blocos de construção básicos da rede por meio de APIs para habilitar os clientes a criar aplicativos complexos que incluem Interfaces de Rede, Balanceadores de Carga e Redes Virtuais
-	As Interfaces de Rede como um novo objeto permitem que uma configuração de rede complicada seja mantida e reutilizada para Máquinas Virtuais
-	Os Balanceadores de Carga como um recurso de primeira classe habilitam as atribuições de Endereço IP
-	As APIs de Rede Virtual granulares permitem simplificar o gerenciamento de Redes Virtuais individuais

## Diferenças conceituais com a introdução de novas APIs

Nesta seção, apresentaremos algumas das mais importantes diferenças conceituais entre as APIs baseadas em XML disponíveis hoje e as APIs baseadas em JSON disponíveis por meio do Gerenciador de Recursos do Azure para Computação, Rede e Armazenamento.

 Item | Gerenciamento de Serviços do Azure (baseado em XML) | Provedores de Computação, Rede e Armazenamento (baseados em JSON)
 ---|---|---
| Serviço de Nuvem para Máquinas Virtuais |	O Serviço de Nuvem era um contêiner para manter as máquinas virtuais que precisavam de Disponibilidade da plataforma e Balanceamento de Carga. | O Serviço de Nuvem não é mais um objeto necessário para criar uma Máquina Virtual usando o novo modelo. |
| Conjuntos de Disponibilidade | A disponibilidade para a plataforma era indicada por meio da configuração do mesmo "AvailabilitySetName" nas Máquinas Virtuais. A contagem máxima de domínios de falha era 2. | O Conjunto de Disponibilidade é um recurso exposto pelo Provedor Microsoft.Compute. Máquinas Virtuais que exigem alta disponibilidade devem ser incluídas no Conjunto de Disponibilidade. A contagem máxima de domínios de falha agora é 3. |
| Grupos de afinidade |	Grupos de Afinidade eram necessários para criar Redes Virtuais. No entanto, com a introdução de Redes Virtuais Regionais, isso não é mais necessário. |Para simplificar, o conceito de Grupos de Afinidade não existe nas APIs expostas por meio do Gerenciador de Recursos do Azure. |
| Balanceamento de Carga | A criação de um Serviço de Nuvem fornece um balanceador de carga implícito para as Máquinas Virtuais implantadas. | O Balanceador de Carga é um recurso exposto pelo provedor Microsoft.Network. A interface de rede principal das Máquinas Virtuais que precisam ter o balanceamento de carga deve fazer referência ao balanceador de carga. Os Balanceadores de Carga podem ser internos ou externos. [Leia mais.](resource-groups-networking.md) |
|Endereço IP Virtual | Os Serviços de Nuvem terão um VIP (Endereço IP Virtual) padrão quando uma VM for adicionada a um serviço de nuvem. O Endereço IP Virtual é o endereço associado ao balanceador de carga implícito. | O Endereço IP público é um recurso exposto pelo provedor Microsoft.Network. O Endereço IP público pode ser Estático (Reservado) ou Dinâmico. IPs Públicos Dinâmicos podem ser atribuídos a um Balanceador de Carga. IPs Públicos podem ser protegidos usando Grupos de Segurança. |
|Endereço IP Reservado|	Você pode reservar um endereço IP no Azure e associá-lo a um Serviço de Nuvem para garantir que o Endereço IP seja temporário. | O Endereço IP Público pode ser criado no modo "Estático" e oferece a mesma funcionalidade de um "Endereço IP Reservado". IPs Públicos Estáticos só podem ser atribuídos a um Balanceador de carga no momento. |
|Endereço IP Público (PIP) por VM | Endereços IP Públicos também podem ser associados diretamente a uma VM. | O Endereço IP público é um recurso exposto pelo provedor Microsoft.Network. O Endereço IP público pode ser Estático (Reservado) ou Dinâmico. No entanto, apenas IPs Públicos dinâmicos podem ser atribuídos a uma Interface de Rede para obter um IP Público por VM no momento. |
|Pontos de extremidade| Pontos de Extremidade de Entrada precisam ser configurados em uma Máquina Virtual para que seja aberta a conectividade para determinadas portas. Um dos modos comuns de se conectar a máquinas virtuais, realizado com a configuração de pontos de extremidade de entrada. | Regras NAT de Entrada podem ser configuradas em Balanceadores de Carga para obter a mesma capacidade de habilitar pontos de extremidade em portas específicas para conectar-se às VMs. |
|Nome DNS| Um serviço de nuvem teria um Nome DNS exclusivo implícito. Por exemplo: `mycoffeeshop.cloudapp.net`. | Os nomes DNS são parâmetros opcionais que podem ser especificados em um recurso de Endereço IP Público. O FQDN estará no seguinte formato: `<domainlabel>.<region>.cloudapp.azure.com`. |
|Interfaces de Rede | A Interface de Rede Primária e Secundária e suas propriedades foram definidas como a configuração de rede de uma Máquina virtual. | A Interface de Rede é um recurso exposto pelo Provedor Microsoft.Network. O ciclo de vida da Interface de Rede não está vinculado a uma Máquina Virtual. |

## Introdução a Modelos do Azure para Máquinas Virtuais

Você pode começar a usar os Modelos do Azure aproveitando as várias ferramentas que oferecemos para desenvolver e implantar na plataforma.

### Portal do Azure

O Portal do Azure continuará a ter a opção de implantar Máquinas Virtuais e Máquinas Virtuais (Visualização) simultaneamente. O Portal do Azure também permitirá implantações de modelos personalizados.

### PowerShell do Azure

O PowerShell do Azure terá dois modos de implantação: modo **AzureServiceManagement** e modo **AzureResourceManager**. O modo AzureResourceManager agora também conterá os cmdlets para gerenciar Máquinas Virtuais, Redes Virtuais e Contas de Armazenamento. Saiba mais sobre isso [aqui](../powershell-azure-resource-manager.md).

### CLI do Azure

A CLI do Azure (Interface de Linha de Comando do Azure) terá dois modos de implantação: modo **AzureServiceManagement** e modo **AzureResourceManager**. O modo AzureResourceManager agora também conterá comandos para gerenciar Máquinas Virtuais, Redes Virtuais e Contas de Armazenamento. Saiba mais sobre isso [aqui](xplat-cli-azure-resource-manager.md).

### Visual Studio

Com a última versão do SDK do Azure para Visual Studio, você pode criar e implantar Máquinas Virtuais e aplicativos complexos diretamente no Visual Studio. O Visual Studio oferece a capacidade de implantar por meio de uma lista predefinida de modelos ou iniciar com um modelo vazio.

### APIs REST

Você pode encontrar a documentação detalhada da API REST para Provedores de Recursos de Computação, Rede e Armazenamento [aqui](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Perguntas frequentes

**Posso criar uma Máquina Virtual usando o novo Gerenciador de Recursos do Azure para implantar em uma Rede Virtual ou Conta de Armazenamento criada usando as APIs de Gerenciamento de Serviços do Azure?**

Não há suporte para isso no momento. Você não pode implantar usando as novas APIs do Gerenciador de Recursos do Azure para implantar uma Máquina Virtual em uma Rede Virtual criada usando as APIs de Gerenciamento de Serviços.

**Posso criar uma Máquina Virtual usando as novas APIs do Gerenciador de Recursos do Azure por meio de uma imagem do usuário criada usando as APIs de Gerenciamento de Serviços do Azure?**

Não há suporte para isso no momento. No entanto, você pode copiar os arquivos VHD de uma Conta de Armazenamento criada usando as APIs de Gerenciamento de Serviços e copiá-los para uma nova conta criada usando as novas APIs do Gerenciador de Recursos do Azure.

**Qual é o impacto sobre a cota da minha assinatura?**

As cotas para as Máquinas Virtuais, Redes Virtuais e Contas de Armazenamento criadas por meio das novas APIs do Gerenciador de Recursos do Azure são separadas das cotas que você tem no momento. Cada assinatura obtém novas cotas para criar os recursos usando as novas APIs. Você pode ler mais sobre as cotas adicionais [aqui](../azure-subscription-service-limits.md).

**Posso continuar a usar meus scripts automatizados para provisionar Máquinas Virtuais, Redes Virtuais, Contas de Armazenamento, etc. por meio das novas APIs do Gerenciador de Recursos do Azure?**

Todos os scripts e a automação que você criou continuarão a funcionar para as Máquinas Virtuais e Redes Virtuais existentes criadas no modo de Gerenciamento de Serviços do Azure. No entanto, os scripts devem ser atualizados para usar o novo esquema para criar os mesmos recursos por meio do novo modo do Gerenciador de Recursos do Azure. Leia mais sobre como modificar seus [Scripts da CLI do Azure](xplat-cli-azure-manage-vm-asm-arm.md).

**As Redes Virtuais criadas usando as novas APIs do Gerenciador de Recursos do Azure podem ser conectadas a meu circuito do Express Route?**

Não há suporte para isso no momento. Você não pode conectar as Redes Virtuais criadas usando as novas APIs do Gerenciador de Recursos do Azure a um circuito do Express Route. Haverá suporte para isso no futuro.

<!--HONumber=52-->
 