
### <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Adicionar chaves de Assinatura e Criptografia no seu Locatário B2C para uso por Políticas Personalizadas

1. Navegue até a folha Identity Experience Framework em suas configurações de locatário do Azure AD B2C.
1. Selecione `Policy Keys` para exibir as chaves disponíveis no seu locatário. Se `B2C_1A_TokenSigningKeyContainer` existir, ignore essa chave.
1. Crie `TokenSigningKeyContainer`  
 * Clique em `+Add`
 * Opções > `Generate`
 * Nome >`TokenSigningKeyContainer` O prefixo B2C_1A_ pode ser adicionado automaticamente.
 * Tipo de chave > `RSA`
 * Datas - usar valores padrões
 * Uso de chave > `Signature`
1. Clique em `Create`
1. Se uma chave nomeada `B2C_1A_TokenEncryptionKeyContainer` existir, ignore essa chave.
1. Crie `TokenEncryptionKeyContainer`.
 * Opções > `Generate`
 * Nome >`TokenSigningKeyContainer` O prefixo B2C_1A_ pode ser adicionado automaticamente.
 * Tipo de chave > `RSA`
 * Datas - usar valores padrões
 * Uso de chave > `Encryption`
1. Clique em `Create`


[!TIP]
As próximas etapas são OPCIONAIS se você deseja oferecer provedores de identidade sociais ou provedores de identidade federados para seus usuários finais.  O Facebook fornece um bom ponto de partida para saber mais sobre provedores de identidade externos com o Azure AD B2C usando políticas personalizadas.

1. Crie `FacebookSecret`.  Embora opcional, esta etapa é recomendada para testar rapidamente sua capacidade de federar externamente.  Isso cria um ponto inicial sólido para desenvolver ainda mais suas diretivas com outros provedores de Id
 * Clique em `+Add`
 * Opções > `Manual`
 * Nome >`FacebookSecret` O prefixo B2C_1A_ pode ser adicionado automaticamente.
 * Segredo > Insira seu FacebookSecret de developers.facebook.com.  *Ele não é o ID do Facebook App*
 * Uso de chave > Assinatura
1. Clique em `Create` e confirme a criação, anote o nome

[!NOTE]
Se você estiver usando políticas internas do Azure AD B2C, você normalmente usará o mesmo segredo para políticas internas e personalizadas. 
