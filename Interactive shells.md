https://gist.github.com/rollwagen/1fdb6b2a8cd47a33b1ecf70fea6aafde

--------------------------------------------------------------------------------
## * Python Interactive shell
```
## Use one of the 3 commands beolow to spawn a python interactive shell
python3 -c 'import pty;pty.spawn("/bin/bash")'
python -c 'import pty;pty.spawn("/bin/bash")'
/usr/bin/python -c 'import pty;pty.spawn("/bin/bash")'

## background the shell
CTRL+Z

## Run commands on host machine and then bring back the shell
stty -a
stty raw -echo; fg
Enter x2

## Once shell (victim) comes back, run commands
export TERM=xterm-256color
stty rows 60 columns 180
```

## * Other Interactive shell
```
python -c 'import pty; pty.spawn("/bin/sh")'

echo os.system('/bin/bash')

/bin/sh -i

perl —e 'exec "/bin/sh";'

perl: exec "/bin/sh";

ruby: exec "/bin/sh"

lua: os.execute('/bin/sh')
```

## (From within IRB)
```
exec "/bin/sh"
```

## (From within vi)
```
:!bash
```

## (From within vi)
```
:set shell=/bin/bash:shell
```

## (/bin/bash shell)
```
SHELL=/bin/bash script -q /dev/null
```

## (/bin/bash with script)
```
/usr/bin/script -qc /bin/bash /dev/null
CTRL + Z
stty -a
echo $TERM
sty raw -echo; fg
export SHELL=bash
export TERM=xterm256-color
```