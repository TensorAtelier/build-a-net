# Build a Net

A game about how modern AI works, played by building it. Drag in parts (a convolution, a LayerNorm,
an attention head), wire them together, and watch real numbers flow through. Shapes are checked at
every connection, so you see the moment a network stops making sense.

**Play it: <https://tensoratelier.com/build-a-net/>** (free, in your browser). Part of
[Tensor Atelier](https://tensoratelier.com).

38 levels across eight chapters:

| Ch. | Topic | Levels |
|---|---|---|
| 1 | Convolution: kernels, stride, padding, output shapes | Sandbox + 9 |
| 2 | Pooling and CNN stacks: receptive fields, parameter budgets, a tiny digit classifier | 6 |
| 2b | Residual blocks: skip wires, 1×1 projections, normalisation | 4 |
| 3 | RNNs and LSTMs, and the fixed-size bottleneck that motivates attention | 3 |
| 4 | Attention: Q/K/V from parts, scaled dot-product, multi-head | 4 |
| 5 | The Transformer block, positional encoding, stacking blocks | 3 |
| 6 | Mixture of Experts: routing, top-k, capacity, active vs total parameters | 4 |
| 7 | Learning from reward: bandit, gridworld, cart-pole, a toy RLHF | 5 |

Every verdict is computed, not hand-waved: levels check the graph you wired, measure its real forward
pass, and in chapter 7 run full training on fixed seeds, so your stars never depend on luck.

## Run

```
npm install
npm run dev      # http://localhost:8871
npm test         # 93 tests: every level has a 3★ answer, known exploits stay closed
npm run build
```

Vanilla JS and Vite, no other dependencies. It deploys to GitHub Pages on every push to `main`
(`.github/workflows/pages.yml`). CI runs on macOS: the cart-pole levels were tuned there, and training
comes out slightly differently on Linux x64.

## How it was built

It was built with AI coding tools from a roadmap with one finish line, one short spec per feature, and
an adversarial review of each. All of it is in this repo:

- [`docs/agents/roadmap.md`](docs/agents/roadmap.md): the goal, the nine features and what's next
- `.scratch/<feature>/plan.md`: each feature's spec and tickets
- `.scratch/<feature>/REPORT.md`: each feature's review verdict and decision log

The game started inside [Fun Sim](https://github.com/TensorAtelier/fun-sim) and moved here on
2026-10-23. File paths in `.scratch/` still use the old layout: `src/sims/build/…` is now `src/build/…`,
and `src/sims/build.js` is `src/game.js`.

## Code

- `src/game.js`: the page. Sandbox, Challenges (level runner, stars, unlocks), Workshop and Reward tabs.
- `src/build/graph.js`: pure block-graph model. Block types, wiring rules (a DAG; an input takes one
  wire), shape inference, parameter counts, level check.
- `src/build/workshop.js`: the Workshop. Parts bin, drag-to-wire board, inspector, receptive-field box.
- `src/build/chapter2.js` … `chapter6.js`: Workshop levels as data (target shape, required and forbidden
  blocks, star budgets, measured checks such as signal strength, word-order sensitivity or dropped words).
- `src/build/words.js`, `digits.js`: the toy vocabulary and ten 28×28 digits the networks run on.
- `src/build/tensor.js`, `forward.js`: plain-JS forward pass with seeded untrained weights, a per-block
  cache and a cost budget. Every Workshop block previews its real output.
- `src/build/rl.js`, `rllevels.js`, `reward.js`: the Reward tab. REINFORCE on a bandit, gridworld and
  cart-pole, and a toy RLHF with a KL penalty.
- `src/neural/net.js`: the small MLP with backprop that cart-pole trains (shared with Fun Sim's Neural Net).
- `src/build/conv.js`, `figure.js`, `levels.js`: chapter 1. Conv math, the input ✱ kernel = output
  figures, and the question generators.
- `src/ui.js`, `src/style.css`: layout and controls shared with Fun Sim.

Progress is saved in `localStorage` under `fun-sim.build.v1`. The key keeps its old name so players who
started on Fun Sim keep their stars (same origin).

## License

Apache-2.0
