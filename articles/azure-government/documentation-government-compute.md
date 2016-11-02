<properties
    pageTitle="Documentação do Azure Governamental | Microsoft Azure"
    description="Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="09/29/2016"
    ms.author="ryansoc"/>



#  <a name="azure-government-compute"></a>Computação do Azure Governamental

##  <a name="virtual-machines"></a>Máquinas Virtuais

Para obter detalhes sobre esse serviço e como usá-lo, consulte [Tamanhos de máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Variações

Os seguintes SKUs de VM estão disponíveis de forma geral (GA) no Azure Government:

SKU da VM|Gov do VA nos EUA|Gov do IA nos EUA|Observações
---|---|---|---
Uma|GA|GA|Nenhum
Dv1|GA|-|Nenhum
DSv1|GA|-|Nenhum
Dv2|GA|GA|15 estará disponível em breve
F|GA|GA|Nenhum
G|Planejado|-|Nenhum

###  <a name="data-considerations"></a>Considerações de dados

As informações a seguir identificam o limite do Azure Governamental para o máquinas virtuais do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dados inseridos, armazenados e processados em uma VM podem conter dados de exportação controlados. Binários em execução nas máquinas virtuais do Azure. Autenticadores estáticos, como senhas e PINs de cartão inteligente para acesso aos componentes da plataforma Azure. Chaves privadas de certificados usados para gerenciar os componentes da plataforma Azure. Cadeias de conexão SQL.  Outros informações/segredos de segurança, como certificados, chaves de criptografia, chaves mestras e chaves de armazenamento armazenadas nos serviços do Azure.  | Metadados não podem conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de sua máquina virtual do Azure.  Não insira dados Regulamentados/controlados nos seguintes campos: Nomes de funções de locatários, Grupos de recursos, Nomes de implantação, Nomes de recursos, Marcas de recurso  

## <a name="next-steps"></a>Próximas etapas

Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>



<!--HONumber=Oct16_HO2-->


