Há 3 SKUs de gateway de VPN:

* Básico
* Standard
* Alto Desempenho

A tabela a seguir mostra os tipos de gateway e a taxa de transferência agregada estimada. Os preços diferem entre os SKUs de gateway. Para obter informações sobre preços, veja [Preços de gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico.

|  | **Taxa de transferência de Gateway de VPN** | **Túneis IPsec máximo de Gateway de VPN** | **Taxa de transferência de Gateway de Rota Expressa** | **Coexistência de Gateway de VPN e a Rota Expressa** |
| --- | --- | --- | --- | --- |
| **SKU Básico** |100 Mbps |10 |500 Mbps |Não |
| **SKU padrão** |100 Mbps |10 |1000 Mbps |Sim |
| **SKU de Alto Desempenho** |200 Mbps |30 |2000 Mbps |Sim |

**Observação:** a taxa de transferência da VPN é uma estimativa aproximada baseada nas medidas entre redes virtuais na mesma região do Azure. Não é uma garantia do que você poderá obter para conexões entre locais na Internet, mas deverá ser usada como a maior medida possível.

<!---HONumber=AcomDC_0224_2016-->