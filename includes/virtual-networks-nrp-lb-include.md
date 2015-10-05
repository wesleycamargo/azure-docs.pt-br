## Balanceador de carga
Um balanceador de carga é usado quando você deseja dimensionar seus aplicativos. Cenários comuns de implantação envolvem aplicativos executados em várias instâncias de VM. As instâncias de VM são apoiadas por um balanceador de carga, que ajuda a distribuir o tráfego de rede para as várias instâncias.

![NICs em uma única VM](./media/resource-groups-networking/Figure5.png)

Os balanceadores de carga contêm os seguintes recursos filho:

- **Configuração de IP front-end** – um balanceador de carga pode incluir um ou mais endereços IP de front-end, também conhecidos como VIPs (IPs virtuais). Esses endereços IP servem como entrada para o tráfego. 
- **Pool de endereços back-end** – estes são os endereços IP associados a NICs de VM aos quais a carga será distribuída.
- **Regras de balanceamento de carga** – uma propriedade de regra mapeia determinados IP de front-end e combinação de portas para um conjunto de endereços IP de back-end e combinação de portas. Com uma única definição de um recurso de balanceador de carga, você pode definir várias regras de balanceamento de carga, cada regra refletindo uma combinação de um IP de front-end e porta e outra combinação de IP de back-end e porta, ambas associadas a VMs. 
- **Sondas** – sondas permitem que você controle a integridade das instâncias de VMs. Se um teste de integridade falhar, a instância VM será retirada automaticamente do rodízio.
- **Regras de NAT de entrada** – regras de NAT definindo o tráfego de entrada fluindo pelo IP front-end e distribuído para o IP de back-end.

<!---HONumber=Sept15_HO4-->