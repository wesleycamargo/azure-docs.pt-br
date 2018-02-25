---
title: "Preparar certificados de infraestrutura de chave pública do Azure pilha para implantação de sistemas de pilha do Azure integradas | Microsoft Docs"
description: Descreve como preparar os certificados PKI de pilha do Azure para sistemas de pilha do Azure integradas.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: b2f1e9bf62773dd7124678552f23dd9262530031
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Preparar certificados PKI de pilha do Azure para implantação
Os arquivos de certificado [obtido da autoridade de certificação de escolha](azure-stack-get-pki-certs.md) devem ser importados e exportados com propriedades que correspondem a requisitos de certificado da pilha do Azure.


## <a name="prepare-certificates-for-deployment"></a>Preparar certificados para implantação
Siga estas etapas para preparar e validar os certificados PKI de pilha do Azure: 

1.  Copiar as versões originais do certificado [obtido da autoridade de certificação de escolha](azure-stack-get-pki-certs.md) em um diretório no host de implantação. 
  > [!WARNING]
  > Não copie os arquivos que já foram importados, exportados ou alterados de alguma forma dos arquivos fornecidos diretamente pela autoridade de certificação.

2.  Importe os certificados no repositório de certificados do computador Local:

    a.  Clique com botão direito no certificado e selecione **Instalar PFX**.

    b.  No **o Assistente de importação de certificado**, selecione **Máquina Local** como o local de importação. Selecione **Avançar**.

    ![Local de importação do computador local](.\media\prepare-pki-certs\1.png)

    c.  Selecione **próximo** no **Escolher arquivo para importar** página.

    d.  Sobre o **proteção de chave privada** página, insira a senha para os arquivos de certificado e, em seguida, habilitar o **marcar esta chave como exportável. Isso permite que você faça o backup ou transportar suas chaves posteriormente** opção. Selecione **Avançar**.

    ![Marcar chaves como exportáveis](.\media\prepare-pki-certs\2.png)

    e.  Escolha **local todos os certificados no repositório a seguir** e, em seguida, selecione **confiabilidade corporativa** como o local. Clique em **Okey** para fechar a caixa de diálogo de seleção de repositório de certificado e, em seguida, **próximo**.

    ![Configurar o repositório de certificados](.\media\prepare-pki-certs\3.png)

  f.    Clique em **concluir** para concluir o Assistente de importação de certificado.

  g.    Repita o processo para todos os certificados que você está fornecendo para sua implantação.

3. Exporte o certificado para o formato do arquivo PFX com os requisitos da pilha do Azure:

  a.    Abra o console do MMC do Gerenciador de certificados e conecte-se ao repositório de certificados do computador Local.

  b.    Vá para o **confiabilidade corporativa** directory.

  c.    Selecione um dos certificados importados por você na etapa 2 acima.

  d.    No console do Gerenciador de barra de certificado tarefas, selecione **ações** > **todas as tarefas** > **exportar**.

  e.    Selecione **Avançar**.

  f.    Selecione **Sim, exportar a chave privada**e, em seguida, clique em **próximo**.

  g.    Na seção de formato do arquivo de exportação, selecione **exportar todas as propriedades estendidas** e, em seguida, clique em **próximo**.

  h.    Selecione **senha** e fornecer uma senha para os certificados. Guarde essa senha, pois ele é usado como um parâmetro de implantação. Selecione **Avançar**.

  i.    Escolha um nome de arquivo e um local para o arquivo pfx exportar. Selecione **Avançar**.

  j.    Selecione **Concluir**.

  k.    Repita esse processo para todos os certificados importados para a implantação na etapa 2 acima.

## <a name="next-steps"></a>Próximas etapas
[Validar certificados PKI](validate-pki-certs.md)