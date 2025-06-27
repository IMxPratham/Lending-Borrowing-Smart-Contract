// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TimeBasedLending {
    address public lender;
    address public borrower;
    uint256 public loanAmount;
    uint256 public borrowTime;
    uint256 public interestRate = 5; 
    bool public isLoanActive;

    function supply() external payable {
        require(!isLoanActive, "Loan already active");
        require(msg.value > 0, "Must send Ether");

        lender = msg.sender;
        loanAmount = msg.value;
    }

    function borrow() external {
        require(!isLoanActive, "Loan already taken");
        require(loanAmount > 0, "No funds available");

        borrower = msg.sender;
        borrowTime = block.timestamp;
        isLoanActive = true;

        payable(borrower).transfer(loanAmount);
    }

    function repay() external payable {
        require(isLoanActive, "No active loan");
        require(msg.sender == borrower, "Only borrower can repay");

        uint256 timePassed = block.timestamp - borrowTime;
        uint256 interest = (loanAmount * interestRate * timePassed) / (365 days * 100);
        uint256 totalDue = loanAmount + interest;

        require(msg.value >= totalDue, "Not enough to repay loan");

        payable(lender).transfer(totalDue);

        if (msg.value > totalDue) {
            payable(msg.sender).transfer(msg.value - totalDue);
        }

        isLoanActive = false;
        loanAmount = 0;
        lender = address(0);
        borrower = address(0);
        borrowTime = 0;
    }

    receive() external payable {}
}
