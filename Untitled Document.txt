pragma solidity ^0.4.19;

contract lotto{
    
    address public owner;
    address[] private bets;
    address[] private luckyBetters;//stores addresses of winners
   
    
    bytes32 private totalHash;
    
    mapping(address => uint256) winners;    //stores winning sum of an address
    mapping(address => uint64) balance;     //stores number of tokens bought
    //mapping(uint64 => address) betters;
    mapping(address => uint256) betters;     //betters and their bets
    uint8 state;
    uint tokenPrice = 1 ether; 
    uint luckyNumber;
    //uint private currentBlockNumber;
    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }
    
    //constructor 
    
    function lotto(uint _winningGuess) public onlyOwner(){   //done
        require(state == 0);
        require(_winningGuess>0 && _winningGuess < 1000000);
        owner = msg.sender;
        //currentBlockNumber = block.number;
        //totalHash = bytes32(0);     
        luckyNumber = uint(keccak256(_winningGuess));
    }//done
    
    
    function purchaseToken()public payable returns(bool){
        require(state == 0);
        require(msg.value > 1 ether);
        
        uint _purchaseMoney = msg.value;
        
        for(uint i = 0; i < _purchaseMoney%1-1; i++){
            balance[msg.sender]++;
        } 
        
        if(_purchaseMoney - _purchaseMoney%1 != 0){
            msg.sender.transfer(_purchaseMoney - _purchaseMoney%1);
        }
        
    } //done
    
    function makeGuess(uint _numberBettedOn) public  returns (bool) {
        require(state == 0);
        require(balance[msg.sender] > 0);
        bets.push(msg.sender);
        betters[msg.sender] = _numberBettedOn;
        balance[msg.sender]--;
    }
     //done
        
        
    
    function closeGame() public {
        require(msg.sender == owner);
        state = 1;
    } //done
    
    function winnerAddress()public returns(address[]){ //can be either public or private and perhaps called only by owner
        require(state == 1);
        
        address _addr;
        
        for(uint i = 0; i < bets.length; i++){
        if(uint32(keccak256(uint(betters[_addr]) == luckyNumber))==0){
            luckyBetters.push(_addr);
            address _winner = _addr;
            winners[_winner] = tokenPrice*bets.length; 
            }
        }
        
        return luckyBetters;
    }
    
    function getPrice() public returns (bool) {
        require(state == 0);
        
        uint256 amount = winners[msg.sender]/2;
        winners[msg.sender] = 0;
        
        address _withdrawer;
        
        
        if (_withdrawer.send(amount)) {
            return true;
            } else {
            winners[msg.sender] = amount;
            return false;
        }
        
        if (owner.send(amount)) {
            return true;
            } else {
            winners[msg.sender] = amount;
            return false;
        }
        
        
    }  //done
 
}
