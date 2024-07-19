# EpicChain Benchmark Framework

## Requirements

- **make**: Ensure `make` is installed on your system.
- **Docker**: Any version that includes Docker Compose V2. For installation, see the [Docker installation guide](https://docs.docker.com/compose/install/?_gl=1*uflyxz*_ga*MTE3MDkzODg4Ny4xNzA5MDM4MDA0*_ga_XJWPQMJYHQ*MTcxMzgwNTA0MC4yMC4xLjE3MTM4MDY5MDQuNjAuMC4w#installation-scenarios).
- **Docker Compose V2 Plugin**: If you’re using Linux, make sure the Docker Engine and Docker CLI are installed. Follow the [installation instructions](https://docs.docker.com/compose/install/?_gl=1*uflyxz*_ga*MTE3MDkzODg4Ny4xNzA5MDM4MDA0*_ga_XJWPQMJYHQ*MTcxMzgwNTA0MC4yMC4xLjE3MTM4MDY5MDQuNjAuMC4w#scenario-two-install-the-compose-plugin).
- **Golang 1.20+**: Ensure Golang version 1.20 or higher is installed.

## Repository Structure

- **.docker**: Contains Docker-related files.
  - **build**: Docker image build files.
  - **ir**: Contains IR Docker files.
  - **rpc**: Contains RPC and Benchmark Docker files.
- **.make**: Contains Makefile-specific files.
- **cmd**: Contains Benchmark source code.
  - **bench**: Source code for Benchmark commands.
  - **gen**: Source code for Transaction generator.
  - **internal**: Common code used in both the bench tool and the generator.
  - **go.mod**: Golang modules file.
  - **go.sum**: Golang modules summary file.
- **.gitignore**: Specifies files and directories to ignore in Git.
- **Makefile**: The main Makefile for managing build and benchmark processes.
- **README.md**: Documentation file.

## Usage Example (Local Benchmark + EpicChain Single Node)

1. **Build Benchmark Images and Binary File**:
    ```
    $ make build
    => Building Bench image registry.epicchain.org/epic-bench/epic-bench:bench
    sha256:yourimagehash
    => Building Go Node image registry.epicchain.org/epic-bench/epic-go:bench
    sha256:yourimagehash
    => Building Sharp Node image registry.epicchain.org/epic-bench/epic-sharp:bench
    sha256:yourimagehash
    => Building Bench binary file
    + export GOGC=off
    + GOGC=off
    + export CGO_ENABLED=0
    + CGO_ENABLED=0
    + go -C cmd build -v -o bin/bench -trimpath ./bench
    ```

2. **Run Benchmark Test**:
    ```
    $ make test
    ./runner.sh --validators 1 -d "GoSingle" -m wrk -w 30 -z 5m -t 30s
    make[1]: Entering directory '/path/to/epic-bench'
    => Stop environment
    => Generate configurations for single-node and multi-node networks from templates
    + cd ./cmd
    + go run ./config/ --go-template go.protocol.template.yml --go-db leveldb --sharp-template sharp.protocol.template.yml --sharp-db LevelDBStore
    creating: 3115231476/go.protocol.template.yml
    creating: 3115231476/sharp.protocol.template.yml
    make[1]: Leaving directory '/path/to/epic-bench'
    [+] Creating 3/1
     ✔ Network epic_go_network  Created                                                                                                                                                                            0.1s 
     ✔ Container ir-node-1     Created                                                                                                                                                                            0.1s 
     ✔ Container ir-healthy-1  Created                                                                                                                                                                            0.0s 
    [+] Running 2/2
     ✔ Container ir-node-1     Healthy                                                                                                                                                                            5.7s 
     ✔ Container ir-healthy-1  Started                                                                                                                                                                            0.2s 
    2024/04/22 17:03:33 Used [node:20331] rpc addresses
    2024/04/22 17:03:33 Run benchmark for GoSingle :: EPIC-GO
    2024/04/22 17:03:34 Read 3000000 txs from /dump.txs
    2024/04/22 17:03:36 CPU: 0.025%, Mem: 23.242MB
    2024/04/22 17:03:38 CPU: 0.286%, Mem: 23.469MB
    2024/04/22 17:03:40 CPU: 0.031%, Mem: 23.781MB
    2024/04/22 17:03:41 Done 6.858495874s
    2024/04/22 17:03:41 Init 30 workers / 5m0s time limit (3000000 txs will try to send)
    2024/04/22 17:03:41 Prepare chain for benchmark
    2024/04/22 17:03:41 Determined validators count: 1
    2024/04/22 17:03:41 Sending EPIC and GAS transfer tx
    2024/04/22 17:03:41 Contract hash: ceb508fc02abc2dc27228e21976699047bbbcce0
    2024/04/22 17:03:41 Sending contract deploy tx
    2024/04/22 17:03:41 Contract was persisted: false
    2024/04/22 17:03:42 Contract was persisted: true
    2024/04/22 17:03:42 fetch current block count
    2024/04/22 17:03:42 Waiting for an empty block to be processed
    2024/04/22 17:03:43 Started test from block = 17 at unix time = 1713805423759
    2024/04/22 17:03:44 empty block: 17
    2024/04/22 17:03:44 CPU: 37.295%, Mem: 38.734MB
    2024/04/22 17:03:45 #18: 13690 transactions in 1011 ms - 13541.048467 tps
    2024/04/22 17:03:46 CPU: 64.889%, Mem: 131.801MB
    2024/04/22 17:03:46 #19: 13440 transactions in 1045 ms - 12861.244019 tps
    2024/04/22 17:03:48 #20: 13625 transactions in 1036 ms - 13151.544402 tps
    2024/04/22 17:03:48 CPU: 64.515%, Mem: 141.691MB
    2024/04/22 17:03:49 #21: 14820 transactions in 1036 ms - 14305.019305 tps
    2024/04/22 17:03:50 #22: 12248 transactions in 1042 ms - 11754.318618 tps
    2024/04/22 17:03:50 CPU: 55.543%, Mem: 165.152MB

    ...

    2024/04/22 17:08:37 CPU: 84.711%, Mem: 703.641MB
    2024/04/22 17:08:38 #287: 10736 transactions in 1013 ms - 10598.223100 tps
    2024/04/22 17:08:39 #288: 8970 transactions in 1030 ms - 8708.737864 tps
    2024/04/22 17:08:39 CPU: 85.515%, Mem: 734.520MB
    2024/04/22 17:08:40 #289: 8333 transactions in 1032 ms - 8074.612403 tps
    2024/04/22 17:08:41 #290: 8100 transactions in 1029 ms - 7871.720117 tps
    2024/04/22 17:08:41 CPU: 82.350%, Mem: 802.832MB
    2024/04/22 17:08:42 #291: 8139 transactions in 1034 ms - 7871.373308 tps
    2024/04/22 17:08:43 #292: 6965 transactions in 1036 ms - 6722.972973 tps
    2024/04/22 17:08:43 CPU: 86.500%, Mem: 851.633MB
    2024/04/

22 17:08:44 #293: 6974 transactions in 1034 ms - 6757.689919 tps
    2024/04/22 17:08:44 CPU: 84.288%, Mem: 885.556MB
    2024/04/22 17:08:45 #294: 8097 transactions in 1032 ms - 7856.990174 tps
    ```

## Key Concepts

- **Benchmark Tool**: The `bench` tool is used to assess the performance of EpicChain under various conditions.
- **Transaction Generator**: This tool generates a specified number of transactions to simulate network activity during the benchmark.
- **Configuration Files**: Templates for different node configurations are used to set up the network environment.
- **Docker Containers**: Docker is used to create isolated environments for benchmarking, ensuring consistency and repeatability.

## Troubleshooting

- **Docker Issues**: Ensure Docker and Docker Compose are properly installed and running. Check the logs for any error messages related to Docker.
- **Benchmark Failures**: If the benchmark fails, review the configuration files and ensure that all dependencies are correctly set up.

## Contacts

For further assistance or questions regarding the EpicChain Benchmark Framework, contact:

- **Support Email**: support@epic-chain.org
- **Technical Lead**: xmoohad Ibrahim - xmoohad@epic-chain.org
