At this point we have deployed a smart contract on the Core testnet and we have a client side application that's ready to interact with it. We just need to wire up the last part! We built a simple page on the last step to help you interact with the smart contract. Since the contract has only two methods (`setGreeting` and `getGreeting`) that's all the UI will do: set a number through the smart contract.

As simple as it sounds, what's happening in the background is actually very powerful: we're using the Core blockchain to store information (in this example, a number) and we're using a smart contract as an interface to read and write that information to the blockchain. What's crucial is that all this is happening without having to spin up a database and an API... So let's go!

---

# 🏋️ Challenge

{% hint style="tip" %}
In the file `components/core/challenges/setter.ts`, implement the `setValue` function.  
{% endhint %}

**Take a few minutes to figure this out.**

```typescript
const setValue = async (contractAddress: string, value: number) => {
  try {
    const provider = new ethers.providers.Web3Provider(window.ethereum);
    const signer = provider.getSigner();
    // try to figure out the expected parameters
    const contract = new ethers.Contract(undefined);
    // try to figure out the expected method
    const transactionResult = undefined;
    const receipt = await transactionResult.wait();
    return {hash: receipt.transactionHash};
  } catch (error) {
    return {
      error: error.message,
    };
  }
};
```

Need some help? Check out these links 👇

- [**Create a Contract using ethers**](https://docs.ethers.io/v5/api/contract/contract/#Contract--creating)
- [**How to call a contract's methods on an ethers Contract object**](https://docs.ethers.io/v5/api/contract/contract/#Contract-functionsCall)
- To read from the blockchain you don't need to spend any tokens, so you can use a provider to create a Contract instance. Writing to the blockchain, you will need to create and sign a transaction through Metamask. Use a `signer` to create the Contract object!

Still not sure how to do this? No problem! The solution is below so you don't get stuck.

---

# 😅 Solution

Import the HelloWorld ABI using `import HelloWorldJson from './HelloWorld.json';`. You can find the ABI after the contract has been deployed using Hardaht in the `artifacts/contracts/HelloWorld.sol/HelloWorld.json`.

The ABI has already been copied to the `components/core/challenges` folder.Just import the ABI from there to interact with the contract in your app.

```typescript
// solution
import {ethers} from 'ethers';
import HelloWorldJson from './HelloWorld.json';

declare let window: {
  ethereum: ethers.providers.ExternalProvider;
};

const setValue = async (contractAddress: string, value: string) => {
  // Ensure value is a string
  try {
    // Check if window.ethereum is available
    if (!window.ethereum) {
      throw new Error('Ethereum provider not found. Please install MetaMask.');
    }

    // Create a provider and get the signer
    const provider = new ethers.providers.Web3Provider(window.ethereum);
    await provider.send('eth_requestAccounts', []);
    const signer = provider.getSigner();

    // Instantiate the contract
    const contract = new ethers.Contract(
      contractAddress,
      HelloWorldJson.abi,
      signer,
    );

    // Call the setGreeting method
    const transactionResult = await contract.setGreeting(value, {
      gasLimit: 500000, // Adjust as needed
    });

    // Wait for the transaction to be mined
    const receipt = await transactionResult.wait();
    return {hash: receipt.transactionHash};
  } catch (error) {
    return {error: error.message};
  }
};

export default setValue;
```

**What happened in the code above?**

- We create Contract objects using
  - The contract address.
  - The contract JSON's ABI.
  - A signer, from the ethers web3 provider.
- We then call the function `set` on this Contract object to operate on our decentralized code. The names of the functions must match the ones we defined in our Solidity smart contract. These are available via the ABI.
- Note: Remember to add an override in the `set` contract call for the `gasLimit` to avoid an issue with transactions reverting!

---

# ✅ Make sure it works

Once the code in `components/core/challenges/setter.ts` is complete, you can enter a value into the textinput then click on **Set Value** to send the transaction and change the data stored in the smart contract.

---

# 🏁 Conclusion

Now that we've set the storage of our contract, we can read it. We're going to learn how to do this in the final tutorial of the pathway.
