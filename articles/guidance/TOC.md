# Arquitetura

## Conceitos básicos de nuvem

### [Projetar aplicativos resilientes](guidance-resiliency-overview.md)
#### [Lista de verificação de resiliência](guidance-resiliency-checklist.md)
#### [Análise do modo de falha](guidance-resiliency-failure-mode-analysis.md)
#### [Recuperação de desastre](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Recuperação de desastres e alta disponibilidade](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Alta disponibilidade](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Lista de verificação de alta disponibilidade](..\resiliency\resiliency-high-availability-checklist.md)
#### [Diretrizes de resiliência de serviço específicos do Azure](..\resiliency\resiliency-service-guidance-index.md)
#### [Recuperação de dados corrompidos ou de exclusão acidental](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Recuperar de falhas locais](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Recuperação de uma interrupção de serviço em toda a região](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Recuperação do local para o Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Orientações técnicas de resiliência do Azure](..\resiliency\resiliency-technical-guidance.md)


## Arquiteturas de referência

### [Executar cargas de trabalho da VM no Azure](guidance-ra-compute.md)
#### [Executar uma VM do Linux no Azure](guidance-compute-single-vm-linux.md)
#### [Executar uma VM do Windows no Azure](guidance-compute-single-vm.md)
#### [Executar várias VMs no Azure para ter escalabilidade e disponibilidade](guidance-compute-multi-vm.md)
#### [Executar VMs do Linux para uma arquitetura de N camadas](guidance-compute-n-tier-vm-linux.md)
#### [Executar VMs do Windows para uma arquitetura de N camadas](guidance-compute-n-tier-vm.md)
#### [Executar VMs do Linux em várias regiões para ter alta disponibilidade](guidance-compute-multiple-datacenters-linux.md)
#### [Executar VMs do Windows em várias regiões para ter alta disponibilidade](guidance-compute-multiple-datacenters.md)

### [Conectar sua rede local ao Azure](guidance-ra-hybrid-networking.md)
#### [Arquitetura de rede híbrida com o Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Arquitetura de rede híbrida com Azure e VPN Local](guidance-hybrid-network-vpn.md)
#### [Arquitetura de rede híbrida altamente disponível](guidance-hybrid-network-expressroute-vpn-failover.md)

### [Proteger o Limite de Nuvem no Azure](guidance-ra-network-security.md)
#### [Proteger arquitetura de rede híbrida no Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [DMZ entre o Azure e a Internet](guidance-iaas-ra-secure-vnet-dmz.md)

### [Gerenciar identidade no Azure](guidance-ra-identity.md)
#### [Implementar o Azure Active Directory](guidance-identity-aad.md)
#### [Estender o ADDS para o Azure](guidance-identity-adds-extend-domain.md)
#### [Criar uma floresta de recursos do ADDS no Azure](guidance-identity-adds-resource-forest.md)
#### [Implementar o ADFS no Azure](guidance-identity-adfs.md)

### [Arquiteturas do aplicativo Web para o Serviço de Aplicativo do Azure](guidance-ra-app-service.md)
#### [Aplicativo Web básico](guidance-web-apps-basic.md)
#### [Aplicativo Web com alta disponibilidade](guidance-web-apps-multi-region.md)
#### [Melhorar a escalabilidade em um aplicativo Web](guidance-web-apps-scalability.md)


## Práticas recomendadas para aplicativos em nuvem

### [Diretrizes de design de API](..\best-practices-api-design.md)
### [Diretrizes para implementação de API](..\best-practices-api-implementation.md)
### [Diretrizes de dimensionamento automático](..\best-practices-auto-scaling.md)
### [Lista de verificação de disponibilidade](..\best-practices-availability-checklist.md)
### [Diretrizes de trabalhos em segundo plano](..\best-practices-background-jobs.md)
### [Regiões Combinadas do Azure](..\best-practices-availability-paired-regions.md)
### [Diretrizes de cache](..\best-practices-caching.md)
### [Diretrizes da Rede de Distribuição de Conteúdo](..\best-practices-cdn.md)
### [Diretrizes de particionamento de dados](..\best-practices-data-partitioning.md)
### [Diretrizes de monitoramento e diagnóstico](..\best-practices-monitoring.md)
### [Segurança de rede e serviços de nuvem da Microsoft](..\best-practices-network-security.md)
### [Padrões para design de modelos do Azure Resource Manager](..\best-practices-resource-manager-design-templates.md)
### [Convenções de nomenclatura recomendadas para recursos do Azure](guidance-naming-conventions.md)
### [Considerações de segurança do Azure Resource Manager](..\best-practices-resource-manager-security.md)
### [Compartilhamento de estado em modelos do Azure Resource Manager](..\best-practices-resource-manager-state.md)
### [Diretrizes gerais de repetição](..\best-practices-retry-general.md)
### [Diretrizes específicas do serviço de repetição](..\best-practices-retry-service-specific.md)
### [Lista de verificação de escalabilidade](..\best-practices-scalability-checklist.md)


## Guias de cenário

### [Elasticsearch nas diretrizes do Azure](guidance-elasticsearch.md)
#### [Executar o Elasticsearch no Azure](guidance-elasticsearch-running-on-azure.md)
#### [Ajustar o desempenho da ingestão de dados](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Ajustar a agregação de dados e o desempenho da consulta](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Configurar resiliência e recuperação](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Criar um ambiente de teste de desempenho](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implementar um plano de teste JMeter](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Implantar uma amostra JUnit do JMeter](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Executar os testes automatizados de resiliência](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Executar testes automatizados de desempenho](guidance-elasticsearch-running-automated-performance-tests.md)

### [Gerenciamento de identidades para aplicativos multilocatários](guidance-multitenant-identity.md)
#### [Visão geral](guidance-multitenant-identity-intro.md)
#### [Aplicativo de Pesquisas Tailspin](guidance-multitenant-identity-tailspin.md)
#### [Autenticação usando o Azure AD e o OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Identidades baseadas em declarações](guidance-multitenant-identity-claims.md)
#### [Inscrição e integração de locatário](guidance-multitenant-identity-signup.md)
#### [Funções do aplicativo](guidance-multitenant-identity-app-roles.md)
#### [Autorização baseada em funções e recursos](guidance-multitenant-identity-authorize.md)
#### [Proteger uma API da Web de back-end](guidance-multitenant-identity-web-api.md)
#### [Tokens de acesso do cache](guidance-multitenant-identity-token-cache.md)
#### [Federação com AD FS de um cliente](guidance-multitenant-identity-adfs.md)
#### [Usar a asserção do cliente para obter tokens de acesso](guidance-multitenant-identity-client-assertion.md)
#### [Usar o Cofre de Chaves do Azure para proteger os segredos do aplicativo](guidance-multitenant-identity-keyvault.md)
#### [Implantação de dispositivos virtuais em alta disponibilidade](guidance-nva-ha.md)


<!--HONumber=Nov16_HO4-->


