# Simple NetCat file transfer

## Máquina que enviará el archivo (Servidor)

```
nc -l -p port -q 1 > archivo < /dev/null
```

## Máquina que recibirá el archivo (Cliente)

```
cat archivo | netcat serve.ip port
```
