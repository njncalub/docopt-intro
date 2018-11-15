# docopt

The command-line interface description language.

---

```bash
$ my_program -h
Usage: my_program tcp <host> <port> [--timeout=<seconds>]
       my_program serial <port> [--baud=9600] [--timeout=<seconds>]
       my_program (-h | --help | --version)
```

---

```python
"""Usage:
    my_program tcp <host> <port> [--timeout=<seconds>]
    my_program serial <port> [--baud=9600] [--timeout=<seconds>]
    my_program (-h | --help | --version)
"""
import sys
import optparse

parser = optparse.OptionParser(version='1.0.0rc2',
                               usage=__doc__.strip())
parser.add_option('--timeout')
parser.add_option('--baud')

options, args = parser.parse_args()

if not args:
    sys.stdout.write(__doc__)
    exit(2)

command = args[0]
if command == 'tcp':
    if len(args) != 3 or options.baud:
        sys.stdout.write(__doc__)
        exit(2)
    host = args[1]
    port = args[2]
elif command == 'serial':
    if len(args) != 2:
        sys.stdout.write(__doc__)
        exit(2)
    host = None
    port = args[1]
else:
    sys.stdout.write(__doc__)
    exit(2)

print(options, command, host, port)
```

@[6-7]
@[9-18]
@[9-18]
@[20-26]
@[27-32]
@[33-35]

---

```python
"""Usage:
    my_program tcp <host> <port> [--timeout=<seconds>]
    my_program serial <port> [--baud=9600] [--timeout=<seconds>]
    my_program (-h | --help | --version)
"""
import argparse

parser = argparse.ArgumentParser(prog='my_program')
parser.add_argument('--version', action='version', version='1.0.0rc2')
subparsers = parser.add_subparsers()

parser_tcp = subparsers.add_parser('tcp')
parser_tcp = add_argument("<host>")
parser_tcp = add_argument("<port>")
parser_tcp = add_argument("--timeout", nargs='?')

parser_serial = subparsers.add_parser('serial')
parser_serial.add_argument("<port>")
parser_serial.add_argument("--baud", nargs=1)
parser_serial.add_argument("--timeout", nargs='?')

print(parser.parse_args())
```

---

```
If you have to refer to the documentation
every time you use a module, find (or build)
a new module.

- Kenneth Reitz
```

---

```python
"""Usage:
    my_program tcp <host> <port> [--timeout=<seconds>]
    my_program serial <port> [--baud=9600] [--timeout=<seconds>]
    my_program (-h | --help | --version)
"""
from docopt import docopt

print(docopt(__doc__, version='1.0.0rc2'))
```

---

# IEEE Std 1003.1

---

# POSIX

---

```
docopt(doc[, argv][, help][, version])
```

---

```python
args['--include']
args['<port>']
args['add']
```

---

```python
def process_options(arglist=None, parse_argv=False, config_file=None,
                    parser=None, verbose=None):
    """Process options passed either via arglist or command line args.
    Passing in the ``config_file`` parameter allows other tools, such as
    flake8 to specify their own options to be processed in pycodestyle.
    """
    if not parser:
        parser = get_parser()
    if not parser.has_option('--config'):
        group = parser.add_option_group("Configuration", description=(
            "The project options are read from the [%s] section of the "
            "tox.ini file or the setup.cfg file located in any parent folder "
            "of the path(s) being processed.  Allowed options are: %s." %
            (parser.prog, ', '.join(parser.config_options))))
        group.add_option('--config', metavar='path', default=config_file,
                         help="user config file location")
    # Don't read the command line if the module is used as a library.
    if not arglist and not parse_argv:
        arglist = []
    # If parse_argv is True and arglist is None, arguments are
    # parsed from the command line (sys.argv)
    (options, args) = parser.parse_args(arglist)
    options.reporter = None

    # If explicitly specified verbosity, override any `-v` CLI flag
    if verbose is not None:
        options.verbose = verbose

    if options.ensure_value('testsuite', False):
        args.append(options.testsuite)
    elif not options.ensure_value('doctest', False):
        if parse_argv and not args:
            if options.diff or any(os.path.exists(name)
                                   for name in PROJECT_CONFIG):
                args = ['.']
            else:
                parser.error('input not specified')
        options = read_config(options, args, arglist, parser)
        options.reporter = parse_argv and options.quiet == 1 and FileReport

    options.filename = _parse_multi_options(options.filename)
    options.exclude = normalize_paths(options.exclude)
    options.select = _parse_multi_options(options.select)
    options.ignore = _parse_multi_options(options.ignore)

    if options.diff:
        options.reporter = DiffReport
        stdin = stdin_get_value()
        options.selected_lines = parse_udiff(stdin, options.filename, args[0])
        args = sorted(options.selected_lines)

    return options, args
```

---

```python
"""Usage: pep8 [options] <input>...

Options:
  --version            show program's version number and exit
  -h, --help           show this help message and exit
  -v, --verbose        print status messages, or debug with -vv
  -q, --quiet          report only file names, or nothing with -qq
  -r, --repeat         show all occurences of the same error
  --exclude=patterns   exclude files or directories which match these comma
                       separated patterns [default: .svn,CVS,.bzr,.hg,.git]
  --filename=patterns  when parsing directories, only check filenames matching
                       these comma separated patterns [default: *.py]
  --select=errors      select errors and warnings (e.g. E,W6)
  --ignore=errors      skip errors and warnings (e.g. E4,W)
  --show-source        show source code for each error
  --show-pep8          show text of PEP 8 for each error
  --statistics         cpimt errors and warnings
  --count              print total number of errors and warnings to standard
                       error and set exit code to 1 if total is not null
  --benchmark          measure processing speed
  --testsuite=dir      run regression tests from dir
  --doctest            run doctest on myself
"""
args = docopt(__doc__, version=__version__)
```
---

```
help message = usage + option's description
```

---

```
Usage:
  my_program command --option <argument>
  my_program [<optional-argument>]
  my_program --another-option=<with-argument>
  my_program (--either-that-option | <or-this-argument>)
  my_program <repeating-argument> <repeating-argument>...
```

---

# <argument> ARGUMENT

```
Usage: my_program <host> <port>
```

---

# -o --option

```
-abc
-a -b -c
--input=ARG
--input ARG
-f FILE
-fFILE
-Wall
-xzfFILE
```

---

```
Usage:
  my_program [-hvqrf NAME] [--exclude=PATTERNS]
             [--select=ERRORS | --ignore=ERRORS] [--show-source]
             [--statistics] [--count] [--benchmark] PATH...
  my_program (--doctest | --testsuite=DIR)
  my_program --version
```

---

# command

```
Usage:
  my_program ship new <name>...
  my_program ship <name> move <x> <y> [--speed=<kn>]
  my_program ship shoot <x> <y>
  my_program mine (set|remove) <x> <y> [--moored|--drifting]
  my_program -h | --help
  my_program --version
```

---

# [optional elements]

```
Usage: my_program [command --option <argument>]

Usage: my_program [command] [--option] [<argument>]
```

---

# (required elements)

```
Usage: my_program (--either-this <and-that> | <or-this>)

Usage: my_program [(<one-argument> <another-argument>)]

Usage: my_program (<odd> <even>)...
```

---

# mutually | exclusive

```
Usage: my_program go (--up | --down | --left | --right)

Usage: my_program go [--up | --down | --left | --right]

Usage: my_program (run [--fast] | jump [--high])
```

---

# element...

```
Usage: my_program open <file>...
       my_program move (<from> <to>)...
```

---

# [options]

```
Usage: my_program [options] <path>

--all             List everything.
--long            Long output.
--human-readable  Display in human-readable format.
```

```
Usage: my_program [-alh] <path>

-a, --all             List everything.
-l, --long            Long output.
-h, --human-readable  Display in human-readable format.
```

---

# [--]

```
Usage: my_program [options] [--] <file>...
```

---

# [-]

```
Usage: my_program [- | <file>...]
```

---

```bash
pip install docopt
```

---

Implementations:

- Python
- CoffeeScript
- JavaScript
- PHP
- Ruby
- Lua
- Bash
- C
- Go

---

[try docopt online](http://try.docopt.org/)
