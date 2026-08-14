### **Goal**
Build a GitHub Actions workflow (`02-contexts.yaml`) to explore **contexts** (`github`, `inputs`, `env`) and variable hierarchy.

---

### **Steps**

#### **1. Core Workflow**
- **File**: `.github/workflows/02-contexts.yaml`
- **Name**: `02 - Contexts`
- **Triggers**: `push`, `workflow_dispatch`
- **Job**: `echo-data` (runs on `ubuntu-latest`)
  - **Step 1**: Print `github` context (event name, ref, SHA, actor, workflow name, run ID, run number).
  - **Step 2**: Print repo variable `MY_VAR` (set to `hello world` via **Settings > Secrets > Variables > New repository variable**).

#### **2. Invalid Context**
- Add `run-name: My custom workflow run name - ${{ runner.os }}` → **Error**: `runner` context unavailable at workflow level.

#### **3. Fix & Extend**
- Replace `run-name` with `06 - Contexts | DEBUG - ${{ inputs.debug }}`.
- Add `workflow_dispatch` input:
  ```yaml
  on:
    workflow_dispatch:
      inputs:
        debug:
          type: boolean
          default: false
  ```
- **Observe**: `debug` defaults to `false` for `push` triggers.

#### **4. Env Hierarchy**
- **Workflow-level env**:
  ```yaml
  env:
    MY_WORKFLOW_VAR: workflow
    MY_OVERWRITTEN_VAR: workflow
  ```
- **Job-level env**:
  ```yaml
  env:
    MY_JOB_VAR: job
    MY_OVERWRITTEN_VAR: job
  ```
- **Step-level env**:
  ```yaml
  env:
    MY_OVERWRITTEN_VAR: step
  ```
- **Steps**:
  1. Print `MY_WORKFLOW_VAR` and `MY_OVERWRITTEN_VAR` (expect `step`).
  2. Repeat in a new step *without* `env` → `MY_OVERWRITTEN_VAR` reverts to `job`.

#### **5. Cleanup**
- Remove `push` trigger; keep only `workflow_dispatch`.

---
**Key Takeaway**: Contexts and `env` vars are **hierarchical** (step > job > workflow). `runner` context is **job-level only**.