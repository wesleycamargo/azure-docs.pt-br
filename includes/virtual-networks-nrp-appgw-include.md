## Application Gateway

O Application Gateway o fornece uma solução de balanceamento de carga HTTP gerenciada pelo Azure com base no balanceamento de carga de camada 7. O balanceamento de carga do aplicativo permite o uso de regras de roteamento para o tráfego de rede com base em HTTP.

Os Application Gateways contêm os seguintes recursos filhos:

- **Pool de servidores back-end** - A lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público. 
- **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
- **Porta front-end** - Essa é a porta pública aberta no Application Gateway. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
- **Ouvinte** - O ouvinte tem uma porta front-end, um protocolo (Http ou Https, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL). 
- **Regra** - A regra vincula o ouvinte e o pool de servidores back-end e define à qual pool de servidores back-end o tráfego deve ser direcionado quando atingir um ouvinte específico. Atualmente, há suporte apenas para a regra básica. A regra básica é a distribuição de carga round robin.

<!---HONumber=Sept15_HO4-->