## Encadeamento de Serviço - Trânsito na rede virtual emparelhada

Embora o uso de rotas do sistema facilite o tráfego automaticamente para a sua implantação, há casos em que você deseja controlar o roteamento de pacotes por meio de um dispositivo virtual. Nesse cenário, há duas VNets em uma assinatura, HubVNet e VNet1, como descrito no diagrama abaixo. Implante a NVA (Dispositivo de Rede Virtual) na VNet HubVNet. Depois de estabelecer o emparelhamento VNet entre HubVNet e VNet1, você pode configurar as Rotas Definidas pelo Usuário e especificar o próximo salto para NVA na HubVNet.

![Tráfego e NVA](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] Para simplificar, suponha que todas as VNets aqui estão na mesma assinatura. Mas isso também funciona para o cenário entre assinaturas.

A principal propriedade para habilitar o Toteamento de tráfego é o parâmetro "Permitir Tráfego Encaminhado". Isso permite aceitar e enviar o tráfego de/para o NVA na VNet emparelhada.

<!---HONumber=AcomDC_0928_2016-->