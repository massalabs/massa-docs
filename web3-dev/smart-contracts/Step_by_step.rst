Web 3 autonomous game tutorial, Step by Step!
=============================================

Intro
-----

In this tutorial, we will see how to step by step develop a web3 autonomous video game based on cats living autonomously on the Massa blockchain. Idea is simple : a user creates a cat, and have to feed him with massa tokens in order to avoid starving and death ! The cat has to eat periodically.

Each cat is technically a smart contract deployed on the Massa blockchain. We will see how to set this smart contract autonomous in order to allows it to carry out actions by itself.


1) How to create and deploy a cat
---------------------------------

First of all, we need to create and deploy on the blockchain the smart contracts for each cat using 2 scripts :

* main.ts : will allow us to deploy the smart contract of the cat.
* cat.ts : will manage all behaviour of the smart contract of the cat.

The main.ts script :
....................

.. code-block:: typescript
  :linenos:

  import { createSC, fileToByteArray, Storage, Context, generateEvent, call, toBytes} from "@massalabs/massa-as-sdk"

  export function main(_args: StaticArray<u8>): void {    
      const bytes: StaticArray<u8> = fileToByteArray('./build/cat.wasm');
      let addr = createSC(bytes);
      generateEvent("A new cat is born! Address of the cat : " + addr.toByteString());

      Storage.setOf(addr, toBytes("birth"), toBytes(Context.timestamp().toString()));
      Storage.setOf(addr, toBytes("name"), toBytes("Massa_cat"));
      Storage.setOf(addr, toBytes("state"), toBytes("ok"));
      Storage.setOf(addr, toBytes("last_meal"), toBytes(Context.timestamp().toString()));
      Storage.setOf(addr, toBytes("hangry_since"), toBytes("0"));

      generateEvent("--- Information about the cat ==> " +
                      "Name :" + call(addr,"get_name",new StaticArray<u8>(0),0) +
                      " || Birthday :" + call(addr,"get_birth",new StaticArray<u8>(0),0) +
                      " || State :" + call(addr,"get_state",new StaticArray<u8>(0),0) +
                      " || Last meal at :" + call(addr,"get_last_meal",new StaticArray<u8>(0),0) +
                      " || Hangry since :" + call(addr,"get_hangry_since",new StaticArray<u8>(0),0)
      );

  }

Code analysis : 
^^^^^^^^^^^^^^^

Let's see line by line what is going on :

* Import Massalabs library :

  .. code-block:: typescript

    import { createSC, fileToByteArray, Storage, Context, generateEvent, call} from "@massalabs/massa-as-sdk"

  ==> The goal of this line is to import from the "massalabs/massa-as-sdk" library the functions that we will be used : { createSC, fileToByteArray, Storage, Context, generateEvent, call}
  
  
* main() function to execute the script :

  .. code-block:: typescript

    export function main(_args: StaticArray<u8>): void {    
        const bytes: StaticArray<u8> = fileToByteArray('./build/cat.wasm');
        let addr = createSC(bytes);
        generateEvent("A new cat is born! Address of the cat : " + addr.toByteString());

  ==> This step declares the function main() that will be executed on the blockchain. Inside the function we can find :
  
  * const bytes: StaticArray<u8> = fileToByteArray('./build/cat.wasm'); ==> in order to create the binary code from the "cat.wasm" file and store it into the bytes variable.
  * let addr = createSC(bytes); ==> in order to instanciate the addr variable and deploy the smart contract of the bytes variable.
  * generateEvent("A new cat is born! Address of the cat : " + addr.toByteString()); ==> will just send a message on the client with the smart contract address, using the function generateEvent("Message").
  
  
* Define the attributes of the new cat :

  .. code-block:: typescript
  
    Storage.setOf(addr, toBytes("birth"), toBytes(Context.timestamp().toString()));
    Storage.setOf(addr, toBytes("name"), toBytes("Massa_cat"));
    Storage.setOf(addr, toBytes("state"), toBytes("ok"));
    Storage.setOf(addr, toBytes("last_meal"), toBytes(Context.timestamp().toString()));
    Storage.setOf(addr, toBytes("hangry_since"), toBytes("0"));
    
  ==> Using the Storage.setOf() function, we can set different attributes as : the name of the cat, the current state of the cat, etc.
  
  Storage.setOf() will technically create a key owned by the smart contract only :
  
  * You can change the value of the key using : Storage.setOf(toBytes("key"), toBytes("value")).
  * You can get the value of the key using : Storage.getOf(toBytes("key")).
  
  Using the Context.timestamp() function, we can get the current timestamp.
  
  
* Get information from the cat :

  .. code-block:: typescript
  
    generateEvent("--- Information about the cat ==> " +  
                      " Name :" + call(addr,"get_name",new StaticArray<u8>(0),0) +
                      " || Birthday :" + call(addr,"get_birth",new StaticArray<u8>(0),0) +
                      " || State :" + call(addr,"get_state",new StaticArray<u8>(0),0) +
                      " || Last meal at :" + call(addr,"get_last_meal",new StaticArray<u8>(0),0) + 
                      " || Hangry since :" + call(addr,"get_hangry_since",new StaticArray<u8>(0),0));
      
  ==> We can print the cat information into the client using the function generateEvent("Message") and using the call() function. 
  
  The call() function allows us to call the functions defined into our cat smart contract knowing the address of this one and should be used like :
  
  call(address_of_the_smart_contract_to_call, "function_to_call", params, tokens_to_send_during_the_call)

   
The cat.ts script :
....................

.. code-block:: typescript

  import { Storage, toBytes } from "@massalabs/massa-as-sdk";

  export function get_name(_args: StaticArray<u8>): StaticArray<u8> {return Storage.get(toBytes("name"));}
  export function get_birth(_args: StaticArray<u8>): StaticArray<u8> {return Storage.get(toBytes("birth"));}
  export function get_state(_args: StaticArray<u8>): StaticArray<u8> {return Storage.get(toBytes("state"));}
  export function get_last_meal(_args: StaticArray<u8>): StaticArray<u8> {return Storage.get(toBytes("last_meal"));}
  export function get_hangry_since(_args: StaticArray<u8>): StaticArray<u8> {return Storage.get(toBytes("hangry_since"));}
  

Code analysis : 
^^^^^^^^^^^^^^^

When the main.ts script is executed for the first time, we declared keys like "name", "birth" etc with specific values.
In order to create the game, we need those keys avaible at each time by someone, or an another smart contract. Thus, we can do it declaring functions callable using : "export function my_function()"

.. code-block:: typescript

  import { Storage, toBytes } from "@massalabs/massa-as-sdk";

  export function get_name(_args: StaticArray<u8>): StaticArray<u8> {return Storage.get(toBytes("name"));}

==> for instance here we declare into the cat smart contract a callable function named "get_name" that will return a StaticArray<u8> with the value of the key "name".

Thus, any smart contract will be able to get the name of the cat using a call() function : 

call(address_of_the_cat, "get_name", new StaticArray<u8>(0), 0)

Lets try our code!
-------------------

* Just compile your main.ts file in the terminal using : 

.. code-block:: bash

  cd ~/my-sc
  yarn run build
  
.. note::
  
  Make sure your file "my-sc/package.json" is written as following :
  
  .. code-block:: json

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
  
* Copy the file "main.wasm" from my-sc/build/main.wasm to massa/massa-client/main.wasm using : 

.. code-block:: bash

  cd ~
  cp my-sc/build/main.wasm massa/massa-client/main.wasm

* Send the smart contract on the blockchain massa using into the massa client the following command : 

.. code-block:: bash

  send_smart_contract your_address main.wasm 10000000 0 0 
  
And get the events sent by "generateEvent()" function used in our script. You can filter them by your emitter address using the following command into the client : 

.. code-block:: bash

  get_filtered_sc_output_event emitter_address=your_address

You should get as following : 

.. code-block:: bash

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

  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668439608968 || State :ok || Last meal at :1668439608968 || Hangry since :0

  
2) How to feed him with tokens
------------------------------

3) How to set him autonomous
----------------------------
