module MyToken {
    use 0x1::Signer;

    resource T {
        coin: u64,
    }

    public fun main(account: &signer) {
        let total_supply: u64 = 10000000;

        let signer_addr: address = Signer::address_of(account);
        let balance: T;
        balance.coin = total_supply;

        // Initialize the vesting schedule
        let issuance_date: u64 = 1646064000; // Example issuance date (2022-02-28)
        let vesting_schedule: u64 = 6 * 30 days; // 6 months in seconds
        let elapsed_time: u64 = CurrentTime::now_seconds() - issuance_date;

        // Function to withdraw available tokens
    function withdrawTokens() public {
        uint256 available = availableTokens(msg.sender);
        require(available > 0, "No tokens available for withdrawal yet");
        vestingSchedule[msg.sender] += available;
        balanceOf[msg.sender] += available;  // Function to withdraw available tokens
  
        }
    }
}

