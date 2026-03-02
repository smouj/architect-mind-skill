name: Arquitecto de Sistemas
version: 1.0.0
description: Diseña patrones de arquitectura de sistemas, planos y especificaciones técnicas para sistemas distribuidos y microservicios
author: Equipo de Arquitectura SMOUJBOT
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
  AWS_PROFILE: "opcional - Perfil de AWS para diagramas de infraestructura"
  TERRAFORM_WORKSPACE: "opcional - Workspace de Terraform para validación de arquitectura"
  DOCKER_REGISTRY: "opcional - Registry para arquitecturas de contenedores"
  ARCH_OUTPUT_DIR: "${OPENCLAW_WORKSPACE}/architecture"
---

# Habilidad de Arquitecto de Sistemas

Diseña patrones y planos de arquitectura de sistemas para sistemas distribuidos, microservicios, infraestructura cloud y arquitecturas empresariales. Genera especificaciones técnicas, plantillas de infraestructura-como-código y registros de decisiones arquitectónicas.

## Propósito

Casos de uso reales:
- Diseñar arquitectura de microservicios para una plataforma SaaS de 10M de usuarios con comunicación orientada a eventos
- Crear plan de migración a cloud de monolito a AWS/Azure/GCP con cero downtime
- Generar módulos de infraestructura-como-código para clústeres Kubernetes con despliegue multi-AZ
- Producir registros de decisiones arquitectónicas (ADRs) para selección de tecnologías y trade-offs
- Diseñar patrones CQRS/Event Sourcing para sistemas financieros de alto rendimiento
- Crear arquitectura de recuperación de desastres con RPO<5min, RTO<30min para cargas reguladas

## Alcance

Comandos (implementaciones reales):
- `architect design microservices --domain <domain> --scale <scale> --output <format>`
- `architect pattern cqrs --aggregate <name> --events <count> --snapshot-frequency <minutes>`
- `architect cloud aws --region <region> --multi-az --vpc-cidr <cidr> --nat-gateway`
- `architect diagram sequence --services <svc1,svc2,svc3> --output plantuml/mermaid --webhook`
- `architect adr create --title "<title>" --status "<proposed|accepted|superseded>" --context "<context>" --decision "<decision>" --consequences "<consequences>"`
- `architect terraform module --type <vpc|eks|rds|lambda> --provider <aws|azure|gcp> --version <semver>`
- `architect validate dependency-graph --input <arch.yml> --check cyclic,fan-out,fan-in`
- `architect cost-estimate --infrastructure <iac-dir> --period <month|quarter|year> --currency <USD|EUR>`

## Proceso de Trabajo

1. **Análisis de Requisitos**
   - Extraer: `architect requirements parse --input <brief.md> --output structured.yml`
   - Identificar: NFRs (métricas de escalabilidad, latencias SLA, necesidades de cumplimiento)
   - Clasificar: cargas transaccionales vs batch, servicios con estado vs sin estado

2. **Selección de Patrones**
   - Ejecutar: `architect patterns match --constraints "<latency:50ms|throughput:10kRPS|availability:99.99%>" --type communication,data,deployment`
   - Generar: `architect patterns apply --selected "<event-driven|api-gateway|saga>" --output architecture.yml`
   - Validar: `architect patterns verify --input architecture.yml --test load,security,resiliency`

3. **Creación de Planos**
   - Crear: `architect blueprint generate --domain <domain> --tech-stack "<Go,Postgres,Kafka>" --output blueprint.md`
   - Incluir: diagramas de flujo de datos, límites de servicio, contratos API (OpenAPI 3.0), modelos de datos
   - Producir: `architect terraform init --blueprint blueprint.md --platform aws --output infra/`

4. **Infraestructura-como-Código**
   - Generar: `architect tf module --type network --vpc-cidr 10.0.0.0/16 --public-subnets 2 --private-subnets 4`
   - Generar: `architect tf module --type eks --cluster-name prod --node-groups 3 --instance-types m5.2xlarge`
   - Generar: `architect helm chart --service <svc> --replicas 3 --resources.requests.cpu 500m --resources.requests.mem 1Gi`

5. **Documentación ADR**
   - Crear: `architect adr list --status accepted --template json`
   - Generar: `architect adr render --id 001 --format markdown --include decisions,consequences,links`
   - Publicar: `architect adr export --output docs/adr/ --format html`

6. **Planificación de Costos y Capacidad**
   - Estimar: `architect cost run --iac-dir infra/ --workload profile=high --period 12months --show-reserved-instances`
   - Salida: `architect cost report --format table/csv/json --include compute,storage,network,total`
   - Validar: `architect capacity plan --target-rps 10000 --p99-latency 100ms --autoscaling policies`

## Reglas de Oro

- Nunca diseñar sin NFRs cuantificados (solicitar métricas antes de comenzar)
- Siempre incluir modos de fallo en diagramas (dibujar lo que se rompe, no solo el happy path)
- Código de infraestructura debe ser agnóstico de entorno (usar workspaces de Terraform, no variables hard-coded)
- Las APIs deben tener contratos OpenAPI antes de implementación (contract-first)
- Secretos nunca en código; solo referenciar Parameter Store/Key Vault
- Multi-región solo cuando el negocio requiere RPO<RTO (justificar complejidad añadida)
- Los flujos de datos deben mostrar límites asíncronos (Kafka, SQS, Pub/Sub explícitamente)
- Service mesh (Istio/Linkerd) solo cuando >5 servicios o se necesita mTLS/circuit-breaking
- Las estimaciones de costo incluyen costos de transferencia de datos (a menudo pasados por alto)
- Los ADRs deben referenciar opciones consideradas y rechazadas con rationale

## Ejemplos

**Ejemplo 1 - Diseño de microservicios para e-commerce:**
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

**Ejemplo 2 - Generación de módulo VPC en AWS:**
```
$ architect terraform module --type vpc --provider aws --vpc-cidr 10.1.0.0/16 --public-subnets 2 --private-subnets 4 --nat-gateway true --output modules/vpc/
Created: modules/vpc/vpc.tf (VPC with 6 subnets across 2 AZs)
Created: modules/vpc/nat.tf (2 NAT Gateways with EIPs)
Created: modules/vpc/outputs.tf (vpc_id, public_subnets, private_subnets)
Validation: terraform fmt passed, terraform validate passed
```

**Ejemplo 3 - Patrón CQRS para servicio de órdenes:**
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

**Ejemplo 4 - Registro de Decisión Arquitectónica:**
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

**Ejemplo 5 - Estimación de costos para clúster GKE:**
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

- Revertir plano: `git revert <commit-hash>; architect blueprint apply --reverted <commit-hash>`
- Destruir infraestructura: `terraform destroy -auto-approve --workspace ${TERRAFORM_WORKSPACE}`
- Eliminar ADR: `architect adr delete --id <number> --move-to superseded`
- Desaplicar módulo Terraform: `terraform apply -replace=<module.resource> -auto-approve` o `terraform state rm <address>`
- Restaurar diagrama anterior: `git checkout HEAD~1 -- docs/diagrams/`
- Revertir estimación de costos: sin estado, simplemente eliminar archivos de salida: `rm -f cost-report-*.{csv,json}`

## Verificación

Ejecutar después de generación de arquitectura:
```bash
# Validar sintaxis IaC
terraform validate infra/
helm lint charts/*

# Verificar sintaxis de diagramas
plantuml -check syntax diagrams/*.puml

# Verificar especificación OpenAPI
openapi-generator-cli validate -i api-spec.yaml

# Ejecutar pruebas de conformidad arquitectónica
architect validate --input architecture.yml --checks naming,circuit-breaker,timeout-policy

# Escaneo de seguridad
trivy config infra/

# Simulación de rendimiento
architect load-test --target <service> --rps 1000 --duration 60s --p99-latency-threshold 200ms
```

## Dependencias

Requeridas:
- terraform >= 1.5.0 (para generación de IaC)
- kubectl >= 1.28 (manifiestos K8s)
- helm >= 3.12 (charts de Helm)
- graphviz >= 8.0 (renderizado de diagramas)
- plantuml >= 1.2023.8 (diagramas de secuencia)
- docker >= 24.0 (imágenes de contenedores)

Opcionales:
- openapi-generator-cli (verificación de contratos API)
- trivy (escaneo de seguridad)
- k6 (pruebas de carga)
- awscli / gcloud / azure-cli (CLIs de proveedores cloud)

## Solución de Problemas

**Error: "Terraform module generation failed - unsupported instance type"**
- Verificar: Compatibilidad de tipo de instancia del proveedor para región seleccionada
- Fix: Usar `architect terraform module --type compute --instance-types <approved-list>` con tipos permitidos del proveedor cloud

**Error: "ADR template not found"**
- Verificar: Archivos de plantilla en directorio `templates/adr/` existen (default.md, exception.md)
- Fix: `architect adr init --templates-dir <custom-dir>` o restaurar plantillas faltantes

**Error: "Diagram rendering timeout - large architecture"**
- Causa: >50 servicios en un solo diagrama causa timeout de PlantUML
- Fix: `architect diagram decompose --by-domain --output diagrams/` luego generar diagramas separados

**Error: "Cost estimation incomplete - missing pricing data"**
- Verificar: API de precios del proveedor cloud accesible, o existe caché de precios
- Fix: Configurar variable de entorno `CACHE_DIR` o usar flag `--use-pricing-api` con credenciales configuradas

**Error: "Kubernetes manifest validation failed"**
- Común: Labels requeridas faltantes o quotas de recursos inválidas
- Fix: `architect kubernetes validate --strict --fixLabels` para añadir labels estándar automáticamente (`app.kubernetes.io/name`, `app.kubernetes.io/part-of`)

**Error: "Pattern application conflict - incompatible services"**
- Causa: Patrones seleccionados tienen requisitos conflictivos (ej. CQRS con requisito de consistencia fuerte)
- Fix: `architect patterns conflict --pattern1 cqrs --pattern2 strong-consistency --resolution choose-weaker-guarantee` o seleccionar patrón alternativo
```