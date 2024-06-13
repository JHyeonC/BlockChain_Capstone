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

### 2. '/block/:blockHash' API에서 BlockHash를 통해 Block을 조회할 때 잘못된 값으로 조회할 경우 Exception 처리
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
			note: `${blockHash}의 block을 찾을 수 없습니다.`
		});
	}
});
```

### 3. '/transaction/broadcast' API에서 body 값이 잘못되었을 경우 Exception 처리
```
app.post('/transaction/broadcast', function(req, res) {
	try{
		const { amount, sender, recipient } = req.body;

		if(!amount || !sender || !recipient) {
			return res.status(400).json({ note : "입력값이 잘못되었습니다." });
		}
```

### 4. '/mine' API에서 새로운 block을 mined & broadcast 하는데 실패하였을 경우 Exception 처리
```
.then(data => {
		res.json({
			note: "New block mined & broadcast successfully",
			block: newBlock
		});
	})
	.catch(error => {
		res.status(400).json({
			note: "새로운 block을 mined & broadcast하는데 실패했습니다.",
			error: error
		});
	});
```

### 5. '/register-and-broadcast-node' API에서 Exception 처리
(1) newNodeUrl 값이 잘못되었을 경우
```
 register a node and broadcast it the network
app.post('/register-and-broadcast-node', function(req, res) {
	const newNodeUrl = req.body.newNodeUrl;

	if(!newNodeUrl){
		return res.status(400).json({ note: "newNodeUrl의 값이 잘못되었습니다."});
	}

	if (bitcoin.networkNodes.indexOf(newNodeUrl) == -1) bitcoin.networkNodes.push(newNodeUrl);
```
(2) 새로운 node를 등록하는데 실패할 경우
```
.then(data => {
		res.json({ note: 'New node registered with network successfully.' });
	})
	.catch((error) => {
		res.status(400).json({
			note: "새로운 node를 등록 및 broadcast 실패하였습니다.",
			error: error
		});
	});
```

### 6. '/register-node' API에서 등록할 노드가 이미 존재하거나, 현재노드일 경우 Exception 처리
```
if (nodeNotAlreadyPresent && notCurrentNode) {
		bitcoin.networkNodes.push(newNodeUrl);
		res.json({ note: 'New node registered successfully.' });
	}else {
		res.status(400).json({
			note: "등록할 node가 이미 존재하거나, 현재 노드입니다."
		});
	}
```

### 7. '/register-nodes-bulk' API에서 allNetworkNodes의 값이 잘못된 경우 Exception 처리
```
// register multiple nodes at once
app.post('/register-nodes-bulk', function(req, res) {
	const allNetworkNodes = req.body.allNetworkNodes;

	if(!allNetworkNodes){
		return res.status(400).json({
			note: "allNetworkNodes의 값이 잘못되었습니다."
		});
	}
```

### 8. '/consensus' API에서 consensus 로직을 완료하지 못했을 경우 Exception 처리
```
.catch((error) => {
		res.status(400).json({
			note: "consensus 처리를 실패했습니다.",
			error: error
		});
	});
```
