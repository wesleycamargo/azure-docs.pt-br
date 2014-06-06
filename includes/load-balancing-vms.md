<properties writer="josephd" editor="tysonn" manager="dongill" />

#Balanceamento de carga de máquinas virtuais#

Todas as máquinas virtuais que você cria no Azure podem se comunicar automaticamente com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual usando um canal de rede privada. Todas as outras comunicações de entrada, como o tráfego iniciado a partir de hosts da Internet ou máquinas virtuais em outros serviços de nuvem ou redes virtuais, exigem um ponto de extremidade.

Os pontos de extremidade podem ser usados para diferentes finalidades. O uso e a configuração padrão de pontos de extremidade em uma máquina virtual que você cria com o Portal de Gerenciamento do Azure são para o protocolo RDP (Remote Desktop) e o tráfego da sessão remoto do Windows PowerShell. Esses pontos de extremidade permitem que você administre remotamente a máquina virtual pela Internet. 

Outro uso de pontos de extremidade é a configuração do Balanceador de Carga do Azure para distribuir um tipo específico de tráfego entre várias máquinas virtuais ou serviços. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web ou funções web.

Cada ponto de extremidade definido para uma máquina virtual é atribuído a uma porta pública e privada, seja TCP ou UDP. Os hosts da Internet enviam o tráfego de entrada para o endereço IP público do serviço em nuvem e de uma porta pública. As máquinas virtuais e serviços dentro do serviço em nuvem detectam seu endereço IP privado e porta privada. O Balanceador de Carga mapeia o endereço IP público e o número da porta do tráfego de entrada para o endereço IP privado e o número da porta da máquina virtual e vice-versa para o tráfego de resposta da máquina virtual.

Quando você configurar o balanceamento de carga de tráfego entre várias máquinas virtuais ou serviços, o Azure fornece distribuição aleatória do tráfego de entrada.

Para um serviço em nuvem que contenha instâncias de funções web ou funções de trabalho, você pode definir um ponto de extremidade público na definição do serviço. Para um serviço em nuvem que contenha as máquinas virtuais, você pode adicionar um ponto de extremidade para uma máquina virtual quando você cria ou você pode adicionar o ponto de extremidade mais tarde. 

A figura a seguir mostra um ponto de extremidade do balanceamento de carga para o tráfego de web padrão (não criptografado) que é compartilhado entre as três máquinas virtuais para a porta TCP pública e privada de 80. Essas três máquinas virtuais estão em um conjunto de balanceamento de carga.

![loadbalancing](./media/load-balancing-vms/LoadBalancing.png)

Quando os clientes de Internet enviam solicitações de página da web para o endereço IP público do serviço em nuvem e a porta TCP 80, o Balanceador de Carga executa um balanceamento aleatório dessas solicitações entre as três máquinas virtuais no conjunto de balanceamento de carga.

Para criar um conjunto de balanceamento de carga de máquinas virtuais do Azure, use as seguintes etapas:


- [Etapa 1: crie a primeira máquina virtual] []
- [Etapa 2: crie máquinas virtuais adicionais no mesmo serviço em nuvem] []
- [Etapa 3: crie um conjunto de balanceamento de carga com a primeira máquina virtual] []
- [Etapa 4: adicione máquinas virtuais ao conjunto de balanceamento de carga] []

## <a id="firstmachine"> </a>Etapa 1: crie a primeira máquina virtual ##

Se você ainda não tiver feito isso, entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com). Você pode criar a primeira máquina virtual usando Da Galeria ou o método de Criação Rápida. 

- **Da Galeria** - o método **Da Galeria** permite que você crie pontos de extremidade ao criar a máquina virtual e permite que você especifique um nome para o serviço em nuvem criado durante a criação da máquina virtual. Para obter instruções, consulte [Criação de uma máquina virtual executando Linux] ou [Criação de uma máquina virtual executando Windows Server].

- **Criação rápida**: cria uma máquina virtual escolhendo uma imagem da Galeria de Imagens e fornece informações básicas. Ao usar esse método, você precisará adicionar o ponto de extremidade depois de criar a máquina virtual. Esse método cria um serviço em nuvem usando um nome padrão. Para obter mais informações, consulte [Como criar rapidamente uma máquina virtual] []. 

**Observação**: depois que a máquina virtual é criada com a Criação Rápida, a página de Serviços de Nuvem do Portal de Gerenciamento lista o nome do novo serviço em nuvem bem como outras informações sobre o serviço.

## <a id="addmachines"> </a>Etapa 2: crie máquinas virtuais adicionais no mesmo serviço em nuvem ##

Crie as máquinas virtuais adicionais no mesmo serviço em nuvem com a primeira máquina virtual usando o método Da Galeria.

## <a id="loadbalance"> </a>Etapa 3: crie um conjunto de balanceamento de carga com a primeira máquina virtual ##

1. No Portal de Gerenciamento do Azure, clique em **Máquinas Virtuais** e depois clique no nome da primeira máquina virtual.
	
2. Clique em **Pontos de Extremidade** e depois em **Adicionar**.

3. Na página Adicionar um ponto de extremidade a uma máquina virtual, clique na seta para a direita.
	
4. Na página Especificar os detalhes do ponto de extremidade:

	- Em **Nome**, digite um nome para o ponto de extremidade ou selecione da lista de pontos de extremidade predefinidos para protocolos comuns.
	-Em **Protocolo**, selecione o protocolo necessário para o tipo de ponto de extremidade, TCP ou UDP, conforme necessário.
	-Em **Porta pública** e **Porta Privada**, digite os números de porta que você deseja que a máquina virtual use, conforme necessário. Você pode usar as regras de firewall e porta privada na máquina virtual para redirecionar o tráfego de uma maneira que seja apropriado ao seu aplicativo. A porta privada pode ser a mesma que a porta pública. Por exemplo, para um ponto de extremidade para o tráfego da web (HTTP), você pode atribuir a porta 80 à porta pública e privada.

5. Selecione **Criar um conjunto de balanceamento de carga** e depois clique na seta para a direita. 

6. Na página Configurar o conjunto de balanceamento de carga, digite um nome para o conjunto de balanceamento de carga e, em seguida, atribua os valores do comportamento do teste do Balanceador de Carga do Azure. O Balanceador de Carga usa testes para determinar se as máquinas virtuais do conjunto de balanceamento de carga estão disponíveis para receber o tráfego de entrada.

7. Clique na marca de seleção para criar o ponto de extremidade de balanceamento de carga. Você verá **Sim** na coluna **Nome do conjunto de balanceamento de carga** da página **Pontos de extremidade** da máquina virtual.


## <a id="addtoset"> </a>Etapa 4: adicione máquinas virtuais ao conjunto de balanceamento de carga ##
Depois de criar o conjunto de balanceamento de carga, adicione as outras máquinas virtuais a ele. Para cada máquina virtual no mesmo serviço em nuvem:

1. No Portal de Gerenciamento, clique em **Máquinas Virtuais**, em seguida clique no nome da máquina virtual, depois clique em **Pontos de extremidade** e então clique em **Adicionar**.
	
2. Na página Adicionar um ponto de extremidade a uma máquina virtual, clique em **Adicionar um ponto de extremidade a um conjunto existente de balanceamento de carga**, selecione o nome do conjunto de balanceamento de carga e, em seguida, clique na seta para a direita.
	
3. Na página Especificar os detalhes do ponto de extremidade, digite um nome para o ponto de extremidade e, em seguida, clique na marca de seleção.

[Etapa 1: crie a primeira máquina virtual]: #firstmachine
[Etapa 2: crie máquinas virtuais adicionais no mesmo serviço em nuvem]: #addmachines
[Etapa 3: crie um conjunto de balanceamento de carga com a primeira máquina virtual]: #loadbalance
[Etapa 4: adicione máquinas virtuais ao conjunto de balanceamento de carga]: #addtoset


<!-- LINKS -->

[Criação de uma máquina virtual executando Linux]: ../virtual-machines-linux-tutorial

[Criação de uma máquina virtual executando Windows Server]: ../virtual-machines-windows-tutorial

[Como criar rapidamente uma máquina virtual]: ../virtual-machines-quick-create

[Como conectar máquinas virtuais em um serviço em nuvem]: ../virtual-machines-connect-cloud-service

[Introdução ao PowerShell do Azure (a página pode estar em inglês)]:http://msdn.microsoft.com/pt-br/library/jj156055.aspx

[Visão geral da rede do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063

