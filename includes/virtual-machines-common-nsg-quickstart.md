No Azure, você abre uma porta ou cria um ponto de extremidade criando um filtro de rede que permite o tráfego na porta escolhida por você em uma sub-rede ou interface de rede de máquina virtual (VM). Esses filtros, que controlam o tráfego de entrada e saída, são colocados em um Grupo de Segurança de Rede e anexados ao recurso que receberá o tráfego.

Vamos usar um exemplo comum de tráfego da Web na porta 80. Quando você tiver uma VM configurada para atender a solicitações da Web na porta TCP 80 padrão (lembre-se de iniciar os serviços apropriados e abrir quaisquer regras de firewall de SO na VM), você:

1. Criará um Grupo de Segurança de Rede.
2. Criará uma regra de entrada permitindo o tráfego com:
  - o intervalo de porta de destino de "80"
  - intervalo de porta de origem "*" (permitindo qualquer porta de origem)
  - um valor de prioridade inferior a 65.500 (para que a prioridade seja maior do que a regra de negação de entrada padrão catch-all)
3. Associe o Grupo de Segurança de Rede à interface de rede da VM ou sub-rede.
    
Você pode criar configurações de rede muito complexas para proteger seu ambiente usando Grupos de Segurança de Rede e regras, em vez de apenas uma ou duas regras que permitam o tráfego HTTP ou o gerenciamento remoto. Para saber mais, confira a seção [”Mais informações”](#more-information-on-network-security-groups) abaixo ou [O que é um Grupo de Segurança de Rede?](../articles/virtual-network/virtual-networks-nsg.md)

<!---HONumber=AcomDC_0601_2016-->