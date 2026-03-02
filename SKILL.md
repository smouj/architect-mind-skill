---
name: System Architect
version: 1.0.0
description: Designs system architecture patterns, blueprints, and technical specifications for distributed systems and microservices
author: SMOUJBOT Architecture Team
requires:
  - terraform
  - kubectl
  - helm
  - docker
  - graphviz
  - plantuml
tags:
  - architecture
  - patterns
  - systems
  - design
  - infrastructure
  - cloud
  - microservices
env:
  AWS_PROFILE: "optional - AWS profile for infrastructure diagrams"
  TERRAFORM_WORKSPACE: "optional - Terraform workspace for architecture validation"
  DOCKER_REGISTRY: "optional - Registry for container architectures"
  ARCH_OUTPUT_DIR: "${OPENCLAW_WORKSPACE}/architecture"
---

# System Architect Skill

Designs system architecture patterns and blueprints for distributed systems, microservices, cloud infrastructure, and enterprise architectures. Generates technical specifications, infrastructure-as-code templates, and architectural decision records.

## Purpose

Real use cases:
- Design microservices architecture for a 10M user SaaS platform with event-driven communication
- Create cloud migration blueprint from monolith to AWS/Azure/GCP with zero downtime
- Generate infrastructure-as-code modules for Kubernetes clusters with multi-AZ deployment
- Produce architecture decision records (ADRs) for technology selection and trade-offs
- Design CQRS/Event Sourcing patterns for high-throughput financial systems
- Create disaster recovery architecture with RPO<5min, RTO<30min for regulated workloads

## Scope

Commands (real implementations):
- `architect design microservices --domain <domain> --scale <scale> --output <format>`
- `architect pattern cqrs --aggregate <name> --events <count> --snapshot-frequency <minutes>`
- `architect cloud aws --region <region> --multi-az --vpc-cidr <cidr> --nat-gateway`
- `architect diagram sequence --services <svc1,svc2,svc3> --output plantuml/mermaid --webhook`
- `architect adr create --title "<title>" --status "<proposed\|accepted\|superseded>" --context "<context>" --decision "<decision>" --consequences "<consequences>"`
- `architect terraform module --type <vpc\|eks\|rds\|lambda> --provider <aws\|azure\|gcp> --version <semver>`
- `architect validate dependency-graph --input <arch.yml> --check cyclic,fan-out,fan-in`
- `architect cost-estimate --infrastructure <iac-dir> --period <month\|quarter\|year> --currency <USD\|EUR>`

## Work Process

1. **Requirement Analysis**
   - Extract: `architect requirements parse --input <brief.md> --output structured.yml`
   - Identify: NFRs (scalability targets, latency SLAs, compliance needs)
   - Classify: transactional vs batch workloads, stateful vs stateless services

2. **Pattern Selection**
   - Execute: `architect patterns match --constraints "<latency:50ms\|throughput:10kRPS\|availability:99.99%>" --type communication,data,deployment`
   - Generate: `architect patterns apply --selected "<event-driven\|api-gateway\|saga>" --output architecture.yml`
   - Validate: `architect patterns verify --input architecture.yml --test load,security,resiliency`

3. **Blueprint Creation**
   - Create: `architect blueprint generate --domain <domain> --tech-stack "<Go,Postgres,Kafka>" --output blueprint.md`
   - Include: data flow diagrams, service boundaries, API contracts (OpenAPI 3.0), data models
   - Produce: `architect terraform init --blueprint blueprint.md --platform aws --output infra/`

4. **Infrastructure-as-Code**
   - Generate: `architect tf module --type network --vpc-cidr 10.0.0.0/16 --public-subnets 2 --private-subnets 4`
   - Generate: `architect tf module --type eks --cluster-name prod --node-groups 3 --instance-types m5.2xlarge`
   - Generate: `architect helm chart --service <svc> --replicas 3 --resources.requests.cpu 500m --resources.requests.mem 1Gi`

5. **ADR Documentation**
   - Create: `architect adr list --status accepted --template json`
   - Generate: `architect adr render --id 001 --format markdown --include decisions,consequences,links`
   - Publish: `architect adr export --output docs/adr/ --format html`

6. **Cost & Capacity Planning**
   - Estimate: `architect cost run --iac-dir infra/ --workload profile=high --period 12months --show-reserved-instances`
   - Output: `architect cost report --format table/csv/json --include compute,storage,network,total`
   - Validate: `architect capacity plan --target-rps 10000 --p99-latency 100ms --autoscaling policies`

## Golden Rules

- Never design without quantified NFRs (request metrics before starting)
- Always include failure modes in diagrams (draw what breaks, not just happy path)
- Infrastructure code must be environment-agnostic (use Terraform workspaces, not hard-coded vars)
- APIs must have OpenAPI contracts before implementation (contract-first)
- Secrets never in code; reference Parameter Store/Key Vault only
- Multi-region only when business requires RPO<RTO (justify added complexity)
- Data flows must show async boundaries (Kafka, SQS, Pub/Sub explicitly)
- Service mesh (Istio/Linkerd) only when >5 services or need mTLS/circuit-breaking
- Cost estimates include data transfer costs (often overlooked)
- ADRs must reference options considered and rejected with rationale

## Examples

**Example 1 - Microservices design for e-commerce:**
```
$ architect design microservices --domain e-commerce --scale 10M_users --output yaml
Domain: e-commerce
Services:
  - name: cart
    type: stateful (Redis)
    qps_target: 5000
    sla_p99: 50ms
  - name: checkout
    type: transactional (Postgres)
    qps_target: 800
    sla_p99: 100ms
  - name: inventory
    type: event-driven (Kafka consumer)
    qps_target: 2000
    sla_p99: 200ms
Event schema: Avro with Schema Registry
API Gateway: Kong (rate limiting, auth)
Monitoring: Prometheus + Grafana + Jaeger
```

**Example 2 - AWS VPC module generation:**
```
$ architect terraform module --type vpc --provider aws --vpc-cidr 10.1.0.0/16 --public-subnets 2 --private-subnets 4 --nat-gateway true --output modules/vpc/
Created: modules/vpc/vpc.tf (VPC with 6 subnets across 2 AZs)
Created: modules/vpc/nat.tf (2 NAT Gateways with EIPs)
Created: modules/vpc/outputs.tf (vpc_id, public_subnets, private_subnets)
Validation: terraform fmt passed, terraform validate passed
```

**Example 3 - CQRS pattern for order service:**
```
$ architect pattern cqrs --aggregate Order --events 12 --snapshot-frequency 100 --output pattern.yml
Aggregate: Order
Events: OrderCreated, OrderConfirmed, OrderPaid, OrderShipped, OrderCancelled
Command side: PostgreSQL (write model)
Query side: MongoDB (read model, denormalized)
Projection: Kafka Streams job (order-events -> order-read)
Snapshotting: Every 100 events, S3 bucket for snapshots
Compaction: Kafka log compaction on order-id
Recovery: Replay from snapshot + remaining events
```

**Example 4 - Architecture Decision Record:**
```
$ architect adr create --title "Adopt Event-Driven Architecture" --status accepted --context "Monolith processes 5k orders/day, batch jobs cause 4h latency" --decision "Split into order service + inventory service communicating via Kafka topics" --consequences "+ independent scaling, - eventual consistency, + resilience to downstream failures"
Created: docs/adr/0001-adopt-event-driven-architecture.md
Decision: Adopt Event-Driven Architecture
Status: accepted
Date: 2024-01-15
Context: Monolith processes 5k orders/day...
Decision: Split into order service + inventory service...
Consequences: + independent scaling, - eventual consistency...
```

**Example 5 - Cost estimation for GKE cluster:**
```
$ architect cost-estimate --infrastructure terraform/gcp/ --period month --currency USD
Resources:
  - GKE Standard cluster: 72.00 USD/month (3 nodes n1-standard-4)
  - Network Egress (1TB): 90.00 USD/month
  - Cloud Storage (logs, 500GB): 10.00 USD/month
  - CloudSQL Postgres ha: 250.00 USD/month (db-f1-micro HA)
  - Pub/Sub (10M msgs): 50.00 USD/month
Total: 472.00 USD/month
Savings with 1-year reserved instances: -120.00 USD/month
Break-even point: 12 months
```

## Rollback

- Revert blueprint: `git revert <commit-hash>; architect blueprint apply --reverted <commit-hash>`
- Destroy infrastructure: `terraform destroy -auto-approve --workspace ${TERRAFORM_WORKSPACE}`
- Remove ADR: `architect adr delete --id <number> --move-to superseded`
- Unapply Terraform module: `terraform apply -replace=<module.resource> -auto-approve` or `terraform state rm <address>`
- Restore previous diagram: `git checkout HEAD~1 -- docs/diagrams/`
- Revert cost estimate: no state, simply remove output files: `rm -f cost-report-*.{csv,json}`

## Verification

Run after architecture generation:
```bash
# Validate IaC syntax
terraform validate infra/
helm lint charts/*

# Check diagram syntax
plantuml -check syntax diagrams/*.puml

# Verify OpenAPI spec
openapi-generator-cli validate -i api-spec.yaml

# Run architectural conformance tests
architect validate --input architecture.yml --checks naming,circuit-breaker,timeout-policy

# Security scan
trivy config infra/

# Performance simulation
architect load-test --target <service> --rps 1000 --duration 60s --p99-latency-threshold 200ms
```

## Dependencies

Required:
- terraform >= 1.5.0 (for IaC generation)
- kubectl >= 1.28 (K8s manifests)
- helm >= 3.12 ( Helm charts)
- graphviz >= 8.0 (diagram rendering)
- plantuml >= 1.2023.8 (sequence diagrams)
- docker >= 24.0 (container images)

Optional:
- openapi-generator-cli (API contract verification)
- trivy (security scanning)
- k6 (load testing)
- awscli / gcloud / azure-cli (cloud provider CLIs)

## Troubleshooting

**Error: "Terraform module generation failed - unsupported instance type"**
- Check: Provider's instance type compatibility for selected region
- Fix: Use `architect terraform module --type compute --instance-types <approved-list>` with allowed types from cloud provider

**Error: "ADR template not found"**
- Check: Template files in `templates/adr/` directory exist (default.md, exception.md)
- Fix: `architect adr init --templates-dir <custom-dir>` or restore missing templates

**Error: "Diagram rendering timeout - large architecture"**
- Cause: >50 services in single diagram causes PlantUML timeout
- Fix: `architect diagram decompose --by-domain --output diagrams/` then generate separate diagrams

**Error: "Cost estimation incomplete - missing pricing data"**
- Check: Cloud provider pricing API accessible, or pricing cache exists
- Fix: Set `CACHE_DIR` env var or use `--use-pricing-api` flag with credentials configured

**Error: "Kubernetes manifest validation failed"**
- Common: Missing required labels or invalid resource quotas
- Fix: `architect kubernetes validate --strict --fixLabels` to auto-add standard labels (`app.kubernetes.io/name`, `app.kubernetes.io/part-of`)

**Error: "Pattern application conflict - incompatible services"**
- Cause: Selected patterns have conflicting requirements (e.g., CQRS with strong consistency requirement)
- Fix: `architect patterns conflict --pattern1 cqrs --pattern2 strong-consistency --resolution choose-weaker-guarantee` or select alternative pattern
```