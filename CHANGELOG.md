# Changelog

Todas as mudanças notáveis neste repositório serão documentadas aqui.

O formato segue (inspirado em) Keep a Changelog e versionamento semântico.

## [Unreleased]
### Added
- Composite action Node CI (`.github/actions/node-ci`).
- Inputs avançados no `ci-node.yaml` (coverage, audit-level, fail-on-audit, package-manager, timeout-minutes).
- Inputs avançados no `ci-python.yaml` (coverage, pip-audit-level, fail-on-audit, timeout-minutes).
- OIDC e outputs no deploy de Function (`python_deploy_az_func.yaml`).
- Concurrency + slot canário opcional em `node-app-service.yaml`.
- Simplificação e contextualização do `.gitignore`.
- Artefatos de coverage e audit (Python) opcionais.

### Changed
- Padronização de inputs para kebab-case em workflows Python build/deploy.
- Uso de permissões mínimas; `security-events: write` movido para job CodeQL.
- Atualização `setup-node` para v4 e `setup-python` para v5 onde aplicável.

### Deprecated
- Uso de inputs antigos (`python_version`, `azure_function_app_name`) – mantenha compatibilidade em callers antes de migrar refs.

### Removed
- Cache npm duplicado em workflow de deploy Node (uso apenas do cache integrado).

### Security
- Opção de falha condicionada em auditoria npm e pip.
- OIDC suportado para Function App (reduz uso de secrets long-lived).

## [0.1.0] - 2025-09-24
### Added
- Versão inicial dos workflows Node CI, Python CI, Node App Service deploy, Python Function build & deploy.
