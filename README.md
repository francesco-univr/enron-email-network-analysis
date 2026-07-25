# Enron Email Network Analysis

> Structural analysis, null-model comparison, robustness testing, community detection, and graph-learning extensions on the SNAP Enron email network.

Complex Network Analysis - Level I Master in Cybersecurity, University of Pisa.  
Author: Francesco Simbola

## The Question

What makes a real organisational communication network different from textbook random graphs?

This project studies whether the Enron email graph is heavy-tailed, small-world, clustered, modular, and robust. It then asks a second question: do the same hubs that make communication efficient also create a structural security weakness?

Short answer: yes. The network combines a heavy-tailed degree distribution with strong local closure and short paths. It is resilient to random failures, but targeted removal of hubs rapidly fragments the giant component.

## Results

| Property | Result | Interpretation |
|---|---:|---|
| Full graph | 36,692 nodes, 183,831 unique undirected edges | Large organisational communication network |
| Giant component | 33,696 nodes, 180,811 edges (91.8% of nodes) | Most activity belongs to one connected structure |
| Mean / maximum degree | 10.73 / 1,383 | A small number of very strong hubs exists |
| Tail fit | TPL best; power-law gamma 1.98, xmin 5 | Heavy-tailed with a finite-size cutoff, not a clean power law |
| Transitivity / average clustering | 0.0851 / 0.5092 | Strong triadic closure |
| ER clustering benchmark | 0.00032 | Local clustering is about 1,600x the equivalent ER graph |
| Average path / diameter estimate | 3.96 / 13 | Small-world communication |
| Degree assortativity | -0.1165 | Hubs connect mainly to lower-degree nodes |
| Communities / modularity | 197 / 0.604 | Clear mesoscale organisation into departments or teams |
| Degree-betweenness correlation | 0.79 | Major hubs are also major bridges |

### Robustness

The operational failure point is the fraction removed when the giant component falls below 1% of the original network.

| Removal strategy | Critical fraction |
|---|---:|
| Random failures | 0.93 |
| Degree-targeted attack | 0.13 |
| Betweenness-targeted attack | 0.20 |

This is the classic robust-yet-fragile pattern: random failures are absorbed for a long time, while targeted attacks on hubs collapse connectivity early. The empirical random-failure point is of the same order as the Molloy-Reed estimate (0.993).

### Graph Learning Extensions

- Spectral embedding (32 dimensions) plus logistic regression recovers Louvain communities with 0.897 accuracy, against a 0.214 majority-class baseline.
- Split conformal prediction reaches 0.905 empirical coverage for a 0.90 target, with an average prediction-set size close to one.
- Game-theoretic centrality uses the Michalak et al. coverage game to identify nodes whose value is not captured by degree alone.
- The companion SI experiment shows that hub-seeded diffusion spreads substantially faster than random seeding.

## Why No Single Null Model Is Enough

The real graph is compared with Erdos-Renyi, Barabasi-Albert, Watts-Strogatz, and Configuration Model networks.

- The Configuration Model reproduces the heavy-tailed degree sequence, but not the observed clustering.
- Watts-Strogatz reproduces high clustering and short paths, but has a narrow degree distribution with no comparable hubs.
- Erdos-Renyi and Barabasi-Albert miss important higher-order structure.

The Enron graph combines heavy tails, triadic closure, modularity, short paths, and disassortative mixing. That combination - not one metric in isolation - is the structural signature of the real communication network.

## Analysis Pipeline

1. Load the SNAP edge list and isolate the giant component.
2. Measure connectivity, degree statistics, clustering, distance, assortativity, and centrality.
3. Fit and compare power law, exponential, lognormal, and truncated power-law tails.
4. Compare the empirical graph with four synthetic null models.
5. Simulate random failures and simultaneous, adaptive, and betweenness-targeted attacks.
6. Detect Louvain communities and export an interactive hub subgraph.
7. Add game-theoretic centrality, spectral graph embeddings, node classification, and conformal prediction.

## Interactive Visualisation

`Enron_hub_viz.html` is a self-contained HTML5 canvas visualisation of the induced hub subgraph:

- 540 nodes and 17,782 edges;
- colour encodes Louvain community;
- node size scales with degree;
- pan, zoom, hover details, and neighbour highlighting;
- no external JavaScript dependencies, so it works offline.

Download the file and open it directly in a browser.

## Project Structure

```text
.
|-- Enron_Analysis.ipynb          # Complete analysis with saved outputs
|-- Enron_hub_viz.html            # Stand-alone interactive hub visualisation
|-- Enron_CNA_Presentation.pptx   # Project presentation
|-- email-Enron.txt/
|   `-- Email-Enron.txt           # SNAP edge list
|-- requirements.txt
`-- README.md
```

## Installation

Python 3.10 or newer is recommended.

```bash
git clone https://github.com/francesco-univr/enron-email-network-analysis.git
cd enron-email-network-analysis
python -m venv .venv
```

Activate the environment, then install the dependencies:

```bash
pip install -r requirements.txt
```

## Reproducing the Analysis

The dataset is already stored at the relative path expected by the notebook.

```bash
jupyter lab Enron_Analysis.ipynb
```

Run all cells from top to bottom. The saved notebook was executed end-to-end with a fixed seed where stochastic algorithms are used. A complete run takes approximately 8-10 minutes on a standard laptop.

## Dataset

The project uses the [SNAP Enron email network](https://snap.stanford.edu/data/email-Enron.html), derived from email data made public during the Federal Energy Regulatory Commission investigation.

The SNAP file stores each undirected relationship in both directions. Loading it as a NetworkX `Graph` collapses 367,662 directed entries into 183,831 unique undirected edges.

## Limitations

- The graph is symmetrised and unweighted, so sender-recipient direction, message frequency, and time are not modelled.
- Average distance is estimated from 200 source nodes; betweenness and diameter are approximated for computational tractability.
- Synthetic-model results are point estimates rather than averages over many independent graph realisations.
- The node-classification task is a transductive probe: labels come from Louvain communities on the same graph.
- Conformal coverage is empirical on one graph split; node dependence weakens the usual i.i.d. guarantee.

## References

- J. Leskovec et al., *Graph Evolution: Densification and Shrinking Diameters*, ACM TKDD, 2007.
- A. Clauset, C. Shalizi, M. Newman, *Power-Law Distributions in Empirical Data*, SIAM Review, 2009.
- T. Michalak et al., *Efficient Computation of the Shapley Value for Game-Theoretic Network Centrality*, JAIR, 2013.

