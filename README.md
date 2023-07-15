# ffyaml

This is a YAML configuration parser for the excellent [peterbourgon/ff][1] module. It is a fork of the `ffyaml` parser included that module. This fork provides a `WithKeyPath` option to specify the location within the YAML document where the configuration resides. It allows one to use a subset of a larger configuration file.

## Usage

Given the following configuration file:

```yaml
name: Super Configuration
plugins:
  cli:
    verbose: true
    workers: 20
```

The following program will parse the options from it:

```go
import (
	"flag"
	"fmt"
	"os"

	"github.com/peterbourgon/ff/v3"
	"github.com/pkazmier/ffyaml"
)

func main() {

	// Define a `flag.FlagSet`
	fs := flag.NewFlagSet("example", flag.ContinueOnError)
	verbose := fs.Bool("verbose", false, "enable verbose logging")
	workers := fs.Int("workers", 10, "number of workers to create")

	// Define the YAML configuration parser that points to the `cli` section:
	parser := ffyaml.New(ffyaml.WithKeyPath("plugins", "cli"))

	// Then invoke `peterbourgon/ff.Parse` with this YAML configuration parser:
	err := ff.Parse(fs, os.Args[1:],
		ff.WithConfigFile("config.yaml"),
		ff.WithConfigFileParser(parser.Parse),
	)

	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}

	fmt.Printf("verbose = %v\n", *verbose)
	fmt.Printf("workers = %v\n", *workers)
}
```

[1]: https://github.com/peterbourgon/ff
