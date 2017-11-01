<a name="virtual-networking-limits-classic"></a>Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo modelo de implantação clássico por assinatura.

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Redes virtuais |50 |100 |
| Sites de redes locais |20 |entre em contato com o suporte |
| Servidores DNS por rede virtual |20 |100 |
| Endereços IP privados por rede virtual |4096 |4096 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500K |500K |
| Grupos de segurança de rede (NSG) |100 |200 |
| Regras de NSG por NSG |200 |400 |
| Tabelas de rota definida pelo usuário |100 |200 |
| Rotas definidas pelo usuário por tabela de rotas |100 |400 |
| Endereços IP públicos (dinâmicos) |5 |entrar em contato com o suporte |
| Endereços IP públicos reservados |20 |entre em contato com o suporte |
| VIP público por implantação |5 |entre em contato com o suporte |
| VIP (ILB) privado por implantação |1 |1 |
| ACLs (Listas de Controle de Acesso de Pontos de Extremidade) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limites de Rede – Azure Resource Manager
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo Azure Resource Manager por região e assinatura.

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Redes virtuais |50 |1000 |
| Sub-redes por rede virtual |1000 |10000 |
| Emparelhamentos de rede virtual por Rede Virtual |10 |50 |
| Servidores DNS por rede virtual |9 |25 |
| Endereços IP privados por rede virtual |4096 |8192 |
| Endereços IP privados por interface de rede |256 |1024 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500K |500K |
| NIC (Interfaces de rede) |350 |20000 |
| Grupos de segurança de rede (NSG) |100 |5.000 |
| Regras de NSG por NSG |200 |500 |
| Os endereços e intervalos IP especificados para a origem ou o destino em uma regra de segurança |2000 |4000 |
| Grupos de segurança do aplicativo |200 |500 |
| Grupos de segurança do aplicativo por configuração de IP, por NIC |10 |20 |
| Configurações de IP por grupo de segurança do aplicativo |1000 |4000 |
| Os grupos de segurança do aplicativo que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |50 |100 |
| Tabelas de rota definida pelo usuário |100 |200 |
| Rotas definidas pelo usuário por tabela de rotas |100 |400 |
| Endereços IP públicos - dinâmicos |(Básico) 60 |entre em contato com o suporte |
| Endereços IP públicos - estáticos |(Basic) 20 |entre em contato com o suporte |
| Endereços IP públicos - estáticos |(Standard) 20 |entre em contato com o suporte |
| Certificados raiz de ponto a site por gateway de VPN |20 |20 |

#### <a name="load-balancer"></a>Limites do Load Balancer

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Balanceadores de Carga | 100 | 1000 |
| Regras por recurso, Básico | 150 | 250 |
| Regras por recurso, Standard | 1250 | 1500 |
| Regras por configuração de IP | 299 |299 |
| Configurações de IP de front-end, Básico | 10 | entre em contato com o suporte |
| Configurações de IP de front-end, Standard | 10 | 600 |
| Pool de back-end, Básico | 100, um único conjunto de disponibilidade | - |
| Pool de back-end, Standard | 1000, única VNet | entre em contato com o suporte |
| Portas de alta disponibilidade, Standard | 1 por front-end interno | - |

[Entre em contato com o suporte](../articles/azure-supportability/resource-manager-core-quotas-request.md ) caso precise aumentar esses limites.

