## uniswap功能说明 ##
1. 添加币对，任何人都可以添加，之后留到这个 币对的手续费会给这个添加币对的人分一部分，大概是30个点。
   
2. 兑换。可以使用eth兑换某个代币。或者用某个代币兑换ETH。兑换的原则是AMM。
    - AMM是一个自动做市策略，他规定了你eth兑换代币的时候可以兑换多少个，也规定了一个代币可以兑换多少个eth。
  
3. 提供流动性
    - 意思就是把某个代币和eth按照1:1的比例注入池子。
4. 流动性挖矿是什么意思？
    - 在3的时候提供流动性的多少可以看作是算力的多少。提供的流动性越多，算力越大，算力越大，挖的平台币越多，例如SUSHI就是这样挖出来的。
5. 取消流动性
    - 把注入池子的eth和定量代币拿出来。
    - 





验证过程：
Yp地址
0xcB968f770Dac20E38557f1A6cbbB0285b81e3583
1. create pool. 
    1. 定价
        - 也是跳到了添加流动性页面
        - Eth/yp=是eth数量 / 币的数量。  
        - Yp/eth= yp/eth

    2. approve yp	
        - 调用yp币种的approve函数
        - 参数是两个，第一个是uniswap的合约地址0x7a250d5630b4cf539739df2c5dacb4c659f2488d。第二个参数是2**256-1
        - 
	3. supply
		- 把eth和yp充值到uniswap合约
			addLiquidityETH
                TX_HASH: 0x277db04ba6d56bfe5bda480ada2872a5363c715518db28e35b467c59a93a9446
                token:yp
                amountTokenDesired:10000
                amountTokenMin:10000
                amountETHMin:0.1
                to: msg.sender
                deadline:2020-09-11 16:01:32. (20分钟过期时间)	前端传过来的

            #	Name	Type	Data
            0	token	address	cb968f770dac20e38557f1a6cbbb0285b81e3583
            1	amountTokenDesired	uint256	10000000000000000000000
            2	amountTokenMin	uint256	10000000000000000000000
            3	amountETHMin	uint256	100000000000000000
            4	to	address	a6efe7c7397a81acb47006a1963e2b4bc5ee0f1f
        -  sender 
           -  0xa6eFE7C7397A81acb47006a1963E2B4Bc5eE0f1f
		- 需要知道factory地址
    		- 0x5C69bEe701ef814a2B6a3EDD4B1652CB9cc5aA6f
		- 先获取币对，币对不存在就创建币对
    		- createPair
		- 创建币对
    		- 参数
        		- tokenA ：yp
        		- tokenB：WETH   
            		- 0xc778417e063141139fce010982780140aa0cd5ab  
          		- pair 
            		- 0x5BC6b696fA2D511A09b583C2D05eC011a86520Bd
  		- mintFee   
    		- 没有设置feeGetter就不mint
  		- liquidity mint
    		- 总量=0
        		- sqrt(weth数量*coin数量)-MINIMUM_LIQUIDITY
      		- 挖到to和0x000.
		- 

	4. 流程总结
       - 没有币对先创建 
         - PairCreated
       - 把base币对转到pair
       - 充值0.1个weth到msg.sender
       - msg.sender把0.1个weth充值到币对
       - mintfee
       - mint
         - 先lock,转到0地址最小流动性MINIMUM_LIQUIDITY
         - 转到交易发起人liquidity
       - update reverse
       - 记录Mint日志
       - 剩余的eth转回去
2. 添加流动性
    - 参数
        #	Name	Type	Data
        0	token	address	cb968f770dac20e38557f1a6cbbb0285b81e3583
        1	amountTokenDesired	uint256	1000000000000000000000
        2	amountTokenMin	uint256	995000000000000000000
        3	amountETHMin	uint256	9950000000000000
        4	to	address	a6efe7c7397a81acb47006a1963e2b4bc5ee0f1f
        5	deadline	uint256	1599830105

    - 充值token 、WETH
    - 计算可以换多少
    - 计算流动性
      - Math.min(amount0.mul(_totalSupply) / _reserve0, amount1.mul(_totalSupply) / _reserve1);
    - mint
3. 取消流动性
    - #	Name	Type	Data
    - removeLiquidityETHWithPermit
        0	token	address	cb968f770dac20e38557f1a6cbbb0285b81e3583
        1	liquidity	uint256	3478505426185217165

        2	amountTokenMin	uint256	1094499999999999968535
        3	amountETHMin	uint256	10944999999999999

        4	to	address	a6efe7c7397a81acb47006a1963e2b4bc5ee0f1f
        5	deadline	uint256	1599832552

        6	approveMax	bool	false


        7	v	uint8	28
        8	r	bytes32	b401dc0ec4f29af2fb665854c6c12e55b893c544a6e62e23c2e2c8466fd5b094
        9	s	bytes32	770e3d5215a96aaeb0a7a29e58ab1f69c8d01a8948b8ac5943c3825d5c0655df

    - 过程
      - sender approve uniswap花pair币
      - 从sender发送liquidity个pair到pair合约
      - pair合约销毁pair币
      - 从pair发送token到swap
      - 从pair合约发送WETH到swap
      - sync reserve
      - emit SYNC
      - 从swap发送token到msg.sender
      - 提现WETH到swap
      - swap合约发送eth到sender


4. swap