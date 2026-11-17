# Opt-in Codex model selection from a small benchmark

This example selects models and reasoning effort for six GSD roles. Copy the entries under `model_overrides` and `effort.agent_overrides` from [config.json](config.json) into the matching objects in your project's `.planning/config.json`. Keep your other project settings. The example sets `runtime` to `codex`; use it only with Codex. Verify each effective assignment with `gsd_run query resolve-model <agent>`. Confirm your Codex account can run these models before adopting the example. For new projects, the same selections can be added to the GSD global defaults.

## Evidence for the selections

Each trial used the installed role instructions in a separate Codex CLI process against an isolated snapshot. Samples used local source code with synthetic inputs. The results below are aggregates. Uncached input is the reported input tokens minus cached input tokens. Durations sum per-process wall times; runs used shared parallel capacity. These are small comparisons, with one trial per case and setting.

| Role | Selected model and effort | Main observation |
|---|---|---|
| Planner | Sol medium | Tied Sol high at 16/16 on two blind-scored plans (124.4 vs 184.4 s). Both finalists passed all seven downstream checks with the same executor and needed no rework. |
| Executor | Terra medium | Both Terra efforts required the same two review fixes across two tasks; medium used 62,411 vs 68,151 uncached input tokens. |
| Verifier | Sol medium | Found a SQLite lock regression missed by Terra high on paired snapshots. In a later six-setting, 36-run matrix, all settings found the three seeded defects; Sol medium also caught a Python 3.9 rollback edge. |
| Code reviewer | Terra medium | Found all three seeded defects and tied Terra high on false findings (2); 178.7 vs 194.5 s and 166,521 vs 191,381 uncached input tokens. |
| Debugger | Terra high | All three settings reproduced both diagnoses. Terra high used 74,934 vs 100,766 uncached input tokens and 103.5 vs 139.0 s against Sol high. |
| Code fixer | Terra medium | Both targeted fixes passed independent checks and the supplied suites across all three settings. Medium used 53,277 vs 56,383 uncached input tokens against Terra high, with similar time. |

A follow-up matrix also exercised plan checking, phase research, integration checking, test authoring, and pattern mapping. It did not support a model change for those roles. In test authoring, all three settings passed their own SQLite test but failed to distinguish the broken snapshot: the injected SQLite error was a different exception type from the one in the bug. The language model's score cannot substitute for checking a regression test against broken code.

The opt-in selections are intended for similar local tasks. Broader workflows, other runtimes, and account-level model availability need independent evidence before changing a shared default.
