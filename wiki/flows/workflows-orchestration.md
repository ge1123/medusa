# Workflows and Orchestration Flow

Use this page when routing built-in workflow, step, compensation, retry, async
step, and transaction execution questions.

## Verified Facts

* Workflow composition primitives live in `packages/core/workflows-sdk`
* Built-in business workflows and steps live in `packages/core/core-flows`
* Transaction execution internals live in `packages/core/orchestration`
* `createWorkflow` registers workflow definitions through `WorkflowManager` and
  returns an exported runner
* `createStep` must run inside a workflow composition context and binds step
  handlers into the current workflow
* Core-flow workflow files usually compose steps with `createWorkflow`,
  `transform`, and `WorkflowResponse`
* Core-flow step files usually resolve module services from the container and
  return `StepResponse`; compensation handlers receive compensation input from
  the `StepResponse`
* `LocalWorkflow` executes, cancels, retries, and marks async step
  success/failure through `TransactionOrchestrator`
* `TransactionOrchestrator` owns transaction state transitions, compensation,
  retries, timeout handling, checkpoints, cancellation, and async resumption

## Routing

Start at the route or service invoking a core-flow. Then inspect the workflow
composition file under `packages/core/core-flows/src/<domain>/workflows`, then
the called step files under `packages/core/core-flows/src/<domain>/steps`.

Drop into `packages/core/workflows-sdk` only when the question concerns
workflow/step registration, composition context, exported runners, or nested
workflow execution.

Drop into `packages/core/orchestration` only when the question concerns
execution state, compensation, retries, async steps, cancellation, checkpoints,
or transaction orchestration internals.

## Source Evidence

* `packages/core/workflows-sdk/src/medusa-workflow.ts` stores exported workflow
  runners
* `packages/core/workflows-sdk/src/utils/composer/create-workflow.ts` registers
  workflow resources and definitions, then exports a runnable workflow
* `packages/core/workflows-sdk/src/utils/composer/create-step.ts` binds step
  handlers into the current workflow composer context
* `packages/core/core-flows/src/store/workflows/create-stores.ts` shows a
  domain workflow composing steps and returning `WorkflowResponse`
* `packages/core/core-flows/src/store/steps/create-stores.ts` shows a step
  resolving `Modules.STORE`, calling module service methods, and compensating
  with deletes
* `packages/core/core-flows/src/common/steps/use-query-graph.ts` resolves
  `ContainerRegistrationKeys.QUERY` and calls `query.graph`
* `packages/core/orchestration/src/workflow/workflow-manager.ts` stores
  workflow definitions, handlers, options, and creates transaction
  orchestrators
* `packages/core/orchestration/src/workflow/local-workflow.ts` wraps execution,
  cancellation, retry, async step state, and action graph mutation
* `packages/core/orchestration/src/transaction/transaction-step.ts` enforces
  allowed state/status transitions and tracks invoke/compensate state
* `packages/modules/workflow-engine-inmemory` and
  `packages/modules/workflow-engine-redis` implement the storage/scheduler
  layer used by orchestration; in-memory uses timers/maps, while Redis uses
  BullMQ jobs/workers and race-prevention locks

## Graph Evidence

* `trace_path(function_name="createWorkflow", direction="inbound", depth=1)`
  shows broad usage across core-flow workflows and plugin workflows
* `trace_path(function_name="createStep", direction="inbound", depth=1)` shows
  broad usage across core-flow steps and workflow-local validation steps

## Query Notes

```text
search_graph(query="createWorkflow createStep", limit=50)
trace_path(function_name="createWorkflow", direction="inbound", depth=1)
trace_path(function_name="createStep", direction="inbound", depth=1)
```

Many workflows and steps are indexed as exported `Variable` nodes. If
`search_graph` misses them, use `query_graph` against variable names containing
`Workflow` or `Step` under `packages/core/core-flows`.

## Open Questions

* Workflow engine module implementations in `packages/modules/workflow-engine-*`
  were not inspected in Batch 2
* This page intentionally avoids enumerating every core-flow domain workflow
* Future runtime investigations should verify current callers before treating
  `GlobalWorkflow` as a primary execution path

## Graph Gaps

* During Batch 2, `search_graph(file_pattern=...)` returned no results for some
  scoped workflow package searches, while `query_graph` by `file_path STARTS
  WITH` worked
* `trace_path(TransactionOrchestrator)` by class name returned no inbound
  callers even though source shows construction through workflow internals
