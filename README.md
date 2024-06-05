# 2024년 4학년 1학기 캡스톤 디자인 - 개발

## 개선사항

### 1. 코인 거래시 계좌 잔액 확인 및 Exception 처리
```
lockchain.prototype.createNewTransaction = function(amount, sender, recipient) {
	const senderBalance = this.getAddressData(sender).addressBalance;
	if(amount > senderBalance) throw new Exception("잔고가 부족하여 거래할 수 없습니다.");
	.....
}
```
```
app.post('/transaction/broadcast', function(req, res) {
	try{
	.....
	}catch(error){
		res.status(400).json({ note: error.message });
	}
});
```

### 2. BlockHash를 통해 Block을 조회할 때 잘못된 값으로 조회할 경우 Exception 처리
```
// get block by blockHash
app.get('/block/:blockHash', function(req, res) { 
	const blockHash = req.params.blockHash;
	const correctBlock = bitcoin.getBlock(blockHash);
	if (correctBlock !== null) {
		res.json({
			block: correctBlock
		});
	} else {
		res.status(404).json({
			error: "Block not found"
		});
	}
});
```
