# bash-installer

A small Bash project created while learning Git.

It contains `install.sh`, a script that:

1. Prints a start message
2. Updates the package lists (`apt update`)
3. Installs a package (`curl`)
4. Prints a completion message

## Usage

On a Debian/Ubuntu system:

```bash
chmod +x install.sh
./install.sh
```

The script uses `sudo`, so you will be asked for your password.
