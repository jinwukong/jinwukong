# Jin · @jinwukong

I build LLM research agents for a quant fund's investment-research desk: the agent runtime, the tool layer, model routing across vendors, evaluation, and the operations behind it. Most of what I send upstream started as something that broke in that production stack.

<p>
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/jinwukong/jinwukong/main/profile/stats-dark.svg">
    <img alt="GitHub stats" height="170" src="https://raw.githubusercontent.com/jinwukong/jinwukong/main/profile/stats.svg">
  </picture>
</p>

## Merged upstream

| Project | Change | Where it came from |
|---|---|---|
| [transformers #49515](https://github.com/huggingface/transformers/pull/49515) | Legacy `rope_scaling` assignment now warns callers to use `rope_parameters` and normalizes supplied legacy `type` keys; assigning `None` remains supported. | Traced the setter behind SGLang's RoPE override bug. Following review, deprecated the alias instead of inferring omitted base parameters. |
| [xgrammar #977](https://github.com/mlc-ai/xgrammar/pull/977) | `\s` / `\S` now match the ECMAScript whitespace class in both regex engines (JSON Schema `pattern` and `from_regex`). Since 0.2.6 a schema such as `^a\sb$` rejected a no-break space on one path and accepted it on the other. | Probing JSON Schema `pattern` behaviour for tool-call grammars; the ASCII escape table was the one place the ECMAScript dialect was not followed. |
| [SGLang #43067](https://github.com/sgl-project/sglang/pull/43067) | `--json-model-override-args` overriding `rope_scaling` no longer drops `rope_theta` under transformers v5, where the model kept generating fluent text with the wrong RoPE base (Llama-3.2-3B GSM8K had fallen 161 → 106). | Traced to the `rope_scaling` alias setter in transformers; paired the SGLang merge fix with the upstream deprecation and normalization change. |
| [OGX #6745](https://github.com/ogx-ai/ogx/pull/6745) | MCP tool results: `resource_link`, `audio`, embedded resources, `structured_content` and `is_error` now reach the model instead of raising or vanishing. | Reading the MCP result path while evaluating OGX (formerly Llama Stack) as an agent backend. |
| [OGX #6776](https://github.com/ogx-ai/ogx/pull/6776) | A first-class `remote::sglang` inference provider: health, model classification, streaming reasoning passthrough, `/v1/rerank`, Anthropic Messages, plus the CI plumbing to run SGLang's CPU build in their integration tests. | The SGLang servers I operate; measured on hosted runners that SGLang's CPU kernels need AVX-512/AMX, which shaped how the CI lane is gated. |
| [OGX #6782](https://github.com/ogx-ai/ogx/pull/6782) | SystemOne judgments through the SGLang provider, preserving usage and handling declared backend extensions before strict response validation. | A live SGLang response carried `x_label_mass`, which the shared wire model rejected. Fixed the boundary and verified the adapter against a running CPU server. |
| [Homebrew #316024](https://github.com/Homebrew/homebrew-core/pull/316024) | New formula `android-file-transfer-linux`: the native `aft-mtp-cli` MTP client with its shared library. | Needed a native MTP CLI on Apple Silicon; the one bundled elsewhere ran under Rosetta. |
| [android-file-transfer-linux #368](https://github.com/whoozle/android-file-transfer-linux/pull/368) | The macOS shared-library build links IOKit/CoreFoundation and installs with a working rpath. | Same need; the fix landed upstream first, then the formula. |

## In review

- [OGX #6822](https://github.com/ogx-ai/ogx/pull/6822): Tev1 model discovery and recorded SGLang integration tests, with explicit CPU requirements for recording and replay on ordinary runners.
- [LiteLLM #45044](https://github.com/BerriAI/litellm/pull/45044): preserve signed thinking replay data in streamed reasoning done items.
- [vLLM #61205](https://github.com/vllm-project/vllm/pull/61205): bound consecutive whitespace in tool-call structural tags, the self-loop that let a model emit spaces until `max_tokens`.
- [SGLang #42984](https://github.com/sgl-project/sglang/pull/42984), [#43119](https://github.com/sgl-project/sglang/pull/43119), [#43120](https://github.com/sgl-project/sglang/pull/43120): GLM-4.7/5.3 and Kimi-K3 tool-call grammars (always-thinking templates, free-text argument close markers, `additionalProperties`).

## Own projects

- [llm-low-cost-search](https://github.com/jinwukong/llm-low-cost-search): async Brave Search plus Readability extraction for LLM/RAG pipelines, 10–20 links per request at roughly 1/8–1/5 the cost of hosted stacks.
- [vnpy-cpv-cta](https://github.com/jinwukong/vnpy-cpv-cta), [vnpy-headless-backtest](https://github.com/jinwukong/vnpy-headless-backtest), [vnpy_TickDataRecorder](https://github.com/jinwukong/vnpy_TickDataRecorder): futures research on vnpy. Price–volume (CPV) factors with a T+0 / T+1 trader split, a headless backtesting runner, and CTP tick recording with cross-source consistency checks.
- [btc-perpetual-alpha](https://github.com/jinwukong/btc-perpetual-alpha): short-horizon prediction on Binance perpetuals from 1-minute data and funding rates, with a reinforcement-learning environment.
- [drl-stocktrading-ensemble](https://github.com/jinwukong/drl-stocktrading-ensemble): reproduction of the ICAIF 2020 ensemble deep-RL trading paper on the Dow 30.

Interests: agent harness design, structured output and tool-call grammars, inference serving, evaluating research agents. Next: an LLM agent plugin for vnpy.
