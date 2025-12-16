# a1-gno-relay

This is a docker setup for testing Gno <-> AtomOne IBC connection and relaying.

Clone this repo, set RELAYER_ADDRESS for AtomOne and Gno in the compose file and run:

```
docker compose build
```

When it's done building, run

```
docker compose up
```

to bring up the two chains.

In order to test relaying, clone the IBC v2 relayer repo: https://github.com/allinbits/ibc-v2-ts-relayer/

and check out the `feat/gno-ibc` branch.

You will need node >v22 and pnpm in order to run the relayer.

With those installed, in the relayer folder run:

```
pnpm install
pnpm build
```

The easiest way to set up an IBC connection between the two chains you have running is to use the gno e2e test script.

Modify `e2e/gno.e2e.ts` to refer to the mnemonics corresponding to the relayer addresses you set up in the docker config above.

You may now run the test to create the IBC link between the two chains:

`pnpm test:gno:e2e`

The test will exit once teh connection has been set up.

To run the configured relayer in relay mode, run:

`node dist/index.js relay`

In `src/tests.ts` you will find examples for submitting transfers to eitehr chain.
