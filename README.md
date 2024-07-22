# StackUp-SmartContractInspection

## Introduction
**Protocol Name:** Uniswap v3: Router

**Category:** DeFi

**Smart Contract:** TransferHelper.sol (located at `SwapRouter/contracts/libraries` if opened in IDE from Etherscan)


## Function Analysis
**Function Name:** safeTransferFrom

**Block Explorer Link:** https://etherscan.io/address/0xe592427a0aece92de3edee1f18e0157c05861564#code, then search for TransferHelper.sol on the page.

**Function Code:**
```
function safeTransferFrom(
        address token,
        address from,
        address to,
        uint256 value
    ) internal {
        (bool success, bytes memory data) =
            token.call(abi.encodeWithSelector(IERC20.transferFrom.selector, from, to, value));
        require(success && (data.length == 0 || abi.decode(data, (bool))), 'STF');
    }
```

**Used Encoding/Decoding or Call Method:** abi.encodeWithSelector, abi.decode, call

## Explanation

**Purpose:** To transfer specified amount of ERC-20 token from a source address to destination address.

**Detailed Usage:** This function provides a mechanism and abstraction to transfer any ERC-20 token by using IERC20 transferFrom selector (encoded by abi.encodeWithSelector) to load the right transferFrom function in the token and then make the token address call that function. With this approach, as long as the address of token passed to safeTransferFrom function is implementation of IERC20, the token will have implemented transferFrom function which can be called by the token address inside safeTransferFrom function. The function also decodes the encoded data as return value from the token call. 

**Impact:** Although I have not read the full codes of UniSwap V3, it is my assumption that as one of DeFi protocol, UniSwap provides the functionality to do swap and to do that, it needs to be able to transfer ERC-20 token from a user's wallet to itself before transferring back to the user the target token that user would like to receive. With the TransferHelper safeTransferFrom function, Uniswap now has a helper function that can dynamically load transferFrom function from any ERC-20 token that can be called to either transfer from user's wallet to itself or transfer from itself to user's wallet. They just need to pass the right token, from, to, and value to the function in their logic.
