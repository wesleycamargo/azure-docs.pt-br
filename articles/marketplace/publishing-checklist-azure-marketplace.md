---
title: Lista de verificação de publicação para Azure Marketplace | Azure
description: Lista de verificação de publicação para Azure Marketplace usando o Portal do Cloud Partner.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 5e40ccc5a11df00b63c99fb606e17fa17e23a2a0
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825745"
---
# <a name="publishing-checklist-for-azure-marketplace"></a>Lista de Verificação da Publicação para Azure Marketplace    
Examine os componentes necessários antes de iniciar o processo de publicação.  

Os artefatos a seguir são necessários para concluir o fluxo de trabalho de publicação Criar Oferta no Portal do Cloud Partner.  

## <a name="checklist"></a>Lista de verificação  

| Tipo de listagem | Tipo de oferta | Artefato de publicação |   
|:--- |:--- |:--- |  
| Todos | Todos | <table> <tr><th>Detalhes da vitrine</th></tr> <tr><td>Nome da oferta (200 caracteres)</td></tr> <tr><td>Descrição (2.000 caracteres)</td></tr> <tr><td>ID MPN</td></tr> <tr><td>Disponibilidade por país/região</td></tr> <tr><td>Setores aplicáveis, categorias e palavras-chave de pesquisa</td></tr> <tr><td>Capturas de tela (1.280 x 720, máximo 5)</td></tr> <tr><td>Documentos de marketing (máximo 3)</td></tr> <tr><td>Destino do cliente potencial</td></tr> <tr><td>Vídeo de visão geral do produto (opcional)</td></tr> </table> <table> <tr><th>Contatos</th></tr> <tr><td>Informações de contato (suporte, engenharia, comercial)</td></tr> </table> <table> <tr><th>Informações técnicas</th></tr> <tr><td>URL dos termos de uso e da política de privacidade</td></tr> </table> <table> <tr><th>Test drive</th></tr> <tr><td>Nome do grupo de recursos do Azure</td></tr> </table> |  
| Todos | Máquina Virtual | <table> <tr><th>Informações técnicas</th></tr> <tr><td>URL do suporte</td></tr> </table> |
| Listar | Serviço de consultoria | <table> <tr><th>Detalhes da vitrine</th></tr> <tr><td>Duração do compromisso</td></tr> <tr><td>Logotipos da empresa (48x48, 216x216)</td></tr> </table> |  
| Avaliação | Todos | <table> <tr><th>Informações técnicas</th></tr> <tr><td>URL da Avaliação</td></tr> <tr><td>Idiomas com suporte</td></tr> <tr><td>Número de versão do aplicativo</td></tr> <tr><td>Data de lançamento do aplicativo</td></tr> <tr><td>URL do suporte</td></tr> </table> |  
| Avaliação | Test drive | <table> <tr><th>Test drive</th></tr> <tr><td>DESCRIÇÃO</td></tr> <tr><td>Duration</td></tr> <tr><td>Manual do usuário</td></tr> <tr><td>Vídeo do test drive (máximo 1)</td></tr> <tr><td>Disponibilidade de test drive no país/região</td></tr> <tr><td>ID de assinatura do Azure</td></tr> <tr><td>ID do locatário do Azure AD</td></tr> <tr><td>ID do aplicativo do Azure AD</td></tr> <tr><td>Chave do aplicativo do Azure AD</td></tr> </table> |  
| Transação | Máquina Virtual | <table> <tr><th>Vitrines</th></tr> <tr><td>Título (50 caracteres)</td></tr> <tr><td>Resumo (200 caracteres)</td></tr> <tr><td>Resumo longo (256 caracteres)</td></tr> <tr><td>Descrição baseada em HTML (3.000 caracteres)</td></tr> <tr><td>Logotipos da empresa (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Detalhes do sistema operacional</td></tr> <tr><td>Portas em uso</td></tr> <tr><td>Protocolos em uso</td></tr> <tr><td>Versão de disco para cada VHD em uso</td></tr> <tr><td>URL de SAS para cada VHD em uso</td></tr> </table> |  
| Transação | Aplicativos do Azure: modelo de solução | <table> <tr><th>Vitrines</th></tr> <tr><td>Título (50 caracteres)</td></tr> <tr><td>Resumo (200 caracteres)</td></tr> <tr><td>Resumo longo (256 caracteres)</td></tr> <tr><td>Descrição baseada em HTML (3.000 caracteres)</td></tr> <tr><td>Logotipos da empresa (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Número de versão</td></tr> <tr><td>Arquivo de pacote que contém<ul> <li>todos os arquivos de modelo</li> <li>Arquivo createUIDefinition</li> </ul> </td></tr> </table> |  
| Transação | Aplicativos do Azure: aplicativo gerenciado | <table> <tr><th>Vitrines</th></tr> <tr><td>Título (50 caracteres)</td></tr> <tr><td>Resumo (200 caracteres)</td></tr> <tr><td>Resumo longo (256 caracteres)</td></tr> <tr><td>Descrição baseada em HTML (3.000 caracteres)</td></tr> <tr><td>Logotipos da empresa (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Número de versão</td></tr> <tr><td>Arquivo de pacote que contém<ul> <li>todos os arquivos de modelo</li> <li>Arquivo createUIDefinition</li> </ul> </td></tr> </table> |  
| Transação | Contêiner | <table> <tr><th>Vitrines</th></tr> <tr><td>Título (50 caracteres)</td></tr> <tr><td>Resumo (200 caracteres)</td></tr> <tr><td>Resumo longo (256 caracteres)</td></tr> <tr><td>Descrição baseada em HTML (3.000 caracteres)</td></tr> <tr><td>Logotipos da empresa (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Detalhes do repositório de imagens do ACR (Registro de Contêiner do Azure): ID de assinatura</td></tr> <tr><td>Detalhes do repositório de imagens do ACR: nome do grupo de recursos</td></tr> <tr><td>Detalhes do repositório de imagens do ACR: nome do registro</td></tr> <tr><td>Detalhes do repositório de imagens do ACR: nome do repositório</td></tr> <tr><td>Detalhes do repositório de imagens do ACR: nome de usuário</td></tr> <tr><td>Detalhes do repositório de imagens do ACR: senha</td></tr> <tr><td>Detalhes do repositório de imagem de ACR: marcas de imagem (opcional)</td></tr> </table> |  
| Transação | Aplicativo SaaS | <table> <tr><th>Vitrines</th></tr> <tr><td>Título (50 caracteres)</td></tr> <tr><td>Resumo (200 caracteres)</td></tr> <tr><td>Resumo longo (256 caracteres)</td></tr> <tr><td>Descrição baseada em HTML (3.000 caracteres)</td></tr> <tr><td>Logotipos da empresa (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)</td></tr> </table> |  

## <a name="next-steps"></a>Próximas etapas
*   Visite a página [Guia do editor do Azure Marketplace e AppSource](./marketplace-publishers-guide.md).  
 
---  
