# Overhead — 10 replayed scenarios, 5 reps, no scoring/judge (harness/overhead.py)

Scenarios C01 C05 C08 S11 S13 S16 S20 D22 D23 D30; configs shuffled per (scenario, rep); 200 runs, 0 errors,
every observed run wrote output. Host: 8 vCPU, governor powersave, not pinned. Workload = cassette replay
(1 s per LLM turn), so wall time is turn-delay bound -- the same regime as the papers' LLM-bound tasks.
Overhead = mean over scenarios of the per-scenario ratio vs bare; 95% CI = bootstrap over the 10 scenarios.

| | wall | agent CPU | tool CPU / run | agent+tool CPU vs bare agent | tool peak RSS | host CPU |
|---|---|---|---|---|---|---|
| bare | 5.846 s | 0.124 s | — | — | — | 0.51 s |
| AgentSight | +0.00 % [−0.04, +0.04] | +0.3 % [−1.2, +1.6] | 0.057 s | +45 % [+38, +51] | 56 MB | +14 % [−7, +47] |
| AgentLoupe | +0.00 % [−0.03, +0.04] | +0.2 % [−1.1, +1.4] | 0.059 s | +46 % [+41, +52] | 56 MB | +25 % [−7, +70] |
| ao | +0.08 % [−0.04, +0.24] | +4.3 % [+2.1, +6.7] | 0.460 s | +368 % [+340, +393] | 105 MB | +85 % [+68, +102] |

- agent CPU = user+sys of the agent tree (probe time in its own syscalls/uprobes is charged here);
  tool CPU = collector process tree (AgentSight/AgentLoupe) or the observer+pipeline cgroups (ao), over
  [workload start, stop] incl. a 2 s drain. Raw per-run rows: runs.jsonl; per-scenario means: summary.json.
- Host CPU is the whole machine (background included): read it as indicative only.
