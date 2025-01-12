
# pkt-electrumx

> Run a PKT Electrumx server with one command

An easily configurable Docker image for running an Electrumx server on the PKT network.

Check it out on dockerhub at [thomasjp0x42/pkt-electrumx](https://hub.docker.com/r/thomasjp0x42/pkt-electrumx).

## Usage

1. Install [pktd](https://github.com/pkt-cash/pktd)

2. Launch pktd with the following arguments (-u and -P for the RPC user and password):

```
pktd --addrindex --txindex --notls --rpclisten=172.17.0.1 -u x -P x
```

3. Make a data directory

```
mkdir $HOME/electrumx_data
```

4. Launch electrumx from docker:

```
docker run \
  -v $HOME/electrumx_data:/data \
  --ulimit nofile=262144:262144 \
  -e DAEMON_URL=http://x:x@172.17.0.1:64765 \
  -e BANDWIDTH_UNIT_COST=9999999999 \
  -e COST_SOFT_LIMIT=50001 \
  -e COST_HARD_LIMIT=50000 \
  -e INITIAL_CONCURRENT=10000000 \
  -e REQUEST_SLEEP=20 \
  -e DONATION_ADDRESS="pkt1qnnkpxmlclruyh6h0j5jhytwyu7rm09paz0kckv" \
  -p 64767:64767 \
  -e REPORT_SERVICES="ssl://yourdomain.example.com:64767" \
  thomasjp0x42/pkt-electrumx
```

Please change the **DONATION_ADDRESS** variable to one of your own wallet addresses, in order to be able to receive donations from ElectrumX wallet users who benefit from your server if they so choose.  There is no default donation address in this docker image.

If there's an SSL certificate/key (`electrumx.crt`/`electrumx.key`) in the `/electrumx_data` volume it'll be used. If not, one will be generated for you.

You can view all ElectrumX environment variables here: https://github.com/spesmilo/electrumx/blob/master/docs/environment.rst

### Automatic discovery
For hardcoded nodes to share your ElectrumX instance to Electrum clients, it is necessary to set the **REPORT_SERVICES** env variable with  
the domain used to expose your node.

### Testing
After your electrumx server has fully imported the blockchain, it will begin serving information to electrum
instances. Once it is serving, you can perform a basic check that it is in fact accessible by using openssl:

```
openssl s_client -connect host.name.of.your.server:64767
```

If it is working correctly, you should see some information about the certificate and SSL session and finally
a hung terminal, press the enter key once and you should see this message:

```
{"jsonrpc": "2.0", "error": {"code": -32700, "message": "invalid JSON"}, "id": null}
```

As a reminder, this test will not work until after electrumx has fully imported the blockchain.

### TCP Port

By default only the SSL port is exposed. You can expose the unencrypted TCP port with `-p 64766:64766`, although this is strongly discouraged.

### RPC Port

To access RPC from your host machine, you'll also need to expose port 8252. You probably only want this available to localhost: `-p 127.0.0.1:8252:8252`.

If you're only accessing RPC from within the container, there's no need to expose the RPC port.

## License

MIT License

* © Luke Childs
* Caleb James DeLisle
* James P. Thomas
