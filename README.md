# Bash Script boilerplate

Yet another bash script with option boilerplate offering :

* Interactive mode
* Quiet mode
* CLI options parser supporting `-n --name --name=Oxy --name Oxy`
* Bundling of flags. ie. `-vf` instead of `-v -f`
* Helper functions for printing messages such as success and error
* Automatically remove color escape codes if the script is piped.

## Starting a new script

### Update options
First of all define, the options you expect the user pass to the script throw interactive mode :

```sh
interactive_opts=(foo bar)
```

Then, update usage text and parameters :

```sh
usage() {
  echo -n "$(basename $0) [OPTION]... [FILE]...

Description of this script.

 Options:
  -f, --foo    foo for script
  -b, --bar    Input bar, it's recommended to insert
               this through the interactive option
  -f, --force       Skip all user interaction
  -i, --interactive Prompt for values
  -q, --quiet       Quiet (no output)
  -v, --verbose     Output more
  -h, --help        Display this help and exit
      --version     Output version information and exit
"
}
```

```sh
# Read the options and set stuff
while [[ $1 = -?* ]]; do
  case $1 in
    -h|--help) usage >&2; safe_exit ;;
    --version) out "$(basename $0) $version"; safe_exit ;;
    -f|--foo) shift; foo=$1 ;;
    -b|--bar) shift; bar=$1 ;;
    -v|--verbose) verbose=1 ;;
    -q|--quiet) quiet=1 ;;
    -i|--interactive) interactive=1 ;;
    -f|--force) force=1 ;;
    --endopts) shift; break ;;
    *) die "invalid option: $1" ;;
  esac
  shift
done
```

### Edit the main() Functions

```sh
# Put your script here
main() {
  echo "Foo : $foo";
  echo "Bar : $bar";
}
```




## Functions

### Print functions

* `die()` Output message to stderr and exit with error code 1.
* `out()` Output message.
* `err()` Output message to stderr but continue running.
* `success()` Output message as a string. Both `success` and `err` will output message with a colorized symbol, as long as the script isn't piped.
* `log()` Will only output message if user has activated verbose flag.
* `notify()` Delegate the message to either `err` or `success` depending on the last return code. *Remember this function needs to be called once a return code is available.* Eg.

  ```bash
  foobar; notify "foobar copied files"

  notify "foobar copied files" $(foobar)
  ```

### Misc helpers

* `escape()` Escape slashes in a string
* `confirm()` Prompt the user to answer Yes or No. *This will automatically return true if --force is used.* Eg.

  ```bash
  if ! confirm "Delete file"; then
    continue;
  fi
  ```
