# How to Start the Official C# Node on macOS

These are step-by-step instructions for how to start the official C# Neo node from source on macOS.

For the official docs, start [here](https://docs.neo.org/docs/en-us/node/cli/setup.html)

## Start C# Neo 2.x Node

- Install C# [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) runtime version 2.1.0
  - To check SDK installations run `dotnet --list-sdks`
  - To check Core installations run `dotnet --list-runtimes`
  - Click [here](https://docs.microsoft.com/en-us/dotnet/core/install/how-to-detect-installed-versions?pivots=os-macos) for more info on installed versions and locations
  - Note that you only need the correct .NET Core runtime version installed
- Clone the [neo-node repo](https://github.com/neo-project/neo-node)
- Checkout `master-2.x` branch
- `cd` into `neo-cli` directory (`neo-node/neo-cli`)
- Run `dotnet publish -c Release`
- `cd` into `neo-node/neo-cli/bin/Release/netcoreapp2.1`
- Create a `Plugins` folder (`mkdir Plugins`)
- Download and unzip a `libleveldb.dylib` from [here](https://github.com/neo-ngd/leveldb/releases)
- Copy and paste (or move) the `libleveldb.dylib` from wherever you downloaded it to `neo-node/neo-cli/bin/Release/netcoreapp2.1`
- Run `dotnet neo-cli.dll` inside `neo-node/neo-cli/bin/Release/netcoreapp2.1` (add `--rpc` for the node to accept RPC calls)
- This should start the `neo>` REPL and you know you've installed everything correctly
- Now you need to install several plugins. Inside the `neo>` REPL run:
  - `install ImportBlocks`
  - `install RpcSystemAssetTracker`
  - `install SimplePolicy`
- Then restart the REPL by running `neo> exit`, then re-run `dotnet neo-cli.dll`
- Run `neo> help` to see available `neo-cli` commands, and check the docs [here](https://docs.neo.org/docs/en-us/node/cli/cli.html)
- The `neo-node/neo-cli` repo has three config files in it: `config.json`, `config.mainnet.json`, `config.testnet.json`
- To use the test net just copy the contents of `config.testnet.json` into `config.json`

## Start C# Neo 3.x Node

- Install C# [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) runtime version 3.0.0
  - To check SDK installations run `dotnet --list-sdks`
  - To check Core installations run `dotnet --list-runtimes`
  - Click [here](https://docs.microsoft.com/en-us/dotnet/core/install/how-to-detect-installed-versions?pivots=os-macos) for more info on installed versions and locations
  - Note that you only need the correct .NET Core runtime version installed
- Clone the [neo-node repo](https://github.com/neo-project/neo-node)
- `cd` into `neo-cli` directory (`neo-node/neo-cli`)
- Run `dotnet publish -c Release`
- `cd` into `neo-node/neo-cli/bin/Release/netcoreapp3.0`
- Create a `Plugins` folder (`mkdir Plugins`)
- Download and unzip a `libleveldb.dylib` from [here](https://github.com/neo-ngd/leveldb/releases)
- Copy and paste (or move) the `libleveldb.dylib` from wherever you downloaded it to `neo-node/neo-cli/bin/Release/netcoreapp3.0`
- Now you need to install the `LevelDBStore` plugin
- Clone the [neo-modules repo](https://github.com/neo-project/neo-modules)
- `cd` into `neo-modules/src/LevelDBStore`
- Run `dotnet publish -c Release` inside `src/LevelDBStore`
- That should produce a bunch of files inside `src/LevelDBStore/bin/Release/netstandard3.0`
- Copy and paste all those files inside `bin/Release/netstandard3.0` into the `neo-node/neo-cli/bin/Release/netcoreapp3.0/Plugins` folder that you created earlier
- Run `dotnet neo-cli.dll` inside `neo-node/neo-cli/bin/Release/netcoreapp3.0` (add `--rpc` for the node to accept RPC calls)
- This should start the `neo>` REPL and you know you've installed everything correctly
- Now you need to install several plugins. Inside the `neo>` REPL run:
  - `install ImportBlocks`
  - `install RpcSystemAssetTracker`
  - `install SimplePolicy`
- Then restart the REPL by running `neo> exit`, then re-run `dotnet neo-cli.dll`
- Run `neo> help` to see available `neo-cli` commands, and check the docs [here](https://docs.neo.org/docs/en-us/node/cli/cli.html)
- The `neo-node/neo-cli` repo has three config files in it: `config.json`, `config.mainnet.json`, `config.testnet.json`
- To use the test net just copy the contents of `config.testnet.json` into `config.json`

## How to Bootstrap the Node Sync

- The instructions in Neo docs [here](https://docs.neo.org/docs/en-us/node/syncblocks.html) are easy enough to follow
- Download and unzip the chain file (`chain.acc`) then move to `neo-node/neo-cli/bin/Release/netcoreapp2.1` (or `netcoreapp3.0`)
- Start the `neo>` CLI REPL with `dotnet neo-cli.dll` and then run `neo> show state`. You should see `block: 0/<blockheight>/<blockheight> connected: 0 unconnected: 0`
  and you should see `<blockheight>` move up quickly. It should take a few hours to sync (depending on blockchain height)
