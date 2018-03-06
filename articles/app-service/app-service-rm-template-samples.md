---
title: "Exemplos de modelo do Azure Resource Manager – Serviço de Aplicativo | Microsoft Docs"
description: "Exemplos de modelo do Azure Resource Manager – Serviço de Aplicativo"
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: e28a27b9a00164fcbba6eb5d3e5435548486ffa4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="azure-resource-manager-templates-for-azure-web-apps"></a>Modelos do Azure Resource Manager para Aplicativos Web do Azure

A tabela a seguir contém links para modelos do Azure Resource Manager. Para obter recomendações sobre como evitar erros comuns ao criar modelos de aplicativo Web, consulte [Orientações sobre a implantação de aplicativos Web com modelos do Azure Resource Manager](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Implantar aplicativo Web**||
| [Implantar aplicativo Web vinculado a um repositório GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Implanta um aplicativo Web do Azure efetua pull do código no GitHub. |
| [Aplicativo Web com slots de implantação personalizados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Implanta um aplicativo Web do Azure com ambientes/slots de implantação personalizados. |
|**Configurar aplicativo Web**||
| [Certificado de aplicativo Web do Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Implanta um certificado de aplicativo Web do Azure do segredo do Key Vault e o usa para uma associação SSL. |
| [Aplicativo Web com domínio personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Implanta um aplicativo Web do Azure com um nome do host personalizado. |
| [Aplicativo Web com domínio personalizado e SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Implanta um aplicativo Web do Azure com um nome de host personalizado e obtém o certificado do aplicativo Web do Key Vault para uma associação SSL. |
| [Aplicativo Web com a extensão GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Implanta um aplicativo Web do Azure com extensão de site Golang, que permite executar aplicativos Web desenvolvidos no Golang no Azure. |
| [Aplicativo Web com o Java 8 e o Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Implanta um aplicativo Web do Azure com Java 8 e Tomcat 8 habilitados, o que permite a execução de aplicativos Java no Azure. |
|**Aplicativo Web do Linux**||
| [Aplicativo Web no Linux com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Implanta um aplicativo Web do Azure no Linux com o banco de dados do Azure para MySQL. |
| [Aplicativo Web no Linux com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Implanta um aplicativo Web do Azure no Linux com o banco de dados do Azure para PostgreSQL. |
|**Aplicativo Web com recursos conectados**||
| [Aplicativo Web com o MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Implanta um aplicativo Web do Azure no Windows com o banco de dados do Azure para MySQL. |
| [Aplicativo Web com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Implanta um aplicativo Web do Azure no Windows com o banco de dados do Azure para PostgreSQL. |
| [Aplicativo Web com um banco de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Implanta um aplicativo Web do Azure e o banco de dados SQL no nível de serviço "Básico". |
| [Aplicativo Web com conexão do Armazenamento de Blobs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Implanta um aplicativo Web do Azure com uma cadeia de conexão de armazenamento de blobs, o que permite usar o Armazenamento de Blobs do Azure do aplicativo Web. |
| [Aplicativo Web com o Cache Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Implanta um aplicativo Web do Azure com o Cache Redis. |
|**Ambiente do Serviço de Aplicativo**||
| [Criar um Ambiente de Serviço de Aplicativo v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Cria um Ambiente de Serviço de Aplicativo v2 em sua rede virtual. |
| [Criar um Ambiente do Serviço de Aplicativo v2 com um Endereço ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Cria um Ambiente de Serviço de Aplicativo v2 em sua rede virtual com um endereço de balanceador de carga interno privado. |
| [Configurar o Certificado SSL padrão para um ASE ILB ou um ASE v2 ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura o certificado SSL padrão para um Ambiente de Serviço de Aplicativo ILB ou um Ambiente de Serviço de Aplicativo v2 ILB |
| | |
