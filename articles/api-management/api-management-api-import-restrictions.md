---
title: "Restrições e problemas conhecidos na importação de API do Gerenciamento de API do Azure | Microsoft Docs"
description: "Detalhes de problemas conhecidos e restrições na importação para o Gerenciamento de API do Azure usando os formatos Open API, WSDL ou WADL."
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 758babce3ed387ed4864f1934650cf701bda788f
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrições de importação de API e problemas conhecidos
## <a name="about-this-list"></a>Sobre esta lista
Ao importar uma API, você pode se deparar com algumas restrições ou identificar problemas que precisam ser corrigidos antes de poder importar com êxito. Este artigo documenta essas informações, organizadas pelo formato de importação da API.

## <a name="open-api"> </a>Open API/Swagger
Caso esteja recebendo erros ao importar seu documento da Open API, verifique se você o validou usando o designer no portal do Azure (Design – Front-End – Editor de Especificação da Open API) ou com uma ferramenta de terceiros, como o <a href="http://www.swagger.io">Editor do Swagger</a>.

* **Nome do host** APIM exige um atributo de nome de host.
* **Caminho base** APIM exige um atributo de caminho base.
* **Esquemas** APIM exige uma matriz de esquema. 

## <a name="wsdl"> </a>WSDL
Arquivos WSDL são usados para gerar as APIs de passagem SOAP ou servir como o back-end de uma API de SOAP para REST.

* **WSDL:Import** – Atualmente, APIM não dá suporte a APIs que estejam usando esse atributo. Os clientes devem mesclar os elementos importados em um documento.
* **Mensagens com diversas partes** Atualmente, APIM não dá suporte a esses tipos de mensagens.
* **wsHttpBinding no WCF** Serviços SOAP criados com o Windows Communication Foundation devem usar basicHttpBinding – não há suporte para wsHttpBinding.
* **MTOM** Serviços que usam MTOM <em>podem</em> funcionar. No momento, não oferecemos suporte oficial.
* APIM não dá suporte para tipos de **recursão** definidos recursivamente (por exemplo, referir-se a uma matriz de si mesmo).

## <a name="wadl"> </a>WADL
Atualmente, não há problemas de importação de WADL conhecidos.
