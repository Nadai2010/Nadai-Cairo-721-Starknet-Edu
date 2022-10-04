# Tutorial Nadai ERC721 on StarkNet

¡Bienvenidos! Este es un taller automatizado que explicará cómo implementar un token ERC721 en StarkNet y personalizarlo para realizar funciones específicas. El estándar ERC721 se describe [aquí](https://docs.openzeppelin.com/contracts/3.x/api/token/erc721). Está dirigido a desarrolladores que: 

- Comprender la sintaxis de Cairo 
- Comprender el estándar de token ERC721 .

## Introducción

### Disclaimer

No espere ningún tipo de beneficio al usar esto, aparte de aprender un montón de cosas interesantes sobre StarkNet, el primer paquete acumulativo de validez de propósito general en Ethereum Mainnet. 

StarkNet todavía está en Alfa. Esto significa que el desarrollo está en curso y que la pintura no está seca en todas partes. Las cosas mejorarán y, mientras tanto, ¡hacemos que las cosas funcionen con un poco de cinta adhesiva aquí y allá! 

### ¿Cómo funciona?

El objetivo de este tutorial es personalizar e implementar un contrato ERC721 en StarkNet. Su progreso será verificado por un contrato de [evaluator contract](contracts/Evaluator.cairo), implementado en StarkNet, que le otorgará puntos en forma de [ERC20 tokens](contracts/token/ERC20/TDERC20.cairo).

Cada ejercicio requerirá que agregue funcionalidad a su token ERC721. 

Para cada ejercicio, deberá escribir una nueva versión en su contrato, implementarlo y enviarlo al evaluador para su corrección. 

### ¿Dónde estoy? 

Este taller es el segundo de una serie destinada a enseñar cómo construir en StarkNet. Echa un vistazo a lo siguiente:
​

| Topic                                             | GitHub repo                                                                            |
| ------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Learn how to read Cairo code                      | [Cairo 101](https://github.com/starknet-edu/starknet-cairo-101)                        |
| Deploy and customize an ERC721 NFT (you are here) | [StarkNet ERC721](https://github.com/starknet-edu/starknet-erc721)                     |
| Deploy and customize an ERC20 token               | [StarkNet ERC20](https://github.com/starknet-edu/starknet-erc20)                       |
| Build a cross layer application                   | [StarkNet messaging bridge](https://github.com/starknet-edu/starknet-messaging-bridge) |
| Debug your Cairo contracts easily                 | [StarkNet debug](https://github.com/starknet-edu/starknet-debug)                       |
| Design your own account contract                  | [StarkNet account abstraction](https://github.com/starknet-edu/starknet-accounts)      |

### Proporcionar comentarios y obtener ayuda 

Una vez que haya terminado de trabajar en este tutorial, ¡sus comentarios serán muy apreciados!

Complete [este formulario](https://forms.reform.app/starkware/untitled-form-4/kaes2e) para informarnos qué podemos hacer para mejorarlo. 

Y si tiene dificultades para seguir adelante, ¡háganoslo saber! Este taller está destinado a ser lo más accesible posible; queremos saber si no es el caso. 

¿Tienes alguna pregunta? Únase a nuestro servidor [Discord server](https://starknet.io/discord), regístrese y únase al canal #tutorials-support. ¿Está interesado en seguir talleres en línea sobre cómo aprender a desarrollar en StarkNet? [Subscríbete aquí](http://eepurl.com/hFnpQ5)

### Contribuyendo 

Este proyecto se puede mejorar y evolucionará a medida que StarkNet madure. ¡Sus contribuciones son bienvenidas! Aquí hay cosas que puede hacer para ayudar: 

- Crea una sucursal con una traducción a tu idioma .
- Corrija los errores si encuentra algunos. 
- Agregue una explicación en los comentarios del ejercicio si cree que necesita más explicación.
- Agregue ejercicios que muestren su característica favorita de El Cairo​.


## Preparándose para trabajar 

### Paso 1: Clonar el repositorio 

- Oficial:

```bash
git clone https://github.com/starknet-edu/starknet-erc721
cd starknet-erc721
```

- Nadai con Soluciones:

```bash
gh repo clone Nadai2010/Nadai-Cairo-721-Starknet-Edu
cd Nadai-Cairo-721-Starknet-Edu
```
### Paso 2: Configure su entorno 

Hay dos formas de configurar su entorno en StarkNet: Una instalación local o usando un contenedor docker.

- Para usuarios de Mac y Linux, recomendamos either
- Para usuarios de Windows recomendamos docker 

Para obtener instrucciones de configuración de producción, escribimos [este artículo](https://medium.com/starknet-edu/the-ultimate-starknet-dev-environment-716724aef4a7).

#### Opción A: Configurar un entorno Python local 

Configure el entorno siguiendo [estas instrucciones](https://starknet.io/docs/quickstart.html#quickstart)
- Instalar [OpenZeppelin's cairo contracts](https://github.com/OpenZeppelin/cairo-contracts).

```bash
pip install openzeppelin-cairo-contracts
```

#### Opción B: Usar un entorno dockerizado

- Linux y macos

Para mac m1: 

```bash
alias cairo='docker run --rm -v "$PWD":"$PWD" -w "$PWD" shardlabs/cairo-cli:latest-arm'
```

Para amd procesadores

```bash
alias cairo='docker run --rm -v "$PWD":"$PWD" -w "$PWD" shardlabs/cairo-cli:latest'
```

- Windows

```bash
docker run --rm -it -v ${pwd}:/work --workdir /work shardlabs/cairo-cli:latest
```

#### Paso 3: Pruebe que puede compilar el proyecto contratos de compilación

```bash
starknet-compile contracts/Evaluator.cairo
```

## Trabajando en el tutorial 

### Flujo de trabajo 

Para hacer este tutorial tendrás que interactuar con el contrato [`Evaluator.cairo`](contracts/Evaluator.cairo). Para validar un ejercicio tendrás que:

- Leer el código del evaluador para averiguar qué se espera de su contrato 
- Personaliza el código de tu contrato 
- Despliéguelo en la red de prueba de StarkNet. Esto se hace usando la CLI. 
- Registre su ejercicio para corrección, usando la función de `submit_exercise` en el evaluador. Esto se hace usando Voyager. 
- Llame a la función correspondiente en el contrato del evaluador para corregir su ejercicio y recibir sus puntos. Esto se hace usando Voyager. 

Por ejemplo para resolver el primer ejercicio el flujo de trabajo sería el siguiente: 


`deploy a smart contract that answers ex1` &rarr; `call submit_exercise on the evaluator providing your smart contract address` &rarr; `call ex1_test_erc721 on the evaluator contract`

**Su objetivo es reunir tantos puntos ERC721-101 como sea posible.** Tenga en cuenta : 

- La función de 'transferencia' de ERC721-101 ha sido deshabilitada para alentarlo a terminar el tutorial con una sola dirección Para recibir puntos, el evaluador debe alcanzar las llamadas a la función distribuir_punto. 
- Este repositorio contiene una interfaz `IExerciseSolution.cairo`. Su contrato ERC721 deberá ajustarse a esta interfaz para validar algunos ejercicios; es decir, su contrato debe implementar todas las funciones descritas en `IExerciseSolution.cairo`.

- **Realmente recomendamos que lea el contrato de [`Evaluator.cairo`](contracts/Evaluator.cairo) para comprender completamente lo que se espera de cada ejercicio**. En este archivo Léame se proporciona una descripción de alto nivel de lo que se espera de cada ejercicio. 

- El contrato de Evaluador a veces necesita realizar pagos para comprar sus tokens. ¡Asegúrate de que tenga suficientes fichas ficticias para hacerlo! De lo contrario, debe obtener tokens ficticios del contrato de tokens ficticios y enviarlos al evaluador.

### Direcciones y contratos oficiales

| Contract code                                                        | Contract on voyager                                                                                                                                                           |
| -------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Points counter ERC20](contracts/token/ERC20/TDERC20.cairo)          | [0xa0b943234522049dcdbd36cf9d5e12a46be405d6b8757df2329e6536b40707](https://goerli.voyager.online/contract/0xa0b943234522049dcdbd36cf9d5e12a46be405d6b8757df2329e6536b40707) |
| [Evaluator](contracts/Evaluator.cairo)                               | [0x2d15a378e131b0a9dc323d0eae882bfe8ecc59de0eb206266ca236f823e0a15](https://goerli.voyager.online/contract/0x2d15a378e131b0a9dc323d0eae882bfe8ecc59de0eb206266ca236f823e0a15) |
| [Dummy ERC20 token](contracts/token/ERC20/dummy_token.cairo)         | [0x52ec5de9a76623f18e38c400f763013ff0b3ff8491431d7dc0391b3478bf1f3](https://goerli.voyager.online/contract/0x52ec5de9a76623f18e38c400f763013ff0b3ff8491431d7dc0391b3478bf1f3) |
| [Dummy ERC721 token](contracts/token/ERC721/TDERC721_metadata.cairo) | [0x4fc25c4aca3a8126f9b386f8908ffb7518bc6fefaa5c542cd538655827f8a21](https://goerli.voyager.online/contract/0x4fc25c4aca3a8126f9b386f8908ffb7518bc6fefaa5c542cd538655827f8a21)   |

​​

## Lista de tareas 

¡Hoy estamos creando un registro de animales! Los animales son criados por criadores. Pueden nacer, morir, reproducirse, venderse. Irás implementando estas características poco a poco. 

### Ejercicio 1: Implementación de un ERC721 

- Cree un contrato de token ERC721. Puedes usar [esta implementación](https://github.com/OpenZeppelin/cairo-contracts/blob/v0.2.1/src/openzeppelin/token/erc721/ERC721_Mintable_Burnable.cairo) como base.
- Despliéguelo en la red de prueba (verifique en el constructor los argumentos necesarios. También tenga en cuenta que los argumentos deben ser decimales). 

```bash
starknet-compile contracts/ERC721/ERC721.cairo --output artifacts/ERC721.json
starknet deploy --contract artifacts/ERC721.json --inputs arg1 arg2 arg3 --network alpha-goerli 
```

- Entrega el token n.° 1 al contrato del evaluador 
- Llame a [`submit_exercise()`](contracts/Evaluator.cairo#L601) en el Evaluador para configurar el contrato que desea evaluar (4 pts) 
- Llame a [`ex1_test_erc721()`](contracts/Evaluator.cairo#L146) en el evaluador para recibir sus puntos (2 pts)


- Nadai con Soluciones [Ejercio 1](https://github.com/Nadai2010/Nadai-Cairo-721-Starknet-Edu/blob/master/contracts/Soluci%C3%B3n/ERC721ex01.md)

### Exercise 2 - Creating token attributes

- Call [`ex2a_get_animal_rank()`](contracts/Evaluator.cairo#L245) to get assigned a random creature to create.
- Read the expected characteristics of your animal from the Evaluator
- Create the tools necessary to record animals characteristics in your contract and enable the evaluator contract to retrieve them trough `get_animal_characteristics` function on your contract ([check this](contracts/IExerciseSolution.cairo))
- Deploy your new contract
- Mint the animal with the desired characteristics and give it to the evaluator
- Call [`submit_exercise()`](contracts/Evaluator.cairo#L601) in the Evaluator to configure the contract you want evaluated
- Call [`ex2b_test_declare_animal()`](contracts/Evaluator.cairo#L258) to receive points (2 pts)

### Exercise 3 - Minting NFTs

- Create a function to allow breeders to mint new animals with the specified characteristics
- Deploy your new contract
- Call [`submit_exercise()`](contracts/Evaluator.cairo#L601) in the Evaluator to configure the contract you want evaluated
- Call [`ex3_declare_new_animal()`](contracts/Evaluator.cairo#L272) to get points (2 pts)

### Exercise 4 - Burning NFTs

- Create a function to allow breeders to declare dead animals (burn the NFT)
- Deploy your new contract
- Call [`submit_exercise()`](contracts/Evaluator.cairo#L601) in the Evaluator to configure the contract you want evaluated
- Call [`ex4_declare_dead_animal()`](contracts/Evaluator.cairo#L323) to get points (2 pts)

### Exercise 5 - Adding permissions and payments

- Use [dummy token faucet](contracts/token/ERC20/dummy_token.cairo) to get dummy tokens
- Use [`ex5a_i_have_dtk()`](contracts/Evaluator.cairo#L406) to show you managed to use the faucet (2 pts)
- Create a function to allow breeder registration.
- This function should charge the registrant for a fee, paid in dummy tokens ([check `registration_price`](contracts/IExerciseSolution.cairo))
- Add permissions. Only allow listed breeders should be able to create animals
- Deploy your new contract
- Call [`submit_exercise()`](contracts/Evaluator.cairo#L601) in the Evaluator to configure the contract you want evaluated
- Call [`ex5b_register_breeder()`](contracts/Evaluator.cairo#L440) to prove your function works. If needed, send dummy tokens first to the evaluator (2pts)

### Exercise 6 - Claiming an NFT

- Mint a NFT with metadata on [this dummy ERC721 token](contracts/token/ERC721/TDERC721_metadata.cairo) , usable [here](https://goerli.voyager.online/contract/0x4fc25c4aca3a8126f9b386f8908ffb7518bc6fefaa5c542cd538655827f8a21)
- Check it on [Aspect](https://testnet.aspect.co/)
- Claim points on [`ex6_claim_metadata_token`](contracts/Evaluator.cairo#L523) (2 pts)

### Exercise 7 - Adding metadata

- Create a new ERC721 contract that supports metadata. You can use [this contract](contracts/token/ERC721/ERC721_metadata.cairo) as a base
- The base token URI is the chosen IPFS gateway
- You can upload your NFTs directly on [this website](https://www.pinata.cloud/)
- Your tokens should be visible on [Aspect](https://testnet.aspect.co/) once minted!
- Deploy your new contract
- Call [`submit_exercise()`](contracts/Evaluator.cairo#L601) in the Evaluator to configure the contract you want evaluated
- Claim points on [`ex7_add_metadata`](contracts/Evaluator.cairo#L557) (2 pts)

​

## Annex - Useful tools

### Converting data to and from decimal

To convert data to felt use the [`utils.py`](utils.py) script
To open Python in interactive mode after running script

  ```bash
  python -i utils.py
  ```

  ```python
  >>> str_to_felt('ERC20-101')
  1278752977803006783537
  ```

### Checking your progress & counting your points

​
Your points will get credited in your wallet; though this may take some time. If you want to monitor your points count in real time, you can also see your balance in voyager!
​

- Go to the  [ERC20 counter](https://goerli.voyager.online/contract/0xa0b943234522049dcdbd36cf9d5e12a46be405d6b8757df2329e6536b40707#readContract)  in voyager, in the "read contract" tab
- Enter your address in decimal in the "balanceOf" function

You can also check your overall progress [here](https://starknet-tutorials.vercel.app)
​

### Transaction status

​
You sent a transaction, and it is shown as "undetected" in voyager? This can mean two things:
​

- Your transaction is pending, and will be included in a block shortly. It will then be visible in voyager.
- Your transaction was invalid, and will NOT be included in a block (there is no such thing as a failed transaction in StarkNet).
​
You can (and should) check the status of your transaction with the following URL  [https://alpha4.starknet.io/feeder_gateway/get_transaction_receipt?transactionHash=](https://alpha4.starknet.io/feeder_gateway/get_transaction_receipt?transactionHash=)  , where you can append your transaction hash.
​

​
