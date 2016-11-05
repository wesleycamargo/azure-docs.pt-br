---
title: Notas de versão do SDK do Azure para .NET 2.9
description: Notas de versão do SDK do Azure para .NET 2.9
services: app-service\web
documentationcenter: .net
author: Juliako
manager: erikre
editor: ''

ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/17/2016
ms.author: juliako

---
# <a name="azure-sdk-for-.net-2.9-release-notes"></a>Notas de versão do SDK do Azure para .NET 2.9
## <a name="overview"></a>Visão geral
Este documento contém as notas de versão do SDK do Azure para a versão do .NET 2.9. 

Para obter informações detalhadas sobre atualizações nesta versão, consulte a [Postagem de anúncio do SDK do Azure 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-2.9-for-visual-studio-2015-update-2-and-visual-studio-"15"-preview"></a>Atualização 2 do SDK do Azure 2.9 para Visual Studio 2015 e Visual Studio "15" Preview
Esta atualização inclui as seguintes correções de bug:

* Problema relacionado à API REST de Geração de Cliente no qual a cadeia de caracteres "Tipo Desconhecido" seria exibida como o nome da pasta de geração de código e/ou o nome do namespace descartado no código gerado.
* Problema relacionado a WebJobs Agendados no qual as informações de autenticação estavam falhando em serem passadas para o processo de provisionamento do Agendador.

Esta atualização inclui o novo recurso a seguir:

* Suporte para Serviços de Aplicativos secundários na guia "Serviços" da caixa de diálogo de provisionamento do Serviço de Aplicativo. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Atualização 2 das Ferramentas do Azure Data Lake para Visual Studio 2015
Essa atualização inclui o seguinte:

* **Ferramentas do Azure Data Lake** para Visual Studio agora estão mescladas ao SDK do Azure para .NET. A ferramenta é instalada automaticamente ao instalar o SDK do Azure. 
  
    A ferramenta é atualizada com frequência, acesse [aqui](http://aka.ms/datalaketool) para obter as atualizações.
* **Gerenciador de Servidores** agora permite exibir tudo e criar algumas entidades de metadados U-SQL. Para saber mais, confira [este](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.

## <a name="hdinsight-tools"></a>Ferramentas do HDInsight
**Ferramentas do HDInsight** para Visual Studio agora dão suporte ao HDInsight versão 3.3, incluindo a exibição de gráficos Tez e outras correções de linguagem.

## <a name="azure-resource-manager"></a>Gerenciador de Recursos do Azure
Essa versão adiciona o suporte para [KeyVault](../resource-manager-keyvault-parameter.md) para modelos ARM.

## <a name="see-also"></a>Consulte também
[Postagem de anúncio do SDK 2.9 do Azure](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

<!--HONumber=Oct16_HO2-->


