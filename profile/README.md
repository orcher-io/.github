<p>
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/orcher-io/.github/main/profile/assets/banner.svg">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/orcher-io/.github/main/profile/assets/banner-light.svg">
    <img alt="ORCHER" src="https://raw.githubusercontent.com/orcher-io/.github/main/profile/assets/banner.svg" width="100%">
  </picture>
</p>

<p align="center"><sub>Workflows that finish, even when the process running them doesn't.</sub></p>

<br />

ORCHER is a durable execution engine. You write workflows as ordinary async
code in Rust, TypeScript or Python. The engine records each step as it
completes, so a crash, a deploy or a restart resumes the run where it stopped
instead of starting it over, or losing it.

- <img height="14" src="https://octicons-col.vercel.app/sync/38BDF0"> **Resumes, never restarts**: a dead worker's runs move to a live one and carry on from the last finished step
- <img height="14" src="https://octicons-col.vercel.app/code/38BDF0"> **Plain code**: decorators and macros on async functions, with no DSL and no YAML
- <img height="14" src="https://octicons-col.vercel.app/clock/38BDF0"> **Durable timers and events**: sleep for days or wait on an outside signal, with nothing kept running
- <img height="14" src="https://octicons-col.vercel.app/iterations/38BDF0"> **Retries with intent**: per-task retry policies, and error types that are never retried
- <img height="14" src="https://octicons-col.vercel.app/history/38BDF0"> **Replayed, not re-run**: a resumed run is rebuilt from its journal, so finished steps return the results they already had
- <img height="14" src="https://octicons-col.vercel.app/zap/38BDF0"> **Written in Rust**: one small engine binary backed by Postgres

<br />

### <img height="16" src="https://octicons-col.vercel.app/play/38BDF0"> Try it in five minutes

Install the [CLI](https://github.com/orcher-io/cli), start an engine on your machine (it needs Docker), and create a project:

```bash
brew install orcher-io/tap/orcher        # or: cargo, pip, npm install orcher
orcher dev start                         # a local engine on localhost:50051
orcher new rust hello && cd hello        # or: python, typescript
cargo run -- worker                      # the worker, in one terminal
cargo run -- start Ada                   # start a workflow, in another
```

To see a crash survived, the [quickstart](https://github.com/orcher-io/quickstart)
runs an order workflow you can kill halfway through, and a new worker ships the
order without charging it twice:

```rust
#[workflow(name = "order")]
async fn order(ctx: WorkflowContext, order_id: String) -> Result<String> {
    let charged: String = ctx.execute_task(charge, order_id.clone()).await?;
    ctx.sleep(Duration::from_secs(10)).await?; // kept by the engine, not the worker
    let shipped: String = ctx.execute_task(ship, order_id).await?;
    Ok(format!("{charged}, then {shipped}"))
}
```

<details>
<summary>Show in Python</summary>

```python
@workflow(name="order")
async def order(ctx: WorkflowContext, order_id: str) -> str:
    charged = await ctx.execute_task(charge, order_id=order_id)
    await ctx.sleep(timedelta(seconds=10))  # kept by the engine, not the worker
    shipped = await ctx.execute_task(ship, order_id=order_id)
    return f"{charged}, then {shipped}"
```

</details>

<details>
<summary>Show in TypeScript</summary>

```typescript
@Workflow({ name: 'order' })
export class Order {
  async run(ctx: WorkflowContext, orderId: string): Promise<string> {
    const charged = await ctx.executeTask(orderTasks.charge, orderId);
    await ctx.sleep(Duration.fromSeconds(10)); // kept by the engine, not the worker
    const shipped = await ctx.executeTask(orderTasks.ship, orderId);
    return `${charged}, then ${shipped}`;
  }
}
```

</details>

The full walkthrough is in [**orcher-io/quickstart**](https://github.com/orcher-io/quickstart).

<br />

### <img height="16" src="https://octicons-col.vercel.app/sparkle-fill/38BDF0"> Built for AI agents

An agent is a long, expensive, unpredictable workflow, which is what durable
execution is for:

- **Model calls run once**: a finished call is journaled, so a crashed agent resumes without paying for, or getting a different answer from, the same call again
- **Tools act once**: a step that sends an email or charges a card is not repeated when the run resumes
- **People in the loop**: wait hours or days for an approval, with a deadline, and nothing kept running
- **Flaky APIs**: retry rate limits and timeouts with backoff, and never retry a request that can't succeed
- **Room for context**: up to 8 MiB per step by default, configurable, and a clear error above it

The quickstart's [agent example](https://github.com/orcher-io/quickstart#-5-an-ai-agent-that-waits-for-approval)
drafts a reply with a model, waits for a person to approve it, then sends it.
CI kills its worker while it waits and checks that the model was called once
and the email sent once.

<br />

### <img height="16" src="https://octicons-col.vercel.app/package/38BDF0"> SDKs

| | Install | Repository |
|---|---|---|
| <img height="14" src="https://cdn.simpleicons.org/rust/CE422B"> **Rust** | `cargo add orcher-sdk` | [sdk-rust](https://github.com/orcher-io/sdk-rust) <a href="https://crates.io/crates/orcher-sdk"><img align="right" src="https://img.shields.io/crates/v/orcher-sdk?style=flat-square&labelColor=0a0a0a&color=04B385&label=crates.io" alt="crates.io"></a> |
| <img height="14" src="https://cdn.simpleicons.org/typescript/3178C6"> **TypeScript** | `npm install @orcher/sdk` | [sdk-ts](https://github.com/orcher-io/sdk-ts) <a href="https://www.npmjs.com/package/@orcher/sdk"><img align="right" src="https://img.shields.io/npm/v/@orcher/sdk?style=flat-square&labelColor=0a0a0a&color=04B385&label=npm" alt="npm"></a> |
| <img height="14" src="https://cdn.simpleicons.org/python/3776AB"> **Python** | `pip install orcher-sdk` | [sdk-py](https://github.com/orcher-io/sdk-py) <a href="https://pypi.org/project/orcher-sdk/"><img align="right" src="https://img.shields.io/pypi/v/orcher-sdk?style=flat-square&labelColor=0a0a0a&color=04B385&label=pypi" alt="PyPI"></a> |

All three are built on [**sdk-core**](https://github.com/orcher-io/sdk-core),
a shared Rust core that handles the connection, the workers and replay, so the
SDKs behave the same. The wire protocol is in [**protos**](https://github.com/orcher-io/protos), and the
[**CLI**](https://github.com/orcher-io/cli) runs a local engine and drives workflows on any engine.

<br />

### <img height="16" src="https://octicons-col.vercel.app/info/38BDF0"> Status

ORCHER is in **developer preview**. The SDKs, the core, the protocol and the CLI
are open source under Apache-2.0. The engine is free to download as a container
image, `ghcr.io/orcher-io/orcher`, for development and evaluation; production
use needs permission while the preview lasts. APIs may change between minor
releases until 1.0, and every change is listed in each repository's changelog.

<br />

### <img height="16" src="https://octicons-col.vercel.app/comment-discussion/38BDF0"> Get involved

- Questions and ideas: [Discussions](https://github.com/orcher-io/quickstart/discussions)
- Bugs and feature requests: open an issue in the repository it concerns
- Security reports: privately, as described in our [security policy](https://github.com/orcher-io/.github/blob/main/SECURITY.md)
- News: join the list at [orcher.io](https://orcher.io)

<sub>Rust, TypeScript and Python logos are trademarks of their respective owners, shown to indicate the languages each SDK is for.</sub>
