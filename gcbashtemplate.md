```
# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

alias la='ls -a'
alias ll='ls -lhp'
alias cp='cp -pr'
alias rm='rm -i'

# Max out machine limits
ulimit -t unlimited              # cputime
ulimit -f unlimited              # filesize
ulimit -d unlimited              # datasize
ulimit -s unlimited              # stacksize
ulimit -c unlimited              # coredumpsize
ulimit -m unlimited              # memoryuse
ulimit -v unlimited              # vmemoryuse
ulimit -l unlimited              # memorylocked
```
