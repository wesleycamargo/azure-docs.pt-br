A tabela a seguir lista os requisitos para gateways de VPN baseados em política e em rota. Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico. Para o modelo clássico, gateways de VPN baseados em políticas são o mesmo que gateways Estáticos e gateways baseados em rota são o mesmos que gateways Dinâmico.


| | **Gateway de VPN Básico Baseado em Política** | **Gateway de VPN Básico Baseado em Rota** | **Gateway de VPN Standard Baseado em Rota** | **Gateway de VPN de Alto Desempenho Baseado em Rota** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
| **Conectividade site a site (S2S)** | Configuração de VPN baseada em política | Configuração de VPN baseada em rota | Configuração de VPN baseada em rota | Configuração de VPN baseada em rota |
| **Conectividade P2S (Ponto a Site**) | Sem suporte | Com suporte (pode coexistir com S2S) | Com suporte (pode coexistir com S2S) | Com suporte (pode coexistir com S2S) |
| **Método de autenticação** | Chave pré-compartilhada | Chave pré-compartilhada para conectividade S2S, Certificados para conectividade P2S | Chave pré-compartilhada para conectividade S2S, Certificados para conectividade P2S | Chave pré-compartilhada para conectividade S2S, Certificados para conectividade P2S |
| **Número máximo de conexões S2S** | 1 | 10 | 10 | 30 |
| **Número máximo de conexões P2S** | Sem suporte | 128 | 128 | 128 |
|**Suporte ao roteamento ativo (BGP)** | Sem suporte | Sem suporte | Sem suporte | Sem suporte |
 

<!---HONumber=AcomDC_0224_2016-->