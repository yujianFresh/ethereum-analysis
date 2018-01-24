node包建立多重协议以太坊节点

一个node是一组服务，通过共享资源提供RPC API。
Services提供devp2p协议，当node实例开始运行，服务被wire到devp2p网络

Node管理资源

Node实例使用到的所有文件系统资源被放到data目录中。
每个资源的路径可以通过额外的node配置改写。
data目录是可选的。==如果没有设置或资源路径没有指定，node包会在内存中创建资源。==

配置Node并开启p2p服务，来访问devp2p网络。
每个devp2p网络上的host有一个唯一标识符，node key.
在重启过程中，Node实例维持这个key。
Node加载static的和trusted可信的节点列表，保证关于其他hosts的知识持久化。

JSON-RPC服务器可以在Node上启动，上面运行着HTTP，WebSocket，IPC。
已注册服务提供的RPC模块，将通过通过这些endpoints提供。
用户可以限制任何endpoint为RPC模块的子集。
Node自身提供debug,admin,web3模块。

通过service context,服务实现可以打开LevelDB数据库。
node包选择每个数据库的文件系统位置。
如果node配置为没有data目录运行，databases替换为内存打开。

Node创建共享的加密的以太坊账户keys的store，Services能够通过service context
访问account manager

在实例之间共享数据目录

如果Multiple node有区别的实例名称，他们能够共享一个数据目录。
共享行为依赖于资源的类型。

devp2p相关资源（node key，static/trusted node列表，known hosts database）存储到与实例名相同的目录中。

LevelDB数据库也存储到实例子目录中。
如果多节点实例使用同一data目录，使用唯一名称打开数据库将为每一个实例创建一个数据库。

账户key store在所有node之间共享，使用一个data目录。
其location可以通过KeyStoreDir配置项修改。

Data Directory Sharing Example见doc.go


