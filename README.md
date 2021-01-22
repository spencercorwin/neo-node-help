# How to Start the Official C# Node on macOS

These are step-by-step instructions for how to start the official C# Neo node from source on macOS.

For the official docs, start [here](https://docs.neo.org/docs/en-us/node/cli/setup.html)

## Start Neo C# 2.x Node From Source

- Install C# [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) runtime version 2.1.0
  - To check SDK installations run `dotnet --list-sdks`
  - To check Core installations run `dotnet --list-runtimes`
  - Click [here](https://docs.microsoft.com/en-us/dotnet/core/install/how-to-detect-installed-versions?pivots=os-macos) for more info on installed versions and locations
  - Note that you only need the correct .NET Core runtime version installed
- Clone the [neo-node repo](https://github.com/neo-project/neo-node)
- Checkout `master-2.x` branch
- `cd` into `neo-cli` directory (`neo-node/neo-cli`)
- To build from Neo and NeoVM source you need to edit the `neo-cli.csproj`
- Comment out the `PackageReference` for Neo and add this line: `<ProjectReference Include="/Users/spencercorwin/neo-2.x/neo/neo.csproj" />` (or whatever the path is to the `csproj` for the Neo repo on your machine)
- You should do the same thing in the Neo repo's `neo.csproj` with the Neo VM package. Comment out the `PackageReference` for NeoVM and replace it with `<ProjectReference Include="/Users/spencercorwin/neo-vm-2.x/src/neo-vm/neo-vm.csproj" />` (or path to NeoVM repo `csproj`)
- Doing this also requires that you put this line in the `neo.csproj` and the `neo-vm.csproj`:

```
  <PackageReference Include="Microsoft.NETFramework.ReferenceAssemblies" Version="1.0.0-preview.2">
    <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
    <PrivateAssets>all</PrivateAssets>
  </PackageReference>
```

- If you're trying to run a local private net that you can control you'll need to edit the `protocol.json` and `config.json` files and create a wallet to use
- To keep this part simple just use the wallet at `./a.json`, use the config at `./config.json` and the protocol at `./protocol.json`. If you want to read more about these things and why things are set to what they are see `./Neo3.md`
- Run `dotnet restore` in `neo-node/neo-cli`
- Run `dotnet publish -c Release` in `neo-node/neo-cli`
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
- Now, when you run `dotnet neo-cli.dll` you should see console logs from logs you put in source code, you should see the network add blocks at the specified rate, and if you run `show state` in the REPL you should see it adding blocks and should have no seeds.
- Assuming the RpcServer plugin is installed the default localhost port should be 10332, which can likely be set in config.
- Blockchain storage location can also be set but defaults to the location that the command is run (`neo-node/neo-cli/bin/Release/netcoreapp3.0`). Log files are also created/stored here

## Claiming GAS on Local Private Network

- Now in order to do things like deploy a contract you'll need to get some GAS in your wallet
- The wallet at `a.json` should have 100000000 NEO in it. Run `list asset` to see your wallet balances
- Run `show gas` to see the GAS available to claim. `unavailable` should show some GAS (more is added each block)
- You may need to wait for a few blocks in order to get the necessary GAS. To speed it up just decrease block time to 1 in the protocol JSON file.
- To make that GAS "available" you need to send NEO to yourself. By running the command `send NEO ANKvkjEEzyFxinptiat1cNpPi9bR5QjMvQ 1` or `send <asset> <to-address> <amount>`
- Now if you run `show gas` you should have GAS available to claim now
- Claim the GAS by running `claim gas all`
- Now if you run `list asset` you should see more GAS in your balance, which you can use to do things like deploy a contract

## Deploy A Contract To Your Local Private Network

- Compile your contract with the NEO•ONE `compile` command: `yarn neo-one compile --avm`
- This will output your contract AVM files (`.avm`) to the `compiled` folder (or wherever the config points to)
- To deploy this using the Neo CLI run `deploy /Users/spencercorwin/neo-one-helloworld-demo/neo-one/compiled/HelloWorld.avm 0710 05 true false false hello 1.0.0 spencercorwin spencer@cool.org hello` (or `deploy <avmFilePath> <paramTypes> <returnTypeHexString> <hasStorage (true|false)> <hasDynamicInvoke (true|false)> <isPayable (true|false) <contractName> <contractVersion> <contractAuthor> <contractEmail> <contractDescription>`)
- You need 500 GAS to do this successfully (it's probably possible to edit this number, but not sure how)
- Doing this should return a contract hash like this: `0x9e6751a06a7e3bd17114fc8344e70118a96dc3bb` which you should copy and prepare to use for invoking your contract
- If you're having problems you can confirm your contract made it into blockchain storage by using Postman to make an RPC call to your local node using the `getcontractstate` method and the scripthash as a param. If it returns the contract state then you should be able to invoke `deploy`
- If it's been successfully deployed you still need to invoke the `deploy` method on the contract by running `invoke <scripthash> deploy`, which should return a `HALT` state
- Now you can invoke your contract methods the same with with `invoke <scripthash> <method> <args>`
- If you need to retry deployment or same contract just stop node, delete node storage then try again
