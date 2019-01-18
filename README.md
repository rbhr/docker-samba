
Docker container that creates SMB share(s)

Completely based on [dastrasmue/rpi-samba](https://github.com/dastrasmue/rpi-samba/). I needed a non-Arm implementation.

## Command Line Example

```
docker run -d \
  -p 137:137/udp \
  -p 138:138/udp \
  -p 139:139 \
  -p 445:445 \
  -p 445:445/udp \
  --restart='always' \
  --hostname 'filer' \
  -v /media/stick:/share/stick \
  --name samba rbhr/samba \
  -u "alice:abc123" \
  -u "bob:secret" \
  -s "Backup directory:/share/stick/backups:rw:alice,bob" \
  -s "Alice (private):/share/stick/data/alice:rw:alice" \
  -s "Bob (private):/share/stick/data/bob:rw:bob" \
  -s "Documents (readonly):/share/stick/data/documents:ro:alice,bob"\
  -s "Public (readonly):/share/stick/data/public:ro:"
```

## Compose Example

```
version: "3"
services:
  samba:                                                                                                                                    
    container_name: samba                                                                                                                   
    image: "rbhr/samba"                                                                                                                     
    ports:                                                                                                                                  
      - "137:137/udp"                                                                                                                       
      - "138:138/udp"                                                                                                                       
      - "139:139"                                                                                                                           
      - "445:445"                                                                                                                           
      - "445:445/udp"                                                                                                                       
    restart: always                                                                                                                         
    network_mode: bridge                                                                                                                    
    hostname: filer                                                                                                                   
    volumes:                                                                                                                                
      - "/media/stick:/share/stick"                                                                                                                                                                                                                                        
    command:  -u "alice:abc123" -u "bob:secret" -s "Backup directory:/share/stick/backups:rw:alice,bob" -s "Alice (private):/share/stick/data/alice:rw:alice" -s "Bob (private):/share/stick/data/bob:rw:bob" -s "Documents (readonly):/share/stick/data/documents:ro:alice,bob" -s "Public (readonly):/share/stick/data/public:ro:"
```

These examples will bind `smbd` to docker host ip address
and mount two directories on docker host to container.
Additionally, the supplied hostname will be used for the NetBIOS name.
Two users will be created and given various access to four shares.
Ommitting users from a share results in guest access.
The `public` share will have guest access and be browsable.

## Connecting
You'll want to expose enough ports to make the server discoverable.
The example above should be enough to get you moving.

Open Finder, then press âŒ˜K. Enter `smb://<docker_host_ip>`
and press `Connect`.
Enter login and password you supplied at the run stage.

