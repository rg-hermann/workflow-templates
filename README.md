# Workflow Templates

Repositório pessoal com **workflows reutilizáveis** do GitHub Actions e **exemplos de callers**.

## Estrutura

```
.github/workflows/        # Workflows reutilizáveis (workflow_call)
reusable-callers/         # Exemplos de uso chamando os reusables
	node/
	python/
```

## Workflows Reutilizáveis Disponíveis

| Workflow | Descrição | Principais Inputs |
|----------|-----------|------------------|
| `ci-node.yaml` | CI Node.js (install/build/test, coverage opcional, audit customizável, CodeQL, dependency review) | `node-version`, `package-manager`, `build-command`, `test-command`, `enable-coverage`, `run-codeql`, `run-npm-audit`, `audit-level` |
| `ci-python.yaml` | CI Python (install, lint, test, pip-audit opcional) | `python-version`, `lint-command`, `test-command`, `run-pip-audit` |
| `ci-java.yaml` | CI Java (Maven/Gradle, JUnit artifacts, JaCoCo opcional, dependency review, CodeQL opcional) | `java-version`, `build-tool`, `build-command`, `test-command`, `enable-coverage`, `run-codeql` |
| `node-app-service.yaml` | Build + deploy para Azure Web App (Node) | `app-name`, `slot-name`, `build-command` |
| `python_build_az_func.yaml` | Build/empacota Azure Function Python (coverage opcional) | `python-version`, `requirements-file`, `enable-coverage` |
| `python_deploy_az_func.yaml` | Deploy de Azure Function Python (cria recursos se faltando, suporta OIDC) | `azure-function-app-name`, `python-version`, `location`, `skip-resource-creation`, `client-id` |

## Como Consumir

Referencie o repositório e o caminho do workflow em `uses:` seguido de um ref (branch, tag ou commit SHA). Exemplo:

```yaml
jobs:
	ci:
		uses: rg-hermann/workflow-templates/.github/workflows/ci-node.yaml@main
		with:
			node-version: '20'
			build-command: 'npm run build'
			test-command: 'npm test'
		secrets: inherit
```

### Dica de Versionamento

Crie tags `v1`, `v1.1.0` etc e consuma sempre uma tag estável:

```yaml
uses: rg-hermann/workflow-templates/.github/workflows/ci-node.yaml@v1
```

## Exemplos (ver pasta `reusable-callers/`)

Node deploy para App Service:
```yaml
jobs:
	deploy:
		uses: rg-hermann/workflow-templates/.github/workflows/node-app-service.yaml@main
		with:
			app-name: my-node-api
			build-command: 'npm run build'
			test-command: 'npm test'
		secrets: inherit
```

Azure Function Python Build + Deploy:
```yaml
jobs:
	build:
		uses: rg-hermann/workflow-templates/.github/workflows/python_build_az_func.yaml@main
		with:
			python-version: '3.11'
		secrets: inherit

	deploy:
		needs: build
		uses: rg-hermann/workflow-templates/.github/workflows/python_deploy_az_func.yaml@main
		with:
			azure-function-app-name: my-func-app
			python-version: '3.11'
			artifact-name: functionapp.zip
		secrets: inherit
```

## Segredos Necessários

| Nome | Uso |
|------|-----|
| `AZURE_CREDENTIALS` | Login via Service Principal (json) para deploys Azure |

OU configure OIDC com: `client-id`, `tenant-id`, `subscription-id` (Node App Service e Python Function deploy). Garanta a role adequada (ex: `Contributor` + `User Access Administrator` quando preciso).

## Boas Práticas Implementadas

- Cache de dependências (`actions/setup-node` e pip cache)
- Composite action interna para Node (`.github/actions/node-ci`)
- Steps opcionais controlados por inputs (segurança/performance)
- `dependency-review-action` em PRs (Node)
- CodeQL opcional
- `pip-audit` / `npm audit` opcionais
- Concurrency em deploy de Function para evitar corrida
- Parametrização de região e grupo de recurso
- OIDC suportado em deploy de Function
- Auditoria com nível configurável e opção de falhar build
- Coverage opcional (Node e Python build)

## Próximos Passos Possíveis

- Adicionar workflow de release semântico automático
- Publicar composite actions para trechos repetidos
- Adicionar suporte a matrix (ex: múltiplas versões de Node/Python)
- Incluir scan SAST adicional (Trivy, etc.)
- Publicar SARIF de pip-audit e npm audit
- Adicionar testes de smoke pós-deploy

## Licença

Uso pessoal – adicione uma licença se for compartilhar publicamente.

## Changelog & Segurança

Veja `CHANGELOG.md` para histórico de mudanças e `SECURITY.md` para política de segurança.

---
Sinta-se livre para abrir issues ou enviar PRs com melhorias.
