# NCCU Blockchain Course - 2025 Spring
![image](/img/pepe.png)
# Prerequisites
1. Get a Metamask wallet and remember your private key.
    - Do NOT use private keys from Foundry.
2. Go to [Ethereum Sepolia Faucet](https://cloud.google.com/application/web3/faucet/ethereum/sepolia) to obtain Sepolia ETH.
    - Note: You can obtain 0.05 Sepolia ETH every 24 hours each wallet address.
    - If there is any error, try to use another Google account. 

#  ERC-20
In this lab, you will use your own wallet to deploy your own ERC‑20 token on the Sepolia testnet.

During the lab, if you have any questions or issues, feel free to ask the teacher or TA.

### 1. Initialize a Foundry project
```
forge init erc20-lab
cd erc20-lab
```
### 2. Install OpenZeppelin Library
After installing the OpenZeppelin library, you can import it into your contract.
```
forge install OpenZeppelin/openzeppelin-contracts
```

### 3. Delete all example contracts
- Delete `Counter.s.sol` in `script` directory
- Delete `Counter.sol` in `src` directory
- Delete `Counter.t.sol` in `test` directory

### 4. Implement ERC-20 contract
- Create a new Solidity file in `src` directory.
- In the file you just created, paste the following code.
    ```
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.20;

    import "lib/openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";
    import "lib/openzeppelin-contracts/contracts/access/Ownable.sol";

    contract MyToken is ERC20, Ownable {
        constructor(uint INITIAL_SUPPLY) ERC20("MyToken", "MTK") Ownable(msg.sender) {
            _mint(msg.sender, INITIAL_SUPPLY);
        }

        function mint(address to, uint256 amount) external onlyOwner {
            _mint(to, amount);
        }
    }
    ```
- Try to modify the contract name, initial supply amount, token name and token symbol to whatever you like.

### 5. Local Test
- Activating Anvil environment in bash. **(Be sure to keep it open)**
    ```
    anvil
    ```
- In `test` directory, create a new `.t.sol` file with the same name as erc20 contract file in `test` directory (e.g. `MyToken.t.sol`).
- Paste the following code in the test file.
    ```
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    import "forge-std/Test.sol";
    import "../src/MyToken.sol";

    contract MyTokenTest is Test {
        MyToken token;

        function setUp() public {
            token = new MyToken(1_000 * 10**18);
        }

        function testInitialSupply() public {
            assertEq(token.totalSupply(), 1_000 * 10**18);
            assertEq(token.balanceOf(address(this)), 1_000 * 10**18);
        }
    }
    ```
- Modify `MyToken` to the name of your ERC-20 contract.
- Run the test:
    ```
    forge test -vv
    ```

### 6. Deploy ERC-20 contract to Sepolia testnet
- In `script` directory, create a new `.s.sol` file with the same name as erc20 contract file in `script` directory (e.g. `MyToken.s.sol`).
- Paste the following code in the script file.
    ```
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    import "forge-std/Script.sol";
    import "../src/MyToken.sol";

    contract DeployMyToken is Script {
        function run() external {
            uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
            vm.startBroadcast(deployerPrivateKey);

            MyToken token = new MyToken(1_000 * 10**18);

            vm.stopBroadcast();

            console.log("Deployed ERC-20 token at:", address(token));
        }
    }
    ```
- Modify `MyToken` to the name of your ERC-20 contract.

- Set environment variable with your private key. Be aware that you need to add `0x` in front of your private key.
    ```
    export PRIVATE_KEY=0x<PRIVATE_KEY>
    ```
    For example:
    ```
    export PRIVATE_KEY=0xfffffffffffffffffffffff
    ```

- Deploy the contract to Sepolia testnet.
    ```
    forge script script/<SCRIPT_NAME>.s.sol \
        --rpc-url Sepolia \
        --broadcast
    ```

### 7. Check the contract
- Goto https://sepolia.etherscan.io/address/<TOKEN_CONTRACT_ADDRESS> to check your contract.
    ![image](/img/etherscan.jpg)
- Check total supply of your token.
    ```
    cast call <CONTRACT_ADDRESS> "totalSupply()" \
        --rpc-url Sepolia
    ```

### 8. Import token in Metamask.

### 9. Transfer token with your classmates!
- Goto [ERC‑20 Collaboration Form](https://docs.google.com/spreadsheets/d/1tCwMNnZe6jjMBQVB9Nb7c0JYvaNeSEe5X2ZiKn6xMJI/edit?usp=sharing). Add you name, wallet address, ERC-20 contract address and token symbol to the form.

- Mint token to specific wallet address.
    ```
    cast send <CONTRACT_ADDRESS> \
        "mint(address,uint256)" <recipient_address> <amount> \
        --rpc-url Sepolia \
        --private-key $PRIVATE_KEY
    ```

- Try to mint or transfer the token to your classmates in the form.
- After you mint token to others, check if the total supply of your token changed?

---
<br>

# ERC-721
In this lab, 
### 1. Initialize a Foundry project
```
forge init erc721-lab
cd erc721-lab
```

### 2. Install OpenZeppelin Library
After installing the OpenZeppelin library, you can import it into your contract.
```
forge install OpenZeppelin/openzeppelin-contracts
```

### 3. Delete all example contracts
- Delete `Counter.s.sol` in `script` directory
- Delete `Counter.sol` in `src` directory
- Delete `Counter.t.sol` in `test` directory

### 4. Implement ERC-721 contract
- Create a new Solidity file in `src` directory.
- In the file you just created, paste the following code.
    ```
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.20;

    import "lib/openzeppelin-contracts/token/ERC721/extensions/ERC721URIStorage.sol";
    import "lib/openzeppelin-contracts/access/Ownable.sol";

    contract MyNFT is ERC721URIStorage, Ownable {
        uint256 private _nextTokenId;

        constructor() ERC721("Pepe the frog", "PEPE") {
            _nextTokenId = 1;
        }

        function mintTo(address to, string calldata tokenURI_) external onlyOwner returns (uint256) {
            uint256 tokenId = _nextTokenId;
            _safeMint(to, tokenId);
            _setTokenURI(tokenId, tokenURI_);
            _nextTokenId += 1;
            return tokenId;
        }
    }

    ```
- Try to modify the contract name, token name and token symbol to whatever you like.

### 5. Build & Test
- Activating Anvil environment in bash. **(Be sure to keep it open)**
    ```
    anvil
    ```
- Build ERC-721 contract:
    ```
    forge build
    ```
- In `test` directory, create a new `.t.sol` file with the same name as ERC-721 contract file (e.g. `MyNFT.t.sol`).
- Paste the following code in the test file.
    ```
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.20;

    import "forge-std/Test.sol";
    import "../src/MyNFT.sol";

    contract MyNFTTest is Test {
        MyNFT nft;
        address user = address(0xBEEF);
        string constant URI = "ipfs://cid/1.json";

        function setUp() public {
            nft = new MyNFT();
        }

        function testMintAndURI() public {
            uint256 id = nft.mintTo(user, URI);
            assertEq(id, 1);
            assertEq(nft.ownerOf(id), user);
            assertEq(nft.tokenURI(id), URI);
        }

        function testNonOwnerCannotMint() public {
            vm.prank(user);
            vm.expectRevert("Ownable: caller is not the owner");
            nft.mintTo(user, URI);
        }
    }
    ```
- Modify `MyNFT.sol` to the name of your ERC-721 contract file.
- Run the test:
    ```
    forge test -vv
    ```

### 6. Deploy ERC-721 contract to Sepolia testnet
- Deploy the contract to Sepolia testnet.
    ```
    forge create src/<ERC-721_FILE_NAME>.sol \
        --rpc-url https://ethereum-sepolia-rpc.publicnode.com \
        --private-key 0x<YOUR_PRIVATE_KEY>
    ```

### 7. Mint your NFT
- Call `mintTo()` to mint the NFT
    ```
    cast send <Contract_Address> \
        "mintTo(address,string)" <Wallet_Address> "<Metadata_URI>" \
        --rpc-url https://ethereum-sepolia-rpc.publicnode.com \
        --private-key 0x<YOUR_PRIVATE_KEY>
    ```
- Check URI of the NFT
    ```
    cast call <Contract_Address> "tokenURI(uint256)" 1 \
        --rpc-url https://ethereum-sepolia-rpc.publicnode.com
    ```

### 8. Check NFTs on Opensea testnet
- Go to [Opensea Testnet](https://testnets.opensea.io/zh-TW)
- Login with your Metamask
- Check your profile
- You should see the NFT you just mint
