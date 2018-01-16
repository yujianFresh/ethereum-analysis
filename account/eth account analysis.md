## 1.personal.newAccount方法
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
 
  
首先会通过账户管理系统（account manager）来获取Keystore，然后通过椭圆加密算法产生公私钥对，并获取地址
  
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
