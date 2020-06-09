# Botan TLS Stream Server Example

This repository show-cases the use of [`Botan::TLS::Stream`](https://botan.randombit.net/handbook/api_ref/tls.html#tls-stream) to implement an asynchronous HTTPS server.

The server implementation is based on the [Boost.Beast asynchronous https server example](https://github.com/boostorg/beast/blob/0fa85226f0316eea13bb13d31aaec699cbc18eae/example/http/server/async-ssl/http_server_async_ssl.cpp).
The changes required to use `Botan::TLS::Stream` instead of the OpenSSL-based `beast::ssl_stream` are very small and can be seen in [this commit](https://github.com/hrantzsch/botan-tls-stream-server-example/commit/ba77ddaf23df2e4cc80edb5a1f12b897af75e1f0).
Most notably,
 * `Botan::TLS::Stream` replaces `beast::ssl_stream`
 * `Botan::TLS::Context` is set up using a number of Botan-specific types and replaces `ssl::context`

In addition, reads the server certificate and private key are read from local files for the sake of simplicity of the example.

## Build and Run

Botan 2.14 or newer is required. To compile the example:
```bash
mkdir build && cd build
cmake .. -DBOTAN_ROOT_DIR=<path-to-botan-directory>
cmake --build .
```

To run the example, generate a self-signed server certificate and key, e.g. using `botan-cli`:
```bash
./botan-cli keygen --algo=ECDSA --params=secp384r1 --output=server_key.pem
./botan-cli gen_self_signed server_key.pem CA --ca --country=VT --dns=ca.example --hash=SHA-384 --output=ca.crt
./botan-cli gen_pkcs10 server_key.pem localhost --output=crt.req
./botan-cli sign_cert ca.crt server_key.pem crt.req --output=server_cert.crt
```

Finally, run the server:
```bash
./botan-tls-stream-server 0.0.0.0 8080 . 3 server_cert.crt server_key.pem
```
