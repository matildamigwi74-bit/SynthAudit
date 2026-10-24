# SynAudit

**Real-time synthetic data quality and privacy monitoring**

Course: Open-Source Technologies and Stream Mining
Supervisor: Lagha Ahmed Fouad
Team: Alina Amanbekova, Matilda Migwi, Diana Grigoryan, Grace Acholla Mbira, Sharon Jepngetich

---

## What this does

Synthetic data lets you share and train models on sensitive records, but it can fail in two
ways: it may stop matching the real data, or it may leak information about real people. Most
tools check a finished dataset once. SynAudit checks synthetic data continuously, as a stream,
so problems show up as soon as they appear.

A producer replays real and synthetic records into Kafka. Four services consume those streams
and measure quality, drift, machine-learning utility and privacy. Results are written to a
time-series store and shown live on a dashboard, with alerts. Everything runs under Docker Compose.

## Architecture

```
  datasets ──► synthetic generation (SDV) ──┐
     │                                      │
     └──────────────────────────────────────┤
                                            ▼
                                     Kafka producer
                                            │
                        ┌───────────────────┴───────────────────┐
                        ▼                                       ▼
                 real-stream topics                  synthetic-stream topics
                        │                                       │
        ┌───────────┬───┴───────┬───────────────┬───────────────┘
        ▼           ▼           ▼               ▼
     quality      drift     ML utility       privacy
        │           │           │               │
        └───────────┴─────┬─────┴───────────────┘
                          ▼
                   time-series store
                          │
                          ▼
                     dashboard + alerts
```

## Parts

| Member | Part | Technology | Algorithms |
|---|---|---|---|
| Matilda | Data preparation and Kafka streaming | Kafka, Docker | Reservoir sampling, DGIM |
| Diana | Synthetic data generation and quality evaluation | SDV, SDMetrics | CluStream, DenStream |
| Grace | Stream processing and drift detection | SciPy, River | ADWIN, Page-Hinkley |
| Sharon | ML utility and privacy evaluation | scikit-learn | Hoeffding tree, Bloom filter |
| Alina | Storage, integration and monitoring | InfluxDB, Grafana | Count-Min Sketch, HyperLogLog |

Each member owns one part, with tasks tracked as issues. We build a simple end-to-end version
first, then add advanced methods. Each algorithm is implemented from its paper.

## The stream contract

Topic names, the record format, and the rules every service codes against are in
**[docs/stream-contract.md](docs/stream-contract.md)**. Read it before writing a consumer.
It is the interface between the ingestion layer and everything downstream, so changes to it
go through a pull request.

## Datasets

| Dataset | Rows | Features | Target |
|---|---|---|---|
| Adult (Census Income) | 48,842 | 14, mixed categorical and numeric | `income` (>50K) |
| Default of Credit Card Clients | 30,000 | 23, mostly numeric | `default_payment_next_month` |

Both are UCI datasets. Raw files are not committed; see `data/README.md` for download steps
once ingestion lands.

## Repository layout

```
docs/                 contracts and design notes
ingestion/            dataset preparation, Kafka producer, stream statistics
generation/           synthetic data generation and quality evaluation
drift/                stream processing and drift detection
utility_privacy/      ML utility and privacy evaluation
storage/              time-series storage, dashboards, integration
data/                 datasets (not committed)
docker/               Docker Compose and service definitions
```

## Getting started

Not yet runnable. Setup instructions arrive with the Docker Compose environment.

## Timeline

| Date | Milestone |
|---|---|
| Oct 20 | Architecture and work plan |
| Nov 12 | Working end-to-end system |
| Nov 26 | Experiments and final report |
| Early Dec | Project defence |
