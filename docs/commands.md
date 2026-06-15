# Command Reference

`node.sh` is the single entry point for every operation on the node. Commands come in two forms:

```bash
node.sh <command>           # acts on the whole node (the active profile)
node.sh <chain> <command>   # acts on one chain
```

Run `node.sh` with no arguments (or `node.sh help`) for the grouped, in-terminal version of this reference.

## Daily operations

The day-to-day commands are built to be easy to read. A single `summary` view shows each chain with its state, height, peers, and sync in one table. `health` returns a proper success or failure exit code, so it drops straight into alerts and monitoring. Logs are easier to follow, and status data can be emitted as JSON for dashboards.

| Command | What it does |
|---|---|
| `node.sh start` | Start every chain in the active profile (background). |
| `node.sh stop` | Stop every chain in the profile (bounded wait, then a clean SIGKILL if needed). |
| `node.sh summary` | One row per chain: state, height, peers, and sync. The at-a-glance fleet view. |
| `node.sh status` | The full labeled status block for each chain in the profile. Add `--verbose` for every chain and field. |
| `node.sh health` | One-line verdict. Exit code is `0` when everything is healthy, non-zero otherwise, so it is cron- and alert-friendly. |
| `node.sh logs [chain] [-f]` | Tail the most recent log (defaults to the main chain). `-f` follows it live. |
| `node.sh restart` | Restart the profile's chains one at a time. ELA is skipped unless you pass `--force` (restarting it would interrupt council consensus). |
| `node.sh version` | Tool version plus each chain's binary version. |

### Machine output for dashboards

| Command | What it does |
|---|---|
| `node.sh summary --json` | The fleet table as JSON, one object per chain. |
| `node.sh <chain> status --json` | A single chain's status as JSON. |
| `node.sh ... --no-color` | Disable ANSI color (also honored via the `NO_COLOR` environment variable and on non-TTY output). |

## First-time setup

Install the dependencies first, then initialize. Opening ports, swap, and autostart are separate, explicit steps so nothing surprising happens on a fresh box.

```bash
sudo apt-get install -y jq lsof apache2-utils curl openssl
```

| Command | What it does |
|---|---|
| `node.sh init` | Initialize the node: download the chain binaries and create the keystores. Resumable - an interrupted run adopts an intact keystore instead of starting over. |
| `node.sh setup` | The same initialization as `init`, followed by a guided "next steps" summary. |
| `node.sh profile [set <name>]` | Show or set what this node runs: `mainchain` (main chain only) or `full` (the full cross-chain stack). |
| `node.sh swap` | Optional. Add 16 GB of swap headroom for the initial sync (recovers an inactive swapfile if one exists). |
| `node.sh firewall` | Open only the peer and consensus ports. Detects your SSH port and asks before enabling `ufw`, so it never locks you out. RPC stays on loopback. |
| `node.sh set_cron` | Enable autostart on reboot and scheduled log compression. |
| `node.sh harden` | Close public access to the RPC, WebSocket, oracle, and arbiter RPC ports, and report any chain that still needs a restart to rebind. Restarts nothing. |
| `node.sh reward [set 0x<addr>]` | Show or set a cold mining reward address for the EVM side chains in one step. |
| `node.sh monitor <url>` | Enroll the node with a read-only monitor over an outbound push. No RPC password and no open port. Run `node.sh monitor` to check it, `node.sh monitor off` to stop. |

## Managing the node

| Command | What it does |
|---|---|
| `node.sh update` | Update the chain binaries in the active profile. |
| `node.sh migrate [--dry-run \| --apply]` | Move an existing node onto this tool. `--dry-run` is read-only and changes nothing; the default run preserves the keystore, chain data, and config and writes rollback snapshots; `--apply` rebinds stale side chains to `127.0.0.1` one at a time and never restarts ELA. |
| `node.sh uninstall` | Stop everything, back up the keystore, and remove the installation after a typed confirmation. |
| `node.sh update_script` | Update `node.sh` itself: download from master, verify the published checksum, syntax-check it, and re-apply hardening. Reports "already up to date" when nothing changed. |
| `node.sh set_path` | Add `node.sh` to the shell `PATH`. |

## Per-chain commands

Target a single chain with `node.sh <chain> <command>`:

```bash
node.sh esc status        # status of the ESC side chain only
node.sh ela logs -f       # follow the main chain log
node.sh eid restart       # restart EID
```

Every chain accepts:

```
start   stop   restart   status [--json]   health   logs [-f]
client   rpc   init   update   version
```

Run `node.sh <chain>` with no command to print that chain's full command list.

**Chains:** `ela` (main chain), the EVM side chains `esc`, `eid`, `pg`, their cross-chain oracles (`esc-oracle`, `eid-oracle`, `pg-oracle`), and the `arbiter`. Which chains are active depends on the profile (`mainchain` or `full`).

### Main-chain governance (ELA)

The main chain adds the BPoS and CRC governance commands, for example `node.sh ela register-bpos`, `vote-bpos`, `stake-bpos`, `register-crc`. See the [BPoS Setup](bpos-setup.md) and [CRC Setup](crc-setup.md) guides for the full workflows.

## Aliases and flags

| | |
|---|---|
| Verb aliases | `up` = `start`, `down` = `stop`, `ps` = `summary`, `rpc` = `jsonrpc`. Kebab-case is accepted (`register-bpos` = `register_bpos`). |
| `--profile <mainchain\|full>` | Override the active profile for a single command. |
| `--no-color` / `NO_COLOR` | Plain output with no ANSI color. |
| `--verbose` / `-v` (on `status`) | Show every chain and every field. |
| `--force` (on `restart`) | Include ELA in a restart (otherwise skipped to protect consensus). |

## Maintenance

| Command | What it does |
|---|---|
| `node.sh update_script` | Self-update the script (checksum-verified, see above). |
| `node.sh set_path` | Install `node.sh` onto the shell `PATH`. |

For the security defaults and the full port table, see [Security](../SECURITY.md). For installing from scratch, see [Quick Setup](quick-setup.md).
