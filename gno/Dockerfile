FROM        golang:1.24-alpine
ENV         CGO_ENABLED=0 GOOS=linux
ARG        RELAYER_ADDRESS
WORKDIR     /app
RUN        apk add --no-cache git
RUN        git clone https://github.com/allinbits/gno.git
WORKDIR     /app/gno
RUN        git checkout ibc-fork
WORKDIR     /app
RUN        git clone https://github.com/allinbits/gno-realms.git
WORKDIR     /app/gno-realms
RUN         cp -R ./gno.land/* /app/gno/examples/gno.land
WORKDIR     /app/gno/contribs/gnodev
RUN        go build -o /usr/local/bin/gnodev .
WORKDIR    /app
RUN        git clone https://github.com/gnolang/tx-indexer.git
WORKDIR    /app/tx-indexer
RUN        	go build -o build/tx-indexer ./cmd
WORKDIR    /app
RUN        echo "#!/bin/sh" > /app/entrypoint.sh && \
           echo "/usr/local/bin/gnodev -paths gno.land/** -root /app/gno -lazy-loader=false -empty-blocks=true -v -add-account $RELAYER_ADDRESS=1000000000000ugnot -node-rpc-listener 0.0.0.0:26657 -web-listener 0.0.0.0:8888 &" >> /app/entrypoint.sh && \
           echo "/app/tx-indexer/build/tx-indexer start --remote http://127.0.0.1:26657 --listen-address 0.0.0.0:8546 --db-path /app/tx-indexer/indexer-db" >> /app/entrypoint.sh && \
           chmod +x /app/entrypoint.sh
EXPOSE 8888
EXPOSE 8546
EXPOSE 26657
ENTRYPOINT  ["/app/entrypoint.sh"]
