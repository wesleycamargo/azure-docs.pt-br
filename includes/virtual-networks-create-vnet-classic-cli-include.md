## Como criar uma rede virtual clássica usando a CLI do Azure

Você pode usar a CLI do Azure para gerenciar os recursos do Azure no prompt de comando de qualquer computador com Windows, Linux ou OSX. Para criar uma rede virtual usando a CLI do Azure, siga as etapas abaixo.

1. Se você nunca usou a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](xplat-cli.md) e siga as instruções até o ponto em que seleciona sua conta e assinatura do Azure.
2. Execute o comando **azure network vnet create** para criar uma rede virtual e uma sub-rede, conforme mostrado abaixo. Observe a saída do comando da CLI. A lista mostrada depois da saída explica os parâmetros usados.

			azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
	
			info:    Executing command network vnet create
			+ Looking up network configuration
			+ Looking up locations
			+ Setting network configuration
			info:    network vnet create command OK

	- **--vnet**. Nome da rede virtual a ser criada. Em nosso cenário, *TestVNet*
	- **-e (ou --address-space)**. Espaço de endereço da rede virtual. Em nosso cenário, *192.168.0.0*
	- **-i (ou -cidr)**. Máscara de rede no formato CIDR. Em nosso cenário, *16*.
	- **-n (ou --subnet-name**). Nome da primeira sub-rede. Em nosso cenário, *FrontEnd*.
	- **-p (ou --subnet-start-ip)**. Endereço IP inicial da sub-rede ou espaço de endereço da sub-rede. Em nosso cenário, *192.168.1.0*.
	- **-r (ou --subnet-cidr)**. Máscara de rede no formato CIDR para a sub-rede. Em nosso cenário, *24*.
	- **-l (ou --location)**. Região do Azure em que a rede virtual será criada. Em nosso cenário, *Central US*.

3. Execute o comando **azure network vnet subnet create** para criar uma sub-rede, conforme mostrado abaixo. Observe a saída do comando. A lista mostrada depois da saída explica os parâmetros usados.

			azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
	
			info:    Executing command network vnet subnet create
			+ Looking up network configuration
			+ Creating subnet "BackEnd"
			+ Setting network configuration
			+ Looking up the subnet "BackEnd"
			+ Looking up network configuration
			data:    Name                            : BackEnd
			data:    Address prefix                  : 192.168.2.0/24
			info:    network vnet subnet create command OK

	- **-t (ou --vnet-name**. Nome da rede virtual onde a sub-rede será criada. Em nosso cenário, *TestVNet*.
	- **-n (ou --name)**. Nome da nova sub-rede. Em nosso cenário, *BackEnd*.
	- **-a (ou --address-prefix)**. Bloco CIDR da sub-rede. Em nosso cenário, *192.168.2.0/24*.

4. Execute o comando **azure network vnet show** para exibir as propriedades de nova vnet, conforme mostrado abaixo.

			azure network vnet show

			info:    Executing command network vnet show
			Virtual network name: TestVNet
			+ Looking up the virtual network sites
			data:    Name                            : TestVNet
			data:    Location                        : Central US
			data:    State                           : Created
			data:    Address space                   : 192.168.0.0/16
			data:    Subnets:
			data:      Name                          : FrontEnd
			data:      Address prefix                : 192.168.1.0/24
			data:
			data:      Name                          : BackEnd
			data:      Address prefix                : 192.168.2.0/24
			data:
			info:    network vnet show command OK

<!---HONumber=August15_HO9-->