## Migração de Caminhos dos Workflows Reutilizáveis

Em setembro/2025 os workflows reutilizáveis foram movidos para a pasta dedicada:

```
.github/workflows/_reusable/
```

### Caminhos Antigos → Novos

| Antigo | Novo |
|--------|------|
| `.github/workflows/ci-node.yaml` | `.github/workflows/_reusable/ci-node.yaml` |
| `.github/workflows/ci-python.yaml` | `.github/workflows/_reusable/ci-python.yaml` |
| `.github/workflows/ci-java.yaml` | `.github/workflows/_reusable/ci-java.yaml` |

### O que fazer se você consumia os antigos

Atualize a referência `uses:` nos seus repositórios para o novo caminho, por exemplo:

```yaml
jobs:
  ci:
    uses: rg-hermann/workflow-templates/.github/workflows/_reusable/ci-node.yaml@v1
```

### Por que a mudança?

1. Separar visualmente workflows reutilizáveis de workflows internos do repositório (CI próprio, release, lint, etc.).
2. Facilitar futura criação de scripts de lint que validem apenas o diretório `_reusable`.
3. Incentivar padronização (todos reusables começam no mesmo prefixo de pasta) e futura versão semântica por tag/branch.

### Backward Compatibility

Os arquivos antigos foram removidos (não criados wrappers) para evitar duplicação. Caso precise de um período de transição com ambos, abra uma issue para discutirmos inclusão de wrappers temporários.

### Próximos Passos

- Implementar lint automático para garantir padronização (actionlint) – ver roadmap no README.
- Introduzir composite actions para reduzir duplicação entre linguagens.

---
Se encontrar qualquer problema na migração, abra uma issue com o snippet do erro e o ref usado.
