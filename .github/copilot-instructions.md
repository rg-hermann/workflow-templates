## Instruções para Copilot / Contribuidores

Este repositório fornece **workflows reutilizáveis** (`on: workflow_call`) para CI e deploy em múltiplas linguagens. O objetivo deste arquivo é dar contexto ao Copilot (e humanos) para gerar novos workflows / melhorias de forma consistente.

### 1. Estrutura de Diretórios

```
.github/workflows/_reusable/   # SOMENTE workflows reutilizáveis (expostos via workflow_call)
.github/workflows/             # Workflows internos (lint, release, etc.)
reusable-callers/              # Exemplos de uso (callers) para documentação/teste
```

### 2. Convenções de Nome
- Reutilizáveis: prefixo de arquivo simples e descritivo. Ex: `ci-node.yaml`, `ci-python.yaml`, `ci-java.yaml` (dentro de `_reusable`).
- Evitar nomes genéricos como `build.yaml`; prefira `ci-<linguagem>.yaml` ou `deploy-<target>.yaml`.
- Jobs internos dentro dos reusables: `ci`, `codeql`, `deploy`, `package` (kebab-case curto).

### 3. Inputs e Outputs
- Use `inputs:` sob `workflow_call` com:
  - `type` explícito (string, boolean, number)
  - `required: false` sempre que houver default seguro.
  - `default:` definido para evitar branches condicionais excessivos.
- Evite proliferar muitos inputs equivalentes—prefira um input principal e derivar lógica.
- Se houver output, publicar via `jobs.<job_id>.outputs` e documentar no README.

### 4. Permissões
Sempre declarar `permissions:` no nível raiz ou por job com princípio de menor privilégio. Padrão mínimo:
```yaml
permissions:
  contents: read
```
Elevar somente quando estritamente necessário (ex: `security-events: write` para CodeQL, `id-token: write` para OIDC deploys, `packages: write` para publicação).

### 5. Pin de Actions
- Usar ao menos versão major (`@v4`, `@v3`).
- Quando segurança for prioridade alta, considerar pin por SHA + comentário indicando origem.

### 6. Caching por Linguagem
- Node: `actions/setup-node@v4` (cache interno com package manager) ou chave custom `hashFiles('**/package-lock.json')`.
- Python: `actions/setup-python@v5` com `cache: 'pip'` + `cache-dependency-path`.
- Java: `actions/setup-java@v4` com `cache: maven|gradle` dependendo de `inputs.build-tool`.
- Evitar caches para diretórios gigantes (ex: `.venv` completo) se lockfile não estável.

### 7. Padrão de Steps (CI Genérico)
Ordem recomendada:
1. Checkout
2. Setup runtime/language
3. Instalação / build
4. Testes (com cobertura opcional)
5. Segurança / auditorias (npm audit, pip-audit, dependency review)
6. CodeQL (job separado, condicional)
7. Upload de artefatos (cobertura, relatórios) – condicional
8. Summary final

### 8. Boas Práticas de Shell
- Sempre iniciar scripts multi-linha com: `set -euo pipefail`.
- Usar variáveis derivadas de inputs via `${{ inputs.<nome> }}` dentro de bloco `run:` preferir quoting.
- Evitar subshells desnecessários.

### 9. Estilo YAML
- Indentação 2 espaços.
- Strings simples sem necessidade de quoting exceto quando contêm `:` ou começam com `{`, `[`.
- Comentários sucintos para blocos importantes.

### 10. Exemplo de Novo Workflow Reutilizável (Skeleton)
```yaml
name: Reusable <Feature>
on:
  workflow_call:
    inputs:
      example-flag:
        type: boolean
        default: false
        required: false
    outputs:
      artifact-path:
        description: Caminho do artefato gerado
        value: ${{ jobs.build.outputs.artifact-path }}

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      artifact-path: dist
    steps:
      - uses: actions/checkout@v4
      - name: Build
        run: echo "Gerar build aqui" && mkdir -p dist && echo ok > dist/marker
      - name: Upload
        uses: actions/upload-artifact@v4
        with:
          name: sample
          path: dist
```

### 11. Matrizes
Para suportar múltiplas linguagens/versões em um único workflow reutilizável:
```yaml
strategy:
  matrix:
    language: [node, python]
    include:
      - language: node
        setup: node@20
      - language: python
        setup: python@3.11
```
Derivar comandos via `case` ou script.

### 12. Resumos (Job Summary)
Adicionar linhas relevantes ao `$GITHUB_STEP_SUMMARY` para facilitar debugging sem abrir logs extensos.

### 13. Erros Comuns a Evitar
- Esquecer `workflow_call` em reusables (há um lint para isso).
- Atribuir permissões amplas (ex: `write` quando não necessário).
- Não definir `timeout-minutes` em jobs longos.
- Inputs boolean como string (use `type: boolean`).

### 14. Segurança e Auditoria
- CodeQL deve ser opcional (input boolean) para evitar custo sempre.
- dependency-review só faz sentido em PRs (`github.event_name == 'pull_request'`).
- Para OIDC / deploy, sempre `permissions: id-token: write` + `actions: read` + `contents: read`.

### 15. Prompts de Exemplo para Copilot
1. "Crie um workflow reutilizável para build e teste de biblioteca Rust com caching cargo e opção de publicar artifact."  
2. "Adicionar input boolean 'skip-tests' a `ci-node.yaml` que quando true pula comandos de teste e coverage."  
3. "Gerar workflow de deploy para Docker image em GHCR com scan Trivy opcional."  
4. "Adicionar matriz de versões Python (3.10,3.11,3.12) ao `ci-python` preservando cobertura apenas em 3.11."  

### 16. Fluxo para Adicionar Novo Reusable
1. Criar arquivo em `_reusable/` com nome claro.
2. Implementar `on: workflow_call` + inputs + permissions mínimos.
3. Rodar lint (GitHub PR abrirá resultado do `Lint Workflows`).
4. Atualizar README: tabela de workflows.
5. (Se aplicável) Adicionar exemplo em `reusable-callers/`.
6. Taggear release após merge se mudança relevante (futuro: automação). 

### 17. Roadmap Interno (resumido)
- Unificar multi-linguagem com matriz.
- Composite action para setup genérico (`setup-runtime`).
- Coverage reports padronizados (ex: convergir para cobertura em SARIF / summary).
- Escanear com Trivy / Semgrep (opcionais).

---
Última atualização: 2025-09. Ajuste este documento sempre que um padrão mudar.
