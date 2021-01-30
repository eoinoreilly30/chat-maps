# Chat Maps

## Run Peer Server
```
docker run --name peerjs -d -v ~/:/ssl -p 9000:9000 peerjs/peerjs-server --port 9000 --allow_discovery --sslkey /ssl/privkey.pem --sslcert /ssl/fullchain.pem
```

