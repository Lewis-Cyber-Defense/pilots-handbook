Processes listening:
```bash
ss -nlp | grep -E 'LISTEN.*(([0-9]+\.){3}[0-9]+|\*|):[0-9]+' | grep -e 'pid=[0-9]*'
```

