<p align="center">
  <a href="https://github.com/JaggerTheBoss/forklana">
    <img alt="Forklana" src="forklana.png" width="250" />
  </a>
</p>

# Forklana

**Forklana** is a fork of [Solana](https://github.com/solana-labs/solana) with a **minimum 0.02 SOL transaction fee** designed to eradicate transaction bundles (MEV bundles). By enforcing a higher base fee, Forklana makes sandwich attacks, frontrunning, and other MEV bundle strategies economically unviable, creating a fairer network for all users.

## Key Differences from Solana

| | Solana | Forklana |
|---|---|---|
| **Minimum Swap Fee** | ~0.000005 SOL | **0.02 SOL** |
| **MEV Bundles** | Rampant | Economically unviable |
| **Sandwich Attacks** | Common | Eradicated |
| **Excess Fee Usage** | Burned (50%) | **Buyback & Burn** |

### Swap-Only Fee

The 0.02 SOL minimum fee **applies specifically to swap transactions** (token-to-token exchanges on DEXs). Standard transfers, account creation, staking, and other non-swap operations retain Solana's default fee structure. This targeted approach eliminates MEV bundles where they cause the most harm — on-chain swaps — without increasing costs for everyday transactions.

Bundlers rely on razor-thin margins across many swap transactions. A higher base fee on swaps destroys that model entirely, making sandwich attacks and frontrunning economically unviable.

### Buyback & Burn Mechanism

Any excess fees collected above the network's base operating cost are used to **buy back and burn the tokens that were used in the swap**. This creates a deflationary feedback loop:

1. User performs a swap and pays the 0.02 SOL fee.
2. The base network fee is distributed to validators as normal.
3. The **remaining excess fee** is routed to a protocol-owned buyback module.
4. The buyback module purchases the token(s) involved in the swap on the open market.
5. The purchased tokens are **permanently burned**, reducing circulating supply.

This mechanism directly benefits holders of any token traded on Forklana — the more a token is swapped, the more of it gets bought back and burned.

---

## Connecting to Forklana with Backpack Wallet

[Backpack](https://backpack.app/) is the recommended wallet for connecting to the Forklana network.

### Steps

1. **Install Backpack** — Download from [backpack.app](https://backpack.app/) and create or import a wallet.
2. **Open Settings** — Click the settings icon in the top-right corner of the Backpack wallet.
3. **Navigate to Solana settings** — Go to **Preferences > Solana**.
4. **Set a custom RPC** — Select **RPC Connection > Custom**.
5. **Enter the Forklana RPC URL** — Paste your Forklana validator's RPC endpoint into the field and click **Switch**.
6. **Confirm** — A checkmark will appear confirming the connection. Your Backpack wallet is now connected to Forklana.

> **Note:** You will need the RPC URL of a running Forklana validator node. See the [Starting a local testnet](#starting-a-local-testnet) section below for how to run your own node.

---

# Building

## **1. Install rustc, cargo and rustfmt.**

```bash
$ curl https://sh.rustup.rs -sSf | sh
$ source $HOME/.cargo/env
$ rustup component add rustfmt
```

When building the master branch, please make sure you are using the latest stable rust version by running:

```bash
$ rustup update
```

When building a specific release branch, you should check the rust version in `ci/rust-version.sh` and if necessary, install that version by running:

```bash
$ rustup install VERSION
```

Note that if this is not the latest rust version on your machine, cargo commands may require an [override](https://rust-lang.github.io/rustup/overrides.html) in order to use the correct version.

On Linux systems you may need to install libssl-dev, pkg-config, zlib1g-dev, protobuf etc.

On Ubuntu:

```bash
$ sudo apt-get update
$ sudo apt-get install libssl-dev libudev-dev pkg-config zlib1g-dev llvm clang cmake make libprotobuf-dev protobuf-compiler
```

On Fedora:

```bash
$ sudo dnf install openssl-devel systemd-devel pkg-config zlib-devel llvm clang cmake make protobuf-devel protobuf-compiler perl-core
```

## **2. Download the source code.**

```bash
$ git clone https://github.com/JaggerTheBoss/forklana.git
$ cd forklana
```

## **3. Build.**

```bash
$ ./cargo build
```

# Testing

**Run the test suite:**

```bash
$ ./cargo test
```

### Starting a local testnet

After building, start a local Forklana validator with a single command:

```bash
$ solana-test-validator
```

This launches a local node with RPC available at:

```
http://localhost:8899
```

You can verify it's running:

```bash
$ solana --url http://localhost:8899 cluster-version
```

Airdrop SOL to yourself for testing:

```bash
$ solana --url http://localhost:8899 airdrop 100
```

Then paste `http://localhost:8899` as the custom RPC in Backpack Wallet to connect.

### Running a public RPC node

To make your Forklana node accessible to others:

1. **Set up a server** with at least 12 CPU cores, 128GB RAM, and an NVMe SSD.
2. **Clone and build** Forklana on the server (see [Building](#building) above).
3. **Run the validator** — it will expose RPC on port `8899` by default.
4. **Open port 8899** in your firewall.
5. Your public RPC URL is `http://<your-server-ip>:8899`.
6. Optionally place it behind a reverse proxy with a domain (e.g. `https://rpc.forklana.com`).

# Benchmarking

First, install the nightly build of rustc. `cargo bench` requires the use of the
unstable features only available in the nightly build.

```bash
$ rustup install nightly
```

Run the benchmarks:

```bash
$ cargo +nightly bench
```

# Release Process

The release process for this project is described [here](RELEASE.md).

# Code coverage

To generate code coverage statistics:

```bash
$ scripts/coverage.sh
$ open target/cov/lcov-local/index.html
```

Why coverage? While most see coverage as a code quality metric, we see it primarily as a developer
productivity metric. When a developer makes a change to the codebase, presumably it's a _solution_ to
some problem. Our unit-test suite is how we encode the set of _problems_ the codebase solves. Running
the test suite should indicate that your change didn't _infringe_ on anyone else's solutions. Adding a
test _protects_ your solution from future changes. Say you don't understand why a line of code exists,
try deleting it and running the unit-tests. The nearest test failure should tell you what problem
was solved by that code. If no test fails, go ahead and submit a Pull Request that asks, "what
problem is solved by this code?" On the other hand, if a test does fail and you can think of a
better way to solve the same problem, a Pull Request with your solution would most certainly be
welcome! Likewise, if rewriting a test can better communicate what code it's protecting, please
send us that patch!

# Disclaimer

All claims, content, designs, algorithms, estimates, roadmaps,
specifications, and performance measurements described in this project
are done with the Solana Labs, Inc. (“SL”) good faith efforts. It is up to
the reader to check and validate their accuracy and truthfulness.
Furthermore, nothing in this project constitutes a solicitation for
investment.

Any content produced by SL or developer resources that SL provides are
for educational and inspirational purposes only. SL does not encourage,
induce or sanction the deployment, integration or use of any such
applications (including the code comprising the Solana blockchain
protocol) in violation of applicable laws or regulations and hereby
prohibits any such deployment, integration or use. This includes the use of
any such applications by the reader (a) in violation of export control
or sanctions laws of the United States or any other applicable
jurisdiction, (b) if the reader is located in or ordinarily resident in
a country or territory subject to comprehensive sanctions administered
by the U.S. Office of Foreign Assets Control (OFAC), or (c) if the
reader is or is working on behalf of a Specially Designated National
(SDN) or a person subject to similar blocking or denied party
prohibitions.

The reader should be aware that U.S. export control and sanctions laws prohibit
U.S. persons (and other persons that are subject to such laws) from transacting
with persons in certain countries and territories or that are on the SDN list.
Accordingly, there is a risk to individuals that other persons using any of the
code contained in this repo, or a derivation thereof, may be sanctioned persons
and that transactions with such persons would be a violation of U.S. export
controls and sanctions law.
