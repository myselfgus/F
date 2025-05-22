# Melhores Práticas para Gerenciamento de Pacotes e Qualidade em Projetos F#

Este guia foi desenvolvido para orientar o desenvolvimento de projetos F# com foco em: segurança, versionamento consistente, código limpo, correto, bem tipado e sempre funcional/pronto para produção.

---

## 1. Consistência e Gerenciamento de Versões

- **Alinhe versões de pacotes:** Mantenha todas as dependências relacionadas (ex: todos os `Microsoft.Extensions.*`) na mesma versão para evitar incompatibilidades.
- **Evite pacotes beta em produção:** Utilize apenas versões estáveis dos pacotes. Exemplo: substitua `Azure.AI.OpenAI beta.9` por uma versão estável assim que possível.
- **Versionamento centralizado:** Utilize `Directory.Packages.props` para gerenciar versões de dependências em múltiplos projetos no repositório.
- **Atualize pacotes obsoletos:** Substitua dependências antigas por versões compatíveis com o .NET e F# mais recentes.
- **Versionamento semântico:** Siga o versionamento semântico (semver) e documente mudanças relevantes no changelog.

## 2. Estrutura, Organização e Ordem de Compilação

- **Ordem de compilação do F#:** Os arquivos `.fs` devem ser listados na ordem correta no `.fsproj`, pois o F# compila os arquivos de cima para baixo.
- **Módulos coesos e pequenos:** Separe seu código em módulos pequenos e de responsabilidade única.
- **Comentários e agrupamento:** Agrupe dependências relacionadas com comentários claros (exemplo: `<!-- Health checks dependencies -->`).

## 3. Uso Explícito e Controle de Dependências

- **Referências explícitas:** Sempre declare explicitamente todas as dependências no arquivo de projeto. Nunca dependa apenas de dependências transitivas.
- **Auditoria de pacotes:** Utilize `dotnet list package --vulnerable` para identificar vulnerabilidades em dependências.
- **Lock files:** Ative o lock de pacotes com `<RestorePackagesWithLockFile>true</RestorePackagesWithLockFile>` para builds reproduzíveis.
- **Dependabot:** Ative o Dependabot para manter dependências sempre atualizadas e seguras.

## 4. Ecossistema F# e Handling Específico

- **Referencie explicitamente FSharp.Core:** Sempre utilize uma versão fixa do `FSharp.Core` no projeto para evitar incompatibilidades.
- **Aproveite o ecossistema F#:**
  - Use `FSharp.Data` para manipulação de dados.
  - Use `FsToolkit.ErrorHandling` para handling funcional de erros e opções.
  - Considere outras bibliotecas idiomáticas como `Expecto` (testes), `FsCheck` (testes de propriedade), `FSharpLint` (lint) e `dotnet-fantomas` (formatador).

## 5. Paket e Gerenciamento Avançado

- **Considere o Paket:** [Paket](https://fsprojects.github.io/Paket/) é uma alternativa avançada para gerenciamento de dependências em F#. Ele permite controle preciso, grouping e restrições de resolução de versões, sendo ideal para projetos complexos ou multi-target.

## 6. Garantia de Qualidade e Automação

- **Integração Contínua (CI):** Configure pipelines (ex: GitHub Actions) para rodar build, testes, lint, análise estática e auditoria de pacotes em todo push/PR.
- **Cobertura de testes:** Mantenha testes unitários, integração e, se possível, de propriedade (com FsCheck).
- **Linters e formatadores:** Use `FSharpLint` e `dotnet-fantomas` localmente e no CI.
- **Pre-commit hooks:** Configure hooks para rodar lint/test antes de cada commit.
- **Release automatizado:** Gere releases automaticamente após builds e testes bem-sucedidos.

## 7. Código Limpo, Seguro e Funcional

- **Abuse dos tipos:** Modele o domínio usando Discriminated Unions e records ao invés de tipos primitivos.
- **Funções puras:** Prefira funções sem efeitos colaterais para facilitar testes e manutenção.
- **Pattern matching completo:** Sempre trate todos os casos em DUs e Option.
- **Evite null:** Use Option e Result para ausência de valores ou erros.
- **Documentação:** Comente DU, records e funções públicas.

## 8. Exemplo de Workflow CI (GitHub Actions)

```yaml
name: Build, Test and Quality Check

on: [push, pull_request]

jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-dotnet@v3
        with:
          dotnet-version: '8.x'
      - run: dotnet restore
      - run: dotnet fsharplint lint src/
      - run: dotnet fantomas --check src/
      - run: dotnet build --no-restore --configuration Release
      - run: dotnet test --no-restore --configuration Release --collect:"XPlat Code Coverage"
      - run: dotnet list package --vulnerable
```

---

**Siga estas práticas para garantir que seu projeto F# seja limpo, seguro, bem tipado e pronto para produção — maximizando a produtividade e a eficiência do desenvolvimento, inclusive com o Copilot!**
