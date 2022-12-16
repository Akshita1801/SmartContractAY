# SmartContractAY
Smart Contract code
Here is the full code of "VendorPayments" Smart Contract.

//SPDX-License-Identifier: MIT
pragma solidity ^0.8.16;

import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";//importing file to make the smart contract upgradeable

contract VendorPayments is OwnableUpgradeable { //interfacing with OwnableUpgradeable contract

    function initialize() initializer public { //function to initialize the owner of the contract
        super.__Ownable_init();
    }

    struct Vendor { //structure for storing vendor's information
    string _name; //vendor's name
    address _vendor; // address of the vendor's account 
    uint _amount; //amount to be sent to the vendor
    }
    mapping(address => uint) public payAmount; //mapping to link address to the balance
 
    Vendor[] public vendors; //array of structure of vendors
    
    event PaymentDone(uint amount); //event for tracing the payments which are already done

    receive() external payable {} //to make the contract capable of receiving ether

    function updateVendors(string memory _name, address _vendor, uint _amount) public onlyOwner { //function to update vendor's information (onlyOwner can modify it)
        Vendor memory Vendor1 = Vendor(_name, _vendor, _amount); 
        vendors.push(Vendor1); //pushing vendor to the array
    }

    function getBalance() public view returns(uint) { //function to get balance
        return address(this).balance;
    }

    function payToTheVendor(address payable _to) public onlyOwner payable { //function for paying the vendor (onlyOwner can call it)
        require(vendors.length > 0, "Vendor does not exist");   //check if there is an element in the array
        require(_to == vendors[0]._vendor, "Not valid vendor"); //check if the address belongs to the vendors array     
        (bool sent, bytes memory data) = _to.call{value: vendors[0]._amount, gas: 21000}(""); //sending ether to the vendor
        require(sent, "Failed to send Ether"); //check if payment is done
        emit PaymentDone(vendors[0]._amount); //log for tracing the payment
        payAmount[_to]+=vendors[0]._amount; //updating the vendor's account once payment is done
    }
    function getWithdrawalsValue() public view returns (uint){ //vendor can call this function to check his/her balance
        return payAmount[msg.sender];
    }
}


//Steps for deploying the contract
//Run initialize
//Deposit some amount into the smart contract
//Run Update Vendors by entering Vendor details
//Run payToTheVendors
//Connect to Vendor account on metamask
//Run getWithdrawalsValue function to see the amount sent
