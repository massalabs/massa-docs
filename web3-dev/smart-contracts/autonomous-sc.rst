.. _sc-autonomous:

Web 3 autonomous game tutorial, Step by Step!
=============================================

In this tutorial, we will see how to develop step by step a web3 autonomous
video game based on cats living autonomously on the Massa blockchain.
Idea is simple : a user creates a cat, and have to feed him with massa tokens
in order to avoid starving and death ! The cat has to eat periodically.

Each cat is technically a smart contract deployed on the Massa blockchain.
We will see how to set this smart contract autonomous in order to allows it
to carry out actions by itself.

.. note::

  If you play for the first time with Massa smart contract, you first of all have
  to install the environement following this: `Getting started Massa tutorial <https://docs.massa.net/en/latest/hackathon.html>`_
  

.. _part1:
1) How to create and deploy a cat
---------------------------------

First of all, we need to create and deploy on the blockchain the smart contracts for each cat using 2 scripts :

* main.ts : will allow us to deploy the smart contract of the cat.
* cat.ts : will manage all behaviour of the smart contract of the cat.

The main.ts script :
....................

.. code-block:: typescript
 

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

  If you want more explanations about the main.ts script, let's see line by line what is going on :

  * Import Massalabs library :

    .. code-block:: typescript

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

    .. code-block:: typescript

      export function main(_args: string): void {    
          const bytes = fileToBase64('./build/cat.wasm');
          let addr = createSC(bytes);
          var no_args = new Args();

          generateEvent("A new cat is born! Address of the cat : " + addr.toByteString());
          
      }

    This step declares the function `main()` that will be executed on the blockchain. Inside the function we can find :

    * `const bytes = fileToBase64('./build/cat.wasm');` in order to create the binary code from the "cat.wasm" file and store it into the bytes variable.
    * `let addr = createSC(bytes);` in order to instanciate the addr variable and deploy the smart contract of the bytes variable.
    * `var no_args = new Args();` instanciate a new Args() object with Null value. We will be used with some specific function like `call()`.
    * `generateEvent("A new cat is born! Address of the cat : " + addr.toByteString());` will just send a message on the client with the smart contract address, using the function `generateEvent("Message")`.


  * Define the attributes of the new cat :

    .. code-block:: typescript

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

    .. code-block:: typescript

      generateEvent("--- Information about the cat ==> " +  
                        " Name :" + call(addr,"get_name","",0) +
                        " || Birthday :" + call(addr,"get_birth","",0) +
                        " || State :" + call(addr,"get_state","",0) +
                        " || Last meal at :" + call(addr,"get_last_meal","",0) + 
                        " || Hangry since :" + call(addr,"get_hangry_since","",0));

    We can print the cat information (into the massa client) using the function `generateEvent("Message")`. 

    The `call()` function allows us to call the functions defined into our cat smart contract knowing the address of this one and get information about the smart contract. This should be used like :
    
    .. code-block:: typescript

    	call(address_of_the_smart_contract_to_call, "function_to_call", "parameters_of_the_function", tokens_to_send_during_the_call)

   
The cat.ts script :
....................

.. code-block:: typescript

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

  .. code-block:: typescript

    import { Storage } from "@massalabs/massa-as-sdk";

    export function get_name(_args: string): string {return Storage.get("name");}

  for instance here we declare into the cat smart contract a callable function named "get_name" that will return a string with the value of the key "name".

  Thus, any smart contract will be able to get the name of the cat using a call() function : 
  
  .. code-block:: typescript

  	call(address_of_the_cat, "get_name", "", 0)

Lets try our code!
..................

* Just compile your main.ts file in the terminal using : 

.. code-block:: bash

  cd ~/my-sc
  yarn run build
  
.. note::
  
  Make sure your file `my-sc/package.json` is written as following :
  
  .. code-block:: json
	    {
	    "name": "my-massa-sc",
	    "version": "1.0.0",
	    "description": "",
	    "main": "index.js",
	    "scripts": {
		"test": "npx astester --imports node_modules/@massalabs/massa-as-sdk/astester.imports.js",
		"asbuild:debug": "asc assembly/index.ts --target debug",
		"asbuild:release": "asc assembly/index.ts --target release",
		"asbuild": "npm run asbuild:debug && npm run asbuild:release",
		"start": "npx serve .",
		"build": "asc assembly/cat.ts --target release --exportRuntime -o build/cat.wasm && asc --transform transformer/file2base64.js assembly/main.ts --target release --exportRuntime -o build/main.wasm",
		"simulate": "node ./simulator/simulate.js",
		"deploy": "ts-node --esm deployer/deployment_script.ts",
		"lint": "eslint --resolve-plugins-relative-to \"./assembly/**/*.{ts,json}\" --fix",
		"prettier": "prettier \"./*.{ts,js,json,md}\" \"./{src,__tests}/**/*.{ts,js,json,md}\" --write",
		"format": "npm run lint && npm run prettier"
	    },
	    "keywords": [],
	    "author": "",
	    "license": "ISC",
	    "devDependencies": {
		"@massalabs/as": "^1.0.2",
		"@massalabs/massa-as-sdk": "github:massalabs/massa-as-sdk",
		"@types/node": "^18.11.3",
		"@typescript-eslint/eslint-plugin": "^5.40.1",
		"@typescript-eslint/parser": "^5.40.1",
		"assemblyscript": "^0.21.7",
		"eslint": "^8.25.0",
		"tester": "https://gitpkg.now.sh/massalabs/as/tester?main",
		"transformer": "https://gitpkg.now.sh/massalabs/as/transformer?main",
		"tslib": "^2.4.0"
	    },
	    "type": "module",
	    "exports": {
		".": {
		    "import": "./build/release.js",
		    "types": "./build/release.d.ts"
		}
	    }
	}
  
* Copy the file "main.wasm" from `my-sc/build/main.wasm` to `massa/massa-client/main.wasm` using : 

.. code-block:: bash

  cd ~
  cp my-sc/build/main.wasm massa/massa-client/main.wasm

* Send the smart contract on the blockchain massa using into the massa client the following command : 

.. code-block:: bash

  send_smart_contract your_address main.wasm 10000000 0 0 
  
And get the events sent by `generateEvent()` function used in our script. You can filter them by your emitter address using the following command into the client : 

.. code-block:: bash

  get_filtered_sc_output_event emitter_address=your_address

You should get as following (The "Data:" lines correspond to the message print using `generateEvent()` function into the smart contract): 

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

  Data: --- Information about the cat ==> Name :Massa_cat || Birthday :1668439608968 || State :ok || Last meal at :1668439608968 || Hangry since :0
  
  
.. _part2:
2) How to feed the cat with tokens
----------------------------------

Now we want to feed our cat with tokens. We have to add some code to main.ts and cat.ts files.

The main.ts script :
....................

We then need to transfert tokens to the smart contract of the cat adding in the main.ts : 

.. code-block:: typescript 

  	import { createSC, fileToBase64, Storage, Context, generateEvent, call, transferCoins, Args } from "@massalabs/massa-as-sdk"

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
		"Name :" + call(addr, "get_name", no_args, 0) +
		" || Birthday :" + call(addr, "get_birth", no_args, 0) +
		" || State :" + call(addr, "get_state", no_args, 0) +
		" || Last meal at :" + call(addr, "get_last_meal", no_args, 0) +
		" || Hangry since :" + call(addr, "get_hangry_since", no_args, 0)
		);


	//transfert 10 tokens to the cat smart contract
	let factor = 100000000;
	transferCoins(addr, 10 * factor);

	//ask to the cat to eat tokens and print the state of the token after eating, and the balance evolution of the smart contract.
	call(addr, "eat", no_args, 0);
	generateEvent("--- Information about the cat ==> " +
		"Name :" + call(addr, "get_name", no_args, 0) +
		" || Birthday :" + call(addr, "get_birth", no_args, 0) +
		" || State :" + call(addr, "get_state", no_args, 0) +
		" || Last meal at :" + call(addr, "get_last_meal", no_args, 0) +
		" || Hangry since :" + call(addr, "get_hangry_since", no_args, 0)
		);

	}

.. note::

  Code analysis : 
 
  .. code-block:: typescript

    let factor = 100000000;
    transferCoins(addr, 10 * factor);

  transfer 10 tokens to the smart contract address. Note that 1 massa token = 100000000 of the standard unit used.

  .. code-block:: typescript

    call(addr, "eat", no_args, 0);

    generateEvent("--- Information about the cat ==> " +
		"Name :" + call(addr, "get_name", no_args, 0) +
		" || Birthday :" + call(addr, "get_birth", no_args, 0) +
		" || State :" + call(addr, "get_state", no_args, 0) +
		" || Last meal at :" + call(addr, "get_last_meal", no_args, 0) +
		" || Hangry since :" + call(addr, "get_hangry_since", no_args, 0)
		);

  call the `eat()` function of the cat smart contract, and print the information about the cat.

The cat.ts script :
....................

.. code-block:: typescript

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

  .. code-block:: typescript

    let factor = 100000000;
    let tokens_to_eat: u64 = 6 * factor;
    let poo_addr = Address.fromByteString("A13ESKj7WRVdjM96ttk2caqzES9nRzwB8pEcMW8GutrPwjo3WQS");

  declares the callable function eat(), and set the number of tokens eaten at each time to 6. The poo_addr is the address where the tokens will be "destoyed" after each meal.

  .. code-block:: typescript

    if (tokens_to_eat <= balance()) {        
            transferCoins(poo_addr, tokens_to_eat);
            generateEvent(Storage.get("name") + " has eaten " + (tokens_to_eat / factor).toString() + " Massa tokens. Current balance : " + (balance() /factor).toString());
            Storage.set("state", "ok");
            Storage.set("last_meal", Context.timestamp().toString());
            Storage.set("hangry_since", "0");
        }

  if there are enought tokens to eat, 6 tokens are transfered to the poo address and the keys of the cat smart contract are updated with new values.

  .. code-block:: typescript

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

.. code-block:: bash

  cd ~/my-sc
  yarn run build
  
* Copy the file "main.wasm" from `my-sc/build/main.wasm` to `massa/massa-client/main.wasm` using : 

.. code-block:: bash

  cd ~
  cp my-sc/build/main.wasm massa/massa-client/main.wasm

* Send the smart contract on the blockchain massa using into the massa client the following command : 

.. code-block:: bash

  send_smart_contract your_address main.wasm 10000000 0 0 
  
And get the events sent by `generateEvent()` function used in our script. You can filter them by your emitter address using the following command into the client : 

.. code-block:: bash

  get_filtered_sc_output_event emitter_address=your_address

You should get as following (The "Data:" lines correspond to the message print using `generateEvent()` function into the smart contract): 

.. code-block:: bash

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


Now if we try to feed the cat 2 times just encapsulating the `eat()` function into a `for` loop : 

.. code-block:: typescript

  for (let i = 0; i < 2; i++) {
        call(addr, "eat", no_args, 0);
        generateEvent("--- Information about the cat ==> " +
		"Name :" + call(addr, "get_name", no_args, 0) +
		" || Birthday :" + call(addr, "get_birth", no_args, 0) +
		" || State :" + call(addr, "get_state", no_args, 0) +
		" || Last meal at :" + call(addr, "get_last_meal", no_args, 0) +
		" || Hangry since :" + call(addr, "get_hangry_since", no_args, 0));
	        
      }  


We observe (only "Data:" line are printed) :

.. code-block:: bash 

  Data: A new cat is born! Address of the cat : A12LCbcpSg4UqadPTtVwwkBvspxLBhujWHVHwZCbUkUXJn6oju19
  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668515058468 || State :ok || Last meal at :1668515058468 || Hangry since :0
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 10
  Data: Massa_cat has eaten 6 Massa tokens. Current balance : 4
  Data: 0--- Informations about the cat ==> Name :Massa_cat || Birthday :1668515058468 || State :ok || Last meal at :1668515058468 || Hangry since :0
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 4
  Data: /! Not enought tokens in the balance! Balance = 4
  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668515058468 || State :starved || Last meal at :1668515058468 || Hangry since :1668515058468


if we try with 3, the cat should die :

.. code-block:: typescript

  for (let i = 0; i < 3; i++) {
        call(addr, "eat", no_args, 0);
        generateEvent("--- Information about the cat ==> " +
		"Name :" + call(addr, "get_name", no_args, 0) +
		" || Birthday :" + call(addr, "get_birth", no_args, 0) +
		" || State :" + call(addr, "get_state", no_args, 0) +
		" || Last meal at :" + call(addr, "get_last_meal", no_args, 0) +
		" || Hangry since :" + call(addr, "get_hangry_since", no_args, 0));
	        
      } 


and saddly ... (only "Data:" line are printed) :

.. code-block:: bash 

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

.. code-block:: typescript
    
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

  .. code-block:: typescript

    generateEvent(call(addr,"loop",no_args,0));

  The `generateEvent()` used before the `call()` prints the return of the `loop()`. Indeed the `loop()` function returns a string during the first call.
  
The cat.ts :
............

.. code-block:: typescript
  
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

  .. code-block::

    export function loop(_args: string): string {
        ...
        }

  This function consists in 3 parts : 

  * The first part is just to check if the cat is dead, and if yes, it stops the loop using `return "0"` :

    .. code-block::

      if(Storage.get("state") == "dead") {
            generateEvent("Cat is dead! End of the loop");
            return "0";
        }

  * The second part is the main mecanism of the autonomous system : the `sendMessage()` :

    .. code-block::
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

    The function `sendMessage()` will call the `loop` function in the futur, allowing us to create an infinite loop. Each iteration of the loop will execute the `eat()` function, allowing the cat to eat by itself periodically. You can define the iteration time period. To get more information about how works the autonomous system and how modify parameters: Go to see the next chapter : 4) Autonomous mecanism fully detailled )
    

  * The third part is just `return "(Loop started)";` during the first call of the `loop()` function by main.ts, in order to signify that the loop started. 

Lets try our code!
..................

Just compile your main.ts file in the terminal using : 

.. code-block:: bash

  cd ~/my-sc
  yarn run build
  
  
Copy the file "main.wasm" from `my-sc/build/main.wasm` to `massa/massa-client/main.wasm` using : 

.. code-block:: bash

  cd ~
  cp my-sc/build/main.wasm massa/massa-client/main.wasm

Send the smart contract on the blockchain massa using into the massa client the following command : 

.. code-block:: bash

  send_smart_contract your_address main.wasm 10000000 0 0 
  
And get the events sent by `generateEvent()` function used in our script. You can filter them by your emitter address using the following command into the client : 

.. code-block:: bash

  get_filtered_sc_output_event emitter_address=your_address

You should get as following (with only `Data:` lines are printed): 

.. code-block:: bash

  ✔ command · get_filtered_sc_output_event emitter_address=A12kgk4YamD6Qt4PdG42iqMSE36BRNiL1JyCmrcGTHrQJuaarMKU

  Data: A new cat is born! Address of the cat : A12mf7ChJMu4nmWNh5WNTqJ5sGkXbpKuvzMzn4oNyde18thNrLev
  Data: --- Informations about the cat ==> Name :Massa_cat || Birthday :1668691842468 || State :ok || Last meal at :1668691842468 || Hangry since :0
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 10
  Data: Massa_cat has eaten 6 Massa tokens. Current balance : 4
  Data: (Loop started)

And when you filter with the cat smart contract address (with only `Data:` lines printed): 

.. code-block:: bash

  ✔ command · get_filtered_sc_output_event emitter_address=A12mf7ChJMu4nmWNh5WNTqJ5sGkXbpKuvzMzn4oNyde18thNrLev

  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 4
  Data: /! Not enought tokens in the balance! Balance = 4
  Data: Massa_cat wants to eat 6 Massa tokens. Current balance : 3
  Data: /! Not enought tokens in the balance! Balance = 3
  Data: Massa_cat is starved since : 1668691842968, he dies with pain!
  Data: Cat is dead! End of the loop


.. _part4:
4) Autonomous mecanism fully detailled
--------------------------------------


In this part we will see in detail how works the autonomous smart contract system.

The time in the Massa Blockchain is based on blocks. The Massa blockchain is made by 32 threads generating 2 blocks per second. You can see the current blockchain overview here : `Massa Explorer <https://massa.net/testnet/>`_

If you click on a block, you can see the period and the thread of the block :


.. note::

	For each period (each "horizontal" line in non 3D view) you have 32 blocks corresponding to the 32 threads (vertical lines). Thus, to target a block you have to define the period and the thread. For instance, you can define the period 93904 and the thread 18 to target the block : 2RWCjRjkPLAGRJSH2yVF4WL3RMdJLmn6Ue5hB5xWE2CySuQtqR.
	
	For the same period 93904, if you define the thread 19, you will target an another block.

With the `sendMessage()` function, we can send an operation to execute in the future, defining the period and the thread.


sendMessage() function structure
................................

The structure of the `sendMessage()` function is :

.. code-block:: typescript

	sendMessage(
		at: Address,
	  	functionName: string,
  		validityStartPeriod: u64,
  		validityStartThread: u8,
  		validityEndPeriod: u64,
  		validityEndThread: u8,
  		maxGas: u64,
  		gasPrice: u64,
  		coins: u64,
  		msg: string,
		)

.. note::

	The parameters have to be used like :

	 * @param {string} at // is the address targeted. You can call the current smart contract, or call any other smart contract knowing the address.
	 * @param {string} functionName // the function that you want to execute in the future.
	 * @param {u64} validityStartPeriod - Period of the validity start slot // the current period. You can get it using `currentPeriod()`.
	 * @param {u8} validityStartThread - Thread of the validity start slot // the current thread. You can get it using `currentThread()`.
	 * @param {u64} validityEndPeriod - Period of the validity end slot // The future period you want to execute the function. 
	 * @param {u8} validityEndThread - Thread of the validity end slot // The future thread you want to execute the function.
	 * @param {u64} maxGas - Maximum gas for the message execution // Gas used to execute the function.
	 * @param {u64} gasPrice - Price of one gas unit // Price of the gas.
	 * @param {u64} coins - Coins of the sender // If you want to send coins in the same operation.
	 * @param {string} msg - serialized data // If you want to add a message to the operation.
 

sendMessage() function, how to use it :
.......................................

As we saw previously, we need to get the current period and thread in order to use the `sendMessage()` function. We can do it using :

.. code-block:: typescript
	
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

.. note::

	This part prepares the variables used in the `sendMessage()` function. We just get the current period and current thread in order to define the variables `next_period` and `next_thread`. 
	
	Here we defined `next_thread` as the current thread +1, but you can stay on the same thread if you want and just define the `next_period` as current_period + number.
	
	The if block avoids the thread to be out of threads maximal range. The period has no range limitations but thread has to be between 0 and 31.
	
	We can get all addresses that called the smart contract using : `let call_stack = addressStack();`. 
	We can get the current smart contract address using : `let cur_addr = call_stack[call_stack.length - 1];`
	

Now we can use the sendMessage() function :

.. code-block::

	   sendMessage(
			cur_addr,
			"function_to_call",
			next_period,
			next_thread,
			next_period + 5,
			next_thread,
			70000000,
			0,
			0,
			""
    		);
    		
.. note::

	* `function_to_call` is the name of the function to call in the targetted smart contract,
	* `next_period` will define the period to target,
	* `next_thread` will define the thread to target,
	* `70000000` is the maximum gas to use,
	* the price gas is define to 0,
	* no coins will be sent,
	* `""` will be the message sent, to the function.
	
.. note::

	/!\ with `sendMessage()` you can call an another smart contract, or you can call a function in the current smart contract. However, to set the smart contract autonomous you have to set a loop as we did previously! 
	
	And don't forget to create a condition to break the loop in order to avoid an infinite loop!
	
	
How to convert time in period and thread:
.........................................

Play with period is not really the easiest way.

If you want to call a function in let's say 10 minutes, you have to convert 10 minutes in period and thread.

.. note::

	The time on the blockchain :

	* Time between the current Thread and the next one = 0.5 seconds.
	* 32 Threads = 1 Period = 32 * 0.5 seconds = 16 seconds.

 
If we want to execute a function 10 minutes in the future (600 seconds), we then have to use sendMessage() function with :

.. code-block:: typescript

	next_period = current_period + 37
	
Indeed : 37 * 16 seconds = 592 seconds; And :

.. code-block:: typescript

	next_thread = current_thread + 16

Indeed : 16 * 0.5 seconds = 8 seconds.

.. note::
	
	Explanation :
	
	 * 10 minutes = 600 seconds
	 * We know that 1 period = 16 seconds. Then 600 seconds = 37.5 periods. We can add 37 periods but not 37.5 because period is u64.
	 * we know that 1 period = 32 Threads. Then 0.5 period = 12 thread. Thus, adding 16 Threads we will add the 0.5 periods missing to the 37.
	 
	 Thus : 37 periods = 592 seconds, and 16 threads = 8 seconds. 592 + 8 = 600 seconds = 10 minutes.
