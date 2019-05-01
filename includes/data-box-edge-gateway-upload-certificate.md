---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64732650"
---
Um certificado SSL correto garante que você está enviando informações criptografadas ao servidor correto. Além de criptografia, o certificado também permite que a autenticação. Você pode carregar seu próprio certificado SSL confiável por meio da interface do PowerShell do dispositivo.

1. [Conectar-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Set-HcsCertificate` cmdlet para carregar o certificado. Quando solicitado, forneça os seguintes parâmetros:

   - `CertificateFilePath` -Caminho para o compartilhamento que contém o arquivo de certificado no *. pfx* formato.
   - `CertificatePassword` -A senha usada para proteger o certificado.
   - `Credentials` -Nome de usuário e senha para acessar o compartilhamento que contém o certificado.

     O exemplo a seguir mostra o uso desse cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

