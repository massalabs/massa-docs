.. index:: api, JSON-RPC, WebSocket

.. _technical-api:


Massa JSON-RPC API
==================

This crate exposes Rust methods (through the `Endpoints` trait) as
JSON-RPC API methods (thanks to the `ParityJSON-RPC <https://github.com/paritytech/jsonrpc>`_ crate).

Massa JSON-RPC API is splitted in two parts :

- **Private API**: used for node management. Default port: 33034 e.g. http://localhost:33034
- **Public API**: used for blockchain interactions. Default port: 33035 e.g. http://localhost:33035

.. warning::
    
    Massa has a new experimental API with both Http and WebSocket support: Default port: 33036
    
    - **Http**: used for node management and blockchain interactions. e.g. http://localhost:33036
    - **WebSocket**: used for streaming blockchain events. e.g. ws://localhost:33036
    
Find the complete Massa `OpenRPC <https://spec.open-rpc.org/>`_  specification `here <https://raw.githubusercontent.com/massalabs/massa/main/massa-node/base_config/openrpc.json>`_.

Integrations
============

- **JavaScript**: use `massa-web3.js <https://github.com/massalabs/massa-web3>`_.
- **Playground**: use `Massa Playground <https://playground.open-rpc.org/?schemaUrl=https://test.massa.net/api/v2&uiSchema[appBar][ui:input]=false&uiSchema[appBar][ui:inputPlaceholder]=Enter%20Massa%20JSON-RPC%20server%20URL&uiSchema[appBar][ui:logoUrl]=https://massa.net/favicons/favicon.ico&uiSchema[appBar][ui:splitView]=false&uiSchema[appBar][ui:darkMode]=false&uiSchema[appBar][ui:title]=Massa&uiSchema[appBar][ui:examplesDropdown]=false&uiSchema[methods][ui:defaultExpanded]=false&uiSchema[methods][ui:methodPlugins]=true&uiSchema[params][ui:defaultExpanded]=false>`_.
- **Postman**: use `Postman collection <https://github.com/massalabs/massa-docs/tree/main/postman>`_.

Error codes
===========

When a call to Massa API fails, it **MUST** return a valid JSON-RPC `error object <https://www.jsonrpc.org/specification#error_object>`_ .

+----------+------------------------+------------------------+
| **Code** | **Message**            | **Meaning**            |
+==========+========================+========================+
|| -32600  || Invalid request       || The JSON sent is not  |
||         ||                       || a valid Request       |
||         ||                       || object                |
+----------+------------------------+------------------------+
|| -32601  || Method not found      || The method does not   |
||         ||                       || exist / is not        |
||         ||                       || available             |
+----------+------------------------+------------------------+
|| -32602  || Invalid params        || Invalid method        |
||         ||                       || parameter(s)          |
+----------+------------------------+------------------------+
|| -32603  || Internal error        || Internal JSON-RPC     |
||         ||                       || error                 |
+----------+------------------------+------------------------+
|| -32700  || Parse error           || Invalid JSON, parsing |
||         ||                       || issue                 |
+----------+------------------------+------------------------+
|| -32000  || Bad request           || Indicates that the    |
||         ||                       || server cannot or will |
||         ||                       || not process the       |
||         ||                       || request due to        |
||         ||                       || something that is     |
||         ||                       || perceived to be a     |
||         ||                       || client error (for     |
||         ||                       || example, malformed    |
||         ||                       || request syntax,       |
||         ||                       || invalid request       |
||         ||                       || message framing, or   |
||         ||                       || deceptive request     |
||         ||                       || routing)              |
+----------+------------------------+------------------------+
|| -32001  || Internal server error || The server            |
||         ||                       || encountered an        |
||         ||                       || unexpected issue      |
+----------+------------------------+------------------------+
|| -32003  || Service Unavailable   || Indicates that the    |
||         ||                       || server is not ready   |
||         ||                       || to handle the request |
+----------+------------------------+------------------------+
|| -32004  || Not found             || Indicates that the    |
||         ||                       || server cannot find    |
||         ||                       || the requested         |
||         ||                       || resource              |
+----------+------------------------+------------------------+
|| -32005  || Method not allowed    || Indicates that the    |
||         ||                       || server knows the      |
||         ||                       || request method, but   |
||         ||                       || the target resource   |
||         ||                       || doesn’t support this  |
||         ||                       || method                |
+----------+------------------------+------------------------+
| -32006   | Send channel error     | Send channel error     |
+----------+------------------------+------------------------+
| -32007   | Receive channel error  | Receive channel error  |
+----------+------------------------+------------------------+
| -32008   | Massa hash error       | ``massa_hash`` error   |
+----------+------------------------+------------------------+
|| -32009  || Consensus error       || Error from Consensus  |
||         ||                       || module                |
+----------+------------------------+------------------------+
|| -32010  || Execution error       || Error from Execution  |
||         ||                       || module                |
+----------+------------------------+------------------------+
|| -32011  || Network error         || Error from Network    |
||         ||                       || module                |
+----------+------------------------+------------------------+
|| -32012  || Protocol error        || Error from Protocol   |
||         ||                       || module                |
+----------+------------------------+------------------------+
| -32013   | Models error           | Error in Models        |
+----------+------------------------+------------------------+
|| -32014  || Time error            || Error from Time       |
||         ||                       || module                |
+----------+------------------------+------------------------+
|| -32015  || Wallet error          || Error from Wallet     |
||         ||                       || module                |
+----------+------------------------+------------------------+
|| -32016  || Inconsistency error   || Inconsistency in the  |
||         ||                       || result of request     |
+----------+------------------------+------------------------+
|| -32017  || Missing command       || Missing command       |
||         || sender                || sender                |
+----------+------------------------+------------------------+
| -32018   | Missing config         | Missing configuration  |
+----------+------------------------+------------------------+
|| -32019  || Wrong API             || The wrong API (either |
||         ||                       || Public or Private)    |
||         ||                       || was called            |
+----------+------------------------+------------------------+

Error example:

.. code-block:: javascript

    {
    "jsonrpc": "2.0",
    "error": {
        "code": -32400,
        "message": "Bad request: too many arguments, maximum authorized 2 but found 3"
    },
    "id": 1
    }


WebSockets support
==================

In this section we'll learn how to enable and subscribe to WebSockets via Postman client.

.. warning::
    - Experimental support for WebSocket is a feature that is subject to change in a future releases.

Available subscriptions: 

- `subscribe_new_blocks/unsubscribe_new_blocks`: subscribe/unsubscribe to/from new produced blocks.
- `subscribe_new_blocks_headers/unsubscribe_new_blocks_headers`: subscribe/unsubscribe to/from new produced blocks headers.
- `subscribe_new_filled_blocks/unsubscribe_new_filled_blocks`: subscribe/unsubscribe to/from new produced filled blocks with operations content.
- `subscribe_new_operations/unsubscribe_new_operations`: subscribe/unsubscribe to/from new received(from network) operations.

To enable WebSocket support in Massa node, edit file :code:`massa-node/config/config.toml` (create it if absent) with the following contents:

.. code-block:: toml

    [api]
        # whether to enable WS.
        enable_ws = true

Postman brings support for WebSocket APIs, more information about it `here <https://blog.postman.com/postman-supports-websocket-apis/>`_.

.. image:: postman_websocket.png

-   run the massa node
-   connect to :code:`ws://localhost:33036`
-   send the request message:

    .. code-block:: json
    
        {
            "jsonrpc": "2.0",
            "id": 1,
            "method": "subscribe_new_filled_blocks",
            "params": []
        }

- If the request succeed, the response will contains a subscription id:
    .. code-block:: json
    
        {
            "jsonrpc": "2.0",
            "result": 3508678639232691,
            "id": 1
        }

- Result:
    .. code-block:: json

        {
            "jsonrpc": "2.0",
            "method": "new_filled_blocks",
            "params": {
                "subscription": 3508678639232691,
                "result": "FILLED_BLOCK_CONTENT_0"
            }
        }

- A message is received everytime a filled block is produced:
    .. code-block:: json

        {
            "jsonrpc": "2.0",
            "method": "new_filled_blocks",
            "params": {
                "subscription": 3508678639232691,
                "result": "FILLED_BLOCK_CONTENT_N"
            }
        }

- unsubscribe and stop receiving new filled blocks:
    .. code-block:: json
    
        {
            "jsonrpc": "2.0",
            "id": 1,
            "method": "unsubscribe_new_filled_blocks",
            "params": [3508678639232691]
        }

- Result:
    .. code-block:: json
    
        {
            "jsonrpc": "2.0",
            "result": true,
            "id": 1
        }


.. note::

    Multiple subscriptions are supported

Explore Massa Blockchain
========================

In this section we'll learn how to interact with Massa blockchain via `curl` commands which
will create JSON-RPC `request <https://www.jsonrpc.org/specification#request_object>`_ calls.

.. warning::
    - We'll use only public API methods in testnet node.

**Public** API

a.k.a. **user mode** methods (running on `<https://test.massa.net/api/v2>`_)

`get_status`
------------

Summary of the current state: time, last final blocks (hash, thread,
slot, timestamp), clique count, connected nodes count.

-   Query:

.. code-block:: shell

    curl --location --request POST 'https://test.massa.net/api/v2' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "jsonrpc": "2.0",
        "id": 1,
        "method": "get_status",
        "params": []
    }'

-   Result:

.. code-block:: javascript

   {
    "jsonrpc": "2.0",
    "result": {
        "config": {
            "block_reward": "0.30",
            "delta_f0": 1088,
            "end_timestamp": 1667142000000,
            "genesis_timestamp": 1665405000000,
            "max_block_size": 500000,
            "operation_validity_periods": 10,
            "periods_per_cycle": 128,
            "roll_price": "100",
            "t0": 16000,
            "thread_count": 32
        },
        "connected_nodes": {
            "P126wpqvDP8GQqeS4WZq9fwRsmzAKrKfQQdXigK3zw53Ai1HW3aN": [
                "147.182.147.178",
                false
            ],
            "P1278WinKWC2RtrbskXwksrGXcHCAWwX8aBhvvMhtEaj3KjejsND": [
                "185.138.164.167",
                true
            ],
            "P12M7iQ4AmbkC2UZXRamHKHgGrq3dJmgCun8QjYQYRRaPcG8Zpvw": [
                "77.222.63.32",
                false
            ],
            "P12QQG516ahWuNaPnRbV4FU8RYuUAH6V7oFqVrotg5xJTXiq73tV": [
                "165.227.35.214",
                true
            ],
            "P12TK7PJreAzh9NrRWFXkKpm354piPupdZsd9i1B7geLJk2fYBVA": [
                "94.250.203.240",
                false
            ],
            "P12cJRDAyctwMQcJ6bxxNbjsnWyKxgHfKVzr9xsJ9A741u4kzYWa": [
                "95.216.156.29",
                true
            ],
            "P12eHJrC3WdZ2qdaLUHP7jNRuaK9WoAV5W4NDjXTgb5mNv38unc8": [
                "167.86.111.35",
                false
            ],
            "P12p6axwXgMW2RrUdFojKaRGvTnb1ajyLkXTnEUcqUjXwnfQMk9w": [
                "173.212.236.220",
                true
            ],
            "P12rJQaPcxj4XNKz1GhfQftxFLNEfJRQzzuXKngimq3VPRSBUAeF": [
                "167.235.145.174",
                true
            ],
            "P12rPDBmpnpnbECeAKDjbmeR19dYjAUwyLzsa8wmYJnkXLCNF28E": [
                "158.69.120.215",
                false
            ],
            "P12vxrYTQzS5TRzxLfFNYxn6PyEsphKWkdqx2mVfEuvJ9sPF43uq": [
                "149.202.89.125",
                true
            ],
            "P12wgY28tM7DY9xD7Auwh3oCijX3XgvkCHnrTqfD5VH6kXp6dkzF": [
                "146.19.24.215",
                true
            ],
            "P16nCxGtVUoEbJE6gGMjPABq3V5RQ1dVB17hqxSSERViB8b1WJN": [
                "159.203.14.185",
                false
            ],
            "P1P19Xw3Kb7bVeQkxpKaJkE5zY7u64gMiJHcVEHpTPwtzvUMa6Q": [
                "5.161.84.250",
                false
            ],
            "P1W6qg7AGkukq16ikJD2Aa41pW6cQfNr123u1KK9yBf92wsi3vj": [
                "84.54.23.207",
                false
            ],
            "P1bxqhJfzre8sGYCF6MA5MW4utVvTJPEKEVnWCLLLifKCUwGsqx": [
                "194.163.189.5",
                true
            ],
            "P1g7MNCLjL9DdFRUWvnwPJg4fjaCQCVke3mSc5k7rFUA7wRbiZB": [
                "95.111.248.121",
                false
            ],
            "P1gP6pLsToXZuFawvcdfYaARv787ezdsQW1Hw27SkwZz2ZgKH9J": [
                "209.126.13.129",
                false
            ],
            "P1hX6SBEU3duEmNuab9QWbh8uLPx7gxDHzSFgNJw4XX5AND5WQs": [
                "139.162.110.127",
                false
            ],
            "P1m6jR5Si3KKQb7VDjpd4HhVstdHJYFHGAKnK9GGszheN6hVtM3": [
                "62.171.141.30",
                false
            ],
            "P1rN38cfybsGB3UgLxWB6qr57MyThVc43imJSKkg5YNgjswnMUF": [
                "144.126.146.140",
                false
            ],
            "P1siwj1nNwHh3HB2bHqU94ESjMgicvxq5kfjDyBpDEUVgwYDFvH": [
                "162.55.181.167",
                true
            ],
            "P1sr9pwXuGAPsrvdnHtiRvQaTkGap92YPptQrEjRcrq8sfqodye": [
                "213.21.221.200",
                false
            ],
            "P1ubGD5Mm3wNmh3zawVR6DUDc3CB4pkDjqmntUGDyVQk4ddAXQa": [
                "194.195.120.50",
                true
            ],
            "P1zGmtwZ7g9tdtwmmhyNvoAE8tdk6qMLw7Pf4uRsBGwyhKEhV6S": [
                "34.125.115.189",
                false
            ],
            "P1zVQSNYWA6bXEGZeJwCgntFJjmvMu8YtgNw9fkiKJ4WmBYXLzo": [
                "65.108.53.204",
                true
            ],
            "P1zb2dnsQpDxcQL3R77fSnhzYXYpwnH5gDXZh4HMa7iAxx57s24": [
                "38.242.158.106",
                false
            ]
        },
        "consensus_stats": {
            "clique_count": 1,
            "end_timespan": 1666542101196,
            "final_block_count": 118,
            "stale_block_count": 0,
            "start_timespan": 1666542041196
        },
        "current_cycle": 555,
        "current_time": 1666542101196,
        "execution_stats": {
            "active_cursor": {
                "period": 71068,
                "thread": 22
            },
            "final_block_count": 105,
            "final_executed_operations_count": 53541,
            "time_window_end": 1666542101196,
            "time_window_start": 1666542041196
        },
        "last_slot": {
            "period": 71068,
            "thread": 26
        },
        "network_stats": {
            "active_node_count": 27,
            "banned_peer_count": 0,
            "in_connection_count": 16,
            "known_peer_count": 10033,
            "out_connection_count": 11
        },
        "next_slot": {
            "period": 71068,
            "thread": 27
        },
        "node_id": "P1VRyXjUaHeJd4Rmr3waVmpZDFzzH5ARRi3f5ye5BYgxBmxHC7X",
        "node_ip": "141.94.218.103",
        "pool_stats": [
            168394,
            1344
        ],
        "version": "TEST.18.0"
    },
    "id": 1
    }

`get_cliques`
-------------

Get information about the block `cliques <https://docs.massa.net/en/latest/general-doc/architecture/archi-global.html#block-cliques>`_  of the graph.

-   Query:

.. code-block:: shell

    curl --location --request POST 'https://test.massa.net/api/v2' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "jsonrpc": "2.0",
        "id": 1,
        "method": "get_cliques",
        "params": []
    }'

-   Result:

.. code-block:: javascript

    {
        "jsonrpc": "2.0",
        "result": [
            {
                "block_ids": [
                    "4ba4uaiofBMAdgXC8zxLaBygieGBB3KyeSGcSrSMMbV9m6sK1",
                    "2sMQGvSuoA1VzqPPFksLDQbE6zsKzVyzaBqDVuEH7W1DRuB2q8",
                    "LmEh7ttGxVr8nFi4t9VNMzCXt3PkjgwFg7pEbbqfkqcarco7r",
                    "t6NxNRaiimUGctzfiMhwqA3uYFGcCAs3KawwgzP7huwAbki88",
                    "Va6njRuA9UyXKDyzj5FqWH7yRsanMBtZByBxfGhqapTyZRvYS",
                    "Do79PdHf7rrssmHmgay4Ypy4kGw4rNwzPq1LZdhdizHSUpj2C",
                    "Ke1LUGXeHNwo7EWpNbVMLQvtCAQdKcdvsVqNJuEAwgiHz8RNP",
                    "2NMDv2JHUKDEFsGm6HAsznAeCKbkLdMmMuTkchUKFMeT1R95rx",
                    "24iPfMJBE5Z89UW3QN1uY4Eu9p6vvToLpqgA3XYAhUSF7XqcUJ",
                    "23dAyzzeKuREJPverLXfVLiXkErz69Rj5dPsrCAua8Rq2Bebe3",
                    "5L1SZveEZBqKHMNwihHLp94hZweiC3m3aAJ68hSDRNpCN8GAx",
                    "5M1W27bkw8zr4PWTQpW61F8eNS1YBYrN9n9ZWP69cSwKDfx1p",
                    "TkVkE8aFTU3qW2cH4PgdAuicaRojkwN9HSfg3TVzY32XKab3R",
                    "2tZckQhui23EEw96awyfYn7B4TUSukoGoZwypkNM1fHymz82dg",
                    "2qsYEAg243dVmj3jCFbiy3BxokPfa5FscYaGoMybSHMW7Rgt1P",
                    "2hoTAg6gK25xde5NbvuoWY5kHwqoLW8vQLDnkx2H5oefHYFswv",
                    "2oacHhWZDpgLTVTZ1m3zaF59dBbKcysvn35jFCptkdVuS9D6go",
                    "5eexoZkpCUEDsiBKPquix1ivwvoS74DTPkqagVAs9kHTrN2cW",
                    "23V2yXFGu8PEgmeb3AyGWWtC74PqZ7krnnaTM7Zcf4rVMbFavR",
                    "aHCAUV5aLLgotKB8V1WJqAaWJGpsYo9RV7T9DRaEtgXTnTKoP",
                    "SVGhtws4yWB1Q1LKcrFR83CnArCqT6kptnpAuxUc4bYtLByQB",
                    "e7JUqLsrAQa9mNsbUzb8rtbSjRMi6kt4fuBNYWJ4qLBFxHx5B",
                    "2fcDY7aiLGL9o8PkssgANsLRcQ1gZBi75L1hP3hyGRNDpnaFdN",
                    "kqCFcbpM4PB3SL411Kvi9yo5jhCwy7ZZZLfE5NV7qeDWeLhwT",
                    "qpm9CSQgKmqomNZpG7yQNsoB2qz3GLyduECaybvqDfVNZMU5X",
                    "SaHyMRiWXLn5GVWQtiUpXFY17qpDN3LENRgDuQmoL7BuKCNEZ",
                    "2SGMPZWdhYm9yvDt3yKuBRa2hJaCQB411U455jwYzKQqY6cXjG",
                    "23pyjncfV9xZYkmQEJjLuxtW8uCC714TR2qHoNQ1XthpmowGym",
                    "2AtdgJJqjV7fN9sm48swQ5cwDpzwznmsPerfJJwj9UfzV4E9zn",
                    "dz9VRGrgfuxuyDPwPNsKQTTtWzsYcHHCFKbX74hKeiZiHWnpk",
                    "2t8WKiLTjtVy8bG8kY4NweqSNCZXHYsiPotAz1mQPYb5uBSg7z",
                    "v1QPVXtuF4qq1bep4QEZXbTHZNEq1XNzx85z87zjfDx99MX2j",
                    "Ba1qfZUgsamwbzRbC37C7qktGZHm8S7vNvtjFY5ZMQdE5JtFX",
                    "2sVmHfTCV1wiEL2TvHtsFm6z72T6fQrCL3hHo791x1G2XeeFo8",
                    "2DQQN8ZVm7Uemp6SSGj57Haa3Z6dmThcMLSFCRfqeN3PGCo2mg",
                    "2SdGdaqiMn1ygxnxSR9rze6H4xRwgDCzMQ654hGKCAHqKYhi79",
                    "t6JkC332fABHr3Eyu3AjxXotuAxCLme9noyq2btoQyd98bb9n",
                    "3nPhmDJFs21QqKLM8QnH1YXBY97ntam2xSEMiGkQnUtLBL1gF",
                    "ZijiXRYJTvw1rDYRQVysQqKVx4Qr3icEYAXdP8a7EeE7BLh6N",
                    "2QjvGQXNGAv1UnEbrbfgsivJicF6GMkGFGS9Bbtn5BrG2xZqoN",
                    "dvTaZff8Myoff5HrQxZGkhtoVjQEM6Pctb54kvYHFGbXLPQM1",
                    "itp7T8Y8zdkGjMRdtrRGMoV9u76RXvqZ9BCgLhxzKMvaiSGV",
                    "9QRsXmeTr5AYRv6sLxNFZ6wDFwH5EX4BwBc43uMEJTWUydgws",
                    "stxd3WPygF2f7oBPbcCJPKSLSftg16VAnZBYieBVt9yUi9hLQ",
                    "5JFwVNbK3unFqq3L5dEno44eJp4KCxiic7T8NaFhpqHuLocRb",
                    "5zcfYVtQAiVNe4o8SFNd1xXY2idF5rVG7Qx8cYPQaRWdkapTJ",
                    "2w2z9ygGDRSMYvA86Mx5SE1fKgd5brcCLQ6xEb3JfWjyu932y6",
                    "2nnFjXay8V8bVhTsf3PgxZwX3Hbpnjq1XHQUG7yFqbD2NAQo2C",
                    "QVniT5MFNwCxoE8DmxmVbHjEAHMreoQsyhE4XFgAr9GeLHxdD",
                    "CarArU3oR94FmbBcerU2agh5tb9g1Y4di9NrzUf3kt5LwrZKQ",
                    "2BG36zn9QMe2hDrVov3JbHeYYKg4LxEJv3Fo7eAogMTLn9agJ9",
                    "HQjsVy1LdscK3dNywgcbsZhVRn81VAPKVdhVQ7oipnzrEanR9",
                    "UC1nMQoCStJ47BXPwrqjKX7Symfv4a9yj7FHCCK1HGAsvBnFH",
                    "26dJaQ8tSyES5NoPLRGcdpawGH8ZX2irMhUUokZ12taZNcwTsV",
                    "grz7vaHHRsKPfiAkNHjt2A5GDDVAHNnThrwQ85iBRgWRLgudX",
                    "RoHBFgxpafkXR9utXJLAanbVAs3Qo8NfCJUtBfR5dnfyWikcs",
                    "2M5mLpaWBkcWB7EkqdAJjcPBWUxBKzBwYMLDB6EjWXch3qTQt6",
                    "2WYzs5RTQasBPpYYsqdAnoVBSuQkHcAM45tcn7a6Eh9omizNZw",
                    "etKfjwLKac5TfnGwJqkyTUHSTp1bmQrqMYpsVXUJs9cpUzPYn",
                    "jUkFF9VfBwsPqm5hZuUJ6JqKfRUNLcki1dWEZLQQvnvWzs55",
                    "hrRHmATD2tNT1LvFUuxH92eGsPCkd7ADwYPEfZsANdCTR1NGR",
                    "2dqq6SN1KTV9QpMUV6cQKkzBYKL7uaDq7vVeDPLkfUJfGWmtdh",
                    "6XpZdE1jZ2HwFx4Uekk2udCdqF6C1SZjDDwAdf6nyHbK6zN22",
                    "k7gHH5YPQ2sxKNbq7fFimrSTH5UuHoyHdn8KKKJT2DdYJBWow",
                    "xSaWHnBY9amMunmZzuk8jHvAi5QMWCBYkhUadisWWDhKLt9zf",
                    "fHiU5y72SmHqS1aBsauar1vCA3XCtn8jivsoCApfsVH4Mfdd"
                ],
                "fitness": 1112,
                "is_blockclique": true
            }
        ],
        "id": 1
    }

`get_stakers`
-------------

Get information about active `stakers <https://docs.massa.net/en/latest/testnet/staking.html>`_  and their roll counts for the current cycle.

-  Query:

.. code-block:: shell

    curl --location --request POST 'https://test.massa.net/api/v2' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "jsonrpc": "2.0",
        "id": 1,
        "method": "get_stakers",
        "params": []
    }'

-   Result:

.. code-block:: javascript

    {
        "jsonrpc": "2.0",
        "result": [
            [
                "A12RHPuU7JFS2rxvxL6MnzVoBJAZr7ivFFJuiRPv4mi5wv8z8VYm",
                112
            ],
            [
                "A12axF2vj3GMV87LV5cEtJwntrzTJXQsYCsp1jtXXqCkiF1X6VwX",
                80
            ],
            ...
            [
                "A112oKyfHsRyaLHdgRDY7EkD1X2Rt8UnMr226BjPxirEsJbFjez",
                1
            ],
            [
                "A114oowRjFLH5nWuL2nhc6RmN2RYZpXu6TXbs1dTxF41Qvwd3Ku",
                1
            ],
        ],
        "id": 1
    }

`get_addresses`
---------------

Get information about `address <https://docs.massa.net/en/latest/general-doc/architecture/archi-intro.html#address>`_ (es) (balances, block creation, ...).

- Query:

.. code-block:: shell

    curl --location --request POST 'https://test.massa.net/api/v2' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "jsonrpc": "2.0",
        "id": 1,
        "method": "get_addresses",
        "params": [["A12s675r1Kn1i7BF8QRVCdqPFiNeAZ1fojs1q2jun6wEGbow1brZ"]]
    }'

-   Result:

.. code-block:: javascript

    {
        "jsonrpc": "2.0",
        "result": [
            {
                "address": "A12s675r1Kn1i7BF8QRVCdqPFiNeAZ1fojs1q2jun6wEGbow1brZ",
                "candidate_balance": "84.243137236",
                "candidate_datastore_keys": [],
                "candidate_roll_count": 1,
                "created_blocks": [],
                "created_endorsements": [
                    "Yed7BJj9QqGG3tDCqoDTn7uMfGJrvPVh9agCYhNoCUUPwHfD3",
                    "TLrtZAgEyHuUooRMCZj6mVXz11QeRvr8WoudTSFLeTku5J5nf"
                ],
                "created_operations": [],
                "cycle_infos": [
                    {
                        "active_rolls": null,
                        "cycle": 590,
                        "is_final": true,
                        "nok_count": 0,
                        "ok_count": 2
                    },
                    {
                        "active_rolls": null,
                        "cycle": 591,
                        "is_final": true,
                        "nok_count": 0,
                        "ok_count": 0
                    },
                    {
                        "active_rolls": null,
                        "cycle": 592,
                        "is_final": true,
                        "nok_count": 0,
                        "ok_count": 0
                    },
                    {
                        "active_rolls": 1,
                        "cycle": 593,
                        "is_final": true,
                        "nok_count": 0,
                        "ok_count": 0
                    },
                    {
                        "active_rolls": 1,
                        "cycle": 594,
                        "is_final": false,
                        "nok_count": 0,
                        "ok_count": 0
                    }
                ],
                "deferred_credits": [],
                "final_balance": "84.243137236",
                "final_datastore_keys": [],
                "final_roll_count": 1,
                "next_block_draws": [
                    {
                        "period": 76077,
                        "thread": 4
                    }
                ],
                "next_endorsement_draws": [
                    {
                        "index": 15,
                        "slot": {
                            "period": 76081,
                            "thread": 1
                        }
                    },
                    {
                        "index": 0,
                        "slot": {
                            "period": 76081,
                            "thread": 2
                        }
                    }
                ],
                "thread": 30
            }
        ],
        "id": 1
    }

`get_graph_interval`
--------------------

Get information about block `graph <https://docs.massa.net/en/latest/technical-doc/glossary.html#graph-stuff>`_  within the specified time interval.

- Query:

.. code-block:: shell

    curl --location --request POST 'https://test.massa.net/api/v2' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "jsonrpc": "2.0",
        "id": 1,
        "method": "get_graph_interval",
        "params": [{"start": 1666559894589, "end": 1666559896589}]
    }'

-   Result:

.. code-block:: javascript

    {
        "jsonrpc": "2.0",
        "result": [
            {
                "creator": "A1DGpvoAMv2GAWKS2BGF4iFQaq6bHDgpfu2vhGFogZMcaSsy7DY",
                "id": "D6kTS4Wh3B7FRDCw6ncqrCuY7NVPYBbGwdSg814Kd13fS7xQa",
                "is_final": false,
                "is_in_blockclique": true,
                "is_stale": false,
                "parents": [
                    "2GcJHmGY1QEyWmr4Rh2QSWcxE5icu8szTLJMyZx6fSGZDFETBZ",
                    "2A1PFNRpR1MtJYwnp9vF3Pcc8xQ54mpPDjLgjJfZb1yxzTDXjZ",
                    "2nL4CBXZKiv4szqvq4cBTtnfUtk5ozRg9Kd45y4UTRNLuHBLHT",
                    "2ZuyfDizeBfMgUHgsLgYd7nRMVbk253A1YZUSpjY6bq3med7LY",
                    "2Sdvt2oBdYXJ2LSP4AYfJ98DE4mGsBpC2pSLWudYkL92utv9EU",
                    "24VPQmcBaFCma9ypn6MMki8FxNQYwcyYnXhhCdAACX1dqFQB94",
                    "trM5GjcVp6z4MWrTxyNJGzPQSR8mbNAnaPqLBLUw8vVaTEdNb",
                    "AfsZ11V2sCcJnWjD68yrXq1D7gvcv36vByXUAGsuohp93yW7u",
                    "eDdqMET8smfSpu93pd8iPsNnnEuhutvH9AqXyXdRRf5GVDK2w",
                    "tbszcUiCBqq6ty33wHq52wZ6kdyTo591KyBDfY4FeWaDueKM7",
                    "tXTMdL67gYMFiNyugNTBLP9dXMrd5hW4yYG7k4iwZtzhWsD8u",
                    "YtQ1UxfuKrgCNYCzbjhDEUbGeRP52j2XizHuK785L7DhHJ1Xr",
                    "ybCiSCUPGJBo9FAKE4zus4CG2sSsxFNmoc7qD2Xrn7TjG5TqN",
                    "zLPd1vNoYuzHuy3kWg5hbfxKaxSAKk9JGYYd38QyMiVZ6K3BF",
                    "2SAHtG3Jn4VHnbzo5bohbLqL7cx7MwQGUr8V6CRaLWsVQetBfu",
                    "2N6Wa26nMkx6yuCPwSGm9Wd1EP7u2Ad67et1evuLj4osEWUTYJ",
                    "2NVq8igEKnXZ1ysatU9xo66PVGZKx8MwQqRfHmRPg6vjeTy3pP",
                    "W5vKLkAyVtG6BNjm4WHC3a7Dz53KDf8v8aKcDjYxRFTXmDd1q",
                    "2q3KnZ4tPfEZStQw7LbGCwfehyPckSpWjQc3cuwCWpzSb7XxRr",
                    "2akcCnYAFnG9RgVWxprsNgwAv5WDQ8Lm9TfTGb7nrsHn4iiDA2",
                    "k3PNeH648id9knfLT2qPpv46AVpZUXaM8qSHgNDx7uw1ieCZL",
                    "2YTQhFoBdBo8ofq4ZRJofCQAGgbrQR27CcvgDkdUKMU4H1Fv8t",
                    "2XcCTND2GpDSmouGD7ev5JHJNYP5gisgpruMkC5G48d57rBQ5i",
                    "4MEYgdhAUUgXLy9CgCMuZTfwTZPLhMbrNm5TFWhDk6EVAoScf",
                    "exawL8H3A42zLr5UcCvjMY4TBBn5u84PMtBCrfyyZPWUhayZt",
                    "LP8FabDMiAwNkqcDs4z163fsU4jEtRqK9j6sfeXPyNqLJye2L",
                    "2gnfhUjLvbRjzu95iTcHSAwF8SenfsCwtLQP3HAEo5Y1NdTUqs",
                    "44S9aCFRVd7zDeTBbbTnjHoqy7Up7EpzLVBmARnyfb6HiJENE",
                    "uM6w8xGvBxjUYSFzn7DUUV3RUoj8V1iPGGka4ap2g6vCvCqoE",
                    "2eejA5Y81RdvDbk2iVFayPvkFpZvm91dNPkq1r1TQMFoaxdwFA",
                    "2o1oMCY867kndLRXQ2AhscxhoTE5Q9xDdZYwu2hKViZh1JV2oa",
                    "gDSvWadXAy2dX9TQya5a5Rj7G7oZSJ3ztsrfKjJMYMFFWwyNA"
                ],
                "slot": {
                    "period": 71152,
                    "thread": 2
                }
            },
            {
                "creator": "A1f2cgeNKMWtauyAxLy1LMqiVt7ZShgffqc9DbfMSCLpv5xovkP",
                "id": "UXCyVSHg18AraZP9BG6gWRszPyVpasQ6NMc5aBJezYyQibnL1",
                "is_final": false,
                "is_in_blockclique": true,
                "is_stale": false,
                "parents": [
                    "2GcJHmGY1QEyWmr4Rh2QSWcxE5icu8szTLJMyZx6fSGZDFETBZ",
                    "2A1PFNRpR1MtJYwnp9vF3Pcc8xQ54mpPDjLgjJfZb1yxzTDXjZ",
                    "2nL4CBXZKiv4szqvq4cBTtnfUtk5ozRg9Kd45y4UTRNLuHBLHT",
                    "2ZuyfDizeBfMgUHgsLgYd7nRMVbk253A1YZUSpjY6bq3med7LY",
                    "2Sdvt2oBdYXJ2LSP4AYfJ98DE4mGsBpC2pSLWudYkL92utv9EU",
                    "24VPQmcBaFCma9ypn6MMki8FxNQYwcyYnXhhCdAACX1dqFQB94",
                    "trM5GjcVp6z4MWrTxyNJGzPQSR8mbNAnaPqLBLUw8vVaTEdNb",
                    "AfsZ11V2sCcJnWjD68yrXq1D7gvcv36vByXUAGsuohp93yW7u",
                    "eDdqMET8smfSpu93pd8iPsNnnEuhutvH9AqXyXdRRf5GVDK2w",
                    "tbszcUiCBqq6ty33wHq52wZ6kdyTo591KyBDfY4FeWaDueKM7",
                    "tXTMdL67gYMFiNyugNTBLP9dXMrd5hW4yYG7k4iwZtzhWsD8u",
                    "YtQ1UxfuKrgCNYCzbjhDEUbGeRP52j2XizHuK785L7DhHJ1Xr",
                    "ybCiSCUPGJBo9FAKE4zus4CG2sSsxFNmoc7qD2Xrn7TjG5TqN",
                    "zLPd1vNoYuzHuy3kWg5hbfxKaxSAKk9JGYYd38QyMiVZ6K3BF",
                    "2SAHtG3Jn4VHnbzo5bohbLqL7cx7MwQGUr8V6CRaLWsVQetBfu",
                    "2N6Wa26nMkx6yuCPwSGm9Wd1EP7u2Ad67et1evuLj4osEWUTYJ",
                    "2NVq8igEKnXZ1ysatU9xo66PVGZKx8MwQqRfHmRPg6vjeTy3pP",
                    "W5vKLkAyVtG6BNjm4WHC3a7Dz53KDf8v8aKcDjYxRFTXmDd1q",
                    "2q3KnZ4tPfEZStQw7LbGCwfehyPckSpWjQc3cuwCWpzSb7XxRr",
                    "2akcCnYAFnG9RgVWxprsNgwAv5WDQ8Lm9TfTGb7nrsHn4iiDA2",
                    "k3PNeH648id9knfLT2qPpv46AVpZUXaM8qSHgNDx7uw1ieCZL",
                    "2YTQhFoBdBo8ofq4ZRJofCQAGgbrQR27CcvgDkdUKMU4H1Fv8t",
                    "2XcCTND2GpDSmouGD7ev5JHJNYP5gisgpruMkC5G48d57rBQ5i",
                    "4MEYgdhAUUgXLy9CgCMuZTfwTZPLhMbrNm5TFWhDk6EVAoScf",
                    "exawL8H3A42zLr5UcCvjMY4TBBn5u84PMtBCrfyyZPWUhayZt",
                    "LP8FabDMiAwNkqcDs4z163fsU4jEtRqK9j6sfeXPyNqLJye2L",
                    "2gnfhUjLvbRjzu95iTcHSAwF8SenfsCwtLQP3HAEo5Y1NdTUqs",
                    "44S9aCFRVd7zDeTBbbTnjHoqy7Up7EpzLVBmARnyfb6HiJENE",
                    "uM6w8xGvBxjUYSFzn7DUUV3RUoj8V1iPGGka4ap2g6vCvCqoE",
                    "2eejA5Y81RdvDbk2iVFayPvkFpZvm91dNPkq1r1TQMFoaxdwFA",
                    "2o1oMCY867kndLRXQ2AhscxhoTE5Q9xDdZYwu2hKViZh1JV2oa",
                    "gDSvWadXAy2dX9TQya5a5Rj7G7oZSJ3ztsrfKjJMYMFFWwyNA"
                ],
                "slot": {
                    "period": 71152,
                    "thread": 1
                }
            },
            {
                "creator": "A1zbiUJjfAjcKg5N2AfMRgHz9Fo4SxhBSNgSv5TrFaDp8t2SfCG",
                "id": "cSuzktjQWxtijFMkBDCzuxnrWv6LgMqcZKoJxE3xhyMgDig6n",
                "is_final": false,
                "is_in_blockclique": true,
                "is_stale": false,
                "parents": [
                    "2GcJHmGY1QEyWmr4Rh2QSWcxE5icu8szTLJMyZx6fSGZDFETBZ",
                    "2A1PFNRpR1MtJYwnp9vF3Pcc8xQ54mpPDjLgjJfZb1yxzTDXjZ",
                    "2nL4CBXZKiv4szqvq4cBTtnfUtk5ozRg9Kd45y4UTRNLuHBLHT",
                    "2ZuyfDizeBfMgUHgsLgYd7nRMVbk253A1YZUSpjY6bq3med7LY",
                    "2Sdvt2oBdYXJ2LSP4AYfJ98DE4mGsBpC2pSLWudYkL92utv9EU",
                    "24VPQmcBaFCma9ypn6MMki8FxNQYwcyYnXhhCdAACX1dqFQB94",
                    "trM5GjcVp6z4MWrTxyNJGzPQSR8mbNAnaPqLBLUw8vVaTEdNb",
                    "AfsZ11V2sCcJnWjD68yrXq1D7gvcv36vByXUAGsuohp93yW7u",
                    "eDdqMET8smfSpu93pd8iPsNnnEuhutvH9AqXyXdRRf5GVDK2w",
                    "tbszcUiCBqq6ty33wHq52wZ6kdyTo591KyBDfY4FeWaDueKM7",
                    "tXTMdL67gYMFiNyugNTBLP9dXMrd5hW4yYG7k4iwZtzhWsD8u",
                    "YtQ1UxfuKrgCNYCzbjhDEUbGeRP52j2XizHuK785L7DhHJ1Xr",
                    "ybCiSCUPGJBo9FAKE4zus4CG2sSsxFNmoc7qD2Xrn7TjG5TqN",
                    "zLPd1vNoYuzHuy3kWg5hbfxKaxSAKk9JGYYd38QyMiVZ6K3BF",
                    "2SAHtG3Jn4VHnbzo5bohbLqL7cx7MwQGUr8V6CRaLWsVQetBfu",
                    "2N6Wa26nMkx6yuCPwSGm9Wd1EP7u2Ad67et1evuLj4osEWUTYJ",
                    "2NVq8igEKnXZ1ysatU9xo66PVGZKx8MwQqRfHmRPg6vjeTy3pP",
                    "W5vKLkAyVtG6BNjm4WHC3a7Dz53KDf8v8aKcDjYxRFTXmDd1q",
                    "2q3KnZ4tPfEZStQw7LbGCwfehyPckSpWjQc3cuwCWpzSb7XxRr",
                    "2akcCnYAFnG9RgVWxprsNgwAv5WDQ8Lm9TfTGb7nrsHn4iiDA2",
                    "k3PNeH648id9knfLT2qPpv46AVpZUXaM8qSHgNDx7uw1ieCZL",
                    "2YTQhFoBdBo8ofq4ZRJofCQAGgbrQR27CcvgDkdUKMU4H1Fv8t",
                    "2XcCTND2GpDSmouGD7ev5JHJNYP5gisgpruMkC5G48d57rBQ5i",
                    "4MEYgdhAUUgXLy9CgCMuZTfwTZPLhMbrNm5TFWhDk6EVAoScf",
                    "exawL8H3A42zLr5UcCvjMY4TBBn5u84PMtBCrfyyZPWUhayZt",
                    "LP8FabDMiAwNkqcDs4z163fsU4jEtRqK9j6sfeXPyNqLJye2L",
                    "2gnfhUjLvbRjzu95iTcHSAwF8SenfsCwtLQP3HAEo5Y1NdTUqs",
                    "44S9aCFRVd7zDeTBbbTnjHoqy7Up7EpzLVBmARnyfb6HiJENE",
                    "uM6w8xGvBxjUYSFzn7DUUV3RUoj8V1iPGGka4ap2g6vCvCqoE",
                    "2eejA5Y81RdvDbk2iVFayPvkFpZvm91dNPkq1r1TQMFoaxdwFA",
                    "2o1oMCY867kndLRXQ2AhscxhoTE5Q9xDdZYwu2hKViZh1JV2oa",
                    "gDSvWadXAy2dX9TQya5a5Rj7G7oZSJ3ztsrfKjJMYMFFWwyNA"
                ],
                "slot": {
                    "period": 71152,
                    "thread": 3
                }
            }
        ],
        "id": 1
    }

`get_blocks`
------------

Get information about `block(s) <https://docs.massa.net/en/latest/general-doc/architecture/archi-intro.html#block>` associated to a given hash(s).
- Query:

.. code-block:: shell

    curl --location --request POST 'https://test.massa.net/api/v2' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "jsonrpc": "2.0",
        "id": 1,
        "method": "get_blocks",
        "params": [["D6kTS4Wh3B7FRDCw6ncqrCuY7NVPYBbGwdSg814Kd13fS7xQa"]]
    }'

-   Result:

.. code-block:: javascript

    {
        "jsonrpc": "2.0",
        "result": {
            "content": {
                "block": {
                    "header": {
                        "content": {
                            "endorsements": [
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 0,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A12N9nUN9r1eUheMZ36AA3RTDYepLtEMpHZoBvzQmxw4hNcJV7tH",
                                    "creator_public_key": "P12qBafeiXMypqiChy7KEjqgAaUzbWJHhJALjfxzzY5hEH5BwL2c",
                                    "id": "2jtHfATDrho9Ttkxz3xp26WwjjREPVQV16fwMUCGyjnEQoyU8p",
                                    "signature": "XLJd5dSZsaQ3UYuuSGBGCbVsEM3aGTxAGigT81bVto7CypivDDwoPb6kJWXKzhvRi14qh3ReFqa7zzf3r5hYf343nqceH"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 1,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A1RMafAnGhMHoVzvtBBP1u6PTCoMRBpQSQxxJb4e6ySDS6BpHxE",
                                    "creator_public_key": "P1GJCRP8UYmkt1ZUYScjuGcXXLmDzq1ijJmYtqKpkgKPBtazRGo",
                                    "id": "qELXLSgYd7aRBqgASfm5u3k4QMBSYBuQK79oMmf9Yohtr71ZH",
                                    "signature": "8kPzmEiku3FNbYgHVeY6cY14cQskDqBS2trH2z8NLyPaU8xauXa7dFMKKKpb88b1eEx1QsSLmTx7iHXrCKYgKm6vjz8EC"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 2,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A123ingVJVrQkHveBCoXCUePWnkYjJ5mJE1gHiEqu1zpqvXJuBSK",
                                    "creator_public_key": "P1tNLmbgiqNjYfA7Xy6QNCVMEPkDtUqHuw9DuVcWf4FoYimJPwb",
                                    "id": "2TbWNGBkPyXHGqVeTQMJrrt2E2858FtiCcBZoySQ5rXVToYDT",
                                    "signature": "XYj5LByWXoi2EXsJh4MVEAzrGy9evcwHFywh4cYFc9S6xEUoyg4wnCqUcy2GA9K4SxK4H4AZyAoE5u4H6dgv1h5Gk8R3H"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 3,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A1bVpBkQo3nt8tKFcCojbB7Nt179finvmm9TJJ7JWqrX5a2cHZM",
                                    "creator_public_key": "P1Jk6uzT4iryr1Q8ACqnKoQjPNKraQnwk972TpPkvpj4JTs5MGS",
                                    "id": "G6bLTh1BWzywrF9tEyScJyVLDxuk3n3abePWxrpcbqh9QQ1ah",
                                    "signature": "JScDT2tpLD8RULoPHCU2HMyxGxaxFExCVYjivbL4cbsyNy5J53pDWHne911eug1UMZFJr3s5y1t6NYy5Mf8zkRCg1JUjY"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 4,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A1HRFkU7Mhww3BckCuvaXizvGCVNWG6ZiERLfUhRytCQibwWBgV",
                                    "creator_public_key": "P1dv7uM55mh39PUrmuYbE7uWi66EUuRGQCRyPh43DPgbgT5DSpJ",
                                    "id": "Utxvrhw5X6rh5JPRE9LEExY1EMYWmoXYoPCApMb6ZTbd1rL73",
                                    "signature": "Mg7ZP3SJgZ97eGEoeuMHxvhv3FGREYqjANHupbjRb1qcDaEfih9xnA5zB5SZfiCRVpFZYXAsbET4GKi7Ne8uNGeS9AHsq"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 5,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A1VRf7guK6CrNkCz8PLAwtG18zrjZTd8PEnWXfjS6RmLLXvNjtu",
                                    "creator_public_key": "P1YudM7ga88ArqVmFipS6Qs36apViTde8MrdfUxmPcT8mEJ6vXh",
                                    "id": "NrCTcEdKB6CWrJnkBarPaseYUNx7uisq73u5PvuDaU7MnyUY5",
                                    "signature": "6CpqaEgzv59QakG8xf1QYwQgaSJxudk1GEGyRdBxFBpG5F3756hULFrZWFmdz66RcWtpT7TZ5CzPPACGLZxLCJow79L4W"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 6,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A12RHPuU7JFS2rxvxL6MnzVoBJAZr7ivFFJuiRPv4mi5wv8z8VYm",
                                    "creator_public_key": "P1LUAvxdts32qQHnCPMqvf3F8WsScshVY1og7d16x8SDvWtCT2Z",
                                    "id": "Rph1xFyRnarENnrm6ZzS8XvDfDPwVf7XVWm2CTZBDSfew3uaY",
                                    "signature": "YyKkN79gvpiEo6zQFbSgZv84sB34EvQ5LXgNp2MVoZAAbSKstGTopJ6t6fzzeDRFNVjbyqc7ZnDbLPq8PZ9WDo1yoepqo"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 7,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A1u9fCMusV2rp3m7uoi2e5EuKNGpgfw9nFxcgH7MxPzrd9nB8Mj",
                                    "creator_public_key": "P12ZVaa7sNWPuMyTC1ijJYHr1NuF2DvZotRxrjZCoHmHJnzk3cUp",
                                    "id": "XxYECs1HHJMqwavdoXx9WDEMGWxgM1ainm2VxtT2Fvan8yEJC",
                                    "signature": "Cg6Ajsp1QrSeLfeTFb8vyHZD63hWzMRyRQRT14MYDFthAfYcocjh4aWTqv8zQCyr2SXBHapiBeaYY6NXRJtzPqukULFQi"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 8,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A1jXja9XVuepgpq94XzfHM6t1GjGJusRVuqA65ADdkupGXkrfCR",
                                    "creator_public_key": "P13wkLrigKC2R8LbUpafsBAA3H6GwnL9DbawDe8Q8uMGqdeeqgv",
                                    "id": "2izC3L1eT9RzANsoowY4SDYbvRH14HFPGMRL3Y2sGP1EjiXR9H",
                                    "signature": "73nbrBKBpyW1unPhioCRwyA6ebqT2MZCU6LJazxLMX4qiKzy9mciwHnZLAnjrMCN6AReqaYE8E4TFQLRSgiYZ2KpqmCUW"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 9,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A121RTpsgvPJtxj9FnSvVJUqMahintXyaATdBcfRdtva9xBhvLmR",
                                    "creator_public_key": "P12qaehFSeCjQu5dqxeYDW8fuz3MieQXDPuCNmL1BpaswezhnDNp",
                                    "id": "hESY57Jhd2JhJfwK4yfFNhMBoDPQp1uhNppCCjB9nbFeeSair",
                                    "signature": "H1st549STBKmehRtqwFTnHVxNX3UrPzfPJU7fhNP3Q1JvpoCjzLEewKRzb7YV6u9oKXEEaPdWXmj5bDGXWJK1mLfAWp8w"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 10,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A12VJh2HhXBTxDHDr9cwayHQTbVbkbu3soQqsekckH3gXLWvxkZ2",
                                    "creator_public_key": "P1ss4j58UtMbVjtP3pKK76Q7mfC3ArEsLr35e9UuCALHLjbsv58",
                                    "id": "2SPHdLgWHYaGbZfV6ZUKJuJVLrWSGXauHHjRJS2tdaS382g4uQ",
                                    "signature": "Kyvz5rJf5x8jVkHxqeqLzsdHgeYWqSYKwmgLXotX8readhkj2Hvbrzkyiwfu96atwRjnm2wsRQRzM6R6AwrrL5nh7y2qa"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 11,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A12fCAsSsLnm6BMkmtq57YJtuPpLPb35H7Q9LoJLBgRptxWsFwnm",
                                    "creator_public_key": "P12DHUNJiWTYzU2hWV4CJH5KT99A4jrEQQte3gkKtnLNj9oWd78w",
                                    "id": "2LXXatLWkH8M1adBeGKwfH4GM7xG7JGYXpxiJYo89Jy1SQ9Cg",
                                    "signature": "QqCYp9oAUjuYppHmLVxyik7A1JqFvYdPpVNXpunzRqHc6QHD56Kundv2vcGaFViozQHGRmyAbjo1JLcp7npTiZmSmT6UY"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 12,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A129Eya4XLQ2nuDJjhFqrEsgdH27g89yVFedR1H2CiDy129Bxn8",
                                    "creator_public_key": "P1xAszTFsXawBtUoJE4hvKrEFYG7DDpZvdkHjvtTH332EF2PwkT",
                                    "id": "YoNAoDbYmFQE2X2G2TPxr2J3UTFrdTYLXEcYgVSLDtoi8iUBF",
                                    "signature": "Ja4Bo1ymdbX7FfgAPxfLBhbGG6KVECvD7GoAxEYqXUsX5y4K4JerNvQS1jBFMdHxNQDYfgG4E8xdm354tegEWujFzuCnL"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 13,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A13CUpBmzTC53uud6XccjPuLLTWVn6A6isfuWrmG4JyUzJocdYY",
                                    "creator_public_key": "P14kHa3rmmFzeH3CeaUaYxvyrE9NpDJXzGrNso42j6wNBRJ8RVL",
                                    "id": "U8weB8dyRFKhFwqwyU2q1BqSkHhFedUB1gJxNS63svqMCah71",
                                    "signature": "A8LDSGq7wJffHrqDaQhWePvPKRU1PGMhBFw9TeqfaK8PGxV7u9tSjWiiWbUjAaWHWWdwF5Cp7htx8MZ8ZKeziLkNizckZ"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 14,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A15dQSTXEj9heazPXuWrqb7YzvK6DkLbafE4iHziSybwyFEw2Pc",
                                    "creator_public_key": "P1Hg9TwLttUaJXVMByr88G9YHsJ7yEtonpXgYBwxezzwhmKfSWk",
                                    "id": "SpZHoVJUekZGAqtj2t5jEoKMHs5mi6oHVzYn1nm4GgoCd53cb",
                                    "signature": "RfwXPp9cHvKpXGyN5M9BQPAgfLqUxE6EbaqQcve3xHWsTQ5b6GiVNYkQjEhEWdLEDB7Z8qQt4TRAK6Rp7aABoWFQ6PF5c"
                                },
                                {
                                    "content": {
                                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                                        "index": 15,
                                        "slot": {
                                            "period": 72180,
                                            "thread": 31
                                        }
                                    },
                                    "creator_address": "A12Em4NP9afTfCQkopdF8WsPkU8PazVGf9J4kzLwBgyc187q6L5d",
                                    "creator_public_key": "P12683Xab7njz4K991Vvx39yBaMmpgJuB3A6d7U8uheYtYraw89X",
                                    "id": "gCeNaZy2ihWV7XfLwnNNsQ8G1m5sMzhMBd8aP8q4JFHUCqV9S",
                                    "signature": "D6A5HYT4FavULrTLWm4CibbWPNNBMtmXBi4XXWF3HDV8cMngs9DNy8PriPJasRodvTGpgEFyP1gowrXDaFU2PcRkB5mit"
                                }
                            ],
                            "operation_merkle_root": "27m2HEnhXU78gob1PUrXqpW8wek49enJGEf4SDo4f2RQ1j3fKD",
                            "parents": [
                                "voH6upJppWUeyZhcZMprzKhyVi5iHhXXpY3UasoUpAQaSi4xX",
                                "PcbnbucTNdMcfUqjGWWwJG5eLMJwvjdx2XTVGDAqGnh9zkYPn",
                                "FBKV2AU9iBnBLpsykQnqADVosCC417o1AdGZzbTCLPe6ENEZS",
                                "v8FBFQ7wshibyME8bTkJbq3HQwqpDvkidB43go2fY1wCrxFGg",
                                "2fc9VWfnH8f883Pf4wouDMuvtpxR7F2fzsf9cbq4A13UXNphdE",
                                "6zvFwHh373wJAKZs3oMRYdw2KwEqsjDQNoqbDjN89UUUEMe5M",
                                "Cpcjt6FcebQwwgS5RVdGKwpCbndVxZJrrFqhA82SFKCZRDEuB",
                                "AcWddtC2cqu9d2pSzyCVea84TZY5bP2bs8CKU8YYjw8vnexzM",
                                "2NehYFSDhAf3cZWb7fDXAWnYYD11uaYYZbuqUX13CBoVchAzGr",
                                "euSAdXo5QudXdFzwqpU67eCUF9b6VM8i9Qy5UKJFFC6EiLiJ4",
                                "DMh3dNWRNsVrV6P5SY9p5RCpvYpjaGdFWUu23HFL6TfKwy63n",
                                "pNKpTb8vh8eZ7YtB11psDH6TX6w9SsLdAwAV5oG6XR4P1Mdmz",
                                "2wenb1UzqRiHV5tixjMMqVkrwb8ywm7HT2UBP9dDZET7mnm3bi",
                                "pwhBtRuLNWP19hKJ5kvKdQjLjnjsJkeMqwMHmHt6ZhdNcYjuf",
                                "En16Wb744Rn2trfkeQREG3HSCju39xu9dK6EXiVhMaQ7QNTQc",
                                "Pn2yvpsFq6YYfH26RqXye9S25hbXgy8gvPQPFnrdVxxcqrZPv",
                                "2AamJsrQ68r3r9bhgsiAKkH7JGUSDLPCZUh91DxeHTBQWBRjQu",
                                "2LSHyysq3PRDhfYF68haUWHUW7oAwZXhzqewLJhAmVRwH6JjVh",
                                "jWvGUobun7mzuaJye5nYEFvSGrmyW4sjrmEZ3mvTAo7iQtmxQ",
                                "JsfRiXaoZY4tDqJWyEEpJkjchrcrGpdi2im77KW9bcGjX8FrW",
                                "FCzk7rYB8ZAqkkSchdxPeZQohjNU4Wwi2TdvNJe1Df14LnxW4",
                                "2HrJiz9fBDVUjVTF6aA3y8bZQcHVpnJygRkt8EtnkCB3HT9dxM",
                                "242ghWYwu8PKZSQoPoij2S8CE2u46zfcGdC42mNN12HFrj55aQ",
                                "2UCUqS26mbdbsFZkL1rNrrQu2SMZQnUZdTJ7tjo7QVjVVFvfxR",
                                "2Ch6hGDfEeUBamUrWojzrTPeswTuGVfwvjfDEKChNHU96A3PXo",
                                "pyK7qz9ebZ9bModjmuvvUoVmvBrLHUsUy8uqWRKemTh2zLFyd",
                                "2SJhPnDC8nV4SkaxTai4Gvxpzv14DeD164XHqKfSbV5byTjfqq",
                                "jsZEsz4U3jazbpLfXvK6BVRKyE4F3Sh6bS26AN9i7vM274XQc",
                                "2K75STq46JPJ2eUzZrepRNDPrfg6NgKC9cZFYFVXfKpJVBQAP8",
                                "3QYdeQrNsbBXpEUbqBY1v1UPWiwnvgSjL2mcG1fzkRC4Mso5J",
                                "V58LdmjJLvQLRVm4bqqiMn2ChGprgLDxwNm4gKJqz3UXFwonK",
                                "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV"
                            ],
                            "slot": {
                                "period": 72180,
                                "thread": 31
                            }
                        },
                        "creator_address": "A12Lh4yCTdF5P9Unt3Aq4aimUFafTiuGoYYHfqU2P68RkY35tFmQ",
                        "creator_public_key": "P12uRiEAgEGHPpNZ7fMFsh3XPYS6A9GB9PgDAQdwFbxCH4nxSQEF",
                        "id": "Fb46NHJCFTVgddSEZMEcmYeYpokvQv8gCYjnDBpXbAQBKpVE3",
                        "signature": "KMcsEkC6CEDE55tmvAfR47vhH1PvAd52SZWUyQ9kdobvkvEGMZS2TrDYHWP6jCod1QeQpaRY1BXM2FMSWn5EuCS5HcVti"
                    },
                    "operations": [
                        "177bzpUmukLarBiRGcTCDE63xqc5nkAKUja414HDmsNS2T3Gy",
                        "1G9oj5pX7Ruj1w6cT9DXY2KMNKM9JgHVfA6JN5nhZchnHCm6s",
                        ...
                        "2wX8fv5WaPpEXFYPbzmyeShVMRNdcqGCxnUCdfXKW2LrVvpkM1",
                        "2wdwXBXxpBeQuPtT5kkEiV3AKsTqWkzN9QLHXskDsZgD8XpdTN"
                    ]
                },
                "is_candidate": true,
                "is_discarded": false,
                "is_final": false,
                "is_in_blockclique": true
            },
            "id": "Fb46NHJCFTVgddSEZMEcmYeYpokvQv8gCYjnDBpXbAQBKpVE3"
        },
        "id": 1
    }

`get_operations`
----------------

Get information about `operation <https://docs.massa.net/en/latest/general-doc/architecture/archi-intro.html#operation>`_ (s) information associated to a given operation(s) ID(s).

- Query:

.. code-block:: shell

    curl --location --request POST 'https://test.massa.net/api/v2' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "jsonrpc": "2.0",
        "id": 1,
        "method": "get_operations",
        "params": [["177bzpUmukLarBiRGcTCDE63xqc5nkAKUja414HDmsNS2T3Gy"]]
    }'

-   Result:

.. code-block:: javascript

    {
        "jsonrpc": "2.0",
        "result": [
            {
                "id": "177bzpUmukLarBiRGcTCDE63xqc5nkAKUja414HDmsNS2T3Gy",
                "in_blocks": [
                    "Fb46NHJCFTVgddSEZMEcmYeYpokvQv8gCYjnDBpXbAQBKpVE3"
                ],
                "in_pool": true,
                "is_final": true,
                "operation": {
                    "content": {
                        "expire_period": 72188,
                        "fee": "0",
                        "op": {
                            "Transaction": {
                                "amount": "0.00040048",
                                "recipient_address": "A1Czd9sRp3mt2KU9QBEEZPsYxRq9TisMs1KnV4JYCe7Z4AAVinq"
                            }
                        }
                    },
                    "creator_address": "A12teNrVETiAfCHHNrDwcxLFZ2WUhtKk1suym6nLPBFcaxjP188w",
                    "creator_public_key": "P1cjQAvB8b2RxpqxVCn54KDjYDmC1wer6tJofohBCToKHWsgoVB",
                    "id": "177bzpUmukLarBiRGcTCDE63xqc5nkAKUja414HDmsNS2T3Gy",
                    "signature": "MnDMrajkMDzRJxiRyWgZCoyTP4k4yWM3raY4vo4SJ8o3CnBFrBnfc15C35xiemJ1zQqtYzYssWN5hWytGDVCsjuT2dt3p"
                }
            }
        ],
        "id": 1
    }

`get_endorsements`
------------------

Get information about `endorsement <https://docs.massa.net/en/latest/general-doc/architecture/endorsements.html>`_ (s) (content, finality ...)

- Query:

.. code-block:: shell

    curl --location --request POST 'https://test.massa.net/api/v2' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "jsonrpc": "2.0",
        "id": 1,
        "method": "get_endorsements",
        "params": [["2jtHfATDrho9Ttkxz3xp26WwjjREPVQV16fwMUCGyjnEQoyU8p"]]
    }'

-   Result:

.. code-block:: javascript

    {
        "jsonrpc": "2.0",
        "result": [
            {
                "endorsement": {
                    "content": {
                        "endorsed_block": "AvvHCpxnX8U6uTQKmpze55vzhkhdbbst9rrhPwbykymjJyzoV",
                        "index": 0,
                        "slot": {
                            "period": 72180,
                            "thread": 31
                        }
                    },
                    "creator_address": "A12N9nUN9r1eUheMZ36AA3RTDYepLtEMpHZoBvzQmxw4hNcJV7tH",
                    "creator_public_key": "P12qBafeiXMypqiChy7KEjqgAaUzbWJHhJALjfxzzY5hEH5BwL2c",
                    "id": "2jtHfATDrho9Ttkxz3xp26WwjjREPVQV16fwMUCGyjnEQoyU8p",
                    "signature": "XLJd5dSZsaQ3UYuuSGBGCbVsEM3aGTxAGigT81bVto7CypivDDwoPb6kJWXKzhvRi14qh3ReFqa7zzf3r5hYf343nqceH"
                },
                "id": "2jtHfATDrho9Ttkxz3xp26WwjjREPVQV16fwMUCGyjnEQoyU8p",
                "in_blocks": [
                    "Fb46NHJCFTVgddSEZMEcmYeYpokvQv8gCYjnDBpXbAQBKpVE3"
                ],
                "in_pool": true,
                "is_final": false
            }
        ],
        "id": 1
    }
