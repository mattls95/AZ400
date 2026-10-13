# AZ-400 — Microsoft DevOps Engineer: Course Notes & Exam Tracker

> A working Markdown notebook for Microsoft Learn AZ-400 modules and exam revision. Module objectives are **paraphrased** for note-taking; use linked official pages as the source of truth.

**Template checked:** 2026-10-13  
**Current exam skills:** July 27, 2026 (English exam).  
**Scope:** 8 Microsoft Learn learning paths · 47 modules · 5 exam domains.

[Official certification](https://learn.microsoft.com/en-us/credentials/certifications/devops-engineer/) · [AZ-400 exam page](https://learn.microsoft.com/en-us/credentials/certifications/exams/az-400/) · [Current study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/az-400) · [AZ-400T00 course](https://learn.microsoft.com/en-us/training/courses/az-400t00)

## How to use this notebook

1. Follow the eight learning paths in order or jump to a weaker topic. Open the Microsoft Learn link for each module.
2. Mark the module objectives as complete only when you can **explain and demonstrate** them without notes.
3. Capture concise decision rules, examples, YAML/CLI/KQL snippets, exam traps, and lab outcomes under the relevant module.
4. Revisit the independent **exam objective tracker** near the end: a training module is not the same as an exam objective.
5. Rate confidence **0–3**: 0 = unknown; 1 = recognize; 2 = can explain; 3 = can implement/troubleshoot.
6. Record an artifact (PR, screenshot, pipeline run, Bicep file, or KQL query) as evidence for hands-on knowledge.

> Suggested hands-on context: a small Python API with GitHub/Azure DevOps CI/CD, a container build, Bicep infrastructure, test gates, and Application Insights. Use synthetic data only.

## Study dashboard

| Exam domain | Weight | Confidence (0–3) | Notes / weak points |
|---|---:|:---:|---|
| Design and implement processes and communications | 10–15% | — | — |
| Design and implement a source control strategy | 10–15% | — | — |
| Design and implement build and release pipelines | 50–55% | — | — |
| Develop a security and compliance plan | 10–15% | — | — |
| Implement an instrumentation strategy | 5–10% | — | — |

**Course progress:** 0 / 47 modules complete  
**Next revision date:** YYYY-MM-DD  
**Exam date:** YYYY-MM-DD / not scheduled

## Learning paths (Microsoft Learn)

1. [Development for Enterprise DevOps](https://learn.microsoft.com/en-us/training/paths/az-400-work-git-for-enterprise-devops/) — 8 modules
2. [Implement CI with Azure Pipelines and GitHub Actions](https://learn.microsoft.com/en-us/training/paths/az-400-implement-ci-azure-pipelines-github-actions/) — 8 modules
3. [Design and implement a release strategy](https://learn.microsoft.com/en-us/training/paths/az-400-design-implement-release-strategy/) — 5 modules
4. [Implement a secure continuous deployment using Azure Pipelines](https://learn.microsoft.com/en-us/training/paths/az-400-implement-secure-continuous-deployment/) — 6 modules
5. [Manage infrastructure as code using Azure and DSC](https://learn.microsoft.com/en-us/training/paths/az-400-manage-infrastructure-as-code-using-azure/) — 6 modules
6. [Implement security and validate code bases for compliance](https://learn.microsoft.com/en-us/training/paths/az-400-implement-security-validate-code-bases-compliance/) — 4 modules
7. [Design and implement a dependency management strategy](https://learn.microsoft.com/en-us/training/paths/az-400-design-implement-dependency-management-strategy/) — 5 modules
8. [Implement continuous feedback](https://learn.microsoft.com/en-us/training/paths/az-400-implement-continuous-feedback/) — 5 modules

---

# Part A — Course-by-course notes

## 1. Development for Enterprise DevOps

**Official path:** [Development for Enterprise DevOps](https://learn.microsoft.com/en-us/training/paths/az-400-work-git-for-enterprise-devops/)  
**Path status:** Not started / In progress / Completed  
**Path review:** YYYY-MM-DD

### 1.1 Introduction to DevOps

**Module:** [Introduction to DevOps](https://learn.microsoft.com/en-us/training/modules/introduction-to-devops/)  
**Exam connection:** Processes & communications / source control  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Explain DevOps principles and how they improve delivery
- [ ] Plan organizational change and agile team practices
- [ ] Distinguish Azure DevOps from GitHub capabilities
- [ ] Review Git fundamentals, licensing, and cycle-time feedback

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Draw a simple issue → branch → PR → CI → deploy → feedback workflow for a synthetic application.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 1.2 Plan Agile with GitHub Projects and Azure Boards

**Module:** [Plan Agile with GitHub Projects and Azure Boards](https://learn.microsoft.com/en-us/training/modules/plan-agile-github-projects-azure-boards/)  
**Exam connection:** Processes & communications  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Compare GitHub Projects with Azure Boards
- [ ] Connect Azure Boards work items to GitHub repositories
- [ ] Configure a GitHub Project board and alternative views
- [ ] Create end-to-end source-to-work-item traceability

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Create an issue, connect it to a GitHub PR, and document the traceability evidence.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 1.3 Design and implement branch strategies and workflows

**Module:** [Design and implement branch strategies and workflows](https://learn.microsoft.com/en-us/training/modules/manage-git-branches-workflows/)  
**Exam connection:** Source control  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Compare common branch models and feature branch workflows
- [ ] Use GitHub Flow for short-lived changes
- [ ] Select branching versus forking for collaboration
- [ ] Enforce merging restrictions and reviews

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Run the same toy change through feature-branch + PR and a release-branch workflow; compare overhead.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 1.4 Collaborate with pull requests in Azure Repos

**Module:** [Collaborate with pull requests in Azure Repos](https://learn.microsoft.com/en-us/training/modules/collaborate-pull-requests-azure-repos/)  
**Exam connection:** Source control / processes  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Design a consistent PR review workflow
- [ ] Configure Azure Repos branch policies and required checks
- [ ] Coordinate cross-team reviewers and feedback
- [ ] Explain how PR governance supports audits

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Configure one Azure Repos policy requiring reviewers and a passing build; test a blocked merge.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 1.5 Explore Git hooks

**Module:** [Explore Git hooks](https://learn.microsoft.com/en-us/training/modules/explore-git-hooks/)  
**Exam connection:** Source control (supplementary)  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Create local Git hooks for quality checks
- [ ] Trigger automated checks around commits
- [ ] Prevent accidental secret commits where feasible
- [ ] Share hooks with a team and understand their limitations

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Write a small pre-commit script in WSL that blocks an intentionally invalid commit; note bypass limitations.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 1.6 Plan to foster inner source

**Module:** [Plan to foster inner source](https://learn.microsoft.com/en-us/training/modules/plan-fostering-inner-source/)  
**Exam connection:** Source control / processes  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Describe inner-source contribution models
- [ ] Use fork-based collaboration
- [ ] Choose forks versus direct branches
- [ ] Manage code sharing across forks

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Fork a practice repo, propose a change through a pull request, and compare permissions with branch access.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 1.7 Manage and configure repositories

**Module:** [Manage and configure repositories](https://learn.microsoft.com/en-us/training/modules/manage-git-repositories/)  
**Exam connection:** Source control / processes  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Choose strategies for large repositories and files (Git LFS, Scalar)
- [ ] Compare monorepos and multi-repo structures
- [ ] Recover or remove repository content with Git
- [ ] Manage tags, changelogs, release notes, and repository permissions

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Create an annotated tag, generate a short changelog, and recover a file from a previous commit.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 1.8 Identify technical debt

**Module:** [Identify technical debt](https://learn.microsoft.com/en-us/training/modules/identify-technical-debt/)  
**Exam connection:** Processes & communications (supporting)  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Identify technical debt and quantify its impact
- [ ] Interpret complexity and code quality signals
- [ ] Plan code review standards and remediation
- [ ] Select static analysis and quality tooling

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Run a Python linter/coverage tool against a tiny sample and record one justified remediation.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

## 2. Implement CI with Azure Pipelines and GitHub Actions

**Official path:** [Implement CI with Azure Pipelines and GitHub Actions](https://learn.microsoft.com/en-us/training/paths/az-400-implement-ci-azure-pipelines-github-actions/)  
**Path status:** Not started / In progress / Completed  
**Path review:** YYYY-MM-DD

### 2.1 Explore Azure Pipelines

**Module:** [Explore Azure Pipelines](https://learn.microsoft.com/en-us/training/modules/explore-azure-pipelines/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Describe pipeline concepts and terminology
- [ ] Identify stages, jobs, steps, tasks, and artifacts
- [ ] Choose when pipeline automation fits the project
- [ ] Explain CI and deployment pipeline responsibilities

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Create a minimal YAML build that checks out Python code and runs pytest.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 2.2 Manage Azure Pipeline agents and pools

**Module:** [Manage Azure Pipeline agents and pools](https://learn.microsoft.com/en-us/training/modules/manage-azure-pipeline-agents-pools/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Choose hosted versus self-hosted agents
- [ ] Configure an agent and an agent pool
- [ ] Secure agent credentials, networking, and permissions
- [ ] Diagnose connectivity and deployment access

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Compare a Microsoft-hosted build with a self-hosted agent plan: network, costs, isolation, patching.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 2.3 Describe pipelines and concurrency

**Module:** [Describe pipelines and concurrency](https://learn.microsoft.com/en-us/training/modules/describe-pipelines-concurrency/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Estimate and configure parallel jobs
- [ ] Distinguish public/open-source versus private project options
- [ ] Contrast classic designer with YAML pipelines
- [ ] Recognize throughput and concurrency constraints

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Create two independent jobs, inspect their scheduling, and explain how parallel-job limits affect duration.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 2.4 Design and implement a pipeline strategy

**Module:** [Design and implement a pipeline strategy](https://learn.microsoft.com/en-us/training/modules/implement-pipeline-strategy/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Select a build strategy and agent demands
- [ ] Design matrix/multi-configuration builds
- [ ] Integrate GitHub code with Azure Pipelines
- [ ] Configure unit/integration tests and code coverage
- [ ] Choose between available source control integrations

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Build a test matrix for two Python versions and publish a test report with a coverage gate.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 2.5 Integrate with Azure Pipelines

**Module:** [Integrate with Azure Pipelines](https://learn.microsoft.com/en-us/training/modules/integrate-azure-pipelines/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Explain detailed YAML pipeline structure
- [ ] Extract reusable templates and variables
- [ ] Model YAML resources and dependencies
- [ ] Use multiple repositories and plan classic-to-YAML migration

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Refactor one pipeline into a reusable YAML template and consume it from a second pipeline.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 2.6 Introduction to GitHub Actions

**Module:** [Introduction to GitHub Actions](https://learn.microsoft.com/en-us/training/modules/introduction-to-github-actions/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Describe Actions events, workflows, jobs, steps, and runners
- [ ] Author a basic workflow in YAML
- [ ] Inspect workflow logs and failed steps
- [ ] Manage action versions and release behavior

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Create a push/PR-triggered workflow with lint and pytest jobs.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 2.7 Learn continuous integration with GitHub Actions

**Module:** [Learn continuous integration with GitHub Actions](https://learn.microsoft.com/en-us/training/modules/learn-continuous-integration-github-actions/)  
**Exam connection:** Build & release pipelines / security  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Build CI workflows using GitHub Actions
- [ ] Use variables and encrypted secrets appropriately
- [ ] Transfer artifacts between jobs and apply release tags
- [ ] Apply safe, reusable Actions practices

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Make a workflow upload a test artifact and mark a release with a Git tag.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 2.8 Design a container build strategy

**Module:** [Design a container build strategy](https://learn.microsoft.com/en-us/training/modules/design-container-build-strategy/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Select container build and packaging strategies
- [ ] Write and troubleshoot Dockerfiles
- [ ] Use multi-stage builds to reduce runtime images
- [ ] Choose an appropriate Azure container hosting target

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Build a multistage container for a small Python API and run a smoke test locally.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

## 3. Design and implement a release strategy

**Official path:** [Design and implement a release strategy](https://learn.microsoft.com/en-us/training/paths/az-400-design-implement-release-strategy/)  
**Path status:** Not started / In progress / Completed  
**Path review:** YYYY-MM-DD

### 3.1 Create a Release Pipeline

**Module:** [Create a Release Pipeline](https://learn.microsoft.com/en-us/training/modules/create-release-pipeline-devops/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Describe release pipeline concepts and artifact sources
- [ ] Select build and deployment tasks
- [ ] Configure stages and deployment jobs
- [ ] Include a database deployment step when relevant

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Model dev → test → prod deployment stages with an artifact promoted between environments.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 3.2 Explore release strategy recommendations

**Module:** [Explore release strategy recommendations](https://learn.microsoft.com/en-us/training/modules/explore-release-strategy-recommendations/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Define appropriate delivery cadence and triggers
- [ ] Select artifact sources and promotion rules
- [ ] Configure approvals and release gates
- [ ] Explain quality gates and GitOps-style releases

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Add a pre-production approval/check and test both passing and blocked deployments.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 3.3 Configure and Provision Environments

**Module:** [Configure and Provision Environments](https://learn.microsoft.com/en-us/training/modules/configure-provision-environments/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Provision and describe deployment target environments
- [ ] Configure secure service connections
- [ ] Implement automated integration and functional checks
- [ ] Apply availability and load testing when useful

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Create a disposable test environment and a deployment smoke-test checklist.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 3.4 Manage and Modularize Tasks and Templates

**Module:** [Manage and Modularize Tasks and Templates](https://learn.microsoft.com/en-us/training/modules/manage-modularize-tasks-templates/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Identify opportunities for reusable task groups
- [ ] Manage variable groups and stage variables
- [ ] Separate shared and environment-specific configuration
- [ ] Recognize when a custom task is justified

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Use a variable group or pipeline template for dev/test settings without duplicating YAML.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 3.5 Automate inspection of health

**Module:** [Automate inspection of health](https://learn.microsoft.com/en-us/training/modules/automate-inspection-health/)  
**Exam connection:** Build & release / instrumentation  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Automate service and release health inspections
- [ ] Route events to notifications and webhooks
- [ ] Configure Azure DevOps and GitHub status alerts
- [ ] Measure the quality of release workflows

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Create a failed-build notification and document the event, recipient, and response action.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

## 4. Implement a secure continuous deployment using Azure Pipelines

**Official path:** [Implement a secure continuous deployment using Azure Pipelines](https://learn.microsoft.com/en-us/training/paths/az-400-implement-secure-continuous-deployment/)  
**Path status:** Not started / In progress / Completed  
**Path review:** YYYY-MM-DD

### 4.1 Introduction to deployment patterns

**Module:** [Introduction to deployment patterns](https://learn.microsoft.com/en-us/training/modules/introduction-to-deployment-patterns/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Distinguish traditional from progressive delivery
- [ ] Explain how microservices affect deployments
- [ ] Select rollout and rollback patterns
- [ ] Assess release risk, cost, and availability

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Draw diagrams comparing in-place, rolling, and blue-green deployment for a small API.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 4.2 Implement blue-green deployment and feature toggles

**Module:** [Implement blue-green deployment and feature toggles](https://learn.microsoft.com/en-us/training/modules/implement-blue-green-deployment-feature-toggles/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Design blue-green deployments and rollback
- [ ] Explain staging/production slots and swapping
- [ ] Use feature toggles independently of code deployment
- [ ] Plan feature-flag lifecycle management

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Describe an App Service slot swap and show a feature toggle that leaves a feature disabled.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 4.3 Implement canary releases and dark launching

**Module:** [Implement canary releases and dark launching](https://learn.microsoft.com/en-us/training/modules/implement-canary-releases-dark-launching/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Plan canary stages and health thresholds
- [ ] Use traffic distribution for incremental exposure
- [ ] Explain Azure Traffic Manager routing choices
- [ ] Contrast canary and dark launch

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Write a 5% → 25% → 100% rollout plan with failure thresholds and rollback triggers.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 4.4 Implement A/B testing and progressive exposure deployment

**Module:** [Implement A/B testing and progressive exposure deployment](https://learn.microsoft.com/en-us/training/modules/implement-test-progressive-exposure-deployment/)  
**Exam connection:** Build & release pipelines  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Define user cohorts and progressive exposure
- [ ] Differentiate A/B experiments from technical canaries
- [ ] Design ring-based deployments
- [ ] Select rollout strategies from business constraints

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Design an A/B rollout and record success metrics separately from reliability metrics.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 4.5 Integrate with identity management systems

**Module:** [Integrate with identity management systems](https://learn.microsoft.com/en-us/training/modules/integrate-identity-management-systems/)  
**Exam connection:** Security & compliance  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Connect enterprise identity services to DevOps platforms
- [ ] Describe GitHub SSO/MFA patterns
- [ ] Choose service principals versus managed identities
- [ ] Configure roles and permissions for workloads and users

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Draft a least-privilege identity plan for a pipeline that deploys to a resource group.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 4.6 Manage application configuration data

**Module:** [Manage application configuration data](https://learn.microsoft.com/en-us/training/modules/manage-application-configuration-data/)  
**Exam connection:** Security & compliance / deployments  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Externalize application configuration safely
- [ ] Manage App Configuration values and labels
- [ ] Link Key Vault to pipelines and apps
- [ ] Configure secure files and feature flags
- [ ] Separate secrets, configuration, and deployment artifacts

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Compare a normal config value, a Key Vault secret, and a feature flag in one deployment design.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

## 5. Manage infrastructure as code using Azure and DSC

**Official path:** [Manage infrastructure as code using Azure and DSC](https://learn.microsoft.com/en-us/training/paths/az-400-manage-infrastructure-as-code-using-azure/)  
**Path status:** Not started / In progress / Completed  
**Path review:** YYYY-MM-DD

### 5.1 Explore Infrastructure as Code and configuration management

**Module:** [Explore Infrastructure as Code and configuration management](https://learn.microsoft.com/en-us/training/modules/explore-infrastructure-code-configuration-management/)  
**Exam connection:** Build & release pipelines — IaC  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Explain environment provisioning and configuration management
- [ ] Distinguish declarative and imperative approaches
- [ ] Use version control for infrastructure configuration
- [ ] Explain idempotency and drift

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Write an idempotency test plan for creating a resource group twice.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 5.2 Create Azure Resources by using Azure Resource Manager templates

**Module:** [Create Azure Resources by using Azure Resource Manager templates](https://learn.microsoft.com/en-us/training/modules/create-azure-resources-using-azure-resource-manager-templates/)  
**Exam connection:** Build & release pipelines — IaC  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Read ARM/Bicep declarations, parameters, and outputs
- [ ] Manage dependencies and reusable modules
- [ ] Choose deployment modes with care
- [ ] Handle sensitive inputs using Key Vault

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Deploy a resource group plus storage resource with parameters, then run a what-if check.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 5.3 Create Azure Resources Using Azure CLI

**Module:** [Create Azure Resources Using Azure CLI](https://learn.microsoft.com/en-us/training/modules/create-azure-resources-by-using-azure-cli/)  
**Exam connection:** Build & release pipelines — IaC  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Use CLI authentication and command syntax
- [ ] Create and query Azure resources interactively
- [ ] Write Bash scripts around az commands
- [ ] Troubleshoot CLI commands and error output

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Use an idempotent Bash script with az group create and a resource lookup.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 5.4 Explore Azure Automation with DevOps

**Module:** [Explore Azure Automation with DevOps](https://learn.microsoft.com/en-us/training/modules/explore-azure-automation-devops/)  
**Exam connection:** Build & release pipelines — IaC  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Configure Azure Automation accounts
- [ ] Explain and execute runbooks
- [ ] Use webhooks for automation triggers
- [ ] Connect runbooks to source control
- [ ] Describe hybrid worker and workflow scenarios

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Sketch a webhook-triggered runbook for an environment cleanup job.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 5.5 Implement Desired State Configuration (DSC)

**Module:** [Implement Desired State Configuration (DSC)](https://learn.microsoft.com/en-us/training/modules/implement-desired-state-configuration-dsc/)  
**Exam connection:** Build & release pipelines — IaC  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Explain and detect configuration drift
- [ ] Describe PowerShell DSC resources and configurations
- [ ] Identify Azure Automation State Configuration behavior
- [ ] Discuss Linux and hybrid configuration management

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Write a declarative configuration policy for a VM and explain how drift is detected.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 5.6 Implement Bicep

**Module:** [Implement Bicep](https://learn.microsoft.com/en-us/training/modules/implement-bicep/)  
**Exam connection:** Build & release pipelines — IaC  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Author Bicep parameters, variables, resources, and modules
- [ ] Use Bicep tooling and Azure CLI
- [ ] Deploy infrastructure from Azure Pipelines
- [ ] Deploy Bicep with GitHub Actions
- [ ] Explain the Bicep/ARM relationship

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Create a parameterized Bicep module and validate/deploy it from a CI/CD workflow.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

## 6. Implement security and validate code bases for compliance

**Official path:** [Implement security and validate code bases for compliance](https://learn.microsoft.com/en-us/training/paths/az-400-implement-security-validate-code-bases-compliance/)  
**Path status:** Not started / In progress / Completed  
**Path review:** YYYY-MM-DD

### 6.1 Introduction to Secure DevOps

**Module:** [Introduction to Secure DevOps](https://learn.microsoft.com/en-us/training/modules/introduction-to-secure-devops/)  
**Exam connection:** Security & compliance  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Identify common application threats (including injection)
- [ ] Embed DevSecOps checks into delivery stages
- [ ] Use threat models to prioritize threats
- [ ] Explain GitHub CodeQL security analysis

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Add a security scan to a toy PR workflow and document when it should block merging.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 6.2 Implement open-source software

**Module:** [Implement open-source software](https://learn.microsoft.com/en-us/training/modules/implement-open-source-software-azure/)  
**Exam connection:** Security & compliance  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Explain dependency supply-chain risks
- [ ] Compare MIT, Apache, GPL, and other license obligations
- [ ] Assess license compatibility concerns
- [ ] Plan vulnerability and license inventories

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Create a small software bill of materials / dependency license review checklist.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 6.3 Software Composition Analysis

**Module:** [Software Composition Analysis](https://learn.microsoft.com/en-us/training/modules/software-composition-analysis/)  
**Exam connection:** Security & compliance  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Explain software composition analysis (SCA)
- [ ] Configure Dependabot or equivalent vulnerability alerts
- [ ] Integrate dependency/license checks in CI
- [ ] Scan container images and triage findings

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Use a harmless vulnerable sample dependency and document alert, severity, remediation, and CI gate.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 6.4 Security monitoring and governance

**Module:** [Security monitoring and governance](https://learn.microsoft.com/en-us/training/modules/security-monitoring-and-governance/)  
**Exam connection:** Security & compliance  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Harden CI/CD permissions and pipelines
- [ ] Explain Defender for Cloud DevOps protections
- [ ] Apply Azure Policy, initiatives, and locks
- [ ] Configure code/secret/dependency scanning
- [ ] Understand links between Defender for Cloud and GitHub Advanced Security

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Design a PR protection policy and automated checks for source, dependencies, and secrets.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

## 7. Design and implement a dependency management strategy

**Official path:** [Design and implement a dependency management strategy](https://learn.microsoft.com/en-us/training/paths/az-400-design-implement-dependency-management-strategy/)  
**Path status:** Not started / In progress / Completed  
**Path review:** YYYY-MM-DD

### 7.1 Explore package dependencies

**Module:** [Explore package dependencies](https://learn.microsoft.com/en-us/training/modules/explore-package-dependencies/)  
**Exam connection:** Build & release pipelines — packages  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Identify direct and transitive dependencies
- [ ] Partition software into sensible components
- [ ] Choose dependency governance practices
- [ ] Scan a codebase to inventory dependencies

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Generate a dependency tree for a small Python project and identify a transitive package.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 7.2 Understand package management

**Module:** [Understand package management](https://learn.microsoft.com/en-us/training/modules/understand-package-management/)  
**Exam connection:** Build & release pipelines — packages  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Explain package feeds and public/private registries
- [ ] Compare hosted package feed choices
- [ ] Consume packages safely
- [ ] Publish versioned packages to a private feed

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Publish a small Python wheel to a test feed (or document the publishing steps).
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 7.3 Migrate, consolidate, and secure artifacts

**Module:** [Migrate, consolidate, and secure artifacts](https://learn.microsoft.com/en-us/training/modules/migrate-consolidating-secure-artifacts/)  
**Exam connection:** Build & release / security  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Assess existing package repositories and migration options
- [ ] Consolidate package sources when justified
- [ ] Configure feed roles and visibility
- [ ] Secure publishing/consuming authentication

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Create an access matrix for package readers, publishers, and administrators.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 7.4 Implement a versioning strategy

**Module:** [Implement a versioning strategy](https://learn.microsoft.com/en-us/training/modules/implement-versioning-strategy/)  
**Exam connection:** Build & release pipelines — packages  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Choose semantic or time-based versioning where applicable
- [ ] Promote versions using artifact-feed views
- [ ] Automate version generation in CI
- [ ] Avoid mutable or ambiguous artifact versions

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Publish candidate and stable package versions; describe promotion without rebuilding.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 7.5 Introduction to GitHub Packages

**Module:** [Introduction to GitHub Packages](https://learn.microsoft.com/en-us/training/modules/introduction-github-packages/)  
**Exam connection:** Build & release pipelines — packages  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Publish and install GitHub Packages
- [ ] Choose package visibility and access
- [ ] Describe deletion and restoration
- [ ] Select correct authentication for registry access

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Document a GitHub Actions workflow that publishes a container package with restricted permissions.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

## 8. Implement continuous feedback

**Official path:** [Implement continuous feedback](https://learn.microsoft.com/en-us/training/paths/az-400-implement-continuous-feedback/)  
**Path status:** Not started / In progress / Completed  
**Path review:** YYYY-MM-DD

### 8.1 Implement Tools to Track Usage and Flow

**Module:** [Implement Tools to Track Usage and Flow](https://learn.microsoft.com/en-us/training/modules/implement-tools-track-usage-flow/)  
**Exam connection:** Instrumentation / processes  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Connect Azure Monitor, Logs, and Application Insights
- [ ] Explain inner-loop versus outer-loop telemetry
- [ ] Write basic KQL queries for application behavior
- [ ] Define actionable flow and usage measurements

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Write KQL for failed requests by operation in 10-minute time buckets.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 8.2 Develop monitor and status dashboards

**Module:** [Develop monitor and status dashboards](https://learn.microsoft.com/en-us/training/modules/develop-monitor-status-dashboards/)  
**Exam connection:** Instrumentation / pipeline maintenance  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Build Azure dashboards and Monitor workbooks
- [ ] Use GitHub metrics and workflow status
- [ ] Measure build failures, duration, and flaky tests
- [ ] Tune pipeline costs and parallel execution
- [ ] Create stakeholder-appropriate visualizations

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Sketch one dashboard: pipeline pass rate, average duration, flaky tests, and recovery trend.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 8.3 Share knowledge within teams

**Module:** [Share knowledge within teams](https://learn.microsoft.com/en-us/training/modules/share-knowledge-within-teams/)  
**Exam connection:** Processes & communications  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Maintain project wikis and Markdown documentation
- [ ] Create Mermaid-based technical diagrams
- [ ] Integrate notifications with Microsoft Teams
- [ ] Design documentation and onboarding practices

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Add a Mermaid pipeline diagram and incident runbook to a GitHub/Azure DevOps wiki.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 8.4 Design processes to automate application analytics

**Module:** [Design processes to automate application analytics](https://learn.microsoft.com/en-us/training/modules/design-processes-automate-application-analytics/)  
**Exam connection:** Instrumentation  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Automate Application Insights analysis
- [ ] Correlate infrastructure and app telemetry
- [ ] Select incident-search and monitoring tools
- [ ] Connect monitoring with incident management

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Trace an example failed API request through logs and dependencies; propose a diagnostic query.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

### 8.5 Manage alerts, blameless retrospectives and a just culture

**Module:** [Manage alerts, blameless retrospectives and a just culture](https://learn.microsoft.com/en-us/training/modules/manage-alerts-blameless-retrospectives-just-culture/)  
**Exam connection:** Instrumentation / processes  
**Date:** YYYY-MM-DD · **Status:** ☐ Not started / ◐ Studying / ☑ Reviewed · **Confidence:** 0 / 1 / 2 / 3

**Learning goals (check after you can explain or do them)**

- [ ] Configure actionable Application Insights alerts
- [ ] Investigate server-side latency and performance
- [ ] Reduce noisy alerts
- [ ] Run blameless retrospectives
- [ ] Capture systemic follow-up actions

**Notes**

- **Concept in my own words:**
- **How it works / key syntax / configuration:**
- **When to use it vs. alternatives:**
- **Limitations, security considerations, or exam traps:**

**Practice and evidence**

- **Suggested small lab:** Write a one-page blameless post-incident review for a simulated failed deployment.
- **Commands / YAML / code / KQL:**
- **Expected result and what actually happened:**
- **Evidence link (PR, run, commit, docs):**

**Recall questions / follow-up**

- **Can I explain this without reading?**
- **What would break in production or appear as an exam scenario?**
- **Questions / revisit on:**

---

# Part B — Official exam objective checklist

> Exam domains and subtopic structure correspond to Microsoft’s AZ-400 guide (skills measured starting July 27, 2026). Checkbox wording below is adapted, not copied verbatim. This checklist complements rather than replaces the 47 course modules.

**Source:** https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/az-400

## E1. Design and implement processes and communications (10–15%)

### Traceability and flow of work

- [ ] Implement work movement with GitHub Flow and similar practices
- [ ] Configure feedback cycles with GitHub Issues and notifications
- [ ] Connect GitHub Projects, Azure Boards, and repositories
- [ ] Trace a change from source/issue to defects and quality results

### DevOps metrics and queries

- [ ] Build dashboards with lead time, cycle time, and recovery time
- [ ] Choose meaningful metrics for planning and development
- [ ] Measure testing and security results
- [ ] Measure deployment flow and operational outcomes

### Collaboration and communication

- [ ] Create wiki/Markdown/Mermaid project documentation
- [ ] Maintain release notes and API docs; automate docs from Git history
- [ ] Use webhooks for integration
- [ ] Connect Azure Boards to GitHub and Teams to GitHub/Azure DevOps

**Weakest topic / next action:**

---

## E2. Design and implement a source control strategy (10–15%)

### Branching strategy

- [ ] Choose trunk-based, feature, or release branches by scenario
- [ ] Configure PR policies, protections, and required reviews
- [ ] Limit merging through policy and branch rules

### Repository administration

- [ ] Select Git LFS or other approaches for large files
- [ ] Plan repository scale using Scalar and cross-repo sharing
- [ ] Set repository permissions and meaningful tags
- [ ] Recover commits/files using Git
- [ ] Remove sensitive or unwanted data from repository history

**Weakest topic / next action:**

---

## E3. Design and implement build and release pipelines (50–55%)

### Package management

- [ ] Choose GitHub Packages versus Azure Artifacts
- [ ] Design feed views, local caches, and upstream sources
- [ ] Choose SemVer, CalVer, or appropriate dependency versioning
- [ ] Version build and release artifacts reproducibly

### Pipeline testing

- [ ] Configure release/quality/security/governance gates
- [ ] Design unit, local, integration, and load testing layers
- [ ] Execute and report tests using agents and pipeline tasks
- [ ] Collect and interpret code coverage

### Pipeline design

- [ ] Select Azure Pipelines or GitHub Actions for the use case
- [ ] Choose hosted/self-hosted infrastructure based on cost, maintenance, and connectivity
- [ ] Connect GitHub code to Azure Pipelines
- [ ] Configure branch/path/schedule/manual triggers
- [ ] Write YAML workflows and Azure Pipelines definitions
- [ ] Order jobs and stages; apply parallelism deliberately
- [ ] Handle hybrid and self-hosted runners/agents and VM-based scenarios
- [ ] Reuse YAML templates, task groups, variables, and variable groups
- [ ] Require appropriate environment approvals and checks

### Deployments

- [ ] Compare blue-green, canary, rings, progressive exposure, feature flags, and A/B tests
- [ ] Order application and dependent service deployments safely
- [ ] Limit downtime using rolling releases, slots, and load balancing
- [ ] Design a hotfix path distinct from routine delivery
- [ ] Plan rollback, failure handling, and resiliency
- [ ] Use Azure App Configuration Feature Manager
- [ ] Deploy containers, scripts, and binaries
- [ ] Include database changes in release orchestration

### Infrastructure as code

- [ ] Select configuration management technology
- [ ] Implement desired-state or configuration management controls
- [ ] Version, test, and deploy IaC from automation
- [ ] Compare ARM, Bicep, Azure Machine Configuration, and State Configuration
- [ ] Describe on-demand environments using Azure Deployment Environments

### Pipeline maintenance

- [ ] Monitor durations, failed builds, and flaky tests
- [ ] Improve pipeline cost, speed, reliability, and performance
- [ ] Tune concurrent job execution
- [ ] Apply artifact and dependency retention policies
- [ ] Move classic Azure Pipelines definitions into YAML

**Weakest topic / next action:**

---

## E4. Develop a security and compliance plan (10–15%)

### Authentication and authorization

- [ ] Choose Entra application/service principal or managed identity
- [ ] Secure GitHub Apps, GITHUB_TOKEN, and PATs
- [ ] Configure Azure DevOps service connections and PATs
- [ ] Apply least-privilege roles and security groups in GitHub and Azure DevOps
- [ ] Select outside collaborator/stakeholder access; configure projects and teams

### Protect sensitive information

- [ ] Store/manage secrets, keys, and certificates in Key Vault
- [ ] Use OIDC/workload identity federation for secretless pipelines
- [ ] Protect secure files used in deployments
- [ ] Prevent leaks through logs, variables, artifacts, and scripts

### Security and compliance scans

- [ ] Set up code, dependency, secret, and license scanning
- [ ] Configure Microsoft Defender for Cloud DevOps Security
- [ ] Configure GitHub Advanced Security for GitHub/Azure DevOps
- [ ] Connect GitHub Advanced Security to Defender for Cloud
- [ ] Scan containers and run CodeQL in a container where needed
- [ ] Respond to vulnerable dependencies through Dependabot alerts

**Weakest topic / next action:**

---

## E5. Implement an instrumentation strategy (5–10%)

### Monitoring configuration

- [ ] Integrate Azure Monitor/Logs with CI/CD tools
- [ ] Collect telemetry with Application Insights and Azure resource insights
- [ ] Create GitHub Insights views and charts
- [ ] Alert on GitHub Actions and Azure Pipelines activity

### Telemetry analysis

- [ ] Diagnose CPU, memory, disk, and network indicators
- [ ] Use application and usage telemetry to identify problems
- [ ] Inspect distributed traces in Application Insights
- [ ] Write filters, projections, aggregations, and time buckets in KQL

**Weakest topic / next action:**

---

# Part C — Gaps and topics to verify explicitly

The official exam can emphasize details not covered deeply by a particular course module. Use this as a targeted review queue:

- [ ] Azure Deployment Environments: templates, catalogs, self-service environments
- [ ] Managed identities vs. app registrations vs. federated service connections
- [ ] GitHub Apps vs. `GITHUB_TOKEN` vs. personal access tokens
- [ ] Azure DevOps access levels, service connections, environments, checks and approvals
- [ ] Artifact retention, up-stream feeds, versioning and provenance
- [ ] Deployment dependency ordering and database schema migration risk
- [ ] Classic-to-YAML migrations and template-driven multi-stage pipelines
- [ ] Container scanning, CodeQL, Dependabot, Defender for Cloud and GitHub Advanced Security
- [ ] Git repository scaling, sensitive history removal, and recovery operations
- [ ] KQL basics, distributed tracing and actionable alerts
- [ ] Current Microsoft Learn docs for features that may have changed since the course was created

# Part D — Reusable notes for labs and practice questions

## Lab log

### LAB-XXX: [Short name]

- **Date / linked module:**
- **Objective and why it matters:**
- **Scenario / starting state:**
- **Environment / prerequisites / expected cost:**
- **Steps / commands / config changes:**
- **Expected vs. observed results:**
- **Negative test or failure simulated:**
- **Troubleshooting and lesson learned:**
- **Cleanup performed:**
- **Repo/PR/run link:**

## Exam scenario / tricky decision

### Q-XXX: [Scenario title]

- **Requirement and constraints:**
- **Candidate solutions:**
- **Best answer and reasoning:**
- **Why alternatives are less suitable:**
- **Documentation link:**
- **Confidence / revisit date:**

## Weekly active recall

| Week | Modules reviewed | Labs completed | Mistakes or weak topics | Next priority |
|---|---|---|---|---|
| YYYY-WW | | | | |

## Quick reference links

- [Azure Pipelines documentation](https://learn.microsoft.com/en-us/azure/devops/pipelines/)
- [GitHub Actions documentation](https://docs.github.com/en/actions)
- [Azure DevOps documentation](https://learn.microsoft.com/en-us/azure/devops/)
- [Bicep documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/)
- [Azure Monitor documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/)
- [Kusto Query Language overview](https://learn.microsoft.com/en-us/kusto/query/)
- [Azure Key Vault documentation](https://learn.microsoft.com/en-us/azure/key-vault/)
- [Exam practice assessment](https://learn.microsoft.com/en-us/credentials/certifications/exams/az-400/)

*Maintenance note: Microsoft regularly updates exam objectives and module content. Check the study guide again before booking the exam.*
