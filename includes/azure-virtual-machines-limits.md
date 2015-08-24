Recurso|Limite padrão|Limite máximo
---|---|---
[Máquinas virtuais](../documentation/services/virtual-machines/) por serviço de nuvem<sup>1</sup>|50|50
Pontos de extremidade de entrada por serviço de nuvem<sup>2</sup>|150|150

<sup>1</sup>Máquinas virtuais criadas no Gerenciamento de Serviços (e não no Gerenciador de Recursos) são armazenadas automaticamente em um serviço de nuvem. Você pode adicionar mais máquinas virtuais a esse serviço de nuvem para disponibilidade e balanceamento de carga. Consulte [Como conectar máquinas virtuais a uma rede virtual ou serviço de nuvem](../virtual-machines/cloud-services-connect-virtual-machine.md).

<sup>2</sup>Pontos de extremidade de entrada permitem comunicações com uma máquina virtual de fora do serviço de nuvem da máquina virtual. As máquinas virtuais no mesmo serviço de nuvem ou rede virtual podem se comunicar automaticamente umas com as outras. Consulte [Como instalar pontos de extremidade em uma máquina virtual](../virtual-machines/virtual-machines-set-up-endpoints.md).

<!---HONumber=August15_HO7-->