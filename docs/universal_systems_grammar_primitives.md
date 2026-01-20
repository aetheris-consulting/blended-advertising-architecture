# Universal Systems Grammar — Architecture Primitives

File: `docs/universal_systems_grammar_primitives.md`

This document encodes a **universal systems grammar** that makes AI-mediated decisions auditable, governable, and scalable across:
- **Enterprise procurement**
- **Personal/household commerce**
- Any other domain where *policy + accountability + workflows* matter

It is built from a small set of primitives derived from the axioms:

- Everything is a **data set**
- Everything is a **file** (versioned artifact)
- Everything is an **organization**
- Everything is a **workflow**
- **Compliance is nested**
- **Organizational interactions are nested**
- Safety/explainability uses **3 internal + 3 external loci**

---

## 0. Axioms → Primitive Map

| Axiom | Primitive family | Why it matters |
|---|---|---|
| Everything is a data set | `Dataset<T>`, `EventStream<TEvent>` | Queryability + provenance |
| Everything is a file | `Artifact`, `ArtifactRef`, `Bundle` | Versioning, diffs, signatures |
| Everything is an organization | `OrgNode`, `OrgEdge`, `Role`, `Authority` | Identity + accountability |
| Everything is a workflow | `Workflow`, `State`, `Transition`, `Gate` | Controlled state changes |
| Compliance is nested | `PolicyStack`, `Constraint`, `Precedence` | Inheritance + overrides |
| Interactions are nested | `Boundary`, `Contract`, `Connector` | Safe integration surfaces |
| 3 internal + 3 external loci | `LociReport` | Explainability + risk control |

---

## 1. Primitive Catalog

### 1.1 Dataset Primitives

**Purpose:** represent any “thing” (items, offers, policies, approvals, outcomes) as structured, queryable data.

**Primitives**
- `Dataset<T>`
- `EventStream<TEvent>`

**Recommended fields**
- `id`
- `schema_version`
- `records[]`
- `provenance` (sources, timestamps, signatures)
- `quality` (completeness, conflict flags)

---

### 1.2 File / Artifact Primitives

**Purpose:** make decisions and objects **serializable, versioned, diffable, and auditable**.

**Primitives**
- `Artifact`
- `ArtifactRef` (immutable pointer)
- `ArtifactBundle` (the unit of accountability for a “case”)

**Recommended fields**
- `artifact_id`
- `type` (Policy, OfferSet, Decision, Explanation, DraftPO, CartDraft, Approval)
- `version`
- `hash` / `signature`
- `created_by` (OrgNode/User/Service)
- `lineage` (derived_from refs)

**Rule:** anything that matters becomes an artifact.

---

### 1.3 Organization Primitives

**Purpose:** model identity, authority, and accountability across people, households, teams, vendors, and platforms.

**Primitives**
- `OrgNode`
- `OrgEdge`
- `Role`
- `Authority`

**OrgEdge types**
- `owns`, `member_of`, `approves_for`, `contracts_with`, `supplies`, `integrates_with`

---

### 1.4 Workflow Primitives

**Purpose:** represent intent as a **state machine** with explicit gates and authority checks.

**Primitives**
- `Workflow`
- `State`
- `Transition`
- `Gate`
- `Checklist`

**Reference workflows**
- Enterprise: `REQUEST → NORMALIZE → SOURCE → EVALUATE → DRAFT → CHECK → APPROVE → RESERVE → COMMIT → FULFILL → CLOSE`
- Personal: `INTENT → DISCOVER → VALIDATE → DRAFT_CART → BUDGET_IMPACT → CONFIRM → PURCHASE → LOG`

---

### 1.5 Nested Compliance Primitives

**Purpose:** enforce constraints using inheritance + precedence.

**Primitives**
- `Policy`
- `PolicyStack`
- `Constraint`
- `Exception`
- `PrecedenceRule`

**PolicyStack layers (typical)**
1. Global (law, platform safety, forbidden categories)
2. Domain (public-sector rules / PCI / HIPAA etc.)
3. Enterprise/Household (tenant/household rules)
4. Dept/CostCenter (budget + sourcing constraints)
5. Workflow step (checker required; thresholds)
6. Transaction context (amount, urgency, location, role)

**Evaluation outputs**
- `ALLOW | DENY | REQUIRE_HUMAN_REVIEW | REQUIRE_MORE_INFO`
- `explanations[]`, `tags[]`, `evidence_refs[]`

---

### 1.6 Nested Interaction Primitives (Boundaries & Connectors)

**Purpose:** all cross-entity interactions occur through explicit, governed boundaries.

**Primitives**
- `Boundary`
- `InteractionContract`
- `Connector`
- `Capability`
- `Offer`

**Connector capability examples**
- `search_catalog`
- `resolve_item`
- `price_quote`
- `inventory_eta`
- `perks_rebates`
- `draft_cart_or_rfq` *(never finalize purchase)*

---

### 1.7 The Six Loci (Explainability & Safety Lens)

**Purpose:** generate safety checks, disclosures, and explanations from six perspectives.

**Primitives**
- `Locus`
- `LociReport`

**Internal loci**
1) Self → Self (goal/budget coherence)  
2) Self within systems of self (roles/authority)  
3) Self within external systems (policies/constraints)

**External loci**
4) Self → Others (impact)  
5) Others → Self (influence/steering)  
6) Others ↔ Others (system integrity/market dynamics)

**LociReport outputs**
- `risks[]`
- `influence_disclosures[]`
- `counterfactuals[]`
- `recommended_gates[]`

---

## 2. Core Services Built from Primitives

- AI Orchestrator
- Normalization Service
- Policy Engine + Explanation Engine
- Budget Reservation Service
- Audit & Event Log (WORM)

---

## 3. “Case Bundle” (Minimal Reference Model)

```json
{
  "case_id": "case_2026_001",
  "org_context": {"tenant_id": "t1", "actor_id": "u77", "role": "REQUESTOR"},
  "workflow_id": "procure_v1",
  "artifacts": [
    {"ref": "artifact:normalized_item:v3"},
    {"ref": "artifact:offers:v1"},
    {"ref": "artifact:policy_eval:v2"},
    {"ref": "artifact:ranked_decision:v1"},
    {"ref": "artifact:explanation:v1"},
    {"ref": "artifact:approval:v1"}
  ],
  "events": ["event:state_changed", "event:approved", "event:committed"]
}
```

---

## 4. Extension Pattern

To extend into a new domain:
1. Add domain datasets + schemas
2. Add normalization rules
3. Add policy stack layers + precedence
4. Define workflows + gates
5. Add connectors behind boundaries
6. Ensure loci checks + audit artifacts exist at every money/risk boundary
