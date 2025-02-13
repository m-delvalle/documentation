# IOTA Go Library

This is the **official** Go client library, which allows you to do the following:
- Create, import, export, and manage accounts
- Send transactions
- Promote and reattach pending transactions
- Request deposits into conditional deposit addresses (CDA)
- Listen to events
- Extend the library functionality with plug-ins
- Interact with an IRI node

To learn more about how the library works:

- See how you can [work with accounts](root://iota-go/0.1/how-to-guides/create-account.md).
- See how you can [send and receive](root://iota-go/0.1/how-to-guides/create-and-manage-cda.md) transactions.

Get the library at the [IOTA Go GitHub repository](https://github.com/iotaledger/iota.go).

:::warning:Beta software
The client libraries are currently in beta. Their use in production is not supported.
:::

Please report any issues in our [issue tracker](https://github.com/iotaledger/iota.go/issues/new).

## Audience

This documentation is designed for people who are familiar with the Go programming language and object-oriented programming concepts. You should also be familiar with basic IOTA concepts such as [address reuse](root://dev-essentials/0.1/concepts/addresses-and-signatures.md#address-reuse), [bundles, and transactions](root://dev-essentials/0.1/concepts/bundles-and-transactions.md).

This guide is designed to let you quickly start exploring and developing applications with IOTA.

## Prerequisites

To download the IOTA Go client library and its dependencies, we recommend that you use [vgo modules](https://github.com/golang/go/wiki/Modules) 
(since Go 1.11) to manage dependencies in your project.

## Download the library

To download the IOTA Go client library and its dependencies, do the following:

1. In any directory outside of $GOPATH, create a directory for your project and initialize it. Change the `<your-module-path>` placeholder to the path of your project such as `github.com/me/awesome-project`.

	```bash
	go mod init <your-module-path>
	```

2. Download the library

	```bash
	go get github.com/iotaledger/iota.go/api
	```

This command downloads the latest version of the IOTA Go client library and writes the version into the `go.mod` file.

## Get started

After you've [downloaded the library](#download-the-library), you can connect to an IRI node to send transactions to it and interact with the ledger.

1. To connect to a local IRI node, do the following:

	```go
	package main

	import (
	    . "github.com/iotaledger/iota.go/api"
	    "fmt"
	)

	var endpoint = "<node-url>"

	func main() {
		// compose a new API instance
		api, err := ComposeAPI(HTTPClientSettings{URI: endpoint})
		must(err)

		nodeInfo, err := api.GetNodeInfo()
		must(err)

		fmt.Println("latest milestone index:", nodeInfo.LatestMilestoneIndex)
	}

	func must(err error) {
		if err != nil {
			panic(err)
		}
	}
	```
## API reference

For details on all available API methods, see the [API folder](https://github.com/iotaledger/iota.go/tree/master/api).
	
## Examples

As well as the following examples, you can take a look at our [examples folder](https://github.com/iotaledger/iota.go/tree/master/api/.examples) for more.

### Create and broadcast transactions

This example shows you how to create and send a transaction to an IRI node by calling the `PrepareTransfers()` method and piping the prepared bundle to the `SendTrytes()` method.

```go
package main

import (
	"fmt"
	"github.com/iotaledger/iota.go/address"
	. "github.com/iotaledger/iota.go/api"
	"github.com/iotaledger/iota.go/bundle"
	. "github.com/iotaledger/iota.go/consts"
	"github.com/iotaledger/iota.go/pow"
	"github.com/iotaledger/iota.go/trinary"
)

var endpoint = "<node-url>"

// must be 81 trytes long and truly random
var seed = trinary.Trytes("AAAA....")

// difficulty of the proof of work required to attach a transaction on the tangle
const mwm = 14

// how many milestones back to start the random walk from
const depth = 3

// can be 90 trytes long (with checksum)
const recipientAddress = "BBBB....."

func main() {

	// get the best available PoW implementation
	_, proofOfWorkFunc := pow.GetFastestProofOfWorkImpl()

	// create a new API instance
	api, err := ComposeAPI(HTTPClientSettings{
		URI: endpoint,
		// (!) if no PoWFunc is supplied, then the connected node is requested to do PoW for us
		// via the AttachToTangle() API call.
		LocalProofOfWorkFunc: proofOfWorkFunc,
	})
	must(err)

	// create a transfer to the given recipient address
	// optionally define a message and tag
	transfers := bundle.Transfers{
		{
			Address: recipientAddress,
			Value:   80,
		},
	}

	// create inputs for the transfer
	inputs := []Input{
		{
			Address:  "CCCCC....",
			Security: SecurityLevelMedium,
			KeyIndex: 0,
			Balance:  100,
		},
	}

	// create an address for the remainder.
	// in this case we will have 20 iotas as the remainder, since we spend 100 from our input
	// address and only send 80 to the recipient.
	remainderAddress, err := address.GenerateAddress(seed, 1, SecurityLevelMedium)
	must(err)

	// we don't need to set the security level or timestamp in the options because we supply
	// the input and remainder addresses.
	prepTransferOpts := PrepareTransfersOptions{Inputs: inputs, RemainderAddress: &remainderAddress}

	// prepare the transfer by creating a bundle with the given transfers and inputs.
	// the result are trytes ready for PoW.
	trytes, err := api.PrepareTransfers(seed, transfers, prepTransferOpts)
	must(err)

	// you can decrease your chance of sending to a spent address by checking the address before
	// broadcasting your bundle.
	spent, err := api.WereAddressesSpentFrom(transfers[0].Address)
	must(err)

	if spent[0] {
		fmt.Println("recipient address is spent from, aborting transfer")
		return
	}

	// at this point the bundle trytes are signed.
	// now we need to:
	// 1. select two tips
	// 2. do proof-of-work
	// 3. broadcast the bundle
	// 4. store the bundle
	// SendTrytes() conveniently does the steps above for us.
	bndl, err := api.SendTrytes(trytes, depth, mwm)
	must(err)

	fmt.Println("broadcasted bundle with tail tx hash: ", bundle.TailTransactionHash(bndl))
}

func must(err error) {
	if err != nil {
		panic(err)
	}
}

```

### Native code and PoW

If the library is compiled with CGO enabled, certain functions such as Curl's `transform()` method will
run native C code for increased speed. 

Certain PoW implementations are enabled if the correct flags are passed while compiling your program:

- `pow_avx` for AVX based PoW
- `pow_sse` for SSE based PoW
- `pow_c128` for C int128 based using PoW
- `pow_arm_c128` for ARM64 int128 C based PoW
- `pow_c` for C based PoW

PoW implementation in Go is always available.
If you want to use local PoW, make sure you define `LocalProofOfWorkFunc` in your provider settings such as `HTTPClientSettings`. 

## Support the project

We thank everyone for their contributions. In order for your pull requests to be accepted, 
they must fulfill the following criteria:
- You must write tests for your additions with Ginkgo
- You must write example code that desribes the parameters and the functionality of your additions 
- Your pull request must pass the continuous integration configuration

### Write tests with Ginkgo

Before your pull requests can be accepted, you must test your code in Ginkgo.

1. Download Ginkgo

	```bash
	go get github.com/onsi/ginkgo/ginkgo
	go get github.com/onsi/gomega/...
	```

2. If you've written a new package, generate a corresponding test-suite file

	```bash
	cd <dir-of-your-package>
	ginkgo bootstrap
	```

3. Generate a new testing file

	```bash
	ginkgo generate <package-name>
	```

After creating a testing file, you'll have following two files:

- `<package-name>_suite_test.go`
- `<package-name>_test.go`

**Note:** You can use the existing tests as a reference on how to write Ginkgo tests or
you can [read the documentation](https://onsi.github.io/ginkgo/).

4. Run your tests
	```bash
	go test -v
	=== RUN   TestAddress
	Running Suite: Address Suite
	============================
	Random Seed: 1542616006
	Will run 11 of 11 specs

	•••••••••••
	Ran 11 of 11 Specs in 0.261 seconds
	SUCCESS! -- 11 Passed | 0 Failed | 0 Pending | 0 Skipped
	--- PASS: TestAddress (0.26s)
	PASS
	ok  	github.com/iotaledger/iota.go/address	0.264s
	```

### Write documentation and example code

While godoc.org gives a good enough documentation of the package already, the IOTA Foundation's
documentation portal needs additional information, such as parameter description, examples and so on.

1. If non existent, add a `.examples` directory in your newly created package

2. Create a new file with the following convention: `<package-name>_examples_test.go` inside
the `.examples` directory

3. Write examples in the following schema:
	```
	// i req: s, The ASCII string to convert to Trytes.
	// o: Trytes, The Trytes representation of the input ASCII string.
	// o: error, Returned for non ASCII string inputs.
	func ExampleASCIIToTrytes() {
		trytes, err := converter.ASCIIToTrytes("IOTA")
		if err != nil {
			// handle error
			return
		}
		fmt.Println(trytes) // output: "SBYBCCKB"
	}
	```

	| Symbol     | Description |
	|:---------------|:--------|
	| i req | Describes a parameter to the function. |
	| i | Describes an optional parameter to the function. |
	| o | Describes a return value of the function. |

	Syntax:

	- For parameters: `<symbol>: <parameter_name>, <description>.`  
	- For return values: `<symbol>: <type>, <description>.`
	- Example function: `Example<OriginFunctionName>`

## Join the discussion

If you want to get involved in the community, need help with getting setup, have any issues related with the library or just want to discuss blockchain, distributed ledgers, and IoT with other people, feel free to join our [Discord](https://discord.iota.org).  
