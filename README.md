# Setup a password manager


First, we need to setup Vault on the remote server. There are several guides on how to do that. Then, we create a configuration file. Note that Vault binds to `localhost`.


```
{
    "storage": {
        "consul": {
            "address": "127.0.0.1:8500",
            "advertise_addr": "https://127.0.0.1:8200",
            "path": "vault"
        }
    },
    "listener": {
        "tcp": {
            "address": "127.0.0.1:8200",
            "tls_cert_file": "/etc/vault.d/ssl/vault.cert",
            "tls_key_file": "/etc/vault.d/ssl/vault.key",
            "tls_disable": 0
        }
    }
}
```


We are now going to use [fw](https://github.com/grocid/fw). Setup certificates for the authentication (perferably, same CA as Consul and Vault). Put the certificates in `/etc/fw.d/ssl/`. Then run

```
$ ./fw 0.0.0.0:8001 127.0.0.1:8200
```

on the remote server. This will create a link between ports `8001` and `8200`, where the latter is the port for Vault. The only way to communicate with Vault from the outside world is via `fw`.

In `/etc/fw.d/token`, you can find the shared secret. Put this into Google Authenticator.

On the client, create a config for `pass`.

```
{
    "token": "<token>",
    "cert": "ca.crt",
    "addr": "myserver.com:8001",
    "auth_addr": "myserver.com:8000"
}
```

The token is associated with the Vault user you wish to use for passwords. The next thing is to authenticate.

```
$ ./pass auth
```

and enter the token from Google Authenticator. Then, you can add a password

```
$ ./pass gen email.com me@email.com
{'password': '1ZLHVYXE5N99OXGL0DKWDKXO3FYALB8FMGBNZRSL3B1PW13ZS7FGLQPDILCW9Q2L', 'username': 'me@email.com'}

$ ./pass list
 🔑   email.com
```

Great. You are done.