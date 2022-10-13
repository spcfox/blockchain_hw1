# Блокчейн. ДЗ 1

- https://github.com/gnosis/MultiSigWallet/blob/master/contracts/MultiSigWallet.sol - сделать, чтобы с баланса multisig-контракта за одну транзакцию не могло бы уйти больше, чем 66 ETH

    ```
             notNull(destination)
             returns (uint transactionId)
         {
    +        require(value <= 66 ether, "Cannot to create transaction with more than 66 ether");
             transactionId = transactionCount;
             transactions[transactionId] = Transaction({
                 destination: destination,
    ```



- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/f2112be4d8e2b8798f789b948f2a7625b2350fe7/contracts/token/ERC20/ERC20.sol - сделать, чтобы токен не мог бы быть transferred по субботам

    ```
         string private _name;
         string private _symbol;

    +    /*
    +     *  Constants
    +     */
    +    uint32 constant day = 24 * 60 * 60;
    +    uint32 constant week = 7 * day;
    +    uint32 constant saturday_offset = 2 * day; // 1 January 1970 is Thursday
    +
         
         ...
    
         function _transfer(address sender, address recipient, uint256 amount) internal virtual {
             require(sender != address(0), "ERC20: transfer from the zero address");
             require(recipient != address(0), "ERC20: transfer to the zero address");
    +        require(
    +            (block.timestamp - saturday_offset) % week < day,
    +            "The token can only be transferred on Saturdays"
    +        );
     
             _beforeTokenTransfer(sender, recipient, amount);
    ```



- https://github.com/mixbytes/solidity/blob/076551041c420b355ebab40c24442ccc7be7a14a/contracts/token/DividendToken.sol - сделать чтобы платеж в ETH принимался только специальной функцией, принимающей помимо ETH еще комментарий к платежу (bytes[32]). Простая отправка ETH в контракт запрещена
 
    ```
     pragma solidity ^0.4.24;
     
    +pragma experimental ABIEncoderV2;
    +
     import 'openzeppelin-solidity/contracts/token/ERC20/StandardToken.sol';
     
     import 'openzeppelin-solidity/contracts/ownership/Ownable.sol';

     ...

             }));
         }
     
    -    function() external payable {
    +    function(bytes[32] memory message) public payable {
             if (msg.value > 0) {
                 emit Deposit(msg.sender, msg.value);
                 m_totalDividends = m_totalDividends.add(msg.value);
    ```
