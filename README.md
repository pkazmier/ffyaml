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

Define a `flag.FlagSet` for the `verbose` and `workers` options:

```go
import (
       "flag"
       "os"

       "github.com/peterbourgon/ff/v3"
       "github.com/pkazmier/ffyaml"
)

fs := flag.NewFlagSet("example", flag.ContinueOnError)
verbose := fs.Bool("verbose", false, "enable verbose logging")
workers := fs.Int("workers", 10, "number of workers to create")
```

Define the YAML configuration parser that points to the `cli` section:

```go
parser := ffyaml.New(ffyaml.WithKeyPath("plugins", "cli"))
```

Then invoke `peterbourgon/ff.Parse` with this YAML configuration parser:

```go
err := ff.Parse(fs, os.Argv[1:],
	ff.WithConfigFile("config"),
	ff.WithConfigFileParser(parser.Parse),
)

fmt.Printf("verbose = %v", *verbose)
fmt.Printf("workers = %v", *workers)
```

[1]: https://github.com/peterbourgon/ff
