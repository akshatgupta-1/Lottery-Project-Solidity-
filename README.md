# Lottery-Project-Solidity-

// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

contract lottery{

    address public manager;
    address payable[] public participants;

    constructor(){

        manager = msg.sender;    //declaring the deployed address as manager.
        
    } 
    
    receive() external payable{

        require(msg.value==1 ether);                //declaring cost as 1 ether.

        participants.push(payable(msg.sender));     //Pushing participant address in the participant array

    }

    function getBalance() public view returns(uint){

        require(msg.sender == manager);             //giving access of this function to only manager
        return address(this).balance;               // checkng balance of the contractr
    }

    function random() public view returns(uint){

        return uint(keccak256(abi.encodePacked(block.difficulty,block.timestamp,participants.length)));
        
                    // randomly generating a integer value from the participants array
        
    }

    function selectWinner() public {

        require(msg.sender==manager);
        require(participants.length>=3);        //minimum 3 participants require to select winner

        address payable winner;

        uint r = random();
        uint index = r % participants.length;               // generating a index for the participants array

        winner = participants[index];                       // transfering the address of the randomly selected winner 
        winner.transfer(getBalance());                      // transfering ethers to winner address from contract account  

        participants = new address payable[](0);            // removing all address from participants array for next lottery round.

    }
}
