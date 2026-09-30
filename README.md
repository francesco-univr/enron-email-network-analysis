# Enron Email Network Analysis

> Structural analysis, null-model comparison, robustness testing, community detection, and graph-learning extensions on the SNAP Enron email network.

![Python](https://img.shields.io/badge/Python-3.10%2B-blue) ![NetworkX](https://img.shields.io/badge/NetworkX-3.2%2B-orange) ![Dataset](https://img.shields.io/badge/dataset-SNAP%20Enron-lightgrey) ![Nodes](https://img.shields.io/badge/nodes-36%2C692-lightgrey) ![Edges](https://img.shields.io/badge/edges-183%2C831-lightgrey)

Complex Network Analysis, Level I Master in Cybersecurity, University of Pisa.  
Author: Francesco Simbola

---

## The Question

What makes a real organisational communication network different from textbook random graphs?

This project studies whether the Enron email graph is heavy-tailed, small-world, clustered, modular, and robust. It then asks a second question: do the same hubs that make communication efficient also create a structural security weakness?

Short answer: yes. The network combines a heavy-tailed degree distribution with strong local closure and short paths. It is resilient to random failures, but targeted removal of hubs rapidly fragments the giant component.

---

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

This is the classic robust-yet-fragile pattern: random failures are absorbed for a long time, while targeted attacks on hubs collapse connectivity early. The empirical random-failure point is of the same order as the Molloy-Reed estimate (0.993, against 0.907 for the equivalent ER graph). Simultaneous and adaptive degree attacks reach the same critical fraction.

As a counterfactual, the notebook builds a graph with the same number of nodes and almost the same mean degree (10.64 against 10.73), but with the bimodal degree sequence that maximises the critical fraction. It holds up much longer under the same attacks, so the fragility comes from how the degrees are distributed rather than from how many links exist.

### Graph Learning Extensions

- Spectral embedding (32 dimensions) plus logistic regression recovers the six largest Louvain communities with 0.897 accuracy, against a 0.214 majority-class baseline.
- Split conformal prediction reaches 0.905 empirical coverage for a 0.90 target, with an average prediction-set size of 1.01.
- Game-theoretic centrality uses the Michalak et al. coverage game. Its Spearman correlation with degree is only 0.38, so it ranks nodes differently from the classic centralities.
- A companion SI worm simulation with five immunisation strategies (random, degree, betweenness, Shapley, acquaintance) is summarised in the presentation: vaccinating 10% of the hubs cuts the reachable fraction from 85% to 14%. This experiment is not part of the notebook.

---

## Why No Single Null Model Is Enough

The real graph is compared with Erdos-Renyi, Barabasi-Albert, Watts-Strogatz, and Configuration Model networks.

- The Configuration Model reproduces the heavy-tailed degree sequence, but not the observed clustering.
- Watts-Strogatz reproduces high clustering and short paths, but has a narrow degree distribution with no comparable hubs.
- Erdos-Renyi and Barabasi-Albert miss important higher-order structure.

The Enron graph combines heavy tails, triadic closure, modularity, short paths, and disassortative mixing. That combination, rather than any single metric, is the structural signature of the real communication network.

---

## Analysis Pipeline

1. Load the SNAP edge list and isolate the giant component.
2. Measure connectivity, degree statistics, clustering, distance, assortativity, and centrality.
3. Fit and compare power law, exponential, lognormal, and truncated power-law tails.
4. Compare the empirical graph with four synthetic null models.
5. Simulate random failures and simultaneous, adaptive, and betweenness-targeted attacks, then compare with an optimally robust graph of the same size and mean degree.
6. Detect Louvain communities and export an interactive hub subgraph.
7. Add game-theoretic centrality, spectral graph embeddings, node classification, and conformal prediction.

---

## Interactive Visualisation

`Enron_hub_viz.html` is a self-contained HTML5 canvas visualisation of the induced hub subgraph (nodes with degree above 100):

- 540 nodes and 17,782 edges;
- colour encodes Louvain community;
- node size scales with degree;
- pan, zoom, hover details, and neighbour highlighting;
- no external JavaScript dependencies, so it works offline.

Download the file and open it directly in a browser. Running the notebook writes a fresh copy as `Enron_hub_viz_generated.html`, so the committed file is never overwritten.

---

## Tech Stack

- **Language**: Python 3.10+ (Jupyter notebook)
- **Networks**: NetworkX, including Louvain community detection
- **Statistics**: powerlaw for tail fitting, statsmodels, SciPy, NumPy
- **Machine learning**: scikit-learn for the embedding classifier and conformal prediction
- **Visualisation**: matplotlib, plus a self-contained HTML5 canvas page generated by the notebook

---

## Project Structure

```text
.
|-- Enron_Analysis.ipynb          # Complete analysis with saved outputs
|-- Enron_hub_viz.html            # Stand-alone interactive hub visualisation
|-- Enron_CNA_Presentation.pptx   # Project presentation, including the SI and immunisation results
|-- email-Enron.txt/
|   `-- Email-Enron.txt           # SNAP edge list
|-- requirements.txt
`-- README.md
```

---

## Installation

Python 3.10 or newer is recommended.

```bash
git clone https://github.com/francesco-univr/enron-email-network-analysis.git
cd enron-email-network-analysis
python -m venv .venv
```

Activate the environment (`source .venv/bin/activate` on Linux and macOS, `.venv\Scripts\activate` on Windows), then install the dependencies:

```bash
pip install -r requirements.txt
```

## Reproducing the Analysis

The dataset is already stored at the relative path expected by the notebook.

```bash
jupyter lab Enron_Analysis.ipynb
```

Run all cells from top to bottom. The saved notebook was executed end-to-end with a fixed seed (42) where stochastic algorithms are used. A complete run takes approximately 8-10 minutes on a standard laptop.

---

## Dataset

The project uses the [SNAP Enron email network](https://snap.stanford.edu/data/email-Enron.html), derived from email data made public during the Federal Energy Regulatory Commission investigation.

The SNAP file stores each undirected relationship in both directions. Loading it as a NetworkX `Graph` collapses 367,662 directed entries into 183,831 unique undirected edges.

---

## Key Learnings

1. **A heavy tail is not automatically a power law.** The truncated power law beat both the pure power law (log-likelihood ratio R = -81.8) and the lognormal (R = -38.9), with p < 0.001 in both tests. Fitting the tail with proper model comparison, as Clauset et al. recommend, supports a heavy tail with a cutoff rather than a scale-free network.

2. **Efficiency and fragility come from the same nodes.** The hubs keep paths short and act as the main bridges (degree-betweenness correlation 0.79). Removing 13% of the nodes by degree breaks the network, while random failures need 93%. From a security point of view, the nodes to monitor and protect are the ones that make the organisation work.

3. **One null model is never enough.** Each synthetic model reproduced one property of Enron and missed another. Only the comparison against all four at once isolated what is specific to the real network.

---

## Limitations

- The graph is symmetrised and unweighted, so sender-recipient direction, message frequency, and time are not modelled.
- Average distance is estimated from 200 source nodes; betweenness and diameter are approximated for computational tractability.
- Synthetic-model results are point estimates rather than averages over many independent graph realisations.
- The node-classification task is a transductive probe: labels come from Louvain communities on the same graph.
- Conformal coverage is empirical on one graph split; node dependence weakens the usual i.i.d. guarantee.
- The SI and immunisation results come from a companion experiment summarised in the presentation and are not reproducible from this repository.

---

## References

- J. Leskovec et al., *Graph Evolution: Densification and Shrinking Diameters*, ACM TKDD, 2007.
- A. Clauset, C. Shalizi, M. Newman, *Power-Law Distributions in Empirical Data*, SIAM Review, 2009.
- T. Michalak et al., *Efficient Computation of the Shapley Value for Game-Theoretic Network Centrality*, JAIR, 2013.
- M. Molloy, B. Reed, *A Critical Point for Random Graphs with a Given Degree Sequence*, Random Structures & Algorithms, 1995.
