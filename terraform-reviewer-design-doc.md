# Terraform Code Reviewer: Multi-Agent System Design Document

**Project:** Google ADK 5-Day Workshop Capstone
**Version:** 2.0 (With MCP Integration)
**Date:** November 18, 2025

---

## Executive Summary

The Terraform Code Reviewer is an autonomous multi-agent system that analyzes Terraform repositories, generates comprehensive documentation, provides usage examples, delivers actionable critiques, and validates all outputs through agentic evaluation. The system leverages Google's Agent Development Kit (ADK) for multi-agent orchestration and integrates with the Terraform Model Context Protocol (MCP) server for authentic Terraform operations.

### Key Capabilities
- **Autonomous Analysis:** Multi-agent discovery and parsing of Terraform codebases
- **Intelligent Documentation:** AI-generated docs with iterative refinement loops
- **Practical Examples:** Self-validating usage examples that actually work
- **Expert Critique:** Industry-standard best practices analysis with security scanning
- **Agentic Evaluation:** Fully automated validation using real Terraform operations via MCP

---

## System Architecture

### Technology Stack
- **Orchestration:** Google ADK (Agent Development Kit)
- **Infrastructure Integration:** Terraform MCP Server
- **Language Model:** Claude Sonnet 4 (via Anthropic API)
- **Validation Tools:** tflint, terrascan, checkov (via MCP)

### Design Philosophy

The system follows a **WHAT → WHY → HOW** progression:

1. **WHAT** (Discovery): Understand what exists in the codebase
2. **WHY** (Context): Explain purpose, rationale, and architectural decisions
3. **HOW** (Guidance): Provide usage instructions and improvement recommendations
4. **EVALUATION** (Validation): Autonomously verify all outputs using real tools

---

## Agent Catalog

### Category: WHAT Agents (Descriptive/Observational)

#### 1. Repository Analyzer Agent

**Purpose:** Catalog and inventory all Terraform components in the repository

**Function:**
- Scans repository structure and identifies all Terraform files (.tf, .tfvars)
- Uses MCP to parse HCL (HashiCorp Configuration Language) properly
- Extracts modules, resources, data sources, variables, and outputs
- Builds a comprehensive inventory of all Terraform components
- Creates file structure map for navigation

**MCP Operations:**
```
- mcp.parse_terraform(repo_path)
- mcp.get_module_structure(module_path)
- mcp.list_resources(path)
- mcp.get_variables(path)
- mcp.get_outputs(path)
```

**Inputs:**
- Terraform repository path
- Optional: configuration for ignored paths

**Outputs:**
```json
{
  "modules": [
    {
      "name": "vpc",
      "path": "modules/networking/vpc",
      "resources": ["aws_vpc", "aws_subnet", ...],
      "variables": [...],
      "outputs": [...],
      "dependencies": [...]
    }
  ],
  "file_structure": {...},
  "total_resources": 45,
  "total_modules": 8
}
```

**Execution Pattern:** Parallel (with Cross-Reference Linker)

---

#### 2. Cross-Reference Linker Agent

**Purpose:** Map dependencies and relationships between modules

**Function:**
- Identifies inter-module dependencies
- Maps data source references across modules
- Tracks variable passing between modules
- Creates dependency graph for visualization
- Identifies potential circular dependencies

**MCP Operations:**
```
- mcp.get_dependencies(module_path)
- mcp.analyze_data_sources(path)
- mcp.get_module_calls(path)
```

**Inputs:**
- Repository structure from Repository Analyzer
- Terraform configuration files

**Outputs:**
```json
{
  "dependency_graph": {
    "nodes": ["vpc", "compute", "database"],
    "edges": [
      {"from": "compute", "to": "vpc", "type": "module_reference"},
      {"from": "database", "to": "vpc", "type": "data_source"}
    ]
  },
  "shared_variables": [...],
  "circular_dependencies": []
}
```

**Execution Pattern:** Parallel (with Repository Analyzer)

---

#### 3. Technical Accuracy Reviewer Agent

**Purpose:** Validate that documentation matches actual code

**Function:**
- Compares documented variables against actual variable definitions
- Verifies default values match code
- Checks type annotations are correct
- Validates output descriptions reference real outputs
- Ensures examples use correct resource names and attributes
- Uses MCP to validate syntax of documented code snippets

**MCP Operations:**
```
- mcp.parse_terraform(code_snippet)
- mcp.terraform_validate(snippet_path)
- mcp.get_variable_details(var_name)
```

**Inputs:**
- Generated documentation
- Original Terraform code
- Shared context from Stage 1

**Outputs:**
```json
{
  "accuracy_score": 98.5,
  "discrepancies": [
    {
      "type": "variable_type_mismatch",
      "documented": "string",
      "actual": "list(string)",
      "location": "modules/vpc/main.tf:15"
    }
  ],
  "verified_items": 124,
  "total_items": 126
}
```

**Execution Pattern:** Parallel in Stage 2b refinement

---

#### 4. Completeness Checker Agent

**Purpose:** Ensure all code elements are documented

**Function:**
- Counts all variables, outputs, and resources in code
- Checks each has corresponding documentation
- Identifies missing descriptions
- Flags undocumented required variables
- Ensures all outputs have purpose statements
- Validates module README completeness

**MCP Operations:**
```
- mcp.get_all_variables(path)
- mcp.get_all_outputs(path)
- mcp.get_all_resources(path)
```

**Inputs:**
- Generated documentation
- Original Terraform code inventory

**Outputs:**
```json
{
  "completeness_score": 95.2,
  "missing_documentation": [
    {
      "type": "variable",
      "name": "database_password",
      "location": "modules/database/variables.tf:42",
      "severity": "high"
    }
  ],
  "coverage": {
    "variables": "98%",
    "outputs": "100%",
    "modules": "95%"
  }
}
```

**Execution Pattern:** Parallel in Stage 2b refinement

---

### Category: WHY Agents (Analytical/Explanatory)

#### 5. Clarity Enhancer Agent

**Purpose:** Improve documentation readability and add contextual explanations

**Function:**
- Analyzes technical documentation for clarity
- Adds "why this matters" context to technical descriptions
- Explains architectural decisions and design patterns
- Improves sentence structure and readability
- Adds examples where concepts are complex
- Ensures consistent terminology throughout

**Inputs:**
- Initial documentation draft
- Code context from Stage 1

**Outputs:**
```json
{
  "clarity_score": 8.5,
  "enhancements": [
    {
      "section": "VPC Module Overview",
      "before": "Creates VPC with subnets",
      "after": "Creates a Virtual Private Cloud (VPC) with public and private subnets across multiple availability zones, providing network isolation and high availability for your infrastructure"
    }
  ],
  "readability_metrics": {
    "flesch_reading_ease": 65,
    "avg_sentence_length": 18
  }
}
```

**Execution Pattern:** Parallel in Stage 2b refinement

---

### Category: WHAT/WHY/HOW Agents (Comprehensive/Overlap)

#### 6. Documentation Generator Agent

**Purpose:** Create comprehensive module documentation

**Function:**
- Generates initial documentation covering WHAT/WHY/HOW
- **WHAT:** Documents all variables, outputs, resources
- **WHY:** Explains module purpose and use cases
- **HOW:** Shows basic usage syntax and requirements
- Creates README.md structure for each module
- Generates variable/output reference tables
- Includes prerequisites and dependencies

**Inputs:**
- Shared context from Stage 1 (Repository Analyzer + Cross-Reference Linker)
- Module metadata and structure

**Outputs:**
```markdown
# VPC Module

## Overview
This module creates a highly available VPC with...

## Usage
```hcl
module "vpc" {
  source = "./modules/vpc"
  ...
}
```

## Variables
| Name | Type | Description | Default |
|------|------|-------------|---------|
| ... | ... | ... | ... |

## Outputs
...
```

**Execution Pattern:** Sequential (requires Stage 1 completion)

---

### Category: HOW Agents (Prescriptive/Instructional)

#### 7. Usage/Examples Agent

**Purpose:** Generate practical, working usage examples

**Function:**
- Creates real-world usage scenarios for each module
- Generates end-to-end examples combining multiple modules
- Produces configuration patterns for common use cases
- Shows terraform plan output examples
- Creates quickstart guides
- **Self-validating:** Uses MCP to test examples before including them

**MCP Operations:**
```
- mcp.create_workspace(name)
- mcp.write_files(workspace_id, files)
- mcp.terraform_init(workspace_id)
- mcp.terraform_validate(workspace_id)
- mcp.terraform_plan(workspace_id, dry_run=True)
- mcp.cleanup_workspace(workspace_id)
```

**Inputs:**
- Module documentation
- Shared context from Stage 1
- Common usage patterns

**Outputs:**
```json
{
  "examples": [
    {
      "name": "basic-vpc",
      "module": "networking/vpc",
      "description": "Basic VPC setup with public and private subnets",
      "code": "module \"vpc\" { ... }",
      "validated": true,
      "terraform_plan_summary": {
        "resources_to_create": 8,
        "estimated_cost": "$45/month"
      }
    }
  ],
  "validation_results": {
    "total_examples": 15,
    "validated_successfully": 14,
    "failed_validation": 1
  }
}
```

**Execution Pattern:** Parallel in Stage 3 (with Best Practices Critic)

---

#### 8. Best Practices Critic Agent

**Purpose:** Provide expert critique and improvement recommendations

**Function:**
- Reviews code against Terraform style guide
- Identifies security vulnerabilities (exposed secrets, overly permissive IAM)
- Flags anti-patterns (hardcoded values, missing variables)
- Checks for deprecated resource types
- Validates state management practices
- Suggests refactoring opportunities
- Uses MCP to run industry-standard linting tools

**MCP Operations:**
```
- mcp.run_tflint(repo_path, config)
- mcp.run_terrascan(repo_path)
- mcp.run_checkov(repo_path)
- mcp.run_security_scan(repo_path)
```

**Inputs:**
- Original Terraform code
- Generated documentation
- Shared context from all previous stages

**Outputs:**
```json
{
  "findings": [
    {
      "severity": "high",
      "category": "security",
      "issue": "S3 bucket has public access enabled",
      "location": "modules/storage/s3.tf:15",
      "recommendation": "Add 'block_public_acls = true' to aws_s3_bucket_public_access_block",
      "tool": "terrascan",
      "remediation_code": "..."
    }
  ],
  "summary": {
    "critical": 2,
    "high": 8,
    "medium": 15,
    "low": 23
  },
  "best_practices_score": 78
}
```

**Execution Pattern:** Parallel in Stage 3 (with Usage/Examples Agent)

---

### Category: META Agents (Orchestration & Validation)

#### 9. Orchestrator Agent

**Purpose:** Coordinate workflow and aggregate results

**Function:**
- Manages stage transitions and agent coordination
- Aggregates outputs from all agents
- Compiles unified final report
- Manages session state for interactive Q&A
- Handles context passing between stages
- Prioritizes findings and recommendations

**Inputs:**
- Outputs from all previous agents
- User queries and commands

**Outputs:**
```json
{
  "final_report": {
    "documentation": {...},
    "examples": [...],
    "critique": {...},
    "evaluation": {...}
  },
  "session_context": {...},
  "prioritized_actions": [
    "Fix critical security issue in S3 module",
    "Add missing documentation for database module",
    ...
  ]
}
```

**Execution Pattern:** Sequential after all stages complete

---

#### 10. Evaluation Agent ⭐ (Agentic)

**Purpose:** Autonomously validate all outputs using real Terraform operations

**Function:**
- **Autonomous Testing:** Creates isolated test workspaces for each example
- **Real Validation:** Runs terraform init/validate/plan on generated examples
- **Accuracy Verification:** Compares documentation against actual parsed code
- **Critique Validation:** Confirms critique findings match linting tool outputs
- **Quality Assessment:** Runs comprehensive quality checks (formatting, security, compliance)
- **Confidence Scoring:** Generates quantitative metrics for all outputs
- **Self-Correction Triggering:** Identifies issues and triggers re-generation

**MCP Operations:**
```
Workspace Management:
- mcp.create_workspace(name)
- mcp.write_files(workspace_id, files)
- mcp.cleanup_workspace(workspace_id)

Terraform Operations:
- mcp.terraform_init(workspace_id)
- mcp.terraform_validate(workspace_id)
- mcp.terraform_plan(workspace_id, dry_run=True)
- mcp.terraform_fmt(path, check=True)

Code Analysis:
- mcp.parse_terraform(path)
- mcp.get_variables(path)
- mcp.get_outputs(path)
- mcp.get_resources(path)

Quality Tools:
- mcp.run_tflint(path)
- mcp.run_terrascan(path)
- mcp.run_checkov(path)
- mcp.check_compliance(path, standards)
```

**Agentic Behaviors:**

1. **Example Validation Loop:**
```python
for each example:
    workspace = create_workspace()
    write_example_to_workspace()
    
    if not terraform_init(workspace).success:
        mark_example_as_failed()
        trigger_usage_agent_regeneration()
    
    if not terraform_validate(workspace).success:
        mark_example_as_failed()
        extract_error_messages()
        trigger_usage_agent_regeneration(errors)
    
    plan_result = terraform_plan(workspace, dry_run=True)
    analyze_plan_output()
    calculate_confidence_score()
    
    cleanup_workspace()
```

2. **Documentation Accuracy Check:**
```python
for each module:
    actual_code = parse_terraform(module_path)
    documented_elements = extract_from_docs(module)
    
    discrepancies = compare(actual_code, documented_elements)
    
    if accuracy_score < 95%:
        trigger_documentation_refinement()
```

3. **Critique Validation:**
```python
tflint_issues = run_tflint(repo)
terrascan_issues = run_terrascan(repo)
checkov_issues = run_checkov(repo)

for each critique_finding:
    if not confirmed_by_tools(finding, all_tools):
        mark_as_false_positive()

for each tool_issue:
    if not in_critique(tool_issue):
        mark_as_missed_issue()
        trigger_critic_agent_reanalysis()
```

**Inputs:**
- All outputs from Stages 1-4
- Original Terraform repository
- Evaluation configuration (thresholds, standards)

**Outputs:**
```json
{
  "evaluation_summary": {
    "timestamp": "2025-11-18T10:30:00Z",
    "overall_score": 94.2,
    "confidence": "high"
  },
  
  "example_validation": {
    "total_examples": 15,
    "passed": 14,
    "failed": 1,
    "pass_rate": 93.3,
    "details": [
      {
        "example": "vpc-basic-usage",
        "module": "networking/vpc",
        "init_success": true,
        "validate_success": true,
        "plan_success": true,
        "confidence_score": 100,
        "terraform_plan_summary": {
          "resources_to_create": 8,
          "resources_to_change": 0,
          "resources_to_destroy": 0
        }
      }
    ]
  },
  
  "documentation_accuracy": {
    "overall_accuracy": 98.5,
    "hallucinations": 1,
    "missing_items": 2,
    "modules_evaluated": 8
  },
  
  "critique_validation": {
    "critique_accuracy": 95.0,
    "false_positive_rate": 5.0,
    "coverage": 88.0,
    "confirmed_issues": 19,
    "false_positives": 1,
    "missed_issues": 3
  },
  
  "quality_metrics": {
    "formatting_score": 100,
    "security": {
      "critical": 0,
      "high": 2,
      "medium": 5,
      "low": 8
    },
    "compliance_score": 92,
    "best_practices_score": 89
  },
  
  "recommendations": [
    "Example 'advanced-vpc' failed validation - needs revision",
    "Documentation missing 2 outputs in 'compute' module",
    "Critique missed 3 security findings from terrascan"
  ],
  
  "self_correction_triggered": [
    {
      "agent": "Usage/Examples Agent",
      "reason": "Example validation failed",
      "action": "Regenerate advanced-vpc example"
    }
  ]
}
```

**Execution Pattern:** Sequential after Stage 4 (Aggregation)

**Self-Correction Loop:**
- If pass_rate < 90%: Trigger Usage Agent to regenerate failed examples
- If accuracy < 95%: Trigger Documentation refinement cycle
- If critique_accuracy < 90%: Trigger Best Practices Critic re-analysis
- Maximum 2 self-correction iterations

---

## Execution Workflow

### Stage 1: Discovery (WHAT)
**Duration:** ~30-60 seconds
**Parallelization:** Yes

```
Repository Analyzer Agent ─┐
                           ├──→ [Shared Context]
Cross-Reference Linker ────┘
```

**Process:**
1. Repository Analyzer scans all .tf files and parses using MCP
2. Cross-Reference Linker maps dependencies simultaneously
3. Both agents contribute to shared context object
4. Context includes: module inventory, file structure, dependency graph

**Output:** Shared Context (JSON object with complete repository metadata)

---

### Stage 2a: Initial Documentation (WHAT/WHY/HOW)
**Duration:** ~2-4 minutes
**Parallelization:** No (requires Stage 1)

```
[Shared Context] → Documentation Generator → [Initial Documentation]
```

**Process:**
1. Documentation Generator receives shared context
2. Creates comprehensive README.md for each module
3. Generates variable/output tables
4. Adds usage instructions and examples
5. Produces initial draft documentation

**Output:** Initial Documentation (Markdown files per module)

---

### Stage 2b: Documentation Refinement (WHAT/WHY)
**Duration:** ~1-3 minutes per iteration
**Parallelization:** Yes
**Iterations:** 1-3 (until quality score ≥ 8/10)

```
[Initial Documentation]
         ↓
    ┌────┴────┬─────────┐
    ↓         ↓         ↓
Technical  Completeness  Clarity
Accuracy   Checker      Enhancer
Reviewer   
    └────┬────┴─────────┘
         ↓
   [Quality Check]
         ↓
    Score ≥ 8? ──Yes──→ [Polished Documentation]
         ↓
        No
         ↓
    [Iteration 2]
```

**Process:**
1. Three agents work in parallel on initial docs
2. Technical Accuracy Reviewer validates facts using MCP
3. Completeness Checker ensures nothing is missing
4. Clarity Enhancer improves readability
5. Each agent assigns quality score (1-10)
6. If average score < 8, run another refinement iteration
7. Maximum 3 iterations

**Quality Scoring:**
- Technical Accuracy: % of verified facts
- Completeness: % of documented elements
- Clarity: Readability score (Flesch-Kincaid)
- Combined Score: Weighted average

**Output:** Polished Documentation (High-quality, verified docs)

---

### Stage 3: Guidance (HOW)
**Duration:** ~3-5 minutes
**Parallelization:** Yes

```
[Polished Documentation + Original Code + Context]
                    ↓
         ┌──────────┴──────────┐
         ↓                     ↓
  Usage/Examples           Best Practices
      Agent                   Critic
         └──────────┬──────────┘
                    ↓
          [Examples + Critique]
```

**Process:**
1. Both agents work in parallel
2. Usage/Examples Agent:
   - Generates practical examples
   - Creates test workspace via MCP
   - Validates each example (init/validate/plan)
   - Only includes working examples
3. Best Practices Critic:
   - Runs tflint, terrascan, checkov via MCP
   - Identifies security issues
   - Flags anti-patterns
   - Generates recommendations

**Output:** 
- Usage Examples (Validated, working code)
- Critique Report (Prioritized findings with remediation)

---

### Stage 4: Aggregation
**Duration:** ~30 seconds
**Parallelization:** No (requires Stage 3)

```
[All Previous Outputs] → Orchestrator → [Unified Report + Session Context]
```

**Process:**
1. Orchestrator compiles all agent outputs
2. Creates unified final report structure
3. Prioritizes action items
4. Initializes interactive session context
5. Prepares Q&A capabilities

**Output:** Complete Package (Ready for evaluation)

---

### Stage 5: Agentic Evaluation ⭐
**Duration:** ~5-10 minutes
**Parallelization:** Internal (multiple workspaces tested concurrently)

```
[Complete Package + Original Repo] → Evaluation Agent + MCP Server
                    ↓
         ┌──────────┼──────────┐
         ↓          ↓          ↓
    Example     Documentation  Critique
   Validation    Accuracy     Validation
                    ↓
         [Evaluation Report]
                    ↓
          Self-Correction
           Triggered?
         ↓             ↓
        Yes            No
         ↓             ↓
   [Re-run Stage]  [Final Session]
```

**Process:**

1. **Example Validation (Parallel):**
   - For each example (can run multiple in parallel):
     - Create isolated test workspace via MCP
     - Write example files to workspace
     - Run terraform init
     - Run terraform validate
     - Run terraform plan (dry-run)
     - Analyze plan output
     - Calculate confidence score
     - Cleanup workspace
   - Aggregate results

2. **Documentation Accuracy:**
   - For each module:
     - Parse actual code via MCP
     - Extract documented elements
     - Compare and identify discrepancies
     - Calculate accuracy score
   - Identify hallucinations and missing items

3. **Critique Validation:**
   - Run tflint via MCP
   - Run terrascan via MCP
   - Run checkov via MCP
   - Compare critique findings with tool outputs
   - Identify false positives
   - Identify missed issues
   - Calculate critique accuracy

4. **Quality Assessment:**
   - Run terraform fmt check
   - Run security scans
   - Check compliance standards
   - Assess code structure
   - Generate comprehensive quality metrics

5. **Self-Correction Decision:**
   - If example pass_rate < 90%: Trigger Usage Agent
   - If doc accuracy < 95%: Trigger Stage 2b refinement
   - If critique accuracy < 90%: Trigger Best Practices Critic
   - Maximum 2 self-correction iterations

**Output:** 
- Evaluation Report (Comprehensive metrics)
- Self-correction actions (if needed)
- Final interactive session (if no corrections needed)

---

### Stage 6: Interactive Session
**Duration:** User-driven
**Parallelization:** N/A

```
[Final Report + Session Context] ←→ User Queries
                ↓
        Orchestrator Agent
                ↓
        Context-aware Responses
```

**Process:**
1. User can ask follow-up questions
2. Orchestrator maintains full context
3. Can drill down into specific modules
4. Can request re-analysis of specific areas
5. Can generate additional examples on demand

**Capabilities:**
- "Tell me more about the security issues in the networking module"
- "Show me an advanced usage example for the database module"
- "What's the priority order for fixing these issues?"
- "Regenerate documentation for the compute module with more detail"

---

## Context Management Strategy

### Context Objects

#### Shared Context (Stage 1 Output)
```json
{
  "repository": {
    "path": "/path/to/repo",
    "total_files": 45,
    "total_modules": 8
  },
  "modules": [
    {
      "name": "vpc",
      "path": "modules/networking/vpc",
      "resources": ["aws_vpc", "aws_subnet", "aws_route_table"],
      "variables": [
        {
          "name": "vpc_cidr",
          "type": "string",
          "default": "10.0.0.0/16",
          "required": false
        }
      ],
      "outputs": [
        {
          "name": "vpc_id",
          "description": "The ID of the VPC"
        }
      ],
      "dependencies": ["data.aws_availability_zones.available"]
    }
  ],
  "dependency_graph": {
    "nodes": ["vpc", "compute", "database"],
    "edges": [...]
  },
  "file_structure": {...}
}
```

#### Documentation Context (Stage 2 Output)
```json
{
  "modules": [
    {
      "name": "vpc",
      "documentation": "# VPC Module\n\n## Overview\n...",
      "quality_scores": {
        "accuracy": 98,
        "completeness": 100,
        "clarity": 8.5
      },
      "refinement_iterations": 2
    }
  ]
}
```

#### Guidance Context (Stage 3 Output)
```json
{
  "examples": [...],
  "critique": {
    "findings": [...],
    "summary": {...}
  }
}
```

#### Evaluation Context (Stage 5 Output)
```json
{
  "evaluation_results": {...},
  "self_corrections": [...]
}
```

### Context Passing Rules

1. **Stage 1 → Stage 2:** Shared Context (read-only)
2. **Stage 2 → Stage 3:** Shared Context + Documentation Context
3. **Stage 3 → Stage 4:** All previous contexts
4. **Stage 4 → Stage 5:** Complete package + original repo
5. **Stage 5 → Stage 6:** All contexts + evaluation results

### Session State Management

- **Persistence:** All context maintained throughout session
- **Updates:** Context can be updated during interactive session
- **Versioning:** Each agent operation creates a new context version
- **Rollback:** Can revert to previous context versions if needed

---

## Performance Characteristics

### Expected Execution Times

| Stage | Duration | Factors |
|-------|----------|---------|
| Stage 1: Discovery | 30-60s | Repository size, file count |
| Stage 2a: Initial Docs | 2-4 min | Number of modules, complexity |
| Stage 2b: Refinement | 1-3 min/iteration | Quality threshold, initial quality |
| Stage 3: Guidance | 3-5 min | Number of examples, linting complexity |
| Stage 4: Aggregation | 30s | Amount of content to compile |
| Stage 5: Evaluation | 5-10 min | Number of examples, validation depth |
| **Total (No Iterations)** | **12-23 min** | Typical small-medium repo |
| **Total (With Iterations)** | **15-30 min** | Including refinement loops |

### Scalability

- **Small Repo (1-3 modules):** ~10-15 minutes
- **Medium Repo (4-10 modules):** ~15-25 minutes
- **Large Repo (11-20 modules):** ~25-40 minutes
- **Enterprise Repo (20+ modules):** ~40-60 minutes

### Parallelization Benefits

- **Stage 1:** 2x speedup (parallel execution)
- **Stage 2b:** 3x speedup (parallel refinement)
- **Stage 3:** 2x speedup (parallel execution)
- **Stage 5:** Variable (parallel workspace testing)

**Overall Speedup:** ~40-50% faster than sequential execution

---

## Success Metrics

### Automated Metrics (Primary)

| Metric | Target | Excellent |
|--------|--------|-----------|
| Example Validation Pass Rate | >90% | >95% |
| Documentation Completeness | >90% | >98% |
| Code-Doc Alignment Accuracy | >95% | >99% |
| Critique Accuracy | >85% | >90% |
| Best Practices Score | >80% | >90% |
| Overall System Score | >85% | >92% |

### Human Evaluation Metrics (Secondary)

| Metric | Target | Scale |
|--------|--------|-------|
| Documentation Clarity | >3.5/5 | 1-5 |
| Example Usefulness | >4.0/5 | 1-5 |
| Critique Actionability | >3.5/5 | 1-5 |
| Overall Satisfaction | >4.0/5 | 1-5 |

### Business Value Metrics

- **Time Saved:** Documentation time reduced by 80%
- **Error Reduction:** 90% fewer undocumented variables
- **Security Improvement:** Critical issues identified before deployment
- **Onboarding Speed:** New team members productive 3x faster

---

## Self-Correction & Quality Loops

### Refinement Loop (Stage 2b)

```
Initial Documentation
         ↓
  Parallel Refinement
         ↓
   Quality Scoring
         ↓
    Score < 8? ──No──→ Proceed
         ↓
        Yes
         ↓
   Iteration + 1
         ↓
  Max Iterations? ──Yes──→ Proceed with best version
         ↓
        No
         ↓
  [Repeat Refinement]
```

**Termination Conditions:**
- Quality score ≥ 8/10
- Maximum 3 iterations reached
- Improvement < 5% between iterations

### Self-Correction Loop (Stage 5)

```
Evaluation Results
         ↓
  Analyze Metrics
         ↓
  Failures Detected? ──No──→ Complete
         ↓
        Yes
         ↓
   Identify Root Cause
         ↓
  Trigger Agent Re-run
         ↓
   Iteration + 1
         ↓
  Max Iterations? ──Yes──→ Complete with warnings
         ↓
        No
         ↓
  [Re-run Failed Stage]
```

**Correction Triggers:**
- Example pass rate < 90% → Re-run Usage/Examples Agent
- Documentation accuracy < 95% → Re-run Stage 2b Refinement
- Critique accuracy < 90% → Re-run Best Practices Critic

**Termination Conditions:**
- All metrics above thresholds
- Maximum 2 correction iterations
- Improvement plateaus (<5% gain)

---

## Error Handling

### Agent-Level Error Handling

Each agent implements:
- **Graceful Degradation:** Continue with reduced functionality if non-critical errors
- **Retry Logic:** 3 attempts for transient failures (API calls, MCP operations)
- **Error Reporting:** Detailed error context for debugging
- **Fallback Strategies:** Alternative approaches if primary method fails

### System-Level Error Handling

- **Stage Failure:** If stage fails completely, save partial results and continue
- **MCP Connection Loss:** Queue operations and retry when connection restored
- **Resource Exhaustion:** Implement workspace limits and cleanup
- **Timeout Protection:** Maximum execution time per agent (10 minutes)

### Error Categories

1. **Recoverable Errors:** Retry automatically
   - Temporary MCP server unavailability
   - Network timeouts
   - Rate limiting

2. **Degraded Mode Errors:** Continue with limitations
   - Some linting tools unavailable
   - Partial repository access
   - Missing optional metadata

3. **Fatal Errors:** Halt and report
   - Invalid Terraform syntax in original repo
   - MCP server permanently unavailable
   - Insufficient permissions

---

## Security Considerations

### MCP Security

- **Workspace Isolation:** Each test runs in isolated workspace
- **Credential Management:** Never expose API keys or secrets
- **Dry-Run Only:** Evaluation uses `terraform plan` only, no `apply`
- **Workspace Cleanup:** Always cleanup workspaces after testing

### Data Privacy

- **No External Storage:** All processing ephemeral
- **Context Encryption:** Sensitive data encrypted in transit
- **Audit Logging:** All MCP operations logged
- **Access Control:** Respect repository permissions

### Code Safety

- **Static Analysis Only:** No code execution outside MCP sandbox
- **Malicious Code Detection:** Scan for suspicious patterns
- **Resource Limits:** Prevent resource exhaustion attacks

---

## Future Enhancements

### Phase 2 Features

1. **Multi-Provider Support:** AWS, Azure, GCP
2. **Custom Policy Engine:** Org-specific compliance rules
3. **Diff Analysis:** Compare changes between versions
4. **Cost Estimation:** Integrate with Infracost
5. **Dependency Updates:** Suggest module version updates

### Advanced Agent Capabilities

1. **Auto-Remediation Agent:** Automatically fix simple issues
2. **Architecture Recommender Agent:** Suggest better designs
3. **Test Generator Agent:** Create Terratest/Kitchen-Terraform tests
4. **Migration Assistant Agent:** Help migrate to Terraform Cloud

### Integration Options

1. **CI/CD Integration:** GitHub Actions, GitLab CI
2. **PR Comments:** Automated code review comments
3. **Slack/Teams Notifications:** Alert on critical findings
4. **Jira Integration:** Create tickets for issues

---

## Appendix A: MCP Operations Reference

### Workspace Management
```
create_workspace(name, config) → workspace_id
write_files(workspace_id, files) → success
cleanup_workspace(workspace_id) → success
```

### Terraform Operations
```
terraform_init(workspace_id, upgrade=False) → result
terraform_validate(workspace_id) → result
terraform_plan(workspace_id, dry_run=True) → result
terraform_fmt(path, check=True) → result
terraform_show(workspace_id, plan_file) → result
```

### Code Analysis
```
parse_terraform(path) → AST
get_module_structure(path) → metadata
get_variables(path) → list[Variable]
get_outputs(path) → list[Output]
get_resources(path) → list[Resource]
get_dependencies(path) → graph
```

### Quality Tools
```
run_tflint(path, config) → findings
run_terrascan(path, policy_type) → findings
run_checkov(path, framework) → findings
run_security_scan(path) → findings
check_compliance(path, standards) → results
```

---

## Appendix B: Agent Communication Protocol

### Message Format
```json
{
  "from_agent": "repository_analyzer",
  "to_agent": "documentation_generator",
  "message_type": "context_sharing",
  "payload": {...},
  "timestamp": "2025-11-18T10:30:00Z",
  "correlation_id": "uuid"
}
```

### Message Types
- `context_sharing`: Share context between agents
- `request_refinement`: Request another agent to refine output
- `validation_result`: Share validation results
- `error_report`: Report errors or issues
- `quality_score`: Share quality metrics

---

## Conclusion

The Terraform Code Reviewer represents a sophisticated multi-agent system that combines autonomous decision-making, parallel execution, iterative refinement, and real-world validation through MCP integration. The system demonstrates:

- **Advanced orchestration** using Google ADK
- **True agentic behavior** with autonomous testing and self-correction
- **Production-quality validation** using industry-standard tools
- **Comprehensive evaluation** with quantitative metrics
- **Scalable architecture** supporting repos of any size

This design showcases the full capabilities of modern agentic AI systems while solving a real-world DevOps challenge: making Terraform codebases more maintainable, secure, and accessible to teams.
