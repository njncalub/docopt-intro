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

[try docopt online](http://try.docopt.org/)
