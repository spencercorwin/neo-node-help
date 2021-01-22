## Start C# Neo 3.x Node

- Install C# [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) runtime version 3.0.0
  - To check SDK installations run `dotnet --list-sdks`
  - To check Core installations run `dotnet --list-runtimes`
  - Click [here](https://docs.microsoft.com/en-us/dotnet/core/install/how-to-detect-installed-versions?pivots=os-macos) for more info on installed versions and locations
  - Note that you only need the correct .NET Core runtime version installed
- Clone the [neo-node repo](https://github.com/neo-project/neo-node)
- `cd` into `neo-cli` directory (`neo-node/neo-cli`)
- Run `dotnet restore`
- Run `dotnet publish -c Release`
- `cd` into `neo-node/neo-cli/bin/Release/netcoreapp3.0`
- Create a `Plugins` folder (`mkdir Plugins`)
- Download and unzip a `libleveldb.dylib` from [here](https://github.com/neo-ngd/leveldb/releases)
- Copy and paste (or move) the `libleveldb.dylib` from wherever you downloaded it to `neo-node/neo-cli/bin/Release/netcoreapp3.0`
- Now you need to install the `LevelDBStore` plugin
- Clone the [neo-modules repo](https://github.com/neo-project/neo-modules)
- `cd` into `neo-modules/src/LevelDBStore`
- Run `dotnet publish -c Release` inside `src/LevelDBStore`
- That should produce a bunch of files inside `src/LevelDBStore/bin/Release/netstandard2.1`
- Copy and paste all those files inside `bin/Release/netstandard2.1` into the `neo-node/neo-cli/bin/Release/netcoreapp3.0/Plugins` folder that you created earlier
- This process can be used for other plugins that are built from source. But there is weird overlap in some of the files copied from each built plugin. So what you can do it just install new plugins (after LevelDBStore plugin) with the CLI command and then
  just change the specific `.dll` file.
- Run `dotnet neo-cli.dll` inside `neo-node/neo-cli/bin/Release/netcoreapp3.0`
- This should start the `neo>` REPL and you know you've installed everything correctly
- Now is when you can install plugins like for an RPC server or for Nep5 token tracking. Inside the `neo>` REPL run:
  - `install RpcServer`
  - Same format for whatever other plugins you need. Check `neo-modules` repo for available plugins
- You may get a 404 error for plugins that you know exist. To fix this you need to check the `PluginURL` that is specified in the `config.json` and make sure it's pointed at the right place. For example, at this writing it needed to be changed from its default to `https://github.com/neo-project/neo-modules/releases/download/v{1}-00/{0}.zip`.
- Then restart the REPL by running `neo> exit`, then re-run `dotnet neo-cli.dll`
- Run `neo> help` to see available `neo-cli` commands, and check the docs [here](https://docs.neo.org/docs/en-us/node/cli/cli.html)
- The `neo-node/neo-cli` repo has three config files in it: `config.json`, `config.mainnet.json`, `config.testnet.json`
- To use the test net just copy the contents of `config.testnet.json` into `config.json`

## How to Run C# Neo3 PrivateNet

- After you've setup the local Neo3 C# node as specified above you just need to change the `config.json` and `protocol.json` files correctly to get a private network running.
- `procol.json`:
  - SeedList should be an empty array
  - ValidatorsCount should be 1
  - MillisecondsPerBlock can be whatever, probably make it 5000
  - Magic can likely be integer in specific range
  - StanbyCommittee should just a single public key for an address that you control (ie. have the private key to)
- `config.json`:
  - Change ConsoleOutput to true to get console logs
  - Change Logger.Active to true for more logs (not sure what exactly this changes/adds)
  - Change P2P.Port to 10003 and P2P.WsPort to 10004 (not actually sure this is necessary)
  - Change PluginURL if necessary
  - Change UnlockWallet.StartConsensus to true and UnlockWallet.IsActive to true (important)
- Add a wallet path to UnlockWallet.Path, like `/Users/spencercorwin/Desktop/b.json` and the password for the wallet
- Now, when you run `dotnet neo-cli.dll` you should see console logs from logs you put in source code, you should see the network add blocks at the specified rate, and if you run `show state` in the REPL you should see it adding blocks and should have no seeds.
- Assuming the RpcServer plugin is installed the default localhost port should be 10332, which can likely be set in config.
- Blockchain storage location can also be set but defaults to the location that the command is run (`neo-node/neo-cli/bin/Release/netcoreapp3.0`). Log files are also created/stored here
- The `neo-node/neo-cli` repo has three config files in it: `config.json`, `config.mainnet.json`, `config.testnet.json`
- To use the test net just copy the contents of `config.testnet.json` into `config.json`

## How to Create a Wallet for Use in PrivateNet

- To get a new wallet you can just use the Neo CLI. Run the CLI like specified above, before or after making all the changes that you want, and just run `wallet create <path/to/new/json/file>`. That will create the new JSON file and should print out your script hash and public key. Make sure you copy paste that public key to the `StandbyCommittee` array, as specified above.
- To get the private key for your address just run `export key <path/to/export/to>` and enter your password. That will produce your WIF key to the file (Not private key. WIF has to be converted to private key if necessary).

## How to Bootstrap the Node Sync

- The instructions in Neo docs [here](https://docs.neo.org/docs/en-us/node/syncblocks.html) are easy enough to follow
- Download and unzip the chain file (`chain.acc`) then move to `neo-node/neo-cli/bin/Release/netcoreapp2.1` (or `netcoreapp3.0`)
- Start the `neo>` CLI REPL with `dotnet neo-cli.dll` and then run `neo> show state`. You should see `block: 0/<blockheight>/<blockheight> connected: 0 unconnected: 0`
  and you should see `<blockheight>` move up quickly. It should take a few hours to sync (depending on blockchain height)
