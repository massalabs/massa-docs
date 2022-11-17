Web 3 autonomous game tutorial, Step by Step!
=============================================

Intro
-----

In this tutorial, we will see how to develop step by step a web3 autonomous video game based on cats living autonomously on the Massa blockchain. Idea is simple : a user creates a cat, and have to feed him with massa tokens in order to avoid starving and death ! The cat has to eat periodically.

Each cat is technically a smart contract deployed on the Massa blockchain. We will see how to set this smart contract autonomous in order to allows it to carry out actions by itself.

.. note::

  If you play for the first time with Massa smart contract, you first of all have to install the environement following this : `Getting started Massa tutorial <https://docs.massa.net/en/latest/hackathon.html>`_
  
Overview
--------

#. How to create and deploy a cat (Go to :ref:`part1`)
#. How to feed the cat with tokens (Go to :ref:`part2`)
#. How to set the cat autonomous (Go to :ref:`part3`)
#. Autonomous mecanism fully detailled (Go to :ref:`part4`)

.. _part1:
1) How to create and deploy a cat
---------------------------------

First of all, we need to create and deploy on the blockchain the smart contracts for each cat using 2 scripts :

* main.ts : will allow us to deploy the smart contract of the cat.
* cat.ts : will manage all behaviour of the smart contract of the cat.

The main.ts script :
....................

.. code-blocks:: typescript
  :linenos:

  import { createSC, fileToBase64, Storage, Context, generateEvent, call, transferCoins, Args} from "@massalabs/massa-as-sdk"

  export function main(_args: string): void {    
      const bytes = fileToBase64('./build/cat.wasm');
      let addr = createSC(bytes);
      var no_args = new Args();
      
      generateEvent("A new cat is born! Address of the cat : " + addr.toByteString());

      Storage.setOf(addr, "birth", Context.timestamp().toString());
      Storage.setOf(addr, "name", "Massa_cat");
      Storage.setOf(addr, "state", "ok");
      Storage.setOf(addr, "last_meal", Context.timestamp().toString());
      Storage.setOf(addr, "hangry_since", "0");

      generateEvent("--- Information about the cat ==> " +
                      "Name :" + call(addr, "get_name", no_args,0) +
                      " || Birthday :" + call(addr, "get_birth", no_args, 0) +
                      " || State :" + call(addr, "get_state", no_args, 0) +
                      " || Last meal at :" + call(addr, "get_last_meal", no_args, 0) +
                      " || Hangry since :" + call(addr, "get_hangry_since", no_args, 0)
                    );

  }

.. note::

  If you are not familiar with smart contract coding, you better read : `Smart contract tutorial <https://docs.massa.net/en/latest/web3-dev/smart-contracts/smart-contract-example-sum.html#sc-example-sum>`_

  If you want more explainations about the main.ts script, let's see line by line what is going on :

  * Import Massalabs library :

    .. code-blocks:: typescript

      import { createSC, fileToBase64, Storage, Context, generateEvent, call, transferCoins, Args} from "@massalabs/massa-as-sdk"

    The goal of this line is to import from the "massalabs/massa-as-sdk" library the functions that we will be used : 

    * createSC() : to deploy a binary on the blockchain
    * fileToBase64() : to create a binary from a .wasm file.
    * Storage() : to get all function related to key storage (see below).
    * Context() : to get all function related to the context of the smart contract as : the current timestamp, the remaining gas, who tranfered coins etc.
    * generateEvent() : to print into the massa-client a message.
    * call() : to call a function from a specific smart contract.
    * transferCoins() : to transfert coins from a smart contract to an address.
    * Args() : to give arguments calling a function from an another smart contract.


  * main() function to execute the script :

    .. code-blocks:: typescript

      export function main(_args: string): void {    
          const bytes = fileToBase64('./build/cat.wasm');
          let addr = createSC(bytes);
          var no_args = new Args();

          generateEvent("A new cat is born! Address of the cat : " + addr.toByteString());

    This step declares the function `main()` that will be executed on the blockchain. Inside the function we can find :

    * `const bytes = fileToBase64('./build/cat.wasm');` in order to create the binary code from the "cat.wasm" file and store it into the bytes variable.
    * `let addr = createSC(bytes);` in order to instanciate the addr variable and deploy the smart contract of the bytes variable.
    * `var no_args = new Args();` instanciate a new Args() object with Null value. We will be used with some specific function like `call()`.
    * `generateEvent("A new cat is born! Address of the cat : " + addr.toByteString());` will just send a message on the client with the smart contract address, using the function `generateEvent("Message")`.


  * Define the attributes of the new cat :

    .. code-blocks:: typescript

      Storage.setOf(addr,"birth",Context.timestamp().toString());
      Storage.setOf(addr,"name","Massa_cat");
      Storage.setOf(addr,"state","ok");
      Storage.setOf(addr,"last_meal",Context.timestamp().toString());
      Storage.setOf(addr,"hangry_since","0");

    Using the `Storage.setOf()` function, we can set different attributes as : the name of the cat, the current state of the cat, etc.

    `Storage.setOf()` will technically create a key owned by the smart contract only :

    * You can change the value of the key using : `Storage.setOf("key","value")`.
    * You can get the value of the key using : `Storage.getOf("key")`.

    Using the `Context.timestamp()` function, we can get the current timestamp.


  * Get information from the cat :

    .. code-blocks:: typescript

      generateEvent("--- Information about the cat ==> " +  
                        " Name :" + call(addr,"get_name","",0) +
                        " || Birthday :" + call(addr,"get_birth","",0) +
                        " || State :" + call(addr,"get_state","",0) +
                        " || Last meal at :" + call(addr,"get_last_meal","",0) + 
                        " || Hangry since :" + call(addr,"get_hangry_since","",0));

    We can print the cat information (into the massa client) using the function `generateEvent("Message")`. 

    The `call()` function allows us to call the functions defined into our cat smart contract knowing the address of this one and get information about the smart contract. This should be used like :

    `call(address_of_the_smart_contract_to_call, "function_to_call", "parameters_of_the_function", tokens_to_send_during_the_call)`

   
The cat.ts script :
....................

.. code-blocks:: typescript

  import { Storage } from "@massalabs/massa-as-sdk";

  export function get_name(_args: string): string {return Storage.get("name");}
  export function get_birth(_args: string): string {return Storage.get("birth");}
  export function get_state(_args: string): string {return Storage.get("state");}
  export function get_last_meal(_args: string): string {return Storage.get("last_meal");}
  export function get_hangry_since(_args: string): string {return Storage.get("hangry_since");}
  

.. note::

  Code analysis : 

  When the main.ts script is executed for the first time, we declare keys like "name", "birth" etc with specific values.
  In order to create the game, we need those keys aviable at each time by someone, or an another smart contract. Thus, we can do it declaring functions callable using : `export function my_function()`

  .. code-blocks:: typescript

    import { Storage } from "@massalabs/massa-as-sdk";

    export function get_name(_args: string): string {return Storage.get("name");}

  for instance here we declare into the cat smart contract a callable function named "get_name" that will return a string with the value of the key "name".

  Thus, any smart contract will be able to get the name of the cat using a call() function : 

  `call(address_of_the_cat, "get_name", "", 0)`

Lets try our code!
-------------------

* Just compile your main.ts file in the terminal using : 

.. code-blocks:: bash

  cd ~/my-sc
  yarn run build
  
.. note::
  
  Make sure your file `my-sc/package.json` is written as following :
  
  .. code-blocks:: json
    {
    "name": "my-sc",
   "version": "0.1.0",
    "scripts": {
      "build": "asc src/cat.ts --transform json-as/transform --target release --exportRuntime --runtime stub --outFile build/cat.wasm && asc src/main.ts --transform json-as/transform --transform transformer/file2base64.js --target release --exportRuntime --runtime stub --outFile build/main.wasm",    
      "clean": "massa-sc-scripts clean"
    },
    "dependencies": {
      "@massalabs/as": "^1.0.2",
      "@massalabs/massa-as-sdk": "^1.1.0",
      "assemblyscript": "^0.21.2",
      "json-as": "^0.5.3",
      "massa-sc-scripts": "4.0.7",
      "mscl-as-transformer": "0.0.1",
      "visitor-as": "^0.10.2"
    },
    "devDependencies": {
      "transformer": "https://gitpkg.now.sh/massalabs/as/transformer?main"
    }
  }
  
* Copy the file "main.wasm" from `my-sc/build/main.wasm` to `massa/massa-client/main.wasm` using : 

.. code-blocks:: bash

  cd ~
  cp my-sc/build/main.wasm massa/massa-client/main.wasm

* Send the smart contract on the blockchain massa using into the massa client the following command : 

.. code-blocks:: bash

  send_smart_contract your_address main.wasm 10000000 0 0 
  
And get the events sent by `generateEvent()` function used in our script. You can filter them by your emitter address using the following command into the client : 

.. code-blocks:: bash

  get_filtered_sc_output_event emitter_address=your_address

You should get as following (The "Data:" lines correspond to the message print using `generateEvent()` function into the smart contract): 

.. code-blocks:: bash

  ✔ command · send_smart_contract A1Yp7R7LmoPewpcNxEReF1kwzaXb7qC4DdW5CqaaZyLxCHKH4dG main.wasm 10000000 0 0 
  Sent operation IDs:
  2o3ktojHdW2Pmd2583417nikLsvuV5Ut3FLYAkZeEgRLUmNN8g

  ✔ command · get_filtered_sc_output_event emitter_address=A1Yp7R7LmoPewpcNxEReF1kwzaXb7qC4DdW5CqaaZyLxCHKH4dG
  Context: Slot: (period: 129211, thread: 9) at index: 6
  On chain execution
  Block id: cz6Sj6jGs8j8qcuaDw25hgtY45ES8RS17ywS7DxLs2m4YkLr9
  Origin operation id: 2o3ktojHdW2Pmd2583417nikLsvuV5Ut3FLYAkZeEgRLUmNN8g
  Call stack: A1Yp7R7LmoPewpcNxEReF1kwzaXb7qC4DdW5CqaaZyLxCHKH4dG

  Data: A new cat is born! Address of the cat : A1pKunDyWRPgGithbkkxavTgchPuSzCQ1MDzKHLA1rdsh3uH4S7

  Context: Slot: (period: 129211, thread: 9) at index: 7
  On chain execution
  Block id: cz6Sj6jGs8j8qcuaDw25hgtY45ES8RS17ywS7DxLs2m4YkLr9
  Origin operation id: 2o3ktojHdW2Pmd2583417nikLsvuV5Ut3FLYAkZeEgRLUmNN8g
  Call stack: A1Yp7R7LmoPewpcNxEReF1kwzaXb7qC4DdW5CqaaZyLxCHKH4dG

  Data: --- Information about the cat ==> Name :Massa_cat || Birthday :1668439608968 || State :ok || Last meal at :1668439608968 || Hangry since :0
  
  
.. _part2:
2) How to feed the cat with tokens
----------------------------------

Now we want to feed our cat with tokens. We have to add some code to main.ts and cat.ts files.

The main.ts script :
....................

We then need to transfert tokens to the smart contract of the cat adding in the main.ts : 

.. code-blocks:: typescript 

  export function main(_args: string): void {
    const bytes = fileToBase64('./build/cat.wasm');
    let addr = createSC(bytes);
    generateEvent("A new cat is born! Address of the cat : " + addr.toByteString());

    Storage.setOf(addr,"birth",Context.timestamp().toString());
    Storage.setOf(addr,"name","Massa_cat");
    Storage.setOf(addr,"state","ok");
    Storage.setOf(addr,"last_meal",Context.timestamp().toString());
    Storage.setOf(addr,"hangry_since","0");

    generateEvent("--- Informations about the cat ==> " +
                    "Name :" + call(addr,"get_name","",0) +
                    " || Birthday :" + call(addr,"get_birth","",0) +
                    " || State :" + call(addr,"get_state","",0) +
                    " || Last meal at :" + call(addr,"get_last_meal","",0) +
                    " || Hangry since :" + call(addr,"get_hangry_since","",0)
    );

    //transfert 10 tokens to the cat smart contract
    let factor = 100000000;
    transferCoins(addr, 10 * factor);

    //ask to the cat to eat tokens and print the state of the token after eating, and the balance evolution of the smart contract.
    call(addr, "eat", "", 0);
    generateEvent("--- Informations about the cat ==> " +
    "Name :" + call(addr,"get_name","",0) +
    " || Birthday :" + call(addr,"get_birth","",0) +
    " || State :" + call(addr,"get_state","",0) +
    " || Last meal at :" + call(addr,"get_last_meal","",0) +
    " || Hangry since :" + call(addr,"get_hangry_since","",0)
                );
}

.. note::

  Code analysis : 
 
  .. code-blocks:: typescript

    let factor = 100000000;
    transferCoins(addr, 10 * factor);

  transfer 10 tokens to the smart contract address. Note that 1 massa token = 100000000 of the standard unit used.

  .. code-blocks:: typescript

    call(addr, "eat", "", 0);

    generateEvent("--- Information about the cat ==> " +
        "Name :" + call(addr,"get_name","",0) +
        " || Birthday :" + call(addr,"get_birth","",0) +
        " || State :" + call(addr,"get_state","",0) +
        " || Last meal at :" + call(addr,"get_last_meal","",0) +
        " || Hangry since :" + call(addr,"get_hangry_since","",0)
                    );
  call the `eat()` function of the cat smart contract, and print the information about the cat.

The cat.ts script :
....................

.. code-blocks:: tyepscript

  import { generateEvent, Storage, balance, Context, transferCoins, Address, sendMessage, currentPeriod, currentThread } from "@massalabs/massa-as-sdk";

  export function get_name(_args: string): string {return Storage.get("name");}
  export function get_birth(_args: string): string {return Storage.get("birth");}
  export function get_state(_args: string): string {return Storage.get("state");}
  export function get_last_meal(_args: string): string {return Storage.get("last_meal");}
  export function get_hangry_since(_args: string): string {return Storage.get("hangry_since");}

  export function eat(_args: string): void {
      let factor = 100000000;
      let tokens_to_eat: u64 = 6 * factor;
      let poo_addr = Address.fromByteString("A13ESKj7WRVdjM96ttk2caqzES9nRzwB8pEcMW8GutrPwjo3WQS");
      
      generateEvent(Storage.get("name") + " wants to eat " + (tokens_to_eat / factor).toString() + " Massa tokens. Current balance : " + (balance() /factor).toString());

      if (tokens_to_eat <= balance()) {        
          transferCoins(poo_addr, tokens_to_eat);
          generateEvent(Storage.get("name") + " has eaten " + (tokens_to_eat / factor).toString() + " Massa tokens. Current balance : " + (balance() /factor).toString());
          Storage.set("state", "ok");
          Storage.set("last_meal", Context.timestamp().toString());
          Storage.set("hangry_since", "0");
      }

      else {
          generateEvent("/!\ Not enought tokens in the balance! Balance = " + (balance() /factor).toString());
          if (Storage.get("state") == "starved") {
              Storage.set("state", "dead");
              generateEvent(Storage.get("name") + " is starved since : " + Storage.get("hangry_since") + ", he dies with pain!"); 
          }
          
          if (Storage.get("state") == "ok") {
              Storage.set("state", "starved");
              Storage.set("hangry_since", Context.timestamp().toString());
          }
      }
  }

.. note::

  Code analysis : 

  .. code-blocks:: typescript

    let factor = 100000000;
    let tokens_to_eat: u64 = 6 * factor;
    let poo_addr = Address.fromByteString("A13ESKj7WRVdjM96ttk2caqzES9nRzwB8pEcMW8GutrPwjo3WQS");

  declares the callable function eat(), and set the number of tokens eaten at each time to 6. The poo_addr is the address where the tokens will be "destoyed" after each meal.

  .. code-blocks:: typescript

    if (tokens_to_eat <= balance()) {        
            transferCoins(poo_addr, tokens_to_eat);
            generateEvent(Storage.get("name") + " has eaten " + (tokens_to_eat / factor).toString() + " Massa tokens. Current balance : " + (balance() /factor).toString());
            Storage.set("state", "ok");
            Storage.set("last_meal", Context.timestamp().toString());
            Storage.set("hangry_since", "0");
        }

  if there are enought tokens to eat, 6 tokens are transfered to the poo address and the keys of the cat smart contract are updated with new values.

  .. code-blocks:: typescript

    else {
              generateEvent("/!\ Not enought tokens in the balance! Balance = " + (balance() /factor).toString());
              if (Storage.get("state") == "starved") {
                  Storage.set("state", "dead");
                  generateEvent(Storage.get("name") + " is starved since : " + Storage.get("hangry_since") + ", he dies with pain!"); 
              }

              if (Storage.get("state") == "ok") {
                  Storage.set("state", "starved");
                  Storage.set("hangry_since", Context.timestamp().toString());
              }
          }

  if not enought tokens are avaible, the key "state" is set to "starved" and the key "hangry_since" is updated. If the state of the cat was already "starved", the key will be updated to "dead".
  
Let's try our code!
...................

Just compile your main.ts file in the terminal using : 

.. code-blocks:: bash

  cd ~/my-sc
  yarn run build
  
* Copy the file "main.wasm" from `my-sc/build/main.wasm` to `massa/massa-client/main.wasm` using : 

.. code-blocks:: bash

  cd ~
  cp my-sc/build/main.wasm massa/massa-client/main.wasm

* Send the smart contract on the blockchain massa using into the massa client the following command : 

.. code-blocks:: bash

  send_smart_contract your_address main.wasm 10000000 0 0 
  
And get the events sent by `generateEvent()` function used in our script. You can filter them by your emitter address using the following command into the client : 

.. code-blocks:: bash

  get_filtered_sc_output_event emitter_address=your_address

You should get as following (The "Data:" lines correspond to the message print using `generateEvent()` function into the smart contract): 

.. code-blocks:: bash

  Context: Slot: (period: 133898, thread: 28) at index: 6
  On chain execution
  Block id: 28mYCc1CLCEGXwLbCRrPKeBdmL8cWGfUADuGoTgm3xKiVJJpm7
  Origin operation id: a9hesDXT5DiJqoZ37rarsEmN716nV8cJj6zVCnbs5is6GCF1n
  Call stack: A12kgk4YamD6Qt4PdG42iqMSE36BRNiL1JyCmrcGTHrQJuaarMKU

  Data: A new cat is born! Address of the cat : A1186aEwXVC5mfdgTqkfdPyVT4cTgW8cfvvw6FdA52YMgdiPvQ9

  Context: Slot: (period: 133898, thread: 28) at index: 7
  On chain execution
  Block id: 28mYCc1CLCEGXwLbCRrPKeBdmL8cWGfUADuGoTgm3xKiVJJpm7
  Origin operation id: a9hesDXT5DiJqoZ37rarsEmN716nV8cJj6zVCnbs5is6GCF1n
  Call stack: A12kgk4YamD6Qt4PdG42iqMSE36BRNiL1JyCmrcGTHrQJuaarMKU

  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668514610468 || State :ok || Last meal at :1668514610468 || Hangry since :0

  Context: Slot: (period: 133898, thread: 28) at index: 8
  On chain execution
  Block id: 28mYCc1CLCEGXwLbCRrPKeBdmL8cWGfUADuGoTgm3xKiVJJpm7
  Origin operation id: a9hesDXT5DiJqoZ37rarsEmN716nV8cJj6zVCnbs5is6GCF1n
  Call stack: A12kgk4YamD6Qt4PdG42iqMSE36BRNiL1JyCmrcGTHrQJuaarMKU,A1186aEwXVC5mfdgTqkfdPyVT4cTgW8cfvvw6FdA52YMgdiPvQ9

  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 10

  Context: Slot: (period: 133898, thread: 28) at index: 9
  On chain execution
  Block id: 28mYCc1CLCEGXwLbCRrPKeBdmL8cWGfUADuGoTgm3xKiVJJpm7
  Origin operation id: a9hesDXT5DiJqoZ37rarsEmN716nV8cJj6zVCnbs5is6GCF1n
  Call stack: A12kgk4YamD6Qt4PdG42iqMSE36BRNiL1JyCmrcGTHrQJuaarMKU,A1186aEwXVC5mfdgTqkfdPyVT4cTgW8cfvvw6FdA52YMgdiPvQ9

  Data: Massa_cat has eaten 6 Massa tokens. Current balance : 4

  Context: Slot: (period: 133898, thread: 28) at index: 10
  On chain execution
  Block id: 28mYCc1CLCEGXwLbCRrPKeBdmL8cWGfUADuGoTgm3xKiVJJpm7
  Origin operation id: a9hesDXT5DiJqoZ37rarsEmN716nV8cJj6zVCnbs5is6GCF1n
  Call stack: A12kgk4YamD6Qt4PdG42iqMSE36BRNiL1JyCmrcGTHrQJuaarMKU

  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668514610468 || State :ok || Last meal at :1668514610468 || Hangry since :0

now if we try to feed the cat 2 times just encapsulating the `eat()` function into a `for` loop : 

.. code-blocs:: typescript

  for (let i = 0; i < 2; i++) {
        call(addr, "eat", "", 0);
        generateEvent("--- Informations about the cat ==> " +
                "Name :" + call(addr,"get_name","",0) +
                " || Birthday :" + call(addr,"get_birth","",0) +
                " || State :" + call(addr,"get_state","",0) +
                " || Last meal at :" + call(addr,"get_last_meal","",0) +
                " || Hangry since :" + call(addr,"get_hangry_since","",0)
                            );        
      }  

we observe (only "Data:" line are printed) :

.. code-blocs:: bash 

  Data: A new cat is born! Address of the cat : A12LCbcpSg4UqadPTtVwwkBvspxLBhujWHVHwZCbUkUXJn6oju19
  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668515058468 || State :ok || Last meal at :1668515058468 || Hangry since :0
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 10
  Data: Massa_cat has eaten 6 Massa tokens. Current balance : 4
  Data: 0--- Informations about the cat ==> Name :Massa_cat || Birthday :1668515058468 || State :ok || Last meal at :1668515058468 || Hangry since :0
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 4
  Data: /! Not enought tokens in the balance! Balance = 4
  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668515058468 || State :starved || Last meal at :1668515058468 || Hangry since :1668515058468


if we try with 3, the cat should die :

.. code-blocs:: typescript

  for (let i = 0; i < 3; i++) {
        call(addr, "eat", "", 0);
        generateEvent("--- Informations about the cat ==> " +
                "Name :" + call(addr,"get_name","",0) +
                " || Birthday :" + call(addr,"get_birth","",0) +
                " || State :" + call(addr,"get_state","",0) +
                " || Last meal at :" + call(addr,"get_last_meal","",0) +
                " || Hangry since :" + call(addr,"get_hangry_since","",0)
                            );        
      }  


and saddly ... (only "Data:" line are printed) :

.. code-blocs:: bash 

  Data: A new cat is born! Address of the cat : A1Gm3kxorw2wpgJ7pGWStWxWjfxVa6qVBtGZ1o5Do2xdgNt4BmP
  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668515298468 || State :ok || Last meal at :1668515298468 || Hangry since :0
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 10
  Data: Massa_cat has eaten 6 Massa tokens. Current balance : 4
  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668515298468 || State :ok || Last meal at :1668515298468 || Hangry since :0
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 4
  Data: /! Not enought tokens in the balance! Balance = 4
  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668515298468 || State :starved || Last meal at :1668515298468 || Hangry since :1668515298468
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 3
  Data: /! Not enought tokens in the balance! Balance = 3
  Data: Massa_cat is starved since : 1668515298468, he dies with pain!
  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668515298468 || State :dead || Last meal at :1668515298468 || Hangry since :1668515298468
  
.. _part3:
3) How to set the cat autonomous
--------------------------------

We want now the cat able to eat autonomously!

To do it, we will use the `sendMessage()` function to replace the `for` loop in the main.ts.

The main.ts script :
....................

.. code-blocks:: typescript
  :linenos:
  
  import { createSC, fileToBase64, Storage, Context, generateEvent, call, transferCoins, Args} from "@massalabs/massa-as-sdk"

  export function main(_args: string): void {
      const bytes = fileToBase64('./build/cat.wasm');
      let addr = createSC(bytes);
      generateEvent("A new cat is born! Address of the cat : " + addr.toByteString());
      var no_args = new Args();

      Storage.setOf(addr,"birth",Context.timestamp().toString());
      Storage.setOf(addr,"name","Massa_cat");
      Storage.setOf(addr,"state","ok");
      Storage.setOf(addr,"last_meal",Context.timestamp().toString());
      Storage.setOf(addr,"hangry_since","0");

      generateEvent("--- Informations about the cat ==> " +
                      "Name :" + call(addr,"get_name",no_args,0) +
                      " || Birthday :" + call(addr,"get_birth",no_args,0) +
                      " || State :" + call(addr,"get_state",no_args,0) +
                      " || Last meal at :" + call(addr,"get_last_meal",no_args,0) +
                      " || Hangry since :" + call(addr,"get_hangry_since",no_args,0)
      );

      //transfert 10 tokens to the cat smart contract
      let factor = 100000000;
      transferCoins(addr, 10 * factor);

      //call the "loop" function of the cat smart contract start in order to start the loop setting the cat autonomous. `generateEvent()` Prints the return of the loop to be sure that the loop was started correctly.
      generateEvent(call(addr,"loop",no_args,0));
  }

.. note:: 

  Code analysis :

  The previous `for` loop written in order to feed the cat x times has been replaced by a `call(addr,"loop",no_args,0)`, targetting the cat smart contract function `loop()`. This function `loop()` starts an infinite loop allowing the cat to eat autonomously until he dies :

  .. code-blocks:: typescript

    generateEvent(call(addr,"loop",no_args,0));

  The `generateEvent()` used before the `call()` prints the return of the `loop()`. Indeed the `loop()` function returns a string during the first call.
  
The cat.ts :
............

.. code-blocks::
  
  import { generateEvent, Storage, balance, Context, transferCoins, Address, sendMessage, currentPeriod, currentThread } from "@massalabs/massa-as-sdk";
  import { addressStack } from "@massalabs/massa-as-sdk/assembly/std/context";

  let factor = 100000000;

  export function get_name(_args: string): string {return Storage.get("name");}
  export function get_birth(_args: string): string {return Storage.get("birth");}
  export function get_state(_args: string): string {return Storage.get("state");}
  export function get_last_meal(_args: string): string {return Storage.get("last_meal");}
  export function get_hangry_since(_args: string): string {return Storage.get("hangry_since");}

  export function eat(_args: string): void {
      let tokens_to_eat: u64 = 6 * factor;
      let poo_addr = Address.fromByteString("A13ESKj7WRVdjM96ttk2caqzES9nRzwB8pEcMW8GutrPwjo3WQS");
      generateEvent(Storage.get("name") + " wants to eat " + (tokens_to_eat / factor).toString() + " Massa tokens. Current balance : " + (balance() /factor).toString());

      if (tokens_to_eat <= balance()) {        
          transferCoins(poo_addr, tokens_to_eat);
          generateEvent(Storage.get("name") + " has eaten " + (tokens_to_eat / factor).toString() + " Massa tokens. Current balance : " + (balance() /factor).toString());
          Storage.set("state", "ok");
          Storage.set("last_meal", Context.timestamp().toString());
          Storage.set("hangry_since", "0");

      }

      else {
          generateEvent("/!\ Not enought tokens in the balance! Balance = " + (balance() /factor).toString());
          if (Storage.get("state") == "starved") {
              Storage.set("state", "dead");
              generateEvent(Storage.get("name") + " is starved since : " + Storage.get("hangry_since") + ", he dies with pain!"); 
          }
          if (Storage.get("state") == "ok") {
              Storage.set("state", "starved");
              Storage.set("hangry_since", Context.timestamp().toString());
          }

      }

  }

  export function loop(_args: string): string {

      if(Storage.get("state") == "dead") {
          generateEvent("Cat is dead! End of the loop");
          return "0";
      }

      const threads: u8 = 32;

      let cur_period = currentPeriod();
      let cur_thread = currentThread();
      let next_thread = cur_thread + 1;
      let next_period = cur_period;
      if (next_thread >= threads) {
          ++next_period;
          next_thread = 0;
      }

      let call_stack = addressStack();
      let cur_addr = call_stack[call_stack.length - 1];

      eat("");
      
      sendMessage(
          cur_addr,
          "loop",
          next_period,
          next_thread,
          next_period + 5,
          next_thread,
          70000000,
          0,
          0,
          ""
      );

      return "(Loop started)";
  }

.. note::

  Code analysis :
 
  The callable `loop()` function has been added :

  .. code-blocks::

    export function loop(_args: string): string {
        ...
        }

  This function consists in 3 parts : 

  * The first part is just to check if the cat is dead, and if yes, it stops the loop using `return "0"` :

    .. code-blocks::

      if(Storage.get("state") == "dead") {
            generateEvent("Cat is dead! End of the loop");
            return "0";
        }

  * The second part is the main mecanism of the autonomous system : the `sendMessage()` :

    .. code-blocks::
        const threads: u8 = 32;

        let cur_period = currentPeriod();
        let cur_thread = currentThread();
        let next_thread = cur_thread + 1;
        let next_period = cur_period;
        if (next_thread >= threads) {
            ++next_period;
            next_thread = 0;
        }

        let call_stack = addressStack();
        let cur_addr = call_stack[call_stack.length - 1];

        eat("");

        sendMessage(
            cur_addr,
            "loop",
            next_period,
            next_thread,
            next_period + 5,
            next_thread,
            70000000,
            0,
            0,
            ""
        );

    The function `sendMessage()` will call the `loop` function in the futur, allowing us to create an infinite loop. Each iteration of the loop will execute the `eat()` function, allowing the cat to eat by itself periodically. (to get more information about the autonomous system, Go to :ref:`part4`)

  * The third part is just `return "(Loop started)";` during the first call of the `loop()` function by main.ts, in order to signify that the loop started. 

Lets try our code!
-------------------

Just compile your main.ts file in the terminal using : 

.. code-blocks:: bash

  cd ~/my-sc
  yarn run build
  
  
Copy the file "main.wasm" from `my-sc/build/main.wasm` to `massa/massa-client/main.wasm` using : 

.. code-blocks:: bash

  cd ~
  cp my-sc/build/main.wasm massa/massa-client/main.wasm

Send the smart contract on the blockchain massa using into the massa client the following command : 

.. code-blocks:: bash

  send_smart_contract your_address main.wasm 10000000 0 0 
  
And get the events sent by `generateEvent()` function used in our script. You can filter them by your emitter address using the following command into the client : 

.. code-blocks:: bash

  get_filtered_sc_output_event emitter_address=your_address

You should get as following (with only `Data:` lines are printed): 

.. code-blocks:: bash

  ✔ command · get_filtered_sc_output_event emitter_address=A12kgk4YamD6Qt4PdG42iqMSE36BRNiL1JyCmrcGTHrQJuaarMKU

  Data: A new cat is born! Address of the cat : A12mf7ChJMu4nmWNh5WNTqJ5sGkXbpKuvzMzn4oNyde18thNrLev
  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668691842468 || State :ok || Last meal at :1668691842468 || Hangry since :0
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 10
  Data: Massa_cat has eaten 6 Massa tokens. Current balance : 4
  Data: (Loop started)

And when you filter with the cat smart contract address (with only `Data:` lines printed): 

.. code-blocks:: bash

  ✔ command · get_filtered_sc_output_event emitter_address=A12mf7ChJMu4nmWNh5WNTqJ5sGkXbpKuvzMzn4oNyde18thNrLev

  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 4
  Data: /! Not enought tokens in the balance! Balance = 4
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 3
  Data: /! Not enought tokens in the balance! Balance = 3
  Data: Massa_cat is starved since : 1668691842968, he dies with pain!
  Data: Cat is dead! End of the loop


.. _part4:
4) Autonomous mecanism fully detailled
......................................
