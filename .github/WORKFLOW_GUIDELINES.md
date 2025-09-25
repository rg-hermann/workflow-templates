# Padrão de Workflows (Expandido)

## Fundamentos
- Todos os workflows devem ser escritos em arquivos `.yaml`.
- Use nomes claros e descritivos para jobs e steps.
- Separe workflows por responsabilidade (CI, CD, release, etc).
- Utilize actions oficiais sempre que possível.
- Adote variáveis de ambiente para segredos.
- Documente workflows no README ou neste arquivo.

## Versionamento de Reusables
- Use tags (`v1`, `v1.1.0`) para estabilidade.
- Evite breaking sem bump de major; adicionar novos inputs com defaults retrocompatíveis.

## Permissões
- Definir `permissions:` mínimos globalmente.
- Elevar somente em jobs específicos (ex: CodeQL => `security-events: write`).

## Inputs & Naming
- Preferir kebab-case: `python-version`, `azure-function-app-name`.
- Manter consistência entre build/deploy; evitar mistura de estilos.

## Reuso
- Usar composite actions para lógica repetida (`.github/actions/*`).
- Evitar duplicar passos de setup, install, test e coverage.

## Segurança
- OIDC > secrets estáticos (Service Principal) sempre que possível.
- Auditoria configurável (`audit-level`, `fail-on-audit`).
- Scripts multi-linha com `set -euo pipefail`.
- Minimizar exposição de segredos (sem eco em logs).

## Performance
- Cache determinístico (pip, npm/pnpm/yarn) usando `hashFiles` adequado.
- Artefatos enxutos (excluir testes, caches, __pycache__).
- Evitar compressão redundante / cache duplicado.

## Cobertura
- Inputs `enable-coverage` e (quando aplicável) `coverage-command`.
- Gerar `coverage.xml` para integração futura (Codecov / QA). 
- Publicar resumo em `$GITHUB_STEP_SUMMARY`.

## Concurrency & Timeouts
- `concurrency` em deploys para evitar corrida.
- `timeout-minutes` configurável para jobs demorados (ex: 15 CI / 25 Deploy).

## Observabilidade
- Resumos de auditoria e coverage em `$GITHUB_STEP_SUMMARY`.
- (Futuro) SARIF para resultados de auditorias (pip-audit / dependências Node).

## Qualidade de Código
- Lint configurável (flake8, ruff) com instalação condicional.
- Fail-fast configurável (futuro) em estratégias de matrix.

## Estrutura Recomendada
```
.github/
	workflows/
	actions/            # composite actions reutilizáveis
	CODEOWNERS
CHANGELOG.md
SECURITY.md
```

## Processo de Atualização
1. Abrir PR com mudanças.
2. Atualizar CHANGELOG.
3. Criar tag após merge se houver mudança funcional relevante.

## Futuro / Backlog
- Release semântico automatizado.
- Matrix multi-version.
- Secret scanning adicional.
- Upload SARIF real de pip-audit / npm audit.

