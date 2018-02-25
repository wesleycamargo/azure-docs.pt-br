---
title: "Validar os certificados de infraestrutura de chave pública do Azure pilha para implantação de sistemas de pilha do Azure integradas | Microsoft Docs"
description: Descreve como validar os certificados PKI de pilha do Azure para sistemas de pilha do Azure integradas.
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
ms.date: 02/23/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: 86f1b889d83905abfb5ddab2e82f32922409ff5f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Validar certificados PKI de pilha do Azure
A ferramenta do verificador de certificado do Azure pilha descrita neste artigo é fornecida pelo OEM incluído com o arquivo deploymentdata.json para validar que o [gerado certificados PKI](azure-stack-get-pki-certs.md) são adequadas para antes da implantação. Certificados devem ser validados com tempo suficiente para testar e obter certificados reemitidos se necessário. 

O verificador de certificado (Certchecker) executa as seguintes verificações:

- **Ler PFX**. Verifica o arquivo PFX válido, a senha correta e avisa se informações públicas não estão protegidas por senha. 
- **Algoritmo de assinatura**. Verifica que o algoritmo de assinatura não é SHA1 
- **Chave privada**. Verifica se a chave privada está presente e é exportada com o atributo do computador Local. 
- **Cadeia de certificados de**. Verifica a cadeia de certificados está intacto, incluindo certificados autoassinados. 
- **Os nomes DNS**. Verifica a SAN contém nomes DNS relevantes para cada ponto de extremidade ou se um suporte curinga estiver presente. 
- **Uso de chave**. Verifica o uso de chave contém Assinatura Digital e a codificação de chave e uso avançado de chave contém a autenticação do servidor e autenticação de cliente. 
- **Tamanho da chave**. Verifica o que tamanho de chave é de 2.048 ou maior 
- **Ordem da cadeia**. Verifica a ordem dos certificados fazendo a cadeia está correta. 
- **Outros certificados**. Certifique-se de que não há outros certificados foram agrupados em PFX que não seja o certificado de folha relevantes e sua cadeia. 
- **Nenhum perfil**. Verifica se que um novo usuário pode carregar os dados PFX sem um perfil de usuário carregado, imitando o comportamento de gMSA contas durante a manutenção do certificado.   

> [!IMPORTANT]
> O arquivo de PFX de certificado PKI e a senha devem ser tratados como informações confidenciais.

## <a name="prerequisites"></a>Pré-requisitos
O sistema deve atender aos seguintes pré-requisitos antes de validar os certificados PKI para implantação de pilha do Azure:
- CertChecker (em PartnerToolKit em \utils\certchecker)
- Certificado SSL exportado seguindo o [instruções de preparação](prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 ou Windows Server 2016

## <a name="perform-certificate-validation"></a>Executar a validação de certificado
Siga estas etapas para preparar e validar os certificados PKI de pilha do Azure: 

1. Extraia o conteúdo do <partnerToolkit>\utils\certchecker para um novo diretório, por exemplo, **c:\certchecker**.

2. Abra o PowerShell como administrador e altere o diretório para a pasta certchecker:

  ```powershell
  cd c:\certchecker
  ```
 
3. Crie uma estrutura de diretório para os certificados, executando os seguintes comandos do PowerShell:

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Se o provedor de identidade para a implantação do Azure pilha é o AD do Azure, remova o **ADFS** e **gráfico** diretórios. 

4. Coloque seu certificado nas pastas apropriadas criadas na etapa anterior, por exemplo: 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - e assim por diante... 

5. Cópia **deploymentdata.json** para o **c:\certchecker** directory.

6. Na janela do PowerShell, execute os seguintes comandos: 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. A saída deve conter Okey para todos os certificados e todos os atributos marcados: 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>Problemas conhecidos 
**Sintoma**: Certchecker será encerrado prematuramente e você receberá o seguinte erro: 
> Com falha

> Detalhes: Este comando não pode ser executado devido ao erro: O nome do diretório é inválido. 

**Causa**: executando certchecker.ps1 de uma pasta restritiva, por exemplo, c:\temp ou % temp % 

**Resolução**: mover a ferramenta certchecker para o novo diretório, por exemplo, C:\CertChecker 


**Sintoma**: Certchecker dá um aviso sobre o uso de pré-1803 (como no exemplo acima da etapa 7):

> [!WARNING]
> Estrutura de pré-1803 certificado. A pasta de estrutura 1803 de pilha do Azure e superior está: ACSTable ACSBlob, ACSQueue, em vez de pasta do ACS. Para obter mais informações, consulte a documentação de implantação.

**Causa**: CertChecker detectou o uso de uma única pasta ACS, isso está correto para implantações antes 1803. Para a versão de pilha do Azure 1803 e acima implantações, a estrutura da pasta altera para ACSTable, ACSQueue, ACSBlob. Certchecker já foi atualizado para suportar essa funcionalidade.

**Resolução**: se Implantando 1802, nenhuma ação é necessária. Se implantar 1803 e acima, substitua o ACS com ACSTable, ACSQueue, ACSBlob e copie os certificados de ACS para essas pastas.

**Sintoma**: testes são ignorados

**Causa**: CertChecker ignorará certos testes se uma dependência não for atendida:
- Outros certificados são ignorados se falhar de cadeia de certificados.
- Nenhum perfil será ignorado se:
  - Há uma política de segurança restringindo a capacidade de criar um usuário temporário e executar o powershell como esse usuário.
  - Falha na verificação de chave privada.

**Resolução**: siga as orientações de ferramentas na seção de detalhes em conjunto de cada certificado de testes.


## <a name="prepare-deployment-script-certificates"></a>Preparar a implantação de certificados de script 
Como etapa final, todos os certificados que você preparou precisam ser colocados nas pastas apropriadas no host de implantação. Em seu host de implantação, crie uma pasta chamada. Certificados * * e coloque-o certificado exportado arquivos nas subpastas correspondentes especificadas no [certificados obrigatórios](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates) seção:

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> Os certificados são marcados com um asterisco * necessário apenas quando o AD FS é usado como o repositório de identidade.


## <a name="next-steps"></a>Próximas etapas
[Integração de identidade do Datacenter](azure-stack-integrate-identity.md)