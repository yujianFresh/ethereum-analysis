## 1.personal.newAccount创建账户方法
用户在控制台输入personal.newAccount会创建一个新的账户，会进入到ethapi.api中的newAccount方法中，这个方法会返回一个地址。
```
 func (s *PrivateAccountAPI) NewAccount(password string) (common.Address, error) {
    acc, err := fetchKeystore(s.am).NewAccount(password)
    if err == nil {
      return acc.Address, nil
    }
    return common.Address{}, err
  }
```
 
  
创建账户过程中，首先会通过账户管理系统（account manager）来获取Keystore，然后通过椭圆加密算法产生公私钥对，并获取地址
  
  ```
  func newKey(rand io.Reader) (*Key, error) {
    privateKeyECDSA, err := ecdsa.GenerateKey(crypto.S256(), rand)
    if err != nil {
      return nil, err
    }
    return newKeyFromECDSA(privateKeyECDSA), nil
  }
  ```
  
在获取到公私钥对后，会对用户输入的密码进行加密，并保存入文件。
```

  func (ks keyStorePassphrase) StoreKey(filename string, key *Key, auth string) error {
    keyjson, err := EncryptKey(key, auth, ks.scryptN, ks.scryptP)
    if err != nil {
      return err
    }
    return writeKeyFile(filename, keyjson)
  }
  ```
  
在保存文件的同时，会将新创建的账户加入到缓存中。
```

  func (ks *KeyStore) NewAccount(passphrase string) (accounts.Account, error) {
    _, account, err := storeNewKey(ks.storage, crand.Reader, passphrase)
    if err != nil {
      return accounts.Account{}, err
    }
    // Add the account to the cache immediately rather
    // than waiting for file system notifications to pick it up.
    ks.cache.add(account)
    ks.refreshWallets()
    return account, nil
  }
  ```
  
  ## 2.personal.listAccounts列出所有账户方法
  
用户在控制台输入personal.listAccounts，会进入到ethapi.api中的listAccounts方法中，这个方法会从用户管理中读取所有钱包信息，返回所有注册钱包下的所有地址信息。
```
 func (s *PrivateAccountAPI) ListAccounts() []common.Address {
  addresses := make([]common.Address, 0) // return [] instead of nil if empty
  for _, wallet := range s.am.Wallets() {
   for _, account := range wallet.Accounts() {
    addresses = append(addresses, account.Address)
   }
  }
  return addresses
 }
 ```
 ## 3.eth.sendTransaction 

 sendTransaction会将交易的信息放入区块的pending区
 ```
 // SendTransaction updates the pending block to include the given transaction.
// It panics if the transaction is invalid.
func (b *SimulatedBackend) SendTransaction(ctx context.Context, tx *types.Transaction) error {
	b.mu.Lock()
	defer b.mu.Unlock()

	sender, err := types.Sender(types.HomesteadSigner{}, tx)
	if err != nil {
		panic(fmt.Errorf("invalid transaction: %v", err))
	}
	nonce := b.pendingState.GetNonce(sender)
	if tx.Nonce() != nonce {
		panic(fmt.Errorf("invalid transaction nonce: got %d, want %d", tx.Nonce(), nonce))
	}

	blocks, _ := core.GenerateChain(b.config, b.blockchain.CurrentBlock(), ethash.NewFaker(), b.database, 1, func(number int, block *core.BlockGen) {
		for _, tx := range b.pendingBlock.Transactions() {
			block.AddTx(tx)
		}
		block.AddTx(tx)
	})
	b.pendingBlock = blocks[0]
	b.pendingState, _ = state.New(b.pendingBlock.Root(), state.NewDatabase(b.database))
	return nil
}
```
 代码会首先恢复转出方的地址，参数包括一个Signer和一个Transaction。
 ```
 //Sender方法会将调用者的地址缓存，如果缓存中的地址和调用者的签名一致，那么就会跳过签名这一步。
 func Sender(signer Signer, tx *Transaction) (common.Address, error) {
  if sc := tx.from.Load(); sc != nil {
   sigCache := sc.(sigCache)
   // If the signer used to derive from in a previous
   // call is not the same as used current, invalidate
   // the cache.
   if sigCache.signer.Equal(signer) {
    return sigCache.from, nil
   }
  }

  //Sender()会从本次数字签名的签名字符串(signature)中恢复出公钥，并转化为tx的(转帐)转出方地址。
  
  addr, err := signer.Sender(tx)
  if err != nil {
   return common.Address{}, err
  }
  tx.from.Store(sigCache{signer: signer, from: addr})
  return addr, nil
 }
 ```
 接下来判断交易的nonce是否一致。如果不一致，这抛弃这笔交易。
 然后会从statedb中读取pending区的block信息，并将其进行更新。
 ```
 // GenerateChain creates a chain of n blocks. The first block's
// parent will be the provided parent. db is used to store
// intermediate states and should contain the parent's state trie.
//
// The generator function is called with a new block generator for
// every block. Any transactions and uncles added to the generator
// become part of the block. If gen is nil, the blocks will be empty
// and their coinbase will be the zero address.
//
// Blocks created by GenerateChain do not contain valid proof of work
// values. Inserting them into BlockChain requires use of FakePow or
// a similar non-validating proof of work implementation.
 func GenerateChain(config *params.ChainConfig, parent *types.Block, engine consensus.Engine, db ethdb.Database, n int, gen func(int, *BlockGen)) ([]*types.Block, []types.Receipts) {
  if config == nil {
   config = params.TestChainConfig
  }
  blocks, receipts := make(types.Blocks, n), make([]types.Receipts, n)
  genblock := func(i int, parent *types.Block, statedb *state.StateDB) (*types.Block, types.Receipts) {
   // TODO(karalabe): This is needed for clique, which depends on multiple blocks.
   // It's nonetheless ugly to spin up a blockchain here. Get rid of this somehow.
   blockchain, _ := NewBlockChain(db, config, engine, vm.Config{})
   defer blockchain.Stop()

   b := &BlockGen{i: i, parent: parent, chain: blocks, chainReader: blockchain, statedb: statedb, config: config, engine: engine}
   b.header = makeHeader(b.chainReader, parent, statedb, b.engine)

   // Mutate the state and block according to any hard-fork specs
   if daoBlock := config.DAOForkBlock; daoBlock != nil {
    limit := new(big.Int).Add(daoBlock, params.DAOForkExtraRange)
    if b.header.Number.Cmp(daoBlock) >= 0 && b.header.Number.Cmp(limit) < 0 {
     if config.DAOForkSupport {
      b.header.Extra = common.CopyBytes(params.DAOForkBlockExtra)
     }
    }
   }
   if config.DAOForkSupport && config.DAOForkBlock != nil && config.DAOForkBlock.Cmp(b.header.Number) == 0 {
    misc.ApplyDAOHardFork(statedb)
   }
   // Execute any user modifications to the block and finalize it
   if gen != nil {
    gen(i, b)
   }

   if b.engine != nil {
    block, _ := b.engine.Finalize(b.chainReader, b.header, statedb, b.txs, b.uncles, b.receipts)
    // Write state changes to db
    _, err := statedb.CommitTo(db, config.IsEIP158(b.header.Number))
    if err != nil {
     panic(fmt.Sprintf("state write error: %v", err))
    }
    return block, b.receipts
   }
   return nil, nil
  }
  for i := 0; i < n; i++ {
   statedb, err := state.New(parent.Root(), state.NewDatabase(db))
   if err != nil {
    panic(err)
   }
   block, receipt := genblock(i, parent, statedb)
   blocks[i] = block
   receipts[i] = receipt
   parent = block
  }
  return blocks, receipts
 }
```
 
 
