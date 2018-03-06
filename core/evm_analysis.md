#EVM分析 临时记录

>EVM不能被重用，非线程安全

Context结构体：为EVM提供辅助信息。一旦提供，不应更改。  
~~~
// Context 为EVM提供辅助信息。一旦提供，不应更改。
type Context struct {
	// CanTransfer 返回 账户是否拥有足够的以太币以执行转账	CanTransfer CanTransferFunc
	// Transfer 转账函数，将以太币从一个账户转到另一个账户
	Transfer TransferFunc
	// GetHash 返回n对应的哈希
	GetHash GetHashFunc

	// Message information
	Origin   common.Address // Provides information for ORIGIN
	GasPrice *big.Int       // Provides information for GASPRICE

	// Block information
	Coinbase    common.Address // Provides information for COINBASE
	GasLimit    uint64         // Provides information for GASLIMIT
	BlockNumber *big.Int       // Provides information for NUMBER
	Time        *big.Int       // Provides information for TIME
	Difficulty  *big.Int       // Provides information for DIFFICULTY
}
~~~


#以太坊虚拟机：
以太坊虚拟机在交易（Transaction）执行中调用，可以说交易的执行主要由EVM完成。state_processor --> state_transition --> EVM   
基本步骤：
~~~
1) Nonce handling  Nonce 处理
2) Pre pay gas     预先支付Gas
3) Create a new state object if the recipient is \0*32 如果接收人是空，那么创建一个新的state object
4) Value transfer  转账
== If contract creation ==
  4a) Attempt to run transaction data 尝试运行输入的数据
  4b) If valid, use result as code for the new state object 如果有效，那么用运行的结果作为新的state object的code
== end ==
5) Run Script section 运行脚本部分
6) Derive new state root 导出新的state root
~~~

##EVM的实现
以太坊的EVM整个完全是自己实现的，能够直接执行Solidity字节码，没有使用任何第三方运行时。  
运行过程是同步的，没有启用go协程。

1. evm最终是调用Interpreter运行字节码；
2. Interpreter.go实现运行处理；解析出操作码后，通过JumpTable获取操作码对应的函数运行，并维护pc计数器、处理返回值等；  
3. jump_table.go定义了操作码的跳转映射；  
4. instructions.go实现每一个操作码的具体的处理； 
5. opcodes.go中定义了操作码常量    


对于EVM的测试，以太坊将测试代码放在了core\vm\runtime目录下，提供了供测试用的运行时及测试用例。
测试用例的示例如：
~~~
func TestExecute(t *testing.T) {
	ret, _, err := Execute([]byte{
		byte(vm.PUSH1), 10,
		byte(vm.PUSH1), 0,
		byte(vm.MSTORE),
		byte(vm.PUSH1), 32,
		byte(vm.PUSH1), 0,
		byte(vm.RETURN),
	}, nil, nil)
	if err != nil {
		t.Fatal("didn't expect error", err)
	}

	num := new(big.Int).SetBytes(ret)
	if num.Cmp(big.NewInt(10)) != 0 {
		t.Error("Expected 10, got", num)
	}
}
~~~

#合约数据的存储
参考：[分享 | 来自10年经验的大咖对以太坊数据存储的思考与解读](http://www.blockchainbrother.com/article/805)