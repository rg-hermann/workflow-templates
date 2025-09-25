# Política de Segurança

## Relato de Vulnerabilidades
Use Issues privadas ou contato direto (caso disponível) para relatar vulnerabilidades. Evite divulgar detalhes publicamente antes de correção.

## Escopo
Este repositório contém templates de workflows GitHub Actions. Os principais vetores a observar:
- Injeção via inputs não validados
- Uso de actions de terceiros não fixadas em SHA/tag confiável
- Vazamento de segredos em logs

## Práticas Adotadas
- Permissões mínimas (`permissions:`) somente onde necessário.
- OIDC preferido a secrets de Service Principal quando possível.
- Auditoria opcional de dependências (npm audit, pip-audit) com níveis configuráveis.
- Concurrency em deploys para evitar race conditions.
- Sanitização simples de nomes para storage account na Function App.

## Recomendações ao Consumidor
- Fixar `uses:` em tags versionadas ou commit SHA.
- Revisar ações de terceiros e habilitar Dependabot para `github-actions`.
- Usar OIDC (federated credentials) com roles mínimas no Azure.
- Evitar expor segredos em outputs/echo.

## Ciclo de Atualização
- Mudanças de segurança serão mencionadas na seção Security do CHANGELOG.

## Contato
Abra uma Issue marcando como "security" ou contacte o mantenedor (quando canal privado existir).
