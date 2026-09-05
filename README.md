# Embedded Neural Signal Models

This repository contains two independent neural-signal decoding projects. They
share repository tooling only; neither project imports code, data, checkpoints,
or results from the other.

## Projects

| Project | Task | Retained model | Status |
|---|---|---|---|
| [`finger_movements/`](finger_movements/) | 28-channel EEG left/right classification | causal 400 ms CSSD + hierarchical LDA | active firmware candidate |
| [`indy_loco/`](indy_loco/) | intracortical x/y velocity decoding | 96-channel, width-64 causal TCN+GRU; optional GRU-hidden residual memory | active deployment and paper-evaluation package |

Each project keeps active code and evidence separate from historical material.
The exact directory names differ slightly between the two projects; use each
project's README and STATUS file as its entry point.

```text
finger_movements/              indy_loco/
├── data/                      ├── data/
├── models/                    ├── models/
├── experiments/              ├── experiment/
├── results/                   ├── results/
├── history/                   ├── history/
├── docs/STATUS.md             └── STATUS.md
├── configs/
└── tests/
```

The repository root contains only shared collaboration infrastructure:
`Makefile`, dependency lists, Ruff, pre-commit, pytest configuration, and this
overview.

## Setup and checks

Use Python 3.10 or newer inside a virtual environment:

```bash
make setup-dev
make lint
make test
make firmware-test
```

`make firmware-test` validates the active FingerMovements C99 implementation.
Processed data can be archived per project, for example:

```bash
make data-archive PROJECT=finger_movements
make data-archive PROJECT=indy_loco
```

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for experiment, data, and protected
artifact rules.
