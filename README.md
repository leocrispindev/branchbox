# branchbox

Uma mini-plataforma que cria um **ambiente efêmero (Postgres + app) para cada Pull Request** na AWS, construída inteiramente com Infraestrutura como Código.

A ideia é a mesma de recursos como o *Branching* da Supabase: abriu um PR, nasce um ambiente isolado; fechou o PR, o ambiente é destruído. O projeto é construído em camadas, ao longo de um cronograma de estudos de 24 semanas, e cada fase é documentada em um artigo.

## Objetivos

- IaC em produção com **Terraform** e **Pulumi** (TypeScript e Go)
- Governança **multi-account** na AWS: Organizations, IAM Identity Center, SCPs, tagging e billing
- Primitivas de segurança: KMS, Secrets Manager, token vending machine, mTLS
- **EKS** com um controller Kubernetes próprio (CRD `PreviewEnv`) e warm pools
- Observabilidade (OpenTelemetry), SLOs e custo por ambiente

## Roteiro

| Fase | Tema | Entrega |
| --- | --- | --- |
| 0 | Base segura | SSO, Budgets, pre-commit, CI com OIDC |
| 1 | Terraform | Backend de state, módulo VPC, import de infra legada |
| 2 | Pulumi (TypeScript) | Componentes, policy as code, migração Terraform → Pulumi |
| 3 | Governança multi-account | Landing zone: OUs, SSO, SCPs, inventário e billing |
| 4 | Go + segurança | Token vending machine e mTLS |
| 5 | Rede e quotas | IPAM, DNS interno, alarmes de Service Quotas |
| 6 | EKS + controller em Go | CRD `PreviewEnv` com warm pool |
| 7 | Ambiente por Pull Request | Webhook → SQS → worker → build isolado |
| 8 | Operação e custo | SLOs, runbooks, custo por ambiente |

## Estrutura

```
bootstrap/   recursos iniciais (ex.: backend de state)
modules/     módulos Terraform reutilizáveis
docs/        padrões e decisões (ex.: observabilidade, ADRs)
.github/     pipelines de CI
```

## Pré-requisitos

- AWS CLI v2 com acesso via IAM Identity Center (`aws sso login`) — sem access keys
- Terraform, Pulumi, Go e Node
- pre-commit, tflint e checkov

```bash
pre-commit install
pre-commit run --all-files
```

## Princípios

- **Nada pelo console**: toda mudança passa por código, PR e pipeline.
- **Credenciais temporárias**: SSO localmente e OIDC no CI.
- **Tags obrigatórias**: `project`, `owner`, `env`, `ttl`.
- **Custo sob controle**: todo lab termina com `destroy`.
- **Observabilidade desde o início**: ver [docs/observabilidade.md](docs/observabilidade.md).
