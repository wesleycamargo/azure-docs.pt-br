# Arquitetura

## Conceitos básicos de nuvem

### [Projeto de aplicativos resilientes do Azure](guidance-resiliency-overview.md)
#### [Lista de verificação de resiliência](guidance-resiliency-checklist.md)
#### [Análise do modo de falha](guidance-resiliency-failure-mode-analysis.md)

#### [Recuperação de desastre para aplicativos criados no Microsoft Azure](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Recuperação de desastres e alta disponibilidade para aplicativos criados no Microsoft Azure](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Alta disponibilidade para aplicativos criados no Microsoft Azure](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Lista de verificação de alta disponibilidade](..\resiliency\resiliency-high-availability-checklist.md)
#### [Diretrizes de resiliência de serviço específicos do Azure](..\resiliency\resiliency-service-guidance-index.md)
#### [Recuperação de dados corrompidos ou de exclusão acidental](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Recuperação de falhas locais no Azure](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Recuperação de uma interrupção de serviço em toda a região](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Recuperação do local para o Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Orientações técnicas de resiliência do Azure](..\resiliency\resiliency-technical-guidance.md)


## Arquiteturas de referência

### Arquitetura de referência de computação
#### [Executando uma VM Linux no Azure](guidance-compute-single-vm-linux.md)
#### [Executando uma VM Windows no Azure](guidance-compute-single-vm.md)
#### [Várias VMs em execução no Azure para escalabilidade e disponibilidade](guidance-compute-multi-vm.md)
#### [VMs do Linux em execução para uma arquitetura de N camadas no Azure](guidance-compute-n-tier-vm-linux.md)
#### [VMs do Windows em execução para uma arquitetura de N camadas no Azure](guidance-compute-n-tier-vm.md)
#### [VMs do Linux em execução em várias regiões para alta disponibilidade](guidance-compute-multiple-datacenters-linux.md)
#### [VMs do Windows em execução em várias regiões para alta disponibilidade](guidance-compute-multiple-datacenters.md)

### [Conexão de sua rede local ao Azure](guidance-connecting-your-on-premises-network-to-azure.md)
#### [Implementação de uma arquitetura de rede híbrida com o Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Implementação de uma arquitetura de rede híbrida com o Azure e a VPN local](guidance-hybrid-network-vpn.md)
#### [Implementação de uma arquitetura de rede híbrida altamente disponível](guidance-hybrid-network-expressroute-vpn-failover.md)

### Proteção do limite de nuvem no Azure
#### [Implementação de uma arquitetura de rede híbrida segura no Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Implementação de uma DMZ entre o Azure e a Internet](guidance-iaas-ra-secure-vnet-dmz.md)

### [Gerenciamento de identidades no Azure](guidance-ra-identity.md)
#### [Implementação do Azure Active Directory](guidance-identity-aad.md)
#### [Extensão do Active Directory Directory Services (ADDS) para o Azure](guidance-identity-adds-extend-domain.md)
#### [Criação de uma floresta de recursos do ADDS (Active Directory Directory Services) no Azure](guidance-identity-adds-resource-forest.md)
#### [Implementação do ADFS (Active Directory Federation Services) no Azure](guidance-identity-adfs.md)

### Arquitetura de referência do aplicativo Web de PaaS
#### [Arquitetura de referência do Azure: aplicativo Web básico](guidance-web-apps-basic.md)
#### [Arquitetura de referência do Azure: aplicativo Web com alta disponibilidade](guidance-web-apps-multi-region.md)
#### [Melhora da escalabilidade em um aplicativo Web](guidance-web-apps-scalability.md)


## Padrões de design na nuvem

## Práticas recomendadas para aplicativos em nuvem

### [Diretrizes de design de API](..\best-practices-api-design.md)
### [Diretrizes para implementação de API](..\best-practices-api-implementation.md)
### [Diretrizes de dimensionamento automático](..\best-practices-auto-scaling.md)
### [Lista de verificação de disponibilidade](..\best-practices-availability-checklist.md)
### [Diretrizes de trabalhos em segundo plano](..\best-practices-background-jobs.md)
### [Continuidade dos negócios e recuperação de desastre (BCDR): Regiões Emparelhadas do Azure](..\best-practices-availability-paired-regions.md)
### [Diretrizes de cache](..\best-practices-caching.md)
### [Diretrizes da Rede de Distribuição de Conteúdo (CDN)](..\best-practices-cdn.md)
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
#### [Execução do Elasticsearch no Azure](guidance-elasticsearch-running-on-azure.md)
#### [Ajuste do desempenho da ingestão de dados para o Elasticsearch no Azure](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Ajuste de desempenho de consulta e de agregação de dados com o Elasticsearch no Azure](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Configuração de resiliência e de recuperação no Elasticsearch no Azure](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Como criar um ambiente de teste de desempenho para o Elasticsearch no Azure](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implementação de um plano de teste do JMeter para o Elasticsearch](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Implantação de uma amostra do JMeter JUnit para testes de desempenho do Elasticsearch](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Execução dos testes automatizados de resiliência do Elasticsearch](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Como executar os testes de desempenho automatizados do Elasticsearch](guidance-elasticsearch-running-automated-performance-tests.md)

### [Gerenciamento de identidades para aplicativos multilocatários no Microsoft Azure](guidance-multitenant-identity.md)
#### [Introdução ao gerenciamento de identidades para aplicativos multilocatários no Microsoft Azure](guidance-multitenant-identity-intro.md)
#### [Sobre o aplicativo Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
#### [Autenticação em aplicativos multilocatários usando o Azure AD e o OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Trabalho com identidades baseadas em declarações em aplicativos multilocatários](guidance-multitenant-identity-claims.md)
#### [Inscrição e integração de locatários em um aplicativo multilocatário](guidance-multitenant-identity-signup.md)
#### [Funções de aplicativo em aplicativos multilocatários](guidance-multitenant-identity-app-roles.md)
#### [Autorização baseada em recursos e baseada em funções em aplicativos multilocatários](guidance-multitenant-identity-authorize.md)
#### [Proteção de uma API Web de back-end em um aplicativo multilocatário](guidance-multitenant-identity-web-api.md)
#### [Tokens de acesso em cache em um aplicativo multilocatário](guidance-multitenant-identity-token-cache.md)
#### [Federação com AD FS de um cliente para aplicativos multilocatários no Azure](guidance-multitenant-identity-adfs.md)
#### [Usando a declaração do cliente para obter tokens de acesso do Azure AD](guidance-multitenant-identity-client-assertion.md)
#### [Como usar o Cofre de Chaves do Azure para proteger segredos do aplicativo](guidance-multitenant-identity-keyvault.md)

#### [Implantação de dispositivos virtuais em alta disponibilidade](guidance-nva-ha.md)


<!--HONumber=Nov16_HO2-->


