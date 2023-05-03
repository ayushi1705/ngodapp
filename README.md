// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

contract MyContract {
    // Your contract code here
}

contract Charity {
    address public owner;
    uint public totalDonations;
    uint public donationCount;

    struct Donation {
        address donor;
        uint amount;
        bool verified;
        address ngo;
        address recipient;
    }

    mapping(uint => Donation) public donations;

    event DonationReceived(address donor, uint amount);
    event DonationVerified(uint donationId);
    event DonationHistory(uint donationId, address donor, uint amount, bool verified, address ngo, address recipient);

    constructor() {
        owner = msg.sender;
    }

    function donate(address ngo, address recipient) public payable {
        require(msg.value > 0, "Donation amount must be greater than zero");

        uint donationId = donationCount++;
        donations[donationId] = Donation(msg.sender, msg.value, false, ngo, recipient);
        totalDonations += msg.value;

        emit DonationReceived(msg.sender, msg.value);
    }

    function verifyDonation(uint donationId) public {
        require(msg.sender == owner, "Only owner can verify donations");
        require(!donations[donationId].verified, "Donation already verified");

        donations[donationId].verified = true;

        emit DonationVerified(donationId);
    }

    function getDonationHistory(uint donationId) public view returns (address, uint, bool, address, address) {
        require(donationId < donationCount, "Invalid donation ID");

        Donation memory donation = donations[donationId];
        return (donation.donor, donation.amount, donation.verified, donation.ngo, donation.recipient);
    }
}
