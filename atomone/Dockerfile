FROM       golang:1.24.5-alpine
ENV        CGO_ENABLED=0 GOOS=linux
ARG        RELAYER_ADDRESS
WORKDIR    /app
RUN        apk add --no-cache git
RUN        apk add --no-cache automake make
RUN        git clone https://github.com/clockworkgr/atomone.git
WORKDIR     /app/atomone
RUN        git checkout feat/gno-lc-with-diff
RUN        make build
WORKDIR     /app/atomone/build
RUN        cp ./atomoned /usr/local/bin/atomoned
RUN         ./atomoned init --chain-id ibctest-1 --default-denom uatone ibctest-1
RUN         ./atomoned keys add validator --keyring-backend="test"
RUN         ./atomoned genesis add-genesis-account $(./atomoned keys show validator -a --keyring-backend="test") 10000000000uatone,100000000000uphoton
RUN         ./atomoned genesis add-genesis-account $RELAYER_ADDRESS 10000000000uatone,100000000000uphoton
RUN         ./atomoned genesis gentx validator 5000000000uatone --keyring-backend="test" --chain-id ibctest-1
RUN         ./atomoned genesis collect-gentxs
RUN          echo "Setting rpc listen address"
RUN         sed -i 's#"tcp://127.0.0.1:26657"#"tcp://0.0.0.0:26657"#g' ~/.atomone/config/config.toml
RUN         sed -i 's/timeout_commit = "5s"/timeout_commit = "1s"/g' ~/.atomone/config/config.toml
RUN         sed -i 's/timeout_propose = "3s"/timeout_propose = "1s"/g' ~/.atomone/config/config.toml
RUN         sed -i 's/index_all_keys = false/index_all_keys = true/g' ~/.atomone/config/config.toml
RUN         sed -i 's/minimum-gas-prices = ""/minimum-gas-prices = "0.0025uatone,0.0025uphoton"/g' ~/.atomone/config/app.toml
EXPOSE 26657
EXPOSE 1317
ENTRYPOINT ["atomoned", "start"]