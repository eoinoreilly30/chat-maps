# Chat Maps

## Run Peer Server
```
docker run -v ~/:/ssl -p 9000:9000 peerjs/peerjs-server --port 9000 --allow_discovery --sslkey /ssl/privkey.pem --sslcert /ssl/fullchain.pem
```

