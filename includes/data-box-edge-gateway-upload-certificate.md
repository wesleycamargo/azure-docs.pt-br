---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 216380cf7069468d13c4e533fc90b2596aa211c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58114289"
---
Um certificado SSL correto garante que você está enviando informações criptografadas ao servidor correto. Além de criptografia, o certificado também permite que a autenticação. Você pode carregar seu próprio certificado SSL confiável por meio da interface do PowerShell do dispositivo.

1. [Conectar-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Set-HcsCertificate` cmdlet para carregar o certificado. Quando solicitado, forneça os seguintes parâmetros:

   - `CertificateFilePath` -Caminho para o compartilhamento que contém o arquivo de certificado no *. pfx* formato.
   - `CertificatePassword` -A senha usada para proteger o certificado.
   - `Credentials` -Nome de usuário e senha para acessar o compartilhamento que contém o certificado.

     O exemplo a seguir mostra o uso desse cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
     ```

